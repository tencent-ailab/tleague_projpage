# Human-Machine Test
We provide guidelines for how a human plays against TStarBot-X.
Please follow the instructions of this page which is self contained (You don't need care the training code when running the human-machine test).
**Note, currently it only plays zerg-vs-zerg on the map "KairosJunction". SC2 version 4.10.0 is required.**

## Prerequisites and Terminology
You need TWO machines for the human-machine test.

**Machine A**: the machine used by a human player.
Windows or MacOS.
Have the SC2 game installed.
Version 4.10.0 game core is required,
which can be ensured by opening an arbitrary 4.10.0 replay file (for example, [here](./README.md#tstarbot-x-replay-files)) that the auto-downloading will be triggered when necessary.
We've tested for MacOS where the SC2 game is downloaded from https://starcraft2.com/.

**Machine B**: the machine where TStarBot-X deploys.
Ubuntu is required.
Using a GPU is recommended (otherwise the NN forward-pass can be slow, causing high delay and degraded performance).
TStarBot-X has been tested on a laptop with GTX 1650 and ubuntu 18.04.

## Installs
Here are the step-by-step instructions.

* Make sure that machine A can connect to machine B via passwordless ssh.
  - One can google for how to do the passwordless ssh setup, e.g.,
  [here](https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/).
  In summary, one generates the public and private keys on machine A,
  then copy (e.g, using `scp` for remote copying) the private key to the folder `~/.ssh` under machine B.
  Make sure the `sshd` service has started on machine B.
  - Verify the passwordless setup by connecting to B from A and see whether it succeeds.
* On machine B, install the two packages `DistLadder3`, `pysc2 (Tencent Extension)`.
See the [section below](#downloads) for downloading.
Just cd to the corresponding folder and typing `pip install -e .` to complete the installation.
The Linux SC2 binary version 4.10.0 is required,
see the link [here](https://github.com/deepmind/pysc2#linux) and [here](https://github.com/Blizzard/s2client-proto#downloads).
Then install the main package `SC2AgentsZoo2` (see the [section below](#downloads) for downloading) for TStarBot-X as follows:
  - To let TStarBot-X use GPU, you need install `cudnn` and `cuda`
  - Have `virtualenv` installed. You can do `pip install virtualenv`
  - Have ssh server started. You can do `apt-get install openssh-server`
  - cd to the folder `SC2AgentsZoo2/agent_TLeagueFormal14`,
  run the command `bash install_virtualenv3.sh` to complete the installation.
* On machine A, install `DistLadder3`, `pysc2 (Tencent Extension)` as aforementioned.
Install also ther commercial SC2 on this machine.

## Usage
Here is how to start the human-machine test.

On machine A, run the command:
```
python3 -m distladder3.bin.play_vs_remote_agent \
--human \
--port 6789 \
--remote <machine-B-usr-name@machine-B-ip-address> \
--replay_dir /Users/your-name/Desktop \
--map KairosJunction \
--game_version 4.10.0 \
--replay_name xxx-A-machine.SC2Replay
```
which starts a game UI that a human can play SC2 with mouse and keyboard.
For the `--remote` arg,
if machine A and B happen to have the same user name,
you can also omit the user name and simply write `--remote <machine-B-ip-address>`.
Examples: `--remote sc2tester@xx.xx.xx.xx` or `--remote xx.xx.xx.xx`.

On machine B, run the command:
```
python3 -m distladder3.bin.play_vs_remote_agent \
--port 6789 \
--replay_dir /Users/your-name/Desktop \
--map KairosJunction \
--game_version 4.10.0 \
--replay_name xxx-B-machine.SC2Replay \
--player_name_path_config "TStarBot-X,/your/path/to/SC2AgentsZoo2/agent_TLeagueFormal14/,/your/path/to/the/config/file.ini"
```
to start the AI,
where the arg `--player_name_path_config` determines an agent by its name (TStarBotX),
path (`/your/path/to/SC2AgentsZoo2/agent_TLeagueFormal14/`),
and config (`/your/path/to/the/config/file.ini`) in comma separated value.

The `*.ini` config file specifies more detailed args for the agent,
e.g.,
whether to use GPU,
the NN model path,
the zstat path (a folder),
the zstat category,
probability for zeroing the zstat,
etc.,
as shown in the snippet below:
```ini
[config]
use_gpu_id=-1
;-1 for not using GPU, 0 means using GPU #0, 1 for GPU #1, etc.
...
chkpoints_root_dir=/Users/usr-name/tstarbotx/data
model_filename=TStarBot-X-33days.model
...
zstat_zeroing_prob=0.0
zstat_category=Normal174
...
tleague_interface_config.zstat_data_src=/Users/usr-name/tstarbotx/data/rp2124-mv8-victory-selected-Misc
...
```

We've prepared several agent config `.ini` files,
as well as the corresponding NN models and zstat files (in a folder),
see the [section below](#downloads).

## Downloads
### Packages
* `DistLadder3`
[Google Drive](https://drive.google.com/file/d/1ufCtU2JIyoSiSMwN4lqT66oxitmZeArh/view?usp=sharing)
or [Tencent Weiyun](https://share.weiyun.com/QFgOzG4n)
* `pysc2 (Tencent Extension)`
[Google Drive](https://drive.google.com/file/d/1rJnmK1aNIFaYuYkXXmkDvTe-JRKrzFhe/view?usp=sharing)
or [Tencent Weiyun](https://share.weiyun.com/mCCEZtOX)
* `SC2AgentsZoo2`
[Google Drive](https://drive.google.com/file/d/1neXug1fn3miHnKu9Z8tBpMC-ZKfIzXAP/view?usp=sharing)
or [Tencent Weiyun](https://share.weiyun.com/NKiLym42)

### Data Files
* zstat files (`rp2124-mv8-victory-selected-Misc`, a folder) used by the 8/25/33 days model
[Google Drive](https://drive.google.com/file/d/1pV8wD_AXbbESQL2L4LticKTTwiaQpLCf/view?usp=sharing)
or [Tencent Weiyun](https://share.weiyun.com/ZXeYGjZp)
* Main Agent 8 days:
  - `ini` config [Google Drive](https://drive.google.com/file/d/1Ed80rcYaafVRGlQJ7hCcsge1snCx8oLQ/view?usp=sharing)
  or [Tencent Weiyun](https://share.weiyun.com/GJ1Bwfie)
  - NN model [Google Drive](https://drive.google.com/file/d/1mJ9s3dpScgKbYj3vZusnC0fJ1IPQuMrc/view?usp=sharing)
  or [Tencent Weiyun](https://share.weiyun.com/spHoQIg3)
* Main Agent 25 days:
  - `ini` config [Google Drive](https://drive.google.com/file/d/1JrfERGRQrVaVPOU8AFjn_B9jhy5eeMl1/view?usp=sharing)
  or [Tencent Weiyun](https://share.weiyun.com/bNGLU4Zj)
  - NN model [Google Drive](https://drive.google.com/file/d/1BcQERcIGZvulCd5M4gCej80lJmdIYcLh/view?usp=sharing)
  or [Tencent Weiyun](https://share.weiyun.com/24QfxkMZ)
* Main Agent 33 days:
  - `ini` config [Google Drive](https://drive.google.com/file/d/1AohBDH4C4Y86usNbEDrVhq1g2ZUEJfvp/view?usp=sharing)
  or [Tencent Weiyun](https://share.weiyun.com/ue3zQ7RG)
  - NN model [Google Drive](https://drive.google.com/file/d/1M6m-vGGGYNI-KHETq8t8gBKi_luuPLyD/view?usp=sharing)
  or [Tencent Weiyun](https://share.weiyun.com/yuh9ZDSe)
