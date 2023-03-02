## Welcome to Stripe PostgreSQL Deployment.

This Remix Stack comes with two GitHub Actions that handle automatically deploying our app to Production and Staging environments. Prior to the first deployment, we'll need to do a few things:

1. [Install Fly](https://fly.io/docs/getting-started/installing-flyctl/)

2. Sign up and Log in to Fly:

```sh
fly auth signup
fly auth login
```

3. Create two apps on Fly, one for staging and one for production: _(Staging steps are optional, you can skip them for the entire process.)_

```sh
fly apps create template-name
fly apps create template-name-staging
```

> **Note**
> Make sure this name matches the `app` set into `fly.toml` file. Otherwise, you will not be able to deploy.

4. Initialize Git:

```sh
git init
```

5. Create a new [GitHub Repository](https://repo.new), and then add it as the remote for your project. **Do not push your app yet!**

```sh
git remote add origin <ORIGIN_URL>
```

6. Add a `FLY_API_TOKEN` to your GitHub repo. To do this, go to your user settings on Fly and create a new [token](https://web.fly.io/user/personal_access_tokens/new), then add it to [your repo secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) with the name `FLY_API_TOKEN`.

7. Add a `SESSION_SECRET` to your fly app secrets, to do this you can run the following commands:

```sh
fly secrets set SESSION_SECRET=$(openssl rand -hex 32) --app template-name
fly secrets set SESSION_SECRET=$(openssl rand -hex 32) --app template-name-staging
```

> **Note**
> If you don't have openssl installed, you can also use [1password](https://1password.com/password-generator/) to generate a random secret, just replace `$(openssl rand -hex 32)` with the generated secret.

8. Create a persistent volume for your PostgreSQL database. Run the following:

```sh
fly postgres create --name template-name-db
fly postgres attach template-name-db

fly postgres create --name template-name-staging-db
fly postgres attach template-name-staging-db
```

> **Note**
> Fly will take care of setting the `DATABASE_URL` secret for you.

9. Set Fly.io secrets for the current template. Fill the required `.env` variables and run the following command in your console.

```sh
flyctl secrets set NODE_ENV=production ENCRYPTION_SECRET="" PROD_HOST_URL="my-app-name.fly.dev" EMAIL_PROVIDER_API_KEY= GOOGLE_CLIENT_ID= GOOGLE_CLIENT_SECRET= STRIPE_PUBLIC_KEY= STRIPE_SECRET_KEY= PROD_STRIPE_WEBHOOK_ENDPOINT=
```

> **Note**
> Important❗️: `ENCRYPTION_SECRET=""` variable requires "" double quotes, otherwise throws an Error at deploy time. Development variables has opted out from this command.
> Also the `PROD_HOST_URL` variable, requires the full name of your deployed project, including the `.fly.dev` domain. This could also set later from fly.io dashboard.

10. Now that everything is set up you can **commit and push** your changes to your repository _(this will automatically deploy your app)_ or you can just directly deploy to Fly.io by running the following command:

```sh
flyctl deploy
```

> **Note**
> Every commit to your `main` branch will trigger a deployment to your production environment, and every commit to your `dev` branch will trigger a deployment to your staging environment.

### Done!

Return to main [Stripe Stack](https://github.com/dev-xo/stripe-stack) repository.
Also leave a ⭐️ if you found it useful! Thank you!
