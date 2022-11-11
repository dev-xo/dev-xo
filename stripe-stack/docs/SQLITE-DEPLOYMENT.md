## Welcome to Stripe SQLite Deployment.

This Remix Stack comes with two GitHub Actions that handle automatically deploying our app to Production and Staging environments. Prior to the first deployment, we'll need to do a few things:

1. [Install Fly](https://fly.io/docs/getting-started/installing-flyctl/)
2. Sign up and Log in to Fly:

```sh
fly auth signup
```

3. Create two apps on Fly, one for staging and one for production:

```sh
fly apps create template-name
fly apps create template-name-staging
```

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

> If you don't have openssl installed, you can also use [1password](https://1password.com/password-generator/) to generate a random secret, just replace `$(openssl rand -hex 32)` with the generated secret.

8. Create a persistent volume for the sqlite database for both your staging and production environments. Run the following:

```sh
fly volumes create data --size 1 --app template-name
fly volumes create data --size 1 --app template-name-staging
```

9. Set Fly.io secrets for the current template. Fill the required `.env` variables and run the following command in your console.

```sh
flyctl secrets set NODE_ENV=production ENCRYPTION_SECRET= PROD_HOST_URL= GOOGLE_CLIENT_ID= GOOGLE_CLIENT_SECRET= GITHUB_CLIENT_ID= GITHUB_CLIENT_SECRET= TWITTER_CLIENT_ID= TWITTER_CLIENT_SECRET= DISCORD_CLIENT_ID= DISCORD_CLIENT_SECRET= STRIPE_PUBLIC_API_KEY= STRIPE_SECRET_API_KEY= PLAN_1_PRICE_ID= PLAN_2_PRICE_ID= PLAN_3_PRICE_ID= PROD_STRIPE_WEBHOOK_ENDPOINT_SECRET= EMAIL_PROVIDER_API_KEY=
```

> Notes: Important: `ENCRYPTION_SECRET=""` variable requires "" double quotes for some reason, otherwhise throws an Error at deploy time. Development variables has opted out from this command.

10. Now that everything is set up you can **commit and push** your changes to your repo.

> Every commit to your `main` branch will trigger a deployment to your production environment, and every commit to your `dev` branch will trigger a deployment to your staging environment.

### Connecting to your database

The SQLite database lives at `/data/sqlite.db` in your deployed application. You can connect to the live database by running `fly ssh console -C database-cli`.

### Done!

Return to main [Stripe Stack](https://github.com/dev-xo/stripe-stack) repository.
