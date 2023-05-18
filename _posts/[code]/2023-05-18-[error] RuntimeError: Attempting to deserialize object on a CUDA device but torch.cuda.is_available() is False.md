(giraffe) nrjeong@mdilrg-64:~/giraffe$ python render.py configs/64res/cats_64_pretrained.yaml 
https://s3.eu-central-1.amazonaws.com/avg-projects/giraffe/models/checkpoint_cats64-ff00b26f.pt
=> Loading checkpoint from url...
Downloading: "https://s3.eu-central-1.amazonaws.com/avg-projects/giraffe/models/checkpoint_cats64-ff00b26f.pt" to /home/nrjeong/.cache/torch/hub/checkpoints/checkpoint_cats64-ff00b26f.pt
100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 25.9M/25.9M [00:07<00:00, 3.42MB/s]
Traceback (most recent call last):
  File "render.py", line 26, in <module>
    checkpoint_io.load(cfg['test']['model_file'])
  File "/home/nrjeong/giraffe/im2scene/checkpoints.py", line 62, in load
    return self.load_url(filename)
  File "/home/nrjeong/giraffe/im2scene/checkpoints.py", line 93, in load_url
    state_dict = model_zoo.load_url(url, progress=True)
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/hub.py", line 750, in load_state_dict_from_url
    return torch.load(cached_file, map_location=map_location)
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 809, in load
    return _load(opened_zipfile, map_location, pickle_module, **pickle_load_args)
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 1172, in _load
    result = unpickler.load()
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 1142, in persistent_load
    typed_storage = load_tensor(dtype, nbytes, key, _maybe_decode_ascii(location))
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 1116, in load_tensor
    wrap_storage=restore_location(storage, location),
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 217, in default_restore_location
    result = fn(storage, location)
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 182, in _cuda_deserialize
    device = validate_cuda_device(location)
  File "/home/nrjeong/.conda/envs/giraffe/lib/python3.8/site-packages/torch/serialization.py", line 166, in validate_cuda_device
    raise RuntimeError('Attempting to deserialize object on a CUDA '
RuntimeError: Attempting to deserialize object on a CUDA device but torch.cuda.is_available() is False. If you are running on a CPU-only machine, please use torch.load with map_location=torch.device('cpu') to map your storages to the CPU.
