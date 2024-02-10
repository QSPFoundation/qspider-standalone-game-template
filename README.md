# Qspider standalone game releases

This repo is a template to start a repo allowing you to build standalone release of your qspider game.

# Usage

Generate new repository from this template

## Adding game files
- clone repository
- put your game files into root folder
- fill in `game.cfg` (if you don't have one already) with game id, title and path to game starting file, use other settings when needed
- replace `game-icon.png` with your game icon - it should be 1024x1024 pixels PNG
- commit and push

## Configure github pages
- Go to repositor
- Open branch dropdown, type `gh-pages` in serch input and press `Create gh-pages branch from main`
- Go to Settings - Pages
- In Build and deployment section check that new `gh-pages` branch is selected in Branch section (change if not)
- after same time you should see `Your site is live at ` with some URL

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
    1. Copy public key (that comes after `Your public key was generated successfully:` text) and paste in into `game.build.conf.json` file into `pubkey` field
    2. Go to repository Settings - Secrets and Variables - Actions and in Repository secrets sections add 2 secrets
      + name: `TAURI_PRIVATE_KEY`, for value copy private key from cli output that comes after text `Your secret key was generated successfully - Keep it secret!`
      + name: `TAURI_KEY_PASSWORD`, and as velue put the password used when generating keys

## Fill in rest of desktop settings
- open `game.build.conf.json`
- update `package.productName` with your game name
- set `package.version` if you want to start not with `1.0.0`
- change `tauri.bundle.identifier` - english letters only separated with dot
- fill `tauri.bundle.publisher`, `tauri.bundle.copyright`, `tauri.bundle.shortDescription` and `tauri.bundle.longDescription` fields
- in `tauri.updater.endpoints` add link to your github pages with `updater.json` file (something https://{user_name}.github.io/{repo_name}/updater.json)
- adjust game window settings in `tauri.window` section
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
- increate game version in `game.build.conf.json` file in `package.version` field
- commit and push
- go through steps in `Release desktop version` section

## No web version

If you don't want online version of game - when making desktop release use `Update github pages (updater only)` workflow instead of `Update github pages (game and updater)`.

## No autoupdates

If you don't want to support autoupdates in your game 

- skip whole `Generate signing keys for game auto updates` section
- in `game.build.conf.json` set `tauri.updater.active` field to `false`
- remove `auri.updater.endpoints`, `auri.updater.dialog` and `auri.updater.pubkey` fields
- use `Update github pages (game only)` workflow when updating web version
