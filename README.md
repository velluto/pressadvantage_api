#Press Advantage API
This is a quick tutorial on how to use the Press Advantage API.  Please contact support@pressadvantage.com with any questions.

#Getting Started
When creating a release you have two major options.  First is a self written release.  You provide the content, and we publish it.

Second, you can leverage our writing services by providing some basic information and we will write a press release based on your order.  You
will then have an opportunity to revise and review it prior to distribution.

NOTE:  The API key and hostname used here are development names and you should be sure to use real data there.

#Organizations
Before you order a release, you need to have an Organization to attach it to.  Organizations hold all of the relevant information that
we need about whoever is publishing the release.

To create an organization:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/organizations.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
  "organization": {
    "name": "Test Organization",
    "contact_name": "The Big Boss",
    "contact_phone": "(425) 555-1212",
    "contact_email": "bossman@sample.org"
  }
}
```

Response:
```
{
  "id": 18,
  "name": "Test Organization",
  "contact_name": "The Big Boss",
  "contact_phone": "(425) 555-1212",
  "contact_email": "bossman@sample.org",
  "about_text": null,
  "address": null,
  "facebook_id": null,
  "twitter_id": null,
  "website_url": null,
  "show_website_in_iframe": false,
  "show_on_pressadvantage_homepage": true
}
```

If you want to get a list of organizations associated with your accounts, you can get one like this:

```
Sending HTTP GET request to: https://app.pressadvantage.com/api/customers/organizations.json?api_key=238416144f20b151d6d6f4710d2578453089fc81
```
Response:
```
[
  {
    "id": 17,
    "name": "sample org",
    "contact_name": "Beefalo Jones",
    "contact_phone": null,
    "contact_email": null,
    "about_text": null,
    "address": null,
    "facebook_id": null,
    "twitter_id": null,
    "website_url": null,
    "show_website_in_iframe": false,
    "show_on_pressadvantage_homepage": true
  },
  {
    "id": 18,
    "name": "Test Organization",
    "contact_name": "The Big Boss",
    "contact_phone": "(425) 555-1212",
    "contact_email": "bossman@sample.org",
    "about_text": null,
    "address": null,
    "facebook_id": null,
    "twitter_id": null,
    "website_url": null,
    "show_website_in_iframe": false,
    "show_on_pressadvantage_homepage": true
  }
]
```

#Self Written Releases
Now we will walk through the process of creating a self-written release.  We will provide content as part of the API request.

First, we create the release:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/with_content.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
  "release": {
    "organization_id": 18,
    "description": "A test release that we wrote ourself",
    "distribution": "standard",
    "schedule_distribution": false,
    "title": "In Imperdiet Et Commodo Vulputate",
    "body": "Maecenas leo odio, condimentum id, luctus nec, molestie sed, justo. Pellentesque viverra pede ac diam. Cras pellentesque volutpat dui.\n\nMaecenas tristique, est et tempus semper, est quam pharetra magna, ac consequat metus sapien ut nunc. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Mauris viverra diam vitae quam. Suspendisse potenti.\n\nNullam porttitor lacus at turpis. Donec posuere metus vitae ipsum. Aliquam non mauris."
  }
}
```


Response from server:
```
{
  "id": 77,
  "state": "needs_to_be_ordered",
  "status": "Not Yet Started",
  "sandbox_order": true,
  "urls": [

  ]
}
```

The state 'needs_to_be_ordered' is an interim state.  It acknowledges your order, but the system has not processed your order yet.  Once the system has
processed your order, the state will change to 'needs_content_approval_by_editors'

NOTE:  We supply you the internal 'state' which is useful programatically, as well as a 'status' field which is a human friendly description of the status of a given release.

```
Sending HTTP GET request to: https://app.pressadvantage.com/api/customers/releases/77.json?api_key=238416144f20b151d6d6f4710d2578453089fc81
```
Response:
```
{
  "id": 77,
  "state": "needs_content_approval_by_editors",
  "status": "Waiting For Editorial Approval",
  "sandbox_order": true,
  "urls": [

  ]
}
```

Normally an editor will review the release and approve or disapprove it.  For sandbox orders, we let you act as the editor and disapprove or approve releases.  To show the various
workflows here we will first reject the release, modify it, and then approve it.

First we will mimic an editorial disapproval:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/77/editor_rejects_content_to_customer.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 77,
  "state": "needs_content_revision_by_customer",
  "status": "Ready For You To Revise Content",
  "distribution_reject_reason": "Sandbox initiated content rejection",
  "sandbox_order": true,
  "urls": [

  ]
}
```

You can see that the response from the server shows the reason why the distribution was rejected.

Now we will resubmit modified content.  We'll just change the title here, but normally you'd follow whatever guidelines the editors gave you:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/77/revise_content.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
  "release": {
    "title": "Varius Nulla Facilisi Cras Non"
  }
}
```


Response from server:
```
{
  "id": 77,
  "state": "needs_content_approval_by_editors",
  "status": "Waiting For Editorial Approval",
  "sandbox_order": true,
  "urls": [

  ]
}
```

