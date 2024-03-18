# Chatbot UI

The open-source AI chat app for everyone.

<img src="./public/readme/screenshot.png" alt="Chatbot UI" width="600">




## Updating

In your terminal at the root of your local Chatbot UI repository, run:

```bash
npm run update
```

If you run a hosted instance you'll also need to run:

```bash
npm run db-push
```

to apply the latest migrations to your live database.

## Hosted Instance on Supabase + Vercel

Il va tout d'abord falloir créer les dockers et monter les runtime de database supabase en local, pour ensuite les "pusher" sur supabase.co.

Follow these steps to get your own Chatbot UI instance.


### 1. Fork the Repo

Sur Github, faire un fork du git officiel https://github.com/mckaywrigley/chatbot-ui.git -> ex devient : https://github.com/mypersonalgithubaccount/chatbot-ui-hosted


Puis, clone le fork
```bash
git clone https://github.com/mypersonalgithubaccount/chatbot-ui-hosted
```

### 2. Install Dependencies

Open a terminal in the root directory of your local Chatbot UI repository and run:

```bash
npm install
```

### 3. Install Supabase & Run Locally

#### Why Supabase?

Previously, we used local browser storage to store data. However, this was not a good solution for a few reasons:

- Security issues
- Limited storage
- Limits multi-modal use cases

We now use Supabase because it's easy to use, it's open-source, it's Postgres, and it has a free tier for hosted instances.

We will support other providers in the future to give you more options.

#### 1. Install Docker

You will need to install Docker to run Supabase locally. You can download it [here](https://docs.docker.com/get-docker) for free.

LANCER LE DOCKER ENGINE

#### 2. Install Supabase CLI

**Windows**

```bash
scoop bucket add supabase https://github.com/supabase/scoop-bucket.git
scoop install supabase
```

#### 3. Start Supabase

In your terminal at the root of your local Chatbot UI repository, run:

```bash
supabase start
```

ATTENDRE 5 MINUTES QUE LES CONTAINERS SOIENT TOUS LANCES

On sait que le service s'est lancé correctement lorsque la commande suivante ne renvoie pas de message d'erreur 

```bash
supabase status
```


### 4. Setup Backend with Supabase

#### 1. Create a new project

Go to [Supabase](https://supabase.com/) and create a new project.

On peut ajouter un mot de passe à la database (pas obligé, mais c'est mieux)

#### 2. Get Project Values

Once you are in the project dashboard, click on the "Project Settings" icon tab on the far bottom left.

Here you will get the values for the following environment variables:

- `Project Ref`: Found in "General settings" as "Reference ID"

- `Project ID`: Found in the URL of your project dashboard (Ex: https://supabase.com/dashboard/project/<YOUR_PROJECT_ID>/settings/general)

While still in "Settings" click on the "API" text tab on the left.

Here you will get the values for the following environment variables:

- `Project URL`: Found in "API Settings" as "Project URL"

- `Anon key`: Found in "Project API keys" as "anon public"

- `Service role key`: Found in "Project API keys" as "service_role" (Reminder: Treat this like a password!)

#### 3. Configure Auth

Next, click on the "Authentication" icon tab on the far left.

In the SMTP Settings tab, make sure "EmailEnable Custom SMTP" is enabled + Fill in sender details & SMTP provider settings to be able to send emails (mandatory to setup first user account on the app at first launch)

#### 4. Connect to Hosted DB

Open up your repository for your hosted instance of Chatbot UI.

In the 1st migration file `supabase/migrations/20240108234540_setup.sql` you will need to replace 2 values with the values you got above:

- `project_url` (line 53): Use the `Project URL` value from above
- `service_role_key` (line 54): Use the `Service role key` value from above

SAVE THE FILE LOCALLY, BUT DO NOT COMMIT NOR PUSH

Now, open a terminal in the root directory of your local Chatbot UI repository. We will execute a few commands here.

Login to Supabase by running:

```bash
supabase login
```

Next, link your project by running the following command with the "Project ID" you got above:

```bash
supabase link --project-ref <project-id>
```

Your project should now be linked.

Finally, push your database to Supabase by running:

```bash
supabase db push
```

Your hosted database should now be set up!

DISCARD THE CHANGES ON MIGRATION FILE

### 5. Setup Frontend with Vercel

Go to [Vercel](https://vercel.com/) and create a new project.

In the setup page, import your GitHub repository for your hosted instance of Chatbot UI. Within the project Settings, in the "Build & Development Settings" section, switch Framework Preset to "Next.js".

In environment variables, add the following from the values you got above:

- `NEXT_PUBLIC_SUPABASE_URL` (=PROJECT URL du projet cloud SUPABASE)
- `NEXT_PUBLIC_SUPABASE_ANON_KEY` (= ANON KEY du projet cloud SUPABASE)
- `SUPABASE_SERVICE_ROLE_KEY` (= SERVICE ROLE KEY du projet cloud SUPABASE)
- `NEXT_PUBLIC_OLLAMA_URL` (only needed when using local Ollama models; default: `http://localhost:11434`)

You can also add API keys as environment variables. => PAS NECESSAIRE CAR CELA SERA RENSEIGNE DEPUIS LE FRONT END

- `OPENAI_API_KEY`
- `AZURE_OPENAI_API_KEY`
- `AZURE_OPENAI_ENDPOINT`
- `AZURE_GPT_45_VISION_NAME`

For the full list of environment variables, refer to the '.env.local.example' file. If the environment variables are set for API keys, it will disable the input in the user settings.

Click "Deploy" and wait for your frontend to deploy.

Once deployed, you should be able to use your hosted instance of Chatbot UI via the URL Vercel gives you.

AU PREMIER DEMARRAGE, IL FAUT SE CREER UN COMPTE -> un email sera envoyé (donc mettre un vrai email). Attention, au moment de confirmer l'email, cela renvoie vers un webhook avec token en "localhost:3000/code?=xxx" -> remplacer par "vercel-url/code?=xxx".


