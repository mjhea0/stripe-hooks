## stripe-hooks

[![Build Status](https://travis-ci.org/pearkes/stripe-hooks.png?branch=master)](https://travis-ci.org/pearkes/stripe-hooks)

This is a Python web application to receive [webhooks](https://stripe.com/docs/webhooks)
from Stripe and send emails accordingly.

There are two types of emails:

- [Notifications](https://github.com/pearkes/stripe-hooks-emails/tree/master/notifications), sent to administrators
- [Receipts](https://github.com/pearkes/stripe-hooks-emails/tree/master/receipts), sent to customers

Use cases:

- Sending notifications about important Stripe events, such as failed
charges or new customers, to administrators
- Sending receipts to user after they have been charged

It supports **all** Stripe [events](https://stripe.com/docs/api#event_types).

The email content included by default is versatile English. Any
of it can be modifed to fit your business or use case. It's easy to
deploy and you shouldn't need to touch Python to configure it.

### Configuration

All of the configuration is done in JSON in the [`configuration.json`](configuration.json)
file.

All receipts and notifications are **off by default** with a blank
`configuration.json`. To activate
a notification or receipt, simply create a new key, named by the
event type (the list can be found [here](https://stripe.com/docs/api#event_types))
and formatted like this:

```json
{
  "charge.failed": {
    "active": true,
    "subject": "Oh nos! A Charge Has Failed!"
  }
}
```

**Please note that the default [`configuration.json`](configuration.json)
has active notifications.**

`subject` is optional. By default, the email subject will be the type,
periods replacing spaces and titlecased, prefixed with your
business name (if it exists) like so: `charge.failed -> [Acme Inc.] Charge Failed`.

Everything falls back to safe, generic defaults, like not showing a business name
if it doesn't exist.

Full configuration could look something like this:

```json
{
  "business": {
    "name": "Acme, Inc.",
    "signoff": "The Acme Team",
    "email": "Acme Support Team <support@example.com>"
  },
  "notifications": {
    "balance.available": {
      "active": true,
      "subject": "Dat chedda is available..."
    },
    "charge.succeeded": {
      "active": true
    },
    "charge.failed": {
      "active": true
    },
    "charge.refunded": {
      "active": true
    }
  },
  "receipts": {
    "invoice.created": {
      "active": true,
      "subject": "New Invoice"
    }
  }
}
```

### Deploying

This is designed to be deployed on Heroku. However, it's simple
enough and has so few dependencies that you could run it pretty
much anywhere.

On Heroku:

    $ git clone --recursive git@github.com:pearkes/stripe-hooks.git
    ...
    $ heroku create
    ...
    $ git push heroku master

Then, you'll need to add your keys:

    $ heroku config:add STRIPE_KEY=foobar AWS_ACCESS_KEY=foobar AWS_SECRET_KEY=foobar

That's it. Register that URL with Stripe and you're good to go.

### Email Provider

Right now, it uses [Amazon SES](http://aws.amazon.com/ses/). Reliable, cheap and
easy to set-up.

### Changing Email Content

The default email is include in a submodule called `stripe-hooks-emails`.

You can fork [that repository](https://github.com/pearkes/stripe-hooks-emails),
then update the `.gitmodules` path to use your fork. Then, all you need
to do is a `git submodule update` and you'll be using your content.

Alternatively, you can just clone the repository bare and work off of that.
However, that will make upstream updates harder to integrate.

### Security

In order to avoid fraudulent requests made to the `/webhook/receive` endpoint,
the only attribute trusted in the payload is the event ID. This ID is then
used to request the event directly from Stripe with proper authentication.

### Testing Templates

There are automated tests to guarantee the integrity of your templates.

See the [contributing guide](CONTRIBUTING.md) to learn how to set-up
an environment to run the tests.

### Contributing

See the [contributing guide](CONTRIBUTING.md).
