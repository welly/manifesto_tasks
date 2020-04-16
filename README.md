# Manifesto Interview Task

## Introduction

This repo and additional accompanying repos have been built to demonstrate the process and thinking behind the tasks requested by Manifesto.

There are three repos related to the tasks which are:

- This one (https://github.com/welly/manifesto_tasks)
- The demo module (https://github.com/welly/manifesto_helper)
- The data source for the location weather (https://github.com/welly/manifesto_json/)

Starting with the data source, I have used a remote JSON server, which can be found at https://my-json-server.typicode.com/welly/manifesto_json to act as an external data provider/API mocking tool. The borough data can be viewed at https://my-json-server.typicode.com/welly/manifesto_json/boroughs and an individual borough using the ID - https://my-json-server.typicode.com/welly/manifesto_json/boroughs/1 - or, as the demo module uses, by passing a querystring parameter https://my-json-server.typicode.com/welly/manifesto_json/boroughs?postcode=E1%206JE.

This repository, in addition to the task documentation, contains exported config which defines the approach used to define the article content type requirements.

The approach taken was to use the [paragraphs](https://www.drupal.org/project/paragraphs) module, which provides the required functionality requested. Three paragraph types had been created - Call to Action, Wysiwyg and Image types. As many of these paragraph types can be added to an individual node and can be drag/dropped into place, which will reflect on viewing the node. Paragraphs provides a simple interface for content editors and keeps enough restraint that a site design can be maintained.

An alternative approach was to enable the Layout Builder module and to allow individual nodes to have their layout altered. Defining a base layout for a particular view mode (full content would be appropriate in this situation) and adding fields directly to the content type rather than via paragraph types would allow a similar editing experience to using paragraphs however it does not quite offer the same flexibility as requested by the task - in particular being able to add as many instances of a particular component as the editor requires as this approach would be limited to a single instance of a field and as such using paragraphs seemed like the correct approach as determined by the task specification.

The second repository contains the demo module code and consists of a block plugin, an event listener and a .module file. The block plugin outputs the borough name and current weather - this is pulled from the data provider using Guzzle/HTTP client. A URL cache context are set so that the block does not display the same data on each article page and two cache tags are set so that the block cache tags can be invalidated by the event listener either as a whole, using the "weather_block" tag, or individually by using the tag appended with the borough ID stored by the data provider.

In this example, I have demonstrated my thinking by using an event listener to invalidate the cache tags, which is triggered by a simple page load. However in a real world situation the business logic for invalidating cache tags - and refreshing the weather widget block - could be determined by a number of different methods, for example:

- A cron task, rather than an event listener, which runs every X minutes that invalidates all weather block cache tags, as we are doing in the example with the event listener.
- Invalidating individual weather widget blocks by the same event listener but targeting a specific cache tag (weather_block:1, for instance) using the current route match service to get the current article node.
- Storing the current weather for a particular article node in a node field or in the key_value table and checking if this value has changed by a call to the data provider and invalidating just this node's weather block cache tag. This again could be done on a page view/load in the same manner as the current event listener.

The alternative approach I considered trying was rather than pulling the data for a borough in on the backend and inserting the values in a block was to simply generate a block as a placeholder and then using ajax to populate the block with data from the data provider. This approach would have the benefit of not having to be as concerned with Drupal's cache mechanism as the block would be populated with borough data "live", however would also lose the benefit of the speed offered by Drupal's cache mechanism in particular in cases where site traffic is high.

In addition to the weather widget block defined in the module repository is the code that provides an approach for the second task - *"If current editor role is not administrator, Article form Title field must have the description "Lorem ipsum dolor sit amet, consectetur adipiscing elit."*. This was solved using a simple hook_field_widget_form_alter hook - checking to see if the current user does have an administrator role and then checking to see if the form being shown is both a node form and an article bundle. If all these were true, we were able to set the description text on the field widget for node title fields.
