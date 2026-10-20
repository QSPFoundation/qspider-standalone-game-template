# Qspider standalone game releases

This repo is a template to start a repo allowing you to build standalone release of your qspider game.

# Usage

Generate new repository from this template. Make sure to check `Include all branches` checkbox (this will activate Github Pages)

## Adding game files
- clone repository
- put your game files into root folder
- fill in `game.cfg` (if you don't have one already) with game id, title and path to game starting file, use other settings when needed
- replace `game-icon.png` with your game icon - it should be 1024x1024 pixels PNG
- commit and push

## Deploying web version of game
- Go to Actions in repository
- in left sidebar select `Update github pages (game only)`
- Click `Run worflow` dropdown in top left corner of the table and click `Run worflow` button inside dropdown
- Select `All workflows` in left sidebar
- Wait for `Update github pages (game only)` and `pages build and deployment` workflows to complete

Now your game should be avaliable under URL you saw when configuring github pages (usually https://{user_name}.github.io/{repo_name}/)

## Generate signing keys for game auto updates (for this step you need NodeJS installed) 
- open terminal app and run
  ```sh
  npx @tauri-apps/cli signer generate
  ```
  It will ask for password - make sure to remember it.
- From output in console you need
    1. Copy public key (that comes after `Your public key was generated successfully:` text) and paste it into `qspider.desktop.json` file into `updater.pubkey` field
    2. Go to repository Settings - Secrets and Variables - Actions and in Repository secrets sections add 2 secrets
      + name: `TAURI_PRIVATE_KEY`, for value copy private key from cli output that comes after text `Your secret key was generated successfully - Keep it secret!`
      + name: `TAURI_KEY_PASSWORD`, and as velue put the password used when generating keys

## Fill in rest of desktop settings
- open `qspider.desktop.json`
- update `productName` with your game name
- set `version` if you want to start not with `1.0.0`
- change `identifier` - english letters only separated with dot
- fill `publisher`, `copyright`, `shortDescription` and `longDescription` fields
- to enable autoupdates set `updater.enabled` to `true`, put your public key into `updater.pubkey` and in `updater.endpoint` add link to your github pages with `updater-v2.json` file (something like `https://{user_name}.github.io/{repo_name}/updater-v2.json`)
- adjust game window settings in `window` section
- commit and push

## Release desktop version
- Go to Actions in repository
- in left sidebar select `Game release`
- Click `Run worflow` dropdown in top left corner of the table and click `Run worflow` button inside dropdown 
- Select `All workflows` in left sidebar
- Wait for `Game release` workflow to complete
- In left sidebar select `Update github pages (game and updater)`
- Click `Run worflow` dropdown in top left corner of the table and click `Run worflow` button inside dropdown 
- Select `All workflows` in left sidebar
- Wait for `Update github pages (game and updater)` and `pages build and deployment` workflows to complete

Link to your release will appear on main repository page in right sidebar

## Release next game versions 
- update your game assets in repository
- increase game version in `qspider.desktop.json` file in `version` field
- commit and push
- go through steps in `Release desktop version` section

## No web version

If you don't want online version of game - when making desktop release use `Update github pages (updater only)` workflow instead of `Update github pages (game and updater)`.

## No autoupdates

If you don't want to support autoupdates in your game

- skip whole `Generate signing keys for game auto updates` section
- in `qspider.desktop.json` keep `updater.enabled` set to `false` (the default)
- use `Update github pages (game only)` workflow when updating web version

## Migrating an existing game repo (Tauri 2)

qSpider desktop builds now use Tauri 2. The game config moved from `game.build.conf.json` to `qspider.desktop.json`.

1. Copy the new workflow files from this repository (`.github/workflows/*.yml`) into your game repository.
2. Create `qspider.desktop.json`. Either fill it in by hand (see the template's file), or run the converter from a qSpider checkout (`pnpm install` first):
   ```sh
   pnpm standalone:config --from-v1 path/to/game.build.conf.json > qspider.desktop.json
   ```
3. **Keep `identifier` exactly as it was before, otherwise players will lose their saves.**
4. Review the list of dropped fields the converter prints to stderr. Settings with no Tauri 2 equivalent are not carried over (typically `tauri.bundle.active`, `tauri.bundle.targets`, `tauri.updater.dialog` and `tauri.windows[0].visible`).
5. Delete `game.build.conf.json`.
6. Use `updater-v2.json` as the updater endpoint (`https://{user_name}.github.io/{repo_name}/updater-v2.json`). Players on the old (Tauri 1) build of your game cannot update to the new one and must reinstall it once.
7. The minimum supported Linux is now Ubuntu 22.04 era (WebKitGTK 4.1).