Now we trigger a sandbox editorial approval: (NOTE:  When we write the release, 'body' will be in HTML with anchor text and 'body_without_markup' will be in plain text.  This is self written so we push it out as-is)

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/77/editor_approves_content.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 77,
  "state": "needs_distribution",
  "status": "Ready For Distribution",
  "title": "Hac Habitasse Platea Dictumst Maecenas",
  "body": "Duis bibendum, felis sed interdum venenatis, turpis enim blandit mi, in porttitor pede justo eu massa. Donec dapibus. Duis at velit eu est congue elementum.\n\nIn hac habitasse platea dictumst. Morbi vestibulum, velit id pretium iaculis, diam erat fermentum justo, nec condimentum neque sapien placerat ante. Nulla justo.\n\nAliquam quis turpis eget elit sodales scelerisque. Mauris sit amet eros. Suspendisse accumsan tortor quis turpis.",
  "body_without_markup": null,
  "sandbox_order": true,
  "urls": [

  ]
}
```

After editorial approval, the system will push it out to syndication immediately, or hold for syndication if you provided a time for distribution.

For your testing purposes, we have added sandbox triggers for interim states so you can add code to handle it if you like, but there is no further input required at this point.

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/77/distribution_ordered.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```

Response from server:
```
{
  "id": 77,
  "state": "needs_pickups_to_be_found",
  "status": "In Process of Distribution",
  "title": "Hac Habitasse Platea Dictumst Maecenas",
  "body": "Duis bibendum, felis sed interdum venenatis, turpis enim blandit mi, in porttitor pede justo eu massa. Donec dapibus. Duis at velit eu est congue elementum.\n\nIn hac habitasse platea dictumst. Morbi vestibulum, velit id pretium iaculis, diam erat fermentum justo, nec condimentum neque sapien placerat ante. Nulla justo.\n\nAliquam quis turpis eget elit sodales scelerisque. Mauris sit amet eros. Suspendisse accumsan tortor quis turpis.",
  "body_without_markup": null,
  "sandbox_order": true,
  "urls": [

  ]
}
```

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/77/distribution_completed.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```

Response from server:
```
{
  "id": 77,
  "state": "completed",
  "status": "Completed and Published",
  "title": "Hac Habitasse Platea Dictumst Maecenas",
  "body": "Duis bibendum, felis sed interdum venenatis, turpis enim blandit mi, in porttitor pede justo eu massa. Donec dapibus. Duis at velit eu est congue elementum.\n\nIn hac habitasse platea dictumst. Morbi vestibulum, velit id pretium iaculis, diam erat fermentum justo, nec condimentum neque sapien placerat ante. Nulla justo.\n\nAliquam quis turpis eget elit sodales scelerisque. Mauris sit amet eros. Suspendisse accumsan tortor quis turpis.",
  "body_without_markup": null,
  "sandbox_order": true,
  "urls": [

  ]
}
```

After distribution has been completed, 'urls' will provide a sample subset of pickups.  We currently do not provide a comprehensive list of pickups available.

#Written For You Releases
Using our writing services is similar, but uses a different endpoint and adds interaction with the writing team as well.

To order a release that uses our writing services, use the following:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
  "release": {
    "organization_id": 18,
    "description": "Sample Writing Order",
    "distribution": "standard",
    "schedule_distribution": true,
    "distribute_at": "2015-10-13T14:58:15-07:00",
    "main_keyword": "primary keyword",
    "url": "https://devpulse.org",
    "keyword": "anchor text",
    "notes": "These are the instructions for the writing team. montes nascetur ridiculus mus etiam vel augue vestibulum rutrum rutrum neque aenean auctor gravida sem"
  }
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_to_be_ordered",
  "status": "Not Yet Started",
  "sandbox_order": true,
  "urls": [

  ]
}
```

Please note that I changed from immediate distribution to scheduled distribution.  This will change the lifecycle when we're done with the release.  This way you can see the event flow there too.

After a release has been submitted for writing, our editors will review the instructions.  Occasionally we will get instructions that are in conflict with our writing guidelines and policies.

When that happens, you have to approve the exception.  Getting a guideline exception means that we will not be able to syndicate that release everywhere we normally would.  Since that is ok
in some cases and not in others, we require you to accept that exception.  As usual, we have a sandbox flow to simulate this:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/79/editor_approves_order_with_exception.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_guideline_exception_approved_by_customer",
  "status": "Ready For You To Confirm Instructions",
  "instruction_guideline_compliance": "exception_minimal",
  "sandbox_order": true,
  "urls": [

  ]
}
```

We will assume here that you want to approve the exception because you don't care about limited distribution:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/79/approve_guideline_exception.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_content_completion_by_writers",
  "status": "Currently Being Written",
  "sandbox_order": true,
  "urls": [

  ]
}
```

