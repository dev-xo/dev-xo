## Welcome to Stripe Stack Deployment

This is the [Stripe Stack](https://github.com/dev-xo/stripe-stack) Deployment for [Fly.io](https://fly.io)

Select the file that matches your database choice and follow the steps inside it. If at any point you get stuck, you can always reach out the [Fly.io Docs](https://fly.io/docs/), or ask for some help into the [Remix Discord](https://discord.com/invite/xwx7mMzVkA).

> **Note**
> If it's your first time using Fly.io, I'll recommend selecting the `SIMPLE-DATABASE.md` file of your choice.

## Stripe Webhook - Production

In order to have success with our deployment, we'll require to get the Api Key for our Stripe Production Webhook. This key will be required to set up the `PROD_STRIPE_WEBHOOK_ENDPOINT` variable in our `.env` file.

Let's see how to get this key:

1. Visit the [Stripe Webhook](https://dashboard.stripe.com/test/webhooks) section.
2. Create a new Webhook Endpoint.
3. Set the name of your future deployed app as the Webhook URL input. _(Check Notes)_
4. Select the latest Stripe API version from `API version` selector.
5. Select the following events to listen on: `checkout.session.completed`, `customer.subscription.updated`, `customer.subscription.deleted`.
6. Add the Endpoint and reveal the `Signing Secret` value that has been provided from Stripe Webhook page.
7. Set this new secret as `PROD_STRIPE_WEBHOOK_ENDPOINT` variable into your `.env` file.

Done! Now we can start receiving Stripe Events to your deployed app.

> **Note**
> Example of a Deployed Webhook Endpoint URL: https://deployed-app.fly.dev/api/webhook