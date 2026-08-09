# ipa-builder

*[Leer en español](README.es.md)*

Free, open-source tool to build an **unsigned** iOS `.ipa` from any Expo /
React Native project, compiling in the cloud with GitHub Actions (macOS
runner) — no Mac and no paid Apple Developer account (\$99/year) required.

The resulting `.ipa` is installed on your iPhone with
[Sideloadly](https://sideloadly.io/) or [AltStore](https://altstore.io/)
using your free Apple ID, which signs it during the install step itself.
This tool only takes care of compiling it.

Anyone looking for a free **iOS IPA generator** / **unsigned IPA builder**
for sideloading is welcome to use this — no signing, no Mac, no paid
developer account. It's open source and contributions are very welcome.

## Why this exists

To test your own iOS app on your own device, Apple normally requires a paid
account if you want to build outside of a Mac. Sideloadly/AltStore avoid
that cost by signing the app themselves with your free Apple ID — but you
still need to build the `.ipa` somewhere with Xcode. GitHub Actions gives
you free macOS runner minutes; this repo automates that build for any Expo/
React Native project of yours.

## Requirements

- A GitHub account with the [`gh` CLI](https://cli.github.com/) installed
  and authenticated (`gh auth login`).
- An Expo/React Native project in a GitHub repo (yours or one you have
  access to), with `app.json` configured (`ios.bundleIdentifier` set).
- Fork this repo (or copy it) to your own account.

## One-time setup

This workflow needs permission to read the repo you want to build (even if
private) when it does `checkout`, using its own token (not the default
`GITHUB_TOKEN`, which only has access to the repo where the workflow lives).

1. Go to https://github.com/settings/tokens?type=beta → **Generate new
   token** (fine-grained).
2. Give it a name, e.g. `ipa-builder-checkout`.
3. Under **Repository access**, choose **Only select repositories** and
   check the repos you want to be able to build. You can edit the token
   later to add more repos.
4. Under **Permissions → Repository permissions**, set **Contents:
   Read-only**. Nothing else is needed.
5. Generate the token and copy it (it's shown only once).
6. In your terminal, inside your copy of this repo, run:
   ```
   gh secret set CROSS_REPO_TOKEN --repo YOUR-USERNAME/ipa-builder
   ```
   and paste the token when prompted.

## Usage (every time you want a new .ipa)

```
gh workflow run build-ipa.yml --repo YOUR-USERNAME/ipa-builder \
  -f repo=YOUR-USERNAME/your-app \
  -f ref=main \
  -f app_dir=. \
  -f ipa_name=my-app-unsigned
```

- `repo`: the repo (owner/name) containing the app you want to build.
- `ref`: branch, tag, or commit (defaults to `main`).
- `app_dir`: folder inside that repo where the Expo project's
  `package.json`/`app.json` live. Use `.` if it's at the repo root.
- `ipa_name`: name you want for the resulting `.ipa`.

You can also trigger it from the web: the **Actions** tab of your copy of
this repo → **Build unsigned iOS IPA** → **Run workflow**, filling in the
same fields.

When it finishes (takes 10-15 min), scroll down to the **Artifacts** section
of that run and download the `.ipa`. It expires after 14 days (GitHub
Actions limit).

## Notes

- The `.ipa` is not signed by Apple: that's intentional, Sideloadly/AltStore
  sign it themselves with your free Apple ID during install.
- With a free Apple signature, the app stops opening after 7 days unless you
  refresh it with Sideloadly/AltServer.
- It uses macOS runner minutes from your GitHub Actions account (macOS
  runners count 10x against the free quota).
- If the token doesn't have access to the target repo, the first step
  (`Checkout target project`) will fail with a permissions error.

## Contributing

This is open source and contributions are welcome — bug reports, feature
ideas, or pull requests. Feel free to open an issue or PR.

## License

MIT — use, modify, and share freely. See [LICENSE](LICENSE).
