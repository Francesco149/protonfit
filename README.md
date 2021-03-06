this script automatically patches wine so fitgirl installers don't hang and generates a startup
script that runs the game with proton as well as a .desktop file for application launchers

NOTE: this has been tested with very few games and makes many assumptions. feel free to contribute
patches for better compat

UPDATE: some of the wine patches used by this script have been merged into wine as of 5.17, so
if you prefer you can just manually use plain old wine now as long as you have 5.17

# dependencies
these are based on arch linux derivatives but you can adapt to other distros
* icoutils (wrestool)
* proton-tkg-git from the chaotic aur https://lonewolf.pedrohlc.com/chaotic-aur/
* a tkg-pds kernel matching your cpu architecture from that same aur will help performance a lot
  thanks to fsync support and other goodies (also in the chaotic aur)
* on amd, you also want mesa-tkg-git and lib32-mesa-tkg-git (also in the chaotic aur) to fix shader
  stutters
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

# using it as a runner for proton without steam
* `protonfit prefix` to create a prefix at ./pfx
* `run` and `tricks` commands from "advanced usage"

# advanced usage
* `protonfit tricks` runs winetricks on the proton profile, for example
  `protonfit tricks vcrun2015`
* protonfit modifies winetricks so you can inject your own custom verbs by placing them in
  `~/.config/tricks` . for example, you can take the [vcrun2019_ge](https://github.com/GloriousEggroll/protonfixes/blob/master/gamefixes/verbs/vcrun2019_ge.verb)
  verb from GloriousEggroll 's custom protonfixes, place it in `~/.config/tricks` and then you
  can install it with `protonfit tricks vcrun2019_ge`
* if `protonfit tricks` fails for some reason, you can also use `PROTON_WINETRICKS=1` and use the
  winetricks ui
* `protonfit run` runs an arbitrary binary with the proton prefix
* to debug wine, you can run `PROTON_LOG=1 WINEDEBUG=+cmd SteamGameId=0 protonfit`
  (change WINEDEBUG value to whatever you like). it will log to ~/.steam-0.log
* export `FIT_ARGS` to pass arguments to the game binary

# if things go wrong
* if the game doesn't run, try running with `STEAM_RUNTIME=1` first, as some system libraries might
  be too new for proton binaries
* you can wipe your install by doing `rm protonfit-*` and `protonfit` again
* some setups will use absurd amounts of ram. if your pc freezes during install it's possible that
  you ran out of ram, try again and tick the ram limiting option
* if you want to regenerate the config and shortcuts and get the .exe picker menu again without
  reinstalling the game, you can do `protonfit configure`
 
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

* save data/config location: `pfx/drive_c/users/steamuser/Local Settings/Application Data/ParadiseKiller/Saved/`

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

## Horizon: Zero Dawn – Complete Edition #1503
### Initial repack
installs, needs `protonfit tricks vcrun2015 mfc140` and the very latest proton-tkg-git
(5.17.r0.gc44db537 or newer)

if it gets killed by out of memory killer, try `echo 1 | sudo tee /proc/sys/vm/overcommit_memory`

if you wish to redo the game optimization process, delete `LocalCacheDX12/PSOCache.bin` in the
game folder

there are severe memory leaks, on my rx 570 I cannot get the game to work no matter what. people
with nvidia have had success on the other hand

* game ram usage spikes up and either gets killed by OOM or crashes by failing to alloc
* the ram spike usage doesn't always happen. sometimes it lasts longer but still crashes during
  initial cutscene
* compiling latest https://github.com/HansKristian-Work/vkd3d-proton doesn't seem to help but it
  seemed to last longer through the cutscene on average
* same behaviour on xanmod, liquorix, zen, tkg-pds
* a few other people report exact same behaviour on similar hardware, with no solution
* tested proton-tkg-git-5.19.r10.g80498dd4-1, the known working proton-ge version,
  wine-staging 5.18, wine-tkg-git 5.18.r11.g19466905-299, all same thing
  on all of them
* tested mesa 20.2.0 and mesa-tkg-git-20.3.0_devel.130091.7d1fb5fffe3, same thing
* amdvlk always lasts longer, but crashes in a similar fashion at around 10GB ram

## Tell Me Why: Complete Season #1566
### Initial repack
installs

* save data/config location `pfx/drive_c/users/steamuser/Local Settings/Application Data/Dontnod/`

## Disgaea 4 Complete+ #1568
### Initial repack
wip, needs `protonfit tricks d3dcompiler_47`, must add `MovieSkip=1` to
`pfx/drive_c/users/steamuser/Application\ Data/Nippon\ Ichi\ Software\,\ Inc/Disgaea\ 4\ Complete+/*/config.ini`
and also set `WindowMode=1` and change `ResolutionWidth` and `ResolutionHeight` to fullscreen.

it's very picky about fullscreen/resolution. sometimes it will get stuck on the loading screen or
a black screen with the wrong settings.

`WindowMode=2` might also help with startup issues.

audio doesn't seem to work

## The Outer Worlds #1257
### Updated The Outer Worlds v1.4.0.595 + Peril on Gorgon DLC
installs, need to install mf with this script https://github.com/z0z0z/mf-install

you need to call the mf-install script in proton mode, like so:

```
PROTON=/usr/share/steam/compatibilitytools.d/proton_tkg_makepkg WINEPREFIX=/path/to/pfx ./mf-install.sh -proton
```

## Hotshot Racing #1567
### Initial repack
installs

## Assassin’s Creed: Odyssey #1021
### Updated Assassin’s Creed: Odyssey – Ultimate Edition v1.5.3 + All DLCs
broken

something goes wrong when `AFR_x64` is called.  it's using the stdin/stdout patched afr from here
https://fileforums.com/showthread.php?p=466467 .

razor's DLL seems to be packed/obfuscated. this would be much easier if these compression tools
were open source...

either way, I initially thought it was failing to read stdin because it couldn't open `CONIN$` when
called manually, but if I do

```
while true; do pid=$(pgrep AFR) && sudo strace -p$pid -s9999 -e write; done > afrlog 2>&1
```

to attach to AFR it seems to be writing binary data to file descriptor 3

logs ends with `+++ exited with 253 +++` . no idea what that means.

for now, you can just use
`Assassin's Creed: Odyssey Ultimate Edition v1.5.3 + All DLC [EMPRESS] [Linux Wine]` hash
`F7D2C023BD6B18CB948607E40F5A4A6E2C0C3AEA`

## Borderlands 3 #1261
### Updated Borderlands 3: Super Deluxe Edition Build 5382210 + All DLCs
installs

* installed with: proton-tkg-git 5.19.r12.gbe9c9681-1
* played with: official proton 5.13
* gpu: rx570
* driver: amdgpu
* mesa: mesa-tkg-git 20.3.0_devel.130274.ddca93ddf70-1

installer is quite slow and memory hungry, I recommend ticking the 3gb ram limit box as it can
get quite unstable even with a lot of ram

the installer might open annoying cmd windows, just dont touch stuff and let it do its thing

if movies are broken, use https://github.com/z0z0z/mf-install and
https://github.com/z0z0z/mf-installcab

## MARVEL’S AVENGERS – V1.3.3 (141640) #1617
### Initial repack
can get in game but it crashes in a specific spot in the intro area that you can't avoid

* installed with: proton-tkg-git 5.19.r12.gbe9c9681-1
* played with: proton-ge-custom-stable-bin 5.9_GE_8_ST-1
* gpu: rx570
* driver: amdgpu
* mesa: mesa 20.2.1
* binary: ./avengers.exe

it's quite memory hungry so make sure you have at least 8GB or memory available at all
times or tick the low ram option

might take hours to unpack

make sure to at least get the selective english bin and select english in the installer

use this command to run the game (assuming you have proton-ge-custom-stable):

```
cd ./data/Games/Marvel\'s\ Avengers
proton 997070 ./avengers.exe
```

## Red Dead Redemption 2 (Build 1311.23, MULTi13)
### Initial repack
installs, runs very smoooth with a very finnicky workaround

* installed with: proton-tkg-git 5.19.r17.g8d04884c-1
* played with: official Proton 5.13 (proton tkg will hang with a black screen)
* gpu: rx570
* driver: amdgpu (radv)
* mesa: mesa-tkg-git 20.3.0_devel.130274.ddca93ddf70-1
* binary: ./RDR2.exe

VERY finnicky to get it to run

after installing, do:

```
echo '-ignorepipelinecache -vulkan -borderless -useMinimumSettings -width 1920 -height 1080' > "data/Games/Red Dead Redemption 2/commandline.txt"
```

you can probably remove the `-useMinimumSettings` , this is just what worked for me

to switch proton version after installing with proton tkg, do

```
rm protonfit-settings
PROTON="/path/to/steamapps/common/Proton 5.13/proton" protonfit
```

and select RDR2.exe again

for some reason, the game only works with wine relay logging enabled, otherwise it crashes after
a couple of seconds.

wine relay logging makes the game unbearably slow, but there's a workaround: we can filter all the
most called winapi's from being logged and it will run smooth

```
cat > relay-workaround.reg << "EOF"
REGEDIT4

[HKEY_CURRENT_USER\Software\Wine\Debug]
"RelayExclude"="ntdll.RtlEnterCriticalSection;ntdll.RtlTryEnterCriticalSection;ntdll.RtlLeaveCriticalSection;kernel32.48;kernel32.49;kernel32.94;kernel32.95;kernel32.96;kernel32.97;kernel32.98;kernel32.TlsGetValue;kernel32.TlsSetValue;kernel32.FlsGetValue;kernel32.FlsSetValue;kernel32.SetLastError;ntdll.NtDelayExecution;KERNEL32.Sleep;ntdll.NtWaitForMultipleObjects;ntdll.RtlRunOnceExecuteOnce;kernelbase.InitOnceExecuteOnce;KERNEL32.WaitForSingleObject;ntdll.NtSetEvent;KERNEL32.SetEvent;KERNEL32.QueryPerformanceCounter;KERNEL32.QueryPerformanceFrequency;winmm.timeGetTime;KERNEL32.GetLastError;ntdll.RtlAllocateHeap;KERNEL32.HeapFree;ntdll.NtReleaseSemaphore;KERNEL32.ReleaseSemaphore;ntdll.RtlNtStatusToDosError;KERNEL32.MulDiv"
EOF

protonfit run regedit relay-workaround.reg
```

now you can run with:

```
pkill -9 -f wine; WINEDEBUG=-all,relay PROTON_LOG=1 SteamGameId=1174180 SteamAppId=1174180 protonfit
```

if it appears to do nothing, do this to unfreeze it

```
PID=$(pgrep RDR2.exe); kill -s SIGSTOP $PID; kill -s SIGCONT $PID
```

it will start right up

to skip splashscreens, install this mod: https://www.nexusmods.com/reddeadredemption2/mods/28

I also used the latest crackfix on cs rin but it might not be necessary

# credits
https://github.com/7oxicshadow/proton-standalone-script/blob/master/proton_launch.sh
which I used as reference
