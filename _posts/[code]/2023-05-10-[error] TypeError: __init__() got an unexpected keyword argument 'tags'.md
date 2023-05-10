---
layout: post
author: jnr
use_math : true
published: true
categories: code
tags: error gym roboschool
---

---
<h2 id="top"></h2><br>

## [Error 1] TypeError: __init__() got an unexpected keyword argument 'tags'
```python
$ python train.py

Traceback (most recent call last):
  File "/home/nrjeong/PPO-PyTorch/train.py", line 10, in <module>
    import roboschool
  File "/home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/__init__.py", line 66, in <module>
    register(
  File "/home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/gym/envs/registration.py", line 484, in register
    new_spec = EnvSpec(
TypeError: __init__() got an unexpected keyword argument 'tags'
```
[reference link](https://github.com/openai/roboschool/issues/208)<br>
$ pip install gym==0.15.4<br>
$ pip install roboschool==1.0.48

but when I enter the second one, I get an error that there is no version satisfies the requirement.<br>

```python
ERROR: Could not find a version that satisfies the requirement roboschool==1.0.48 (from versions: 1.0.1, 1.0.3, 1.0.6, 1.0.7, 1.0.8, 1.0.15, 1.0.16, 1.0.17, 1.0.18, 1.0.19, 1.0.20, 1.0.34)
ERROR: No matching distribution found for roboschool==1.0.48
```
so if I just run, I get another problem. <br><br>

## [Error 2] ImportError: [file_path]/site-packages/roboschool/cpp_household.so: invalid ELF header
```python
$ python train.py

Traceback (most recent call last):
  File "/home/nrjeong/PPO-PyTorch/train.py", line 10, in <module>
    import roboschool
  File "/home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/__init__.py", line 167, in <module>
    from roboschool.gym_pendulums import RoboschoolInvertedPendulum
  File "/home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/gym_pendulums.py", line 1, in <module>
    from roboschool.scene_abstract import SingleRobotEmptyScene
  File "/home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/scene_abstract.py", line 12, in <module>
    from roboschool  import cpp_household   as cpp_household
ImportError: /home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/cpp_household.so: invalid ELF header
```

[reference link1](https://velog.io/@everyman123/Mujoco-py-%EC%84%A4%EC%B9%98%EB%B2%95)   
[reference link2](https://blog.naver.com/pjhool/222525089743)<br>
follow these reference link, but I don't know it really worked.
so I found another solution.<br>

[reference link3](https://github.com/openai/roboschool/issues/112)<br>
$ sudo apt-get update<br>
$ sudo apt-get upgrade<br><br>

$ sudo apt-get install curl<br>
$ curl -O https://bootstrap.pypa.io/get-pip.py<br>
$ sudo python get-pip.py<br>
$ sudo python3 get-pip.py<br><br>

$ sudo pip3 install gym<br><br>

$ sudo apt-get install git<br>
$ cd<br>
$ mkdir GitHub<br>
$ cd GitHub<br>
$ git clone https://github.com/openai/roboschool<br>
$ cd roboschool<br>
$ pwd<br><br>

$ export ROBOSCHOOL_PATH=/home/GitHub/roboschool<br><br>

$ sudo apt install cmake ffmpeg pkg-config qtbase5-dev libqt5opengl5-dev libassimp-dev libboost-python-dev libtinyxml-dev<br>
$ sudo apt-get install python3.5-dev<br><br>

$ cd<br>
$ cd GitHub<br>
$ git clone https://github.com/olegklimov/bullet3 -b roboschool_self_collision<br>
$ mkdir bullet3/build<br>
$ cd bullet3/build<br>
$ cmake -DBUILD_SHARED_LIBS=ON -DUSE_DOUBLE_PRECISION=1 -DCMAKE_INSTALL_PREFIX:PATH=$ROBOSCHOOL_PATH/roboschool/cpp-household/bullet_local_install -DBUILD_CPU_DEMOS=OFF -DBUILD_BULLET2_DEMOS=OFF -DBUILD_EXTRAS=OFF  -DBUILD_UNIT_TESTS=OFF -DBUILD_CLSOCKET=OFF -DBUILD_ENET=OFF -DBUILD_OPENGL3_DEMOS=OFF ..<br>
$ make -j4<br>
$ make install<br>
$ cd ../..<br><br>

$ sudo pip3 install -e $ROBOSCHOOL_PATH<br>
(or just $ cd roboschool    $ sudo pip3 install -e .)<br><br>

$ python3 $ROBOSCHOOL_PATH/agent_zoo/RoboschoolHumanoidFlagrun_v0_2017may.py<br>
(or just $ python3 agent_zoo/RoboschoolHumanoidFlagrun_v0_2017may.py)<br><br><br>

I undergo these method in Ubuntu, not on the server.<br>
and then, move '~/Github/roboschool' file to '~miniconda3/envs/[your_env_name]/lib/python3.9/site-packages/roboschool' after erase the existed roboschool file in miniconda3.<br>
([your_env_name] is the environment that you made in anaconda)<br>
after that, I met problem3.<br>

## [Error 3] gym.error.UnregisteredEnv: No registered env with id: RoboschoolWalker2d-v1
```python
$ python train.py

============================================================================================
/home/burger/miniconda3/envs/ppo/lib/python3.9/site-packages/torch/cuda/__init__.py:546: UserWarning: Can't initialize NVML
  warnings.warn("Can't initialize NVML")
Device set to : NVIDIA GeForce RTX 2060
============================================================================================
============================================================================================
training environment name : RoboschoolWalker2d-v1
Traceback (most recent call last):
  File "/home/burger/miniconda3/envs/ppo/lib/python3.9/site-packages/gym/envs/registration.py", line 132, in spec
    return self.env_specs[id]
KeyError: 'RoboschoolWalker2d-v1'

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/home/burger/Desktop/PPO-PyTorch/train.py", line 254, in <module>
    train()
  File "/home/burger/Desktop/PPO-PyTorch/train.py", line 53, in train
    env = gym.make(env_name)
  File "/home/burger/miniconda3/envs/ppo/lib/python3.9/site-packages/gym/envs/registration.py", line 156, in make
    return registry.make(id, **kwargs)
  File "/home/burger/miniconda3/envs/ppo/lib/python3.9/site-packages/gym/envs/registration.py", line 100, in make
    spec = self.spec(path)
  File "/home/burger/miniconda3/envs/ppo/lib/python3.9/site-packages/gym/envs/registration.py", line 142, in spec
    raise error.UnregisteredEnv('No registered env with id: {}'.format(id))
gym.error.UnregisteredEnv: No registered env with id: RoboschoolWalker2d-v1
```
I just go to '~miniconda3/envs/[your_env_name]/lib/python3.9/site-packages/gym/envs' and add code
```python
register(
    id='RoboschoolWalker2d-v1',
    max_episode_steps=1000,
    entry_point='gym.envs.mujoco:Walker2dEnv',
)
```
to __init__.py

then, I could run the code. <br>

[`Top`](#top)
