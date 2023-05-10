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

## [Error 2] ImportError: /home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/cpp_household.so: invalid ELF header
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
$ sudo apt-get update
$ sudo apt-get upgrade

$ sudo apt-get install curl
$ curl -O https://bootstrap.pypa.io/get-pip.py
$ sudo python get-pip.py
$ sudo python3 get-pip.py

$ sudo pip3 install gym

$ sudo apt-get install git
$ cd
$ mkdir GitHub
$ cd GitHub
$ git clone https://github.com/openai/roboschool
$ cd roboschool
$ pwd

$ export ROBOSCHOOL_PATH=/home/GitHub/roboschool

$ sudo apt install cmake ffmpeg pkg-config qtbase5-dev libqt5opengl5-dev libassimp-dev libboost-python-dev libtinyxml-dev
$ sudo apt-get install python3.5-dev

$ cd
$ cd GitHub
$ git clone https://github.com/olegklimov/bullet3 -b roboschool_self_collision
$ mkdir bullet3/build
$ cd bullet3/build
$ cmake -DBUILD_SHARED_LIBS=ON -DUSE_DOUBLE_PRECISION=1 -DCMAKE_INSTALL_PREFIX:PATH=$ROBOSCHOOL_PATH/roboschool/cpp-household/bullet_local_install -DBUILD_CPU_DEMOS=OFF -DBUILD_BULLET2_DEMOS=OFF -DBUILD_EXTRAS=OFF  -DBUILD_UNIT_TESTS=OFF -DBUILD_CLSOCKET=OFF -DBUILD_ENET=OFF -DBUILD_OPENGL3_DEMOS=OFF ..
$ make -j4
$ make install
$ cd ../..

$ sudo pip3 install -e $ROBOSCHOOL_PATH
(or just $ cd roboschool    $ sudo pip3 install -e .)

$ python3 $ROBOSCHOOL_PATH/agent_zoo/RoboschoolHumanoidFlagrun_v0_2017may.py
(or just $ python3 agent_zoo/RoboschoolHumanoidFlagrun_v0_2017may.py)

I undergo these method in Ubuntu, not on the server.
and then, move '~/Github/roboschool' file to '~miniconda3/envs/[your_env_name]/lib/python3.9/site-packages/roboschool' after erase the existed roboschool file in miniconda3.
([your_env_name] is the environment that you made in anaconda)
after that, I met problem3.

## [Error 3] ImportError: /home/nrjeong/.conda/envs/ppo/lib/python3.9/site-packages/roboschool/cpp_household.so: invalid ELF header
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



- $O(2^{n})$: 지수형 빅-오. 사용하기에 매우 무리가 있음. <br>

[`Top`](#top)
