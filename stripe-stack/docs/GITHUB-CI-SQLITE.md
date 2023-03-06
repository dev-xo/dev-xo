## Welcome to Stripe Stack SQLite Deployment.

This is the Intermediate Deployment for [Fly.io](https://fly.io). If you are new to Fly, I'll recommend selecting the [SIMPLE-DATABASE.md](https://github.com/dev-xo/dev-xo/tree/main/stripe-stack/docs) file of your choice.

> **Note**
> Before continuing, make sure you have the required Stripe Webhook Secret Key for production. If not, please check the [following docs](https://github.com/dev-xo/dev-xo/tree/main/stripe-stack/docs).

Let's deploy our app!

1. [Install Fly](https://fly.io/docs/getting-started/installing-flyctl/)
2. Sign up or Log in into Fly:

```sh
fly auth signup
fly auth login
```

3. Create your apps on Fly, one for staging and one for production: _(Staging steps are optional, you can skip them for the entire process.)_

```sh
fly apps create template-name
fly apps create template-name-staging
```

> **Note**
> You'll have to replace `template-name` with the name of your app for the rest of the steps.
> Make sure this name matches the `app` set into `fly.toml` file. Otherwise, you will not be able to deploy.

4. Initialize Git:

```sh
git init
```

5. Create a new [GitHub Repository](https://repo.new), and then add it as the remote for your project. **Do not push your app yet!**

```sh
git remote add origin <ORIGIN_URL>
```

6. Add a `FLY_API_TOKEN` to your GitHub repo. To do this, go to your user settings on Fly and create a new [token](https://web.fly.io/user/personal_access_tokens/new), then add it to [your repository secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) with the name `FLY_API_TOKEN`.

7. Add a `SESSION_SECRET` to your fly app secrets, to do this you can run the following commands:

```sh
fly secrets set SESSION_SECRET=$(openssl rand -hex 32) --app template-name
fly secrets set SESSION_SECRET=$(openssl rand -hex 32) --app template-name-staging
```

> **Note**
> If you don't have openssl installed, you can also use [1password](https://1password.com/password-generator/) to generate a random secret, just replace `$(openssl rand -hex 32)` with the generated secret.

8. Create a persistent volume for your SQLite database. Run the following:

```sh
fly volumes create data --size 1 --app template-name
fly volumes create data --size 1 --app template-name-staging
```

9. Set Fly.io secrets for the current template. Fill the required `.env` variables and run the following command in your console.

```sh
flyctl secrets set NODE_ENV=production ENCRYPTION_SECRET="" PROD_HOST_URL="https://stripe-stack.fly.dev" EMAIL_PROVIDER_API_KEY= GOOGLE_CLIENT_ID= GOOGLE_CLIENT_SECRET= STRIPE_PUBLIC_KEY= STRIPE_SECRET_KEY= PROD_STRIPE_WEBHOOK_ENDPOINT=
```

> **Warning**
> `ENCRYPTION_SECRET=""` variable requires "" double quotes, otherwise throws an Error at deploy time.
> Also the `PROD_HOST_URL` variable, requires the full name of your app, including `https` and `.fly.dev` domain. Example: `https://my-app.fly.dev`

10. Now that everything is set up you can **commit and push** your changes to your repository _(this will automatically deploy your app)_ or you can just directly deploy to Fly.io by running the following command:

```sh
flyctl deploy
```

> **Note**
> Every commit to your `main` branch will trigger a deployment to your production environment, and every commit to your `dev` branch will trigger a deployment to your staging environment.

### Connecting to your database

The SQLite database lives at `/data/sqlite.db` in your deployed application. You can connect to the live database by running `fly ssh console -C database-cli`.

### Done!

Return to main [Stripe Stack](https://github.com/dev-xo/stripe-stack) repository.<br />
Also leave a ⭐️ if you found it useful! Thank you!
