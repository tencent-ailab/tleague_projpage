# ViZDoom Experiments
This page contains the resources for the experiments of ViZDoom as discussed in the TLeague technical report.

## Trained Model
A trained model for the experiments discussed in the technical report (CIG 2016 Track 1) has been given in the evaluation code,
see the [section below](#install-myplayer).

## Evaluation
The evaluation code can be found here: 
[Google Drive](https://drive.google.com/file/d/1soi_nHglpSazRv2znZzbqO3S6GcFJoPL/view?usp=sharing) 
or [Tencent Weiyun](https://share.weiyun.com/YyN0IqXS),
which is a modification over the original evaluation code https://github.com/mihahauke/VDAIC2017

Our modification allows a synchronous mode for the host, "F1" and "MyPlayer",
as discussed in the technical report and is summarized below.

The root config file `_vizdoom.cfg` overrides all the private configurations of an agent,
so we've commented out the `ASYNC_PLAYER` setting in `_vizdoom.cfg`:
```buildoutcfg
doom_scenario_path = cig2017.wad
# window_visible = False
# mode = ASYNC_PLAYER
game_args += -join localhost
```

For "F1", we add extra arguments to `f1/my_glorious_agent.py`
```
    -c F1_COLOR, --color F1_COLOR
                            0 - green, 1 - gray, 2 - brown, 3 - red, 4 - light
                            gray, 5 - light brown, 6 - light red, 7 - light blue
                            (default: 0)
    -w, --watch           window visible (default: False)
    -mode MODE, --mode MODE
                            1 for PLAYER, 2 for ASYNC_PLAYER (default: 1)
```
and default "F1" to green color and synchronous player.

For the host, we add an argument
```
    -mode MODE, --mode MODE
                            1 for PLAYER, 2 for ASYNC_PLAYER, 3 for SPECTATOR, 4
                            for ASYNC_SPECTATOR (default: 1)  
```
to allow it be synchronous (mode `3`, the `SPECTATOR`).

### Installation
The official code requires each submitted agent be packed as docker (see the descriptions at https://github.com/mihahauke/VDAIC2017).
In our modified code, 
we do a common `pip install` for "MyPlayer", 
and docker build for any other third-party agent (e.g., "F1"),
as explained below.

#### Install MyPlayer
Install the three packages `Arena`, `TLeague`, `TPolicies`, respectively:
```
cd MyPlayer/Arena
pip3 install -e .
cd MyPlayer/TLeague
pip3 install -e .
cd MyPlayer/TPolicies
pip3 install -e .
```
Note, the MyPlayer evaluation code here is self-contained and relies on the old `TLeague`, `Arena`, `TPolicies` code that is somehow different from the `dev-open` branch. 
You can avoid the possible conflicts by using, for example, `virtualenv`.

An NN model has been shipped with the code and placed in the path `MyPlayer/model/`.

#### Install F1
Build the image:
```bash
sudo chmod u+x build.sh
./build.sh f1 
```
The corresponding NN model has been contained.

### Install host
Build the image:
```bash
sudo chmod u+x build.sh
./build.sh host
```

### Run
To run the evaluation, start the `host`, `MyPlayer` and `F1` in separate terminals. 
`tmux` is recommended.
Also, ensure
```bash
sudo chmod u+x run.sh
```

For 1 MyPlayer, 7 builtin bots, run the following commands in separate terminals:
```bash
./run.sh host -mode 3 -b 7 
bash MyPlayer/TLeague/tleague/sandbox/example_evaluation_vd.sh evaluation
``` 
where `-mode 3` means synchronous spectator, `-b 7` means adding 7 builtin bots.

For 1 MyPlayer, 1 F1 and 6 builtin bots, run the following commands in separate terminals:
```bash
./run.sh host -mode 3 -b 6 -p 2
bash MyPlayer/TLeague/tleague/sandbox/example_evaluation_vd.sh evaluation
./run.sh f1
```
where `-p 2` means there are 2 AI agents to join in.

For 4 MyPlayer, 4 F1, run the following commands in separate terminals:
```bash
./run.sh host -mode 3 -p 8
bash MyPlayer/TLeague/tleague/sandbox/example_evaluation_vd.sh evaluation
bash MyPlayer/TLeague/tleague/sandbox/example_evaluation_vd.sh evaluation
bash MyPlayer/TLeague/tleague/sandbox/example_evaluation_vd.sh evaluation
bash MyPlayer/TLeague/tleague/sandbox/example_evaluation_vd.sh evaluation
./run.sh f1
./run.sh f1
./run.sh f1
./run.sh f1
```

## Downloads
TODO: link to the video clips for the evaluation