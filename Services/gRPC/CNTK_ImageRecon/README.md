## Image Recognition (Flowers & Dogs)

### 1. Reference:

- This service uses [CNTK Image Recognition](https://cntk.ai/pythondocs/CNTK_301_Image_Recognition_with_Deep_Transfer_Learning.html) to perform image recognition on photos.

### 2. Preparing the file structure:

- For this service you'll need to download 2 trained models (flowers and dogs).
- Clone this repository:
```
$ git clone https://github.com/singnet/dnn-model-services.git
$ cd dnn-model-services/utils
$ ./get_cntk_models.sh
$ ls -la Resources/Models
total 458416
drwxrwxr-x 2 user user      4096 Nov  8 08:49 .
drwxrwxr-x 3 user user      4096 Nov  8 08:49 ..
-rw-rw-r-- 1 user user 234830033 Ago 28 17:28 dogs_ResNet152_20.model
-rw-rw-r-- 1 user user 234574954 Ago 28 15:57 flowers_ResNet152_20.model
$ cd ..
```

### 3. Running the service:

- To get the `YOUR_AGENT_ADDRESS` you must have already published a service (check this [link](https://github.com/singnet/wiki/tree/master/tutorials/howToPublishService)).
- Create the SNET Daemon's config JSON file. It must looks like this:
```
$ cd Services/gRPC/CNTK_ImageRecon
$ cat snetd_image_recon_service_config.json
{
    "DAEMON_TYPE": "grpc",
    "DAEMON_LISTENING_PORT": "7007",
    "BLOCKCHAIN_ENABLED": true,
    "ETHEREUM_JSON_RPC_ENDPOINT": "https://kovan.infura.io",
    "AGENT_CONTRACT_ADDRESS": "YOUR_AGENT_ADDRESS",
    "SERVICE_TYPE": "grpc",
    "PASSTHROUGH_ENABLED": true,
    "PASSTHROUGH_ENDPOINT": "http://localhost:7003",
    "LOG_LEVEL": 10,
    "PRIVATE_KEY": "YOUR_PRIVATE_KEY"
}
```
- Install all dependencies:
```
$ pip3 install -r requirements.txt
```
- Generate the gRPC codes:
```
$ sh buildproto.sh
```
- Start the service and SNET Daemon:
```
$ python3 run_image_recon_service.py --daemon-conf .
```

### 4. Calling the service:

- Inputs:
  - `model`: DNN Model ("ResNet152").
  - `img_path`: An image URL.

- Local (testing purpose):

```
$ python3 test_image_recon_service.py 
Endpoint (localhost:7003): 
Method (flowers|dogs): flowers
Model (ResNet152): <ENTER>
Image (Link): https://www.fiftyflowers.com/site_files/FiftyFlowers/Image/Product/Mini-Black-Eye-bloom-350_c7d02e72.jpg
1.8751
{1: '98.93%: sunflower', 2: '00.64%: black-eyed susan', 3: '00.16%: barbeton daisy', 4: '00.14%: oxeye daisy', 5: '00.03%: daffodil'}

$ python3 test_image_recon_service.py 
Endpoint (localhost:7000): 
Method (flowers|dogs): dogs
Model (ResNet152): <ENTER>
Image (Link): https://cdn2-www.dogtime.com/assets/uploads/2011/01/file_22950_standard-schnauzer-460x290.jpg
1.5280
{1: '99.83%: Miniature_schnauzer', 2: '00.09%: Alaskan_malamute', 3: '00.05%: Giant_schnauzer', 4: '00.01%: Bouvier_des_flandres', 5: '00.01%: Lowchen'}
```

- Through SingularityNET:

```
$ snet set current_agent_at YOUR_AGENT_ADDRESS
set current_agent_at YOUR_AGENT_ADDRESS

$ snet client call flowers '{"model": "ResNet152", "img_path": "https://www.fiftyflowers.com/site_files/FiftyFlowers/Image/Product/Mini-Black-Eye-bloom-350_c7d02e72.jpg"}'
...
Read call params from cmdline...

Calling service...

    response:
        delta_time: '1.5536'
        top_5: '{1: ''98.93%: sunflower'', 2: ''00.64%: black-eyed susan'', 3: ''00.16%:
            barbeton daisy'', 4: ''00.14%: oxeye daisy'', 5: ''00.03%: daffodil''}'
```