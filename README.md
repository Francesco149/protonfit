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

# advanced usage
* `protonfit tricks` runs winetricks on the proton profile, for example
  `protonfit tricks vcrun2015`
* `protonfit run` runs an arbitrary binary with the proton prefix
* to debug wine, you can run `PROTON_LOG=1 WINEDEBUG=+cmd SteamGameId=0 protonfit`
  (change WINEDEBUG value to whatever you like). it will log to ~/.steam-0.log

# games tested
`installs` means that it installs and appears to run but was not extensively tested in-game.

* Iron Harvest: installs
* Crusader Kings 3: installs
* Age of Empires II - Definitive Edition: installs, requires `protonfit tricks vcrun2015` after
  install to run
* Gaia Beyond: installs, setting it to borderless instead of fullscreen is recommended so tabbing
  out of the game is faster

# credits
https://github.com/7oxicshadow/proton-standalone-script/blob/master/proton_launch.sh
which I used as reference
