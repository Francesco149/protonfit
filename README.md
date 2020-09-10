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

# credits
https://github.com/7oxicshadow/proton-standalone-script/blob/master/proton_launch.sh
which I used as reference
