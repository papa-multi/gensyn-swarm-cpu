![image](https://github.com/user-attachments/assets/8ad5a694-e287-4d45-ba57-203f58a19714)






# 1) Install Dependencies :

```
 sudo apt update && sudo apt upgrade -y
```

```
sudo apt install screen curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```

```
sudo apt install python3 python3-pip python3-venv python3-dev -y
```
```
sudo apt update
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash -
sudo apt install -y nodejs
node -v
npm install -g yarn
yarn -v
```


```
curl -o- -L https://yarnpkg.com/install.sh | bash
```

```
export PATH="$HOME/.yarn/bin:$HOME/.config/yarn/global/node_modules/.bin:$PATH"
```

```
source ~/.bashrc
```

---

## 2) Get HuggingFace Access token :
**1- Create account in [HuggingFace](https://huggingface.co/)**

**2- Create an Access Token with `Write` permissions [here](https://huggingface.co/settings/tokens) and save it**

---

## 3) Clone the Repository :
```bash
git clone https://github.com/gensyn-ai/rl-swarm/
```


```
cd rl-swarm
```


```
nano run_rl_swarm.sh
```



*We need the login information to be saved so that if the system crashes, the data won’t be lost.
To do this, open the file and comment out the line that deletes the login data.

*Open the file in your editor.

*Press `Ctrl + W` to search.

*Look for the following line: 

rm -r $ROOT_DIR/modal-login/temp-data/*.json 2> /dev/null || true

*Add a `#` at the beginning of the line to comment it out, so it looks like this:

 ` #rm -r $ROOT_DIR/modal-login/temp-data/*.json 2> /dev/null || true`




## 4) Run the swarm :


### CLI Method 
1- Open a screen to run it in background
```bash
screen -S swarm
```
2- Get into the `rl-swarm` directory
```
cd rl-swarm
```
3- Install swarm
```bash
python3 -m venv .venv

source .venv/bin/activate
# if not worked, then:
. .venv/bin/activate

./run_rl_swarm.sh
```


## 5) Login :
**1- You have to receive `Waiting for userData.json to be created...` in logs**

![image](https://github.com/user-attachments/assets/140f7d32-844f-4cf0-aac4-a91e9a14c1aa)

**2- Open login page in browser**
* **Local PC:** Open `http://localhost:3000/` in your browser
* **GPU Cloud & VPS Users: Tunnel to external URL:**
  * 1- Open a new terminal
  * 2- Install **localtunnel**:
    ```
    sudo npm install -g localtunnel
    ```
  * 3- Get a password:
    ```
    curl https://loca.lt/mytunnelpassword
    ```
  * The password is actually your VPS IP
  * 4- Get URL
    ```
    lt --port 3000
    ```
  * Visit the prompted url, and enter your password to access Gensyn login page

**3- Login with your preferred method**

![image](https://github.com/user-attachments/assets/f33ea530-b15f-4af7-a317-93acd8618a9f)

* After login, your terminal starts installation.

**4- Answer prompts:**
* `Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N]` >>> Press `N` to join testnet
  * `HuggingFace` needs `2GB` upload bandwidth for each model you train, you can press `Y`, and enter your access-token.
* `Enter the name of the model you want to use in huggingface repo/name format, or press [Enter] to use the default model.` >>> For default model, press `Enter`  or choose one of these (More model parameters (B) need more vRAM):
  * `Gensyn/Qwen2.5-0.5B-Instruct`
  * `Qwen/Qwen3-0.6B`
  * `nvidia/AceInstruct-1.5B`
  * `dnotitia/Smoothie-Qwen3-1.7B`
  * `Gensyn/Qwen2.5-1.5B-Instruct`
 



After the installation is complete and your information appears, press `Ctrl + C`


## 6) creat file:


```
nano rl-node.sh
```

use this :

```
#!/bin/bash

# ----------------- Settings -----------------
# 1. Place your Hugging Face API key here
API_KEY="YOUR_API_KEY_GOES_HERE"

# 2. The full path to the rl-swarm directory
RL_SWARM_DIR="/root/rl-swarm"

# 3. Log file name
LOG_FILE="${RL_SWARM_DIR}/gensyn_node.log"
# -------------------------------------------

# Infinite loop to manage the screen session
while true; do
    echo "=================================================="
    echo "$(date): Creating new screen session 'gensyn'..."
    echo "Displaying output in screen and logging to: ${LOG_FILE}"
    echo "=================================================="

    # *** THIS IS THE ONLY LINE THAT HAS CHANGED ***
    # The output of the script is now piped to 'tee'
    # 'tee -a' appends the output to the log file AND prints it to standard output (the screen).
    CMD_TO_RUN="cd ${RL_SWARM_DIR} && source .venv/bin/activate && { echo 'y'; echo '${API_KEY}'; echo ''; } | ./run_rl_swarm.sh 2>&1 | tee -a ${LOG_FILE}"

    # Create a detached screen session and run the command.
    screen -dmS gensyn bash -c "${CMD_TO_RUN}"

    echo "Node is running inside 'screen -S gensyn'. Waiting for 3 hours."
    
    # Wait for 3 hours
    sleep 10800

    echo "=================================================="
    echo "$(date): Time is up. Terminating the screen session..."
    echo "=================================================="
    
    # Terminate the screen session.
    screen -XS gensyn quit
    sleep 5
done
```

## 7)run aiagn:

```
chmod +x rl-node.sh
```


```
tmux new -s rl-node
```



## 8) check with this:





