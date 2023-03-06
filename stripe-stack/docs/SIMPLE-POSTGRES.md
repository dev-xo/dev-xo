## Welcome to Stripe Stack Postgres Deployment.

This is the Simple Deployment for [Fly.io](https://fly.io). If you are new to Fly.io, I'll recommend checking the [Fly.io Getting Started](https://fly.io/docs/getting-started/) guide.

> **Note**
> Before continuing, make sure you have the required Stripe Webhook Secret Key for production. If not, please check the [following docs](https://github.com/dev-xo/dev-xo/tree/main/stripe-stack/docs).

Let's deploy our app!

1. [Install Fly](https://fly.io/docs/getting-started/installing-flyctl/)
2. Sign up or Log in into Fly:

```sh
fly auth signup
fly auth login
```

3. Create your Fly app:

```sh
fly apps create template-name
```

> **Note**
> You'll have to replace `template-name` with the name of your app for the rest of the steps.
> Make sure this name matches the `app` set into `fly.toml` file. Otherwise, you will not be able to deploy.

4. Add a `SESSION_SECRET` to your fly app secrets, to do this you can run the following command:

```sh
fly secrets set SESSION_SECRET=$(openssl rand -hex 32) --app template-name
```

> **Note**
> If you don't have openssl installed, you can also use [1password](https://1password.com/password-generator/) to generate a random secret, just replace `$(openssl rand -hex 32)` with the generated secret.

5. Create a persistent volume for your PostgreSQL database. Run the following:

```sh
fly postgres create --name template-name-db
fly postgres attach template-name-db
```

6. Set up your Fly.io secrets for the current app. Fill the bellow `.env` variables and run the following command in your console.

```sh
flyctl secrets set NODE_ENV=production ENCRYPTION_SECRET="" PROD_HOST_URL="https://stripe-stack.fly.dev" EMAIL_PROVIDER_API_KEY= GOOGLE_CLIENT_ID= GOOGLE_CLIENT_SECRET= STRIPE_PUBLIC_KEY= STRIPE_SECRET_KEY= PROD_STRIPE_WEBHOOK_ENDPOINT=
```

> **Warning**
> `ENCRYPTION_SECRET=""` variable requires "" double quotes, otherwise throws an Error at deploy time.
> Also the `PROD_HOST_URL` variable, requires the full name of your app, including `https` and `.fly.dev` domain. Example: `https://my-app.fly.dev`

7.  Now that everything is set up we can deploy to Fly.io by running the following command:

```sh
flyctl deploy
```

### Done!

Return to main [Stripe Stack](https://github.com/dev-xo/stripe-stack) repository.<br />
Also leave a ⭐️ if you found it useful! Thank you!
