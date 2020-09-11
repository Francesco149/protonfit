this script automatically patches wine so fitgirl installers don't hang and generates a startup
script that runs the game with proton as well as a .desktop file for application launchers

NOTE: this has been tested with very few games and makes many assumptions. feel free to contribute
patches for better compat

# dependencies
* icoutils (wrestool)
* proton-ge-custom or other proton versions (specify by exporting $PROTON to the proton binary)
* steam (export STEAM_HOME_DIR if it's not at ~/.steam): you must run and update it once. no need
  to have an account or log in.
* dmenu or rofi
* curl

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

# games tested
* `wip` means that it doesn't work but work has been done to get it closer to running
* `installs` means that it installs and appears to run but was not extensively tested in-game.
* `use borderless` means that it's recommended to go into the graphics options and set the game
to "borderless window" instead of fullscreen, which will make tabbing out of the game faster

## Iron Harvest
installs

## Crusader Kings 3
installs

## Age of Empires II - Definitive Edition
installs, requires `protonfit tricks vcrun2015` after install to run

## Gaia Beyond
installs, use borderless

## Paradise Killer
installs, use borderless. controller doesn't work even though it shows up in control panel

## Puyo Puyo Tetris
wip, manually kill the setup.exe process after it's done to make install continue

CODEX's proper crack is needed, you can install it by running these commands (7z required)

    cd pfx/drive_c/Games/Puyo\ Puyo\ Tetris/
    7z x /path/to/PPT_CODEX_Proper_Crack.zip
    # accept all overwrites
    rm antidenuvo.dll Denuvo64.sys winmm.dll

wine fails to properly initialize `codex64.dll` unfortunately, probably because it tries to
camouflage itself as steamclient64, some research here: https://www.reddit.com/r/wine_gaming/comments/fg4ecu/some_research_regarding_later_denuvo_workarounds/?sort=top

# credits
https://github.com/7oxicshadow/proton-standalone-script/blob/master/proton_launch.sh
which I used as reference
