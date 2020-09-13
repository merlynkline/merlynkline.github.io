---
layout: post
title:  "Inside out code"
date:   2020-09-07
categories: codecraft
---
An anti-pattern I have come across frequently in legacy code is one where code at the top level of the business logic is written as a simple procedure with the smallest possible [cyclomatic complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity) and the actual business logic is displaced to deeper levels where it is out of the way and does not disrupt the primary narrative of the code. While this approach does have an easily grasped top level, unfortunately it tends to lead to repetition, complexity and confusion as the various parts that make up the substance of the code must express the business logic over and over again, in subtly different ways. I think of this pattern as "**Inside Out**" because a lot of the business logic that's hidden on the inside would be better off on the outside, where we can quickly see and apprehend it.

To illustrate this idea, consider a simple [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) database management application running on a web server and using the web browser to present the user interface. Such an application would have a controller to handle HTTP requests coming from the browser, act on them, and present a response. Skipping some of the details, we might start by writing something like this (in an arbitrary pseudo-code I just invented for the purpose):

``` javascript
proc crud_controller(params) {
    try {
        validate_request(params);
        log_request(params);
        var record = update_database(params);
        log_success(params, record);
        display_result(params, record);
    }
     catch(error) {
        system.log("ERROR in '" + params.action + "' : " + error);
        display_error(params, error);
    }
}
```

This presents a nice simple narrative, describing how we will handle all requests, logging what happens, presenting results to the user, and reporting any errors that occur. We just need to add the various procedures that are called here to complete the application. The first one called is `validate_request`, which might look like this:

``` javascript
proc validate_request(params) {
    if(params.action == "delete" or params.action == "read" or params.action == "update") {
        if(params.id == "") {
            throw "A record ID must be supplied";
        }
    }
    if(params.action == "create" or params.action == "update") {
        // Do field validation
    }
}
```

