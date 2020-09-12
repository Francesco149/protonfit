this script automatically patches wine so fitgirl installers don't hang and generates a startup
script that runs the game with proton as well as a .desktop file for application launchers

NOTE: this has been tested with very few games and makes many assumptions. feel free to contribute
patches for better compat

UPDATE: some of the wine patches used by this script have been merged into wine as of 5.17, so
if you prefer you can just manually use plain old wine now as long as you have 5.17

# dependencies
* icoutils (wrestool)
* proton-ge-custom or other proton versions (specify by exporting $PROTON to the proton binary).
  `proton-ge-custom-bin` works fine for a lot of games, but it's recommended to compile
  `proton-ge-custom` from aur instead of downloading binaries, as this will integrate much better
  with your system libraries. see the part on compiling proton-ge-custom below.
* steam-native-runtime
* dmenu or rofi
* curl
* if your distro doesn't provide a steam native runtime and you don't want to manually find
  all the libraries, install steam and export STEAM_RUNTIME=1. you must run and update steam at
  least once. no need to have an account or log in.
  if your steam install isn't at ~/.steam , also export STEAM_HOME_DIR
 
# install
```
curl -lO https://raw.githubusercontent.com/Francesco149/protonfit/master/protonfit
chmod +x ./protonfit
sudo mv ./protonfit /usr/local/bin/
```

# usage
open a terminal, `cd` to the path where `setup.exe` is located and run `protonfit`

NOTE: it's recommended to NOT install directx updates and visual c++ runtime and stuff like that.
      instead, use `tricks` later if needed.

# advanced usage
* `protonfit tricks` runs winetricks on the proton profile, for example
  `protonfit tricks vcrun2015`
* `protonfit run` runs an arbitrary binary with the proton prefix
* to debug wine, you can run `PROTON_LOG=1 WINEDEBUG=+cmd SteamGameId=0 protonfit`
  (change WINEDEBUG value to whatever you like). it will log to ~/.steam-0.log

# if things go wrong
* if the game doesn't run, try running with `STEAM_RUNTIME=1` first, as some system libraries might
  be too new for proton binaries. this can also be solved by just compiling `proton-ge-custom`
  from the aur instead of installing `proton-ge-custom-bin`
* you can wipe your install by doing `rm protonfit-*` and `protonfit` again
* some setups will use absurd amounts of ram. if your pc freezes during install it's possible that
  you ran out of ram, try again and tick the ram limiting option
* if you want to regenerate the config and shortcuts and get the .exe picker menu again without
  reinstalling the game, you can do `FITDBG_SKIP_SETUP=1 protonfit` assuming the game is already
  correctly installed

# compiling proton-ge-custom from source
* set up an arch aur helper. I use trizen.
* install `python-defcon`, but edit the PKGBUILD when prompted and remove the `check()` part.
* install `python-fontpens`, but edit the PKGBUILD when prompted and remove the checkdepends line
* finally, install `proton-ge-custom`
 
# games tested
* `wip` means that it doesn't work but work has been done to get it closer to running
* `installs` means that it installs and appears to run but was not extensively tested in-game.
* `use borderless` means that it's recommended to go into the graphics options and set the game
to "borderless window" instead of fullscreen, which will make tabbing out of the game faster

## Iron Harvest #1552
### v1.0.0.1600 rev.37863 (Build 5487982)
installs

## Crusader Kings 3 #1553
### v1.0.2 + 2 DLCs + Multiplayer
installs

## Age of Empires II - Definitive Edition #1276
### Build 36906 + Enhanced Graphics Pack
installs, requires `protonfit tricks vcrun2015` after install to run

## Gaia Beyond #1534
### Hotfix
installs, use borderless

## Paradise Killer #1559
### Initial repack
installs, use borderless

## Puyo Puyo Tetris #928
### Update 4
wip, manually kill the setup.exe process after it's done to make install continue

CODEX's proper crack is needed, you can install it by running these commands (7z required)

    cd pfx/drive_c/Games/Puyo\ Puyo\ Tetris/
    7z x /path/to/PPT_CODEX_Proper_Crack.zip
    # accept all overwrites
    rm antidenuvo.dll Denuvo64.sys winmm.dll

wine fails to properly initialize `codex64.dll` unfortunately, probably because it tries to
camouflage itself as steamclient64, some research here: https://www.reddit.com/r/wine_gaming/comments/fg4ecu/some_research_regarding_later_denuvo_workarounds/?sort=top

trace: https://gist.github.com/601e40a0c6773111c4c90780b4860c81

## Minecraft Dungeons #1463
### v1.4.3.0_4640940 + 2 DLCs + Multiplayer
wip, setup completes but it needs to run a server that binds on port 443 which needs root. another
issue is that it creates a temp directory with no read permissions which again is only accessible
as root.

the solution I'm thinking is to extract/reverse engineer the python server it runs and just run
Dungeons.exe instead

## As Far as the Eye #1564
### v1.0.0b
installs, use borderless.

## Tamarin #1565
### Initial repack
installs, set render resolution to 100% to fix weird letterboxing

## The Unfinished Swan #1563
### Initial repack
wip, needs `protonfit tricks d3dcompiler_47` . clicking Toys or Play crashes.

# credits
https://github.com/7oxicshadow/proton-standalone-script/blob/master/proton_launch.sh
which I used as reference