Now the writers are doing their thing.  As you might expect, we have a sandbox trigger to simulate the writers delivering an order:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/79/writers_deliver_order.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_content_approval_by_customer",
  "status": "Ready For You To Approve Content",
  "editor_message": null,
  "title": "suspendisse potenti cras in purus eu magna vulputate",
  "body": "<p>Nulla ut erat id mauris vulputate elementum. Nullam varius. Nulla facilisi.</p><p>Cras non velit nec nisi vulputate nonummy. Maecenas tincidunt lacus at velit. Vivamus vel nulla eget eros elementum pellentesque.</p><p>Quisque porta volutpat erat. Quisque erat eros, viverra eget, congue eget, semper rutrum, nulla. Nunc purus.</p><p>Phasellus in felis. Donec semper sapien a libero. Nam dui.</p><p>Proin leo odio, porttitor id, consequat in, consequat ut, nulla. Sed accumsan felis. Ut at dolor quis odio consequat varius.</p>",
  "body_without_markup": "Nulla ut erat id mauris vulputate elementum. Nullam varius. Nulla facilisi.\n\nCras non velit nec nisi vulputate nonummy. Maecenas tincidunt lacus at velit. Vivamus vel nulla eget eros elementum pellentesque.\n\nQuisque porta volutpat erat. Quisque erat eros, viverra eget, congue eget, semper rutrum, nulla. Nunc purus.\n\nPhasellus in felis. Donec semper sapien a libero. Nam dui.\n\nProin leo odio, porttitor id, consequat in, consequat ut, nulla. Sed accumsan felis. Ut at dolor quis odio consequat varius.",
  "sandbox_order": true,
  "urls": [

  ]
}
```

At this point you can either approve the content or send it back for revision.  Let's ask for a revision first, then approve it.

First the revision request:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/79/reject_content_to_writers.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
  "release": {
    "rewrite_instructions": "Detailed instructions to the writing team for changes go here."
  }
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_content_revision_by_writers",
  "status": "Currently Being Revised By Writers",
  "sandbox_order": true,
  "urls": [

  ]
}
```

Again, we can use the same sandbox trigger as above to simulate the writers making the requested changes.

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/79/writers_deliver_order.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_content_approval_by_customer",
  "status": "Ready For You To Approve Content",
  "editor_message": null,
  "title": "lorem id ligula suspendisse ornare consequat lectus in",
  "body": "<p>Praesent id massa id nisl venenatis lacinia. Aenean sit amet justo. Morbi ut odio.</p><p>Cras mi pede, malesuada in, imperdiet et, commodo vulputate, justo. In blandit ultrices enim. Lorem ipsum dolor sit amet, consectetuer adipiscing elit.</p><p>Proin interdum mauris non ligula pellentesque ultrices. Phasellus id sapien in sapien iaculis congue. Vivamus metus arcu, adipiscing molestie, hendrerit at, vulputate vitae, nisl.</p><p>Aenean lectus. Pellentesque eget nunc. Donec quis orci eget orci vehicula condimentum.</p><p>Curabitur in libero ut massa volutpat convallis. Morbi odio odio, elementum eu, interdum eu, tincidunt in, leo. Maecenas pulvinar lobortis est.</p>",
  "body_without_markup": "Praesent id massa id nisl venenatis lacinia. Aenean sit amet justo. Morbi ut odio.\n\nCras mi pede, malesuada in, imperdiet et, commodo vulputate, justo. In blandit ultrices enim. Lorem ipsum dolor sit amet, consectetuer adipiscing elit.\n\nProin interdum mauris non ligula pellentesque ultrices. Phasellus id sapien in sapien iaculis congue. Vivamus metus arcu, adipiscing molestie, hendrerit at, vulputate vitae, nisl.\n\nAenean lectus. Pellentesque eget nunc. Donec quis orci eget orci vehicula condimentum.\n\nCurabitur in libero ut massa volutpat convallis. Morbi odio odio, elementum eu, interdum eu, tincidunt in, leo. Maecenas pulvinar lobortis est.",
  "sandbox_order": true,
  "urls": [

  ]
}
```

This time, we're going to approve the content.  When you approve content unchanged, it will go straight to the distribution step.  We're going to make a minor edit though so you can see the interim step ... the editing staff
needs to make sure everything is still within guidelines, etc.

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/79/approve_content.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
  "release": {
    "title": "This is a different title."
  }
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_content_approval_by_editors",
  "status": "Waiting For Editorial Approval",
  "sandbox_order": true,
  "urls": [

  ]
}
```

We'll use the sandbox command to approve the content:

```
Sending HTTP POST request to: https://app.pressadvantage.com/api/customers/releases/sandbox/79/editor_approves_content.json?api_key=238416144f20b151d6d6f4710d2578453089fc81 with POST Payload:
{
}
```


Response from server:
```
{
  "id": 79,
  "state": "needs_distribution_waiting_for_scheduled_date",
  "status": "Will Distribute At 2015-10-13T14:58:15-07:00",
  "sandbox_order": true,
  "urls": [

  ]
}
```

You can see how the distribution is scheduled for a future time.  This will automatically move down the same distribution event path as our self-written release once that time has been reached.