Here we get some simple business logic that controls the validation depending on the action to be performed (I've left out a big chunk for brevity). Next we could implement `log_request`:

``` javascript
proc log_request(params) {
    var message = "Request '" + params.action + "'";
    if(params.action == "delete" or params.action == "read" or params.action == "update") {
        message += ": record #" + id;
    }
    if(params.action == "create" or params.action == "update") {
        message += ": " + params.fields;
    }
    system.log(message);
}
```

We see some more business logic in here, and the logic itself is a repetition of that in `validate_request` but finding a way to combine these would be difficult, and probably confusing, so we'll leave it like this and move on to `update_database`:

``` javascript
proc update_database(params) {
    var record;
    switch(params.action) {
        case 'create': record.id     = database.create_record(params.fields);
        case 'read':   record.fields = database.read_record(params.id);
        case 'update': record.fields = database.update_record(params.id, params.fields);
        case 'delete': record.fields = database.delete_record(params.fields);
    }
    return(record);
}
```

The business logic here is different but it's nice and clear; a simple dispatcher. And so we carry on, implementing the remaining parts like this:

``` javascript
proc display_result(params, record) {
    var page_template;
    switch(params.action) {
        case 'create':
            page_template = new template("message_template");
            page_template.message = "Database record created";
        case 'read':
            page_template = new template("record_template");
            page_template.message = "Database record";
            page_template.record = record;
        case 'update':
            page_template = new template("message_template");
            page_template.message = "Database record updated";
        case 'delete':
            page_template = new template("record_template");
            page_template.message = "Database record deleted";
            page_template.record = record;
    }
    webserver.send_page(page_template);
}

proc display_error(params, record) {
    var page_template = new template("error_template");
    page_template.error = error
    webserver.send_page(page_template);
}

proc log_request(params) {
    var message = "Request '" + params.action + "'";
    if(params.action == "delete" or params.action == "read" or params.action == "update") {
        message += ": record #" + id;
    }
    if(params.action == "create" or params.action == "update") {
        message += ": " + params.fields;
    }
    system.log(message);
}

proc log_success(params, record) {
    var message = "Success '" + params.action + "': record #" + record.id;
    if(params.action == "read") {
        message += ": " + record.fields;
    }
    system.log(message);
}
```

And that's all there is to it! There's some more similar logic in there; determining which bits are identical is left as an exercise for the reader.

This code should work (obviously I haven't tested it so it's definitely broken) but, despite the simplicity, it is already quite hard to tell what exactly will happen for each of the four CRUD actions. This will make debugging hard, and any attempt to change the behaviour of one of the actions might easily have an unexpected side-effect on another unless we apply a great deal of care to understanding exactly how each line of code is involved in each of the workflows. Any further work on this code, e.g. to add new actions or deal with details that have been skipped over here, will rapidly increase the level of complexity, and thus the difficulty of maintenance.

It turns out that refactoring in this code to expose the business logic at the top level not only makes our intent more immediately obvious also removes nearly all the cyclomatic complexity and nearly all the repetition. This is a good sign of a concise expression of intent and it prominently exposing the significant differences between the workflows.

One way to do this might look more like this:

``` javascript
proc crud_controller(params) {
    try {
        switch(params.action) {
            case 'create': create_record(params);
            case 'read':   read_record(params);
            case 'update': update_record(params);
            case 'delete': delete_record(params);
        }
    }
     catch(error) {
        system.log("ERROR in '" + params.action + "': " + error);
        display_error(params, error);
    }
}

proc create_record(params) {
    validate_fields(params);
    system.log(request_description(params) + ": " + params.fields)
    var record = database.create_record(params.fields);
    system.log(success_message(params, record));
    display_message("Database record created");
}

proc read_record(params) {
    validate_id(params);
    system.log(request_description_with_id(params))
    var record = database.read_record(params.id);
    system.log(success_message(params, record) + ": " + record.fields);
    display_record("Database record", record);
}

proc update_record(params) {
    validate_id(params);
    validate_fields(params);
    system.log(request_description_with_id(params) + ": " + params.fields)
    var record = database.update_record(params.id, params.fields);
    system.log(success_message(params, record));
    display_message("Database record updated");
}

proc delete_record(params) {
    validate_id(params);
    system.log(request_description_with_id(params))
    var record = database.delete_record(params.id);
    system.log(success_message(params, record));
    display_record("Database record deleted", record);
}

proc validate_id(params) {
    if(params.id == "") {
        throw "A record ID must be supplied";
    }
}

proc validate_fields(params) {
    // Do field validation
}

proc success_message(params, record) {
    return("Success '" + params.action + "': record #" + record.id);
}

proc request_description(params) {
    return("Request '" + params.action + "'");
}

proc request_description_with_id(params) {
    return(_request_description(params) + ": record #" + params.id);
}

proc display_message(message) {
    var page_template = new template("message_template");
    page_template.message = message;
    webserver.send_page(page_template);
}

proc display_record(message, record) {
    var page_template = new template("record_template");
    page_template.message = message;
    page_template.fields = record;
    webserver.send_page(page_template);
}

proc display_error(params, record) {
    var page_template = new template("error_template");
    page_template.error = error
    webserver.send_page(page_template);
}
```

Here, we can quickly see that the controller consists of a dispatcher to select a handler for each of the four workflows, and an error handler to deal with any exceptions. The narrative at the top level remains simple, and it is easy to drill down into the handlers to find out what each of them does. Although there is now a little more cyclomatic complexity here, this is now all of it!

The handlers in turn are each very simple and we can quickly see what each one of them does, drilling down through the layers to get to further detail should it be necessary for whatever we are trying to understand. Thus each layer of the code is expressing a simple idea at its own level of granularity and we need look no deeper than the granularity of the problem we are solving to understand the behaviour of the code at that level. Overall cyclomatic complexity is greatly reduced and where there is some repetition (e.g. `read_record` and `delete_record` have the same first two lines) this is in simple logic and could easily be sprouted if we desired, or we could choose to see it as a concise expression of the similarities between the handlers and leave it in place.

It's easy to see how inside out code develops when we start with a simple top-level narrative and then fill in the pieces, handling special cases as we come across them. It's important to be aware of how complexity is growing as your code develops, watching out for this anti-pattern and eliminating it by making time to perform frequent refactoring as we go. This process has the added benefit of requiring constant re-analysis of the workflows, often exposing errors in planned implementations and revealing useful similarities and efficiencies that might be exploited.

Rushing features out of the door to production with some of the code inside out is easily done and usually regretted later.

Merlyn Kline  
September 2020
