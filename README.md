# SaaSassin Community

**Free, open-source Circle alternative.** Own the community, learning, events, and memberships your audience makes valuable.

Community and courses for creators and organizations operating paid or private communities.

- **Try it:** [community.saasassin.net](https://community.saasassin.net)
- **Research brief and product decision:** [saasassin.net/targets/circle](https://saasassin.net/targets/circle)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Falibad%2Fsaasassin-community&project-name=saasassin-community&repository-name=saasassin-community&env=NEXT_PUBLIC_FIREBASE_API_KEY%2CNEXT_PUBLIC_FIREBASE_AUTH_DOMAIN%2CNEXT_PUBLIC_FIREBASE_PROJECT_ID%2CNEXT_PUBLIC_FIREBASE_APP_ID%2CFIREBASE_PROJECT_ID%2CFIREBASE_CLIENT_EMAIL%2CFIREBASE_PRIVATE_KEY&envDescription=Firebase+web+app+config+and+service-account+credentials+%28see+the+README%29&envLink=https%3A%2F%2Fgithub.com%2Falibad%2Fsaasassin-community%231-create-a-firebase-project)

Circle is a trademark of its owner and is named here only to describe what this product
replaces. SaaSassin Community is an independent, clean-room product and is not affiliated
with or endorsed by Circle.

## What you can do today

| Who | Can now |
|-----|---------|
| Owner | Create a community, add discussion / announcements / events spaces, make a space private to a hand-picked member list **or to whole cohorts**, reorder spaces |
| Owner / admin | Share an invite link, switch it off, or replace it (old links stop working); add an existing account by email as member / moderator / admin; change roles; remove members (they cannot rejoin by link) |
| Owner / admin | Create **and edit** membership plans that unlock private spaces, grant and revoke them by hand, **archive a plan and choose explicitly whether current holders keep access or lose it**, unarchive, and read the immutable access history |
| Owner / admin | **Migrate from Circle**: upload the members and/or posts CSV, read a preview and mapping report (what will be invited, imported, matched, created, skipped and why), then import. Members become pending invites — no accounts are created, nobody is emailed. Re-running the same files imports nothing twice |
| Owner / admin | **Group members into cohorts** with start dates; open private spaces to cohorts; **drip course lessons** N days after each member's cohort start |
| Owner / admin | **Set the posting limit** (posts + replies per member per 10 minutes) |
| Invitee | Open `/apps/community/join/[tenantId]/[inviteCode]`, sign in, join. **If they were imported from Circle with that email, their profile is pre-filled and their imported posts become theirs** |
| Member | **Choose a unique @handle** (validated, changeable, audited); read a feed **that loads page by page with "Load more"**; post, reply (threads also page), react (one each of Like / Celebrate / Insightful), save, search, edit a profile, browse the directory — **and never see another member's email anywhere** |
| Member | **Mute** a member (their posts, replies and notifications disappear for you) or **block** them (also stops them replying to your posts or mentioning you). Private: the other member is not told |
| Member | RSVP to events and add them to a calendar (`.ics`); take courses (lessons unlock on the cohort schedule), tick lessons off, earn a certificate |
| Member | In-app inbox of replies and @mentions; report posts; appeal a removal once per removal |
| Moderator | Pin / unpin, close / reopen comments, remove (with a reason) and restore posts, remove comments, work the reports queue and the appeals queue (never their own appeal), check members in at events. **Exempt from the posting limit; cannot be stopped from replying by a block.** Audit log shows public names, not emails |
| Owner / admin | Download the whole community as versioned JSON (`net.saasassin.workspace-export` manifest + 23 typed files) |

Full status, verification and known gaps: [docs/STATUS.md](docs/STATUS.md).

## Self-host

SaaSassin Community is a Next.js app backed by Firebase Authentication and Cloud Firestore.
You need Node.js 20.9 or newer, [pnpm](https://pnpm.io/installation) and a Firebase project of
your own.

### 1. Create a Firebase project

1. Create a project in the [Firebase console](https://console.firebase.google.com/).
2. **Firestore:** Build → Firestore Database → Create database.
3. **Authentication:** Build → Authentication → Get started. Community signs people in with
   **Google** and **Email/Password**; step 2 below enables both, or enable them yourself under
   Authentication → Sign-in method.
4. **Web app config:** Project settings → General → Your apps → add a Web app. Copy its config
   into the `NEXT_PUBLIC_FIREBASE_*` variables.
5. **Service account:** Project settings → Service accounts → Generate new private key. From the
   downloaded JSON, set `FIREBASE_PROJECT_ID` (`project_id`), `FIREBASE_CLIENT_EMAIL`
   (`client_email`) and `FIREBASE_PRIVATE_KEY` (`private_key`). Never commit the key, and never
   give these variables a `NEXT_PUBLIC_` prefix.
6. Once you deploy, add your domain under Authentication → Settings → Authorized domains
   (`localhost` is already allowed).

### 2. Deploy the rules, indexes and sign-in providers

```bash
pnpm install
pnpm exec firebase login
pnpm exec firebase deploy --only firestore:rules,firestore:indexes,auth --project <your-project-id>
```

`firestore.rules` denies all browser access to product data: every read and write goes through the server routes, which check the session, tenant membership and role with the Firebase Admin SDK. `auth` enables the Google and email/password providers listed in `firebase.json`.

### 3. Run it

```bash
cp .env.example .env.local   # fill in the values from step 1
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000). This repository serves SaaSassin
Community at the root of every host: `next.config.ts` sets `STANDALONE_PRODUCT=community`,
and `src/proxy.ts` maps `/` to the product (`/apps/community`) and `/sign-in` to its sign-in page.

### 4. Deploy to Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Falibad%2Fsaasassin-community&project-name=saasassin-community&repository-name=saasassin-community&env=NEXT_PUBLIC_FIREBASE_API_KEY%2CNEXT_PUBLIC_FIREBASE_AUTH_DOMAIN%2CNEXT_PUBLIC_FIREBASE_PROJECT_ID%2CNEXT_PUBLIC_FIREBASE_APP_ID%2CFIREBASE_PROJECT_ID%2CFIREBASE_CLIENT_EMAIL%2CFIREBASE_PRIVATE_KEY&envDescription=Firebase+web+app+config+and+service-account+credentials+%28see+the+README%29&envLink=https%3A%2F%2Fgithub.com%2Falibad%2Fsaasassin-community%231-create-a-firebase-project)

The button copies this repository into your GitHub account, creates a Vercel project and asks for
the Firebase variables. To deploy an existing fork instead, import it in Vercel and add the
variables from `.env.example` under Project Settings → Environment Variables. Either way, add the
Vercel domain to your Firebase authorized domains afterwards.

## Tests

```bash
pnpm lint
pnpm typecheck
pnpm test        # unit tests
pnpm build
```

The Firestore suites run against the local Firestore emulator, which needs a Java 21+ JDK first on
your `PATH`:

```bash
export JAVA_HOME=/path/to/jdk-21   # macOS example: /Library/Java/JavaVirtualMachines/temurin-21.jdk/Contents/Home
export PATH="$JAVA_HOME/bin:$PATH"
pnpm test:rules      # Firestore security rules
pnpm test:emulator   # the golden workflow against a real Firestore emulator
```

## Contributing

Development happens in the SaaSassin monorepo,
[github.com/alibad/saasassin](https://github.com/alibad/saasassin), the single source of truth for
all ten SaaSassin products. This repository is generated from it by the monorepo's
`scripts/export-product.mjs`, so accepted changes land in the monorepo first and are then synced
here. Open issues and pull requests there or here.

Report vulnerabilities privately as described in [SECURITY.md](SECURITY.md).

## Talk to us

Moving off Circle, running Community for your team, or want to help build it?
[Tell us on saasassin.net](https://saasassin.net/targets/circle#interest).

## License

[GNU Affero General Public License v3.0](LICENSE) (AGPL-3.0-only). Use it, self-host it and modify
it; if you run a modified version as a network service, publish your changes under the same
license.
