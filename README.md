# tangentscraps/tweetlr

> A single-user Twitter clone in PHP 5 with the Silex microframework —
> Vagrant-provisioned, MySQL-backed, 140-character tweets, seeded with
> Twain/Churchill/Groucho/Feynman quotes.
> Repo: https://github.com/tangentscraps/tweetlr — 15 files, 33 commits,
> Sep 23–28 2015. **Archived 2026-10-19.**

## What it is

**Tweetlr** is "an open-source social media experience for hipsters on the go"
(per the README) — and also, per the repo description, "awesome php5 twitter
clone for your web 3.0 startup." It is a working single-user Twitter clone
built in five days (2015-09-23 → 2015-09-28), 33 commits, by tangentstorm
alone.

The app ships as a Vagrant appliance (`vagrant up`, then point the browser at
`localhost:8080`): Apache + PHP5 + MySQL on a `hashicorp/precise32` box, with
the code mounted as the Apache root. You log in as `tweetlr` / `passwd`, type
into a textarea (with a live character counter and a "tweet yo self."
placeholder), and your tweets land in a MySQL `tweets` table; the front page
shows the 16 most recent. `@mentions` become hyperlinks via a Twig `linkify`
filter, and `/by/<username>` shows one user's posts.

The seed data is the charming part: `cfg/create-db.sql` pre-loads users
`samuel`, `dparker`, `groucho`, `nancy`, `winston`, `feynman` and thirteen
tweets that are famous quotes — the tea-poisoning exchange between nancy and
winston, "The first principle is that you must not fool yourself…", etc.

## Tech stack

- **Backend:** PHP 5, **Silex** microframework (Symfony's micro-kernel, the
  2015-era way to do small PHP apps), Doctrine DBAL for MySQL access
- **Views:** Twig templates (`login.twig`, `tweets.twig`) with a little inline
  JavaScript for the character counter
- **Structure:** a `TweetControllerProvider` mounts routes, a `TweetService`
  holds the queries, a `UserServiceProvider` does the current-user plumbing —
  the commit history shows a deliberate mid-week refactor ("refactor crud
  logic to use the Service pattern")
- **Auth:** Silex `SecurityServiceProvider`, one hard-coded admin user
  (`tweetlr`/`passwd`); new posts are stored with `uid => 1` hard-coded in
  `TweetService::create`
- **Ops:** Vagrantfile + Apache config + `create-db.sql` in `cfg/` — the
  whole environment is provisioned, not documented

## Inventory

| Path | Contents |
|------|----------|
| `README.org` | One page: what it is, `vagrant up`, login creds, `vagrant destroy` |
| `app/index.php` | The whole app bootstrap: providers, firewall, routes, dev helpers |
| `app/lib/Tweetlr/TweetController.php` | Routes: `/`, `/tweet` (POST), `/by/<username>` |
| `app/lib/Tweetlr/TweetControllerProvider.php` | Mounts the controller on `/` |
| `app/lib/Tweetlr/TweetService.php` | `recent()`, `by($username)`, `create($tweet)` — the queries |
| `app/lib/Tweetlr/UserServiceProvider.php` | Current-user plumbing |
| `app/lib/Tweetlr/SecurityController.php` | Login wiring |
| `app/views/tweets.twig` | Timeline page + tweet form + char-counter JS |
| `app/views/login.twig` | Login page (styled 2015-09-27) |
| `app/styles.css` | Rounded corners, drop shadows, fonts (per the commit log) |
| `app/scratch.php` | Three stray unused lines (Swiftmailer registration, dead experiments) |
| `cfg/create-db.sql` | Schema + seed users/quotes + the `recent` view |
| `cfg/composer.json` | Dependency manifest (PHP/Silex/Twig/Doctrine) |
| `cfg/apache.cfg` | VirtualHost config for the VM |
| `Vagrantfile` | `hashicorp/precise32` box, guest 80 → host 8080 |

## Surprising / notable

- **Built in five days:** the commit log is a clean one-week sprint — Vagrant
  box and empty Silex scaffold on Sep 23, DB + auth + views Sep 24–27,
  character limit + linkify polish on Sep 28. The last commit is "linkify the
  link, since github doesn't." — a joke about README.org links.
- **The comments are the documentation:** `index.php` is full of `!!` margin
  notes griping about Silex's security docs ("so annoying"), a joke NIST
  security protocol in the DB password comment, and a live `/encode/{passwd}`
  dev helper for generating password hashes.
- **The anonymous-user workaround:** he wanted the front page visible without
  login, hit a Silex bug where the user is always `'anon'`, and just punted —
  "the spec for this app only requires that posts be shown after the user
  logs in, so I just decided to put the front page behind the firewall."
- **Seed passwords are MD5** (`md5('yay4tweetz')` etc.) while the real login
  uses a digest hash — the seed users are set dressing, noted as such in a SQL
  comment.
- PHP5 + Silex is a dead stack (Silex itself was end-of-lifed in 2018), so this
  is a period piece — which is exactly why the repo was archived rather than
  revived.
- No linked streams or videos found in the repo. Verdict at triage: **document**
  (done — this file) + **archive** (done, 2026-10-19).
