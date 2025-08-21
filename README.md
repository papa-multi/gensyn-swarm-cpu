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



---

## 2) Clone the Repository :
```
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




## 3) Run the swarm :


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

```


Note: Before running `swarm`, make sure to run these commands on the CPU to prevent certain errors


```
sed -i -E 's/(num_train_samples:\s*)2/\1 1/' rgym_exp/config/rg-swarm.yaml
```
```
pip install --force-reinstall transformers==4.51.3 trl==0.19.1

pip freeze
```


```
./run_rl_swarm.sh
```




## 5) Login :
**1- You have to receive `Waiting for userData.json to be created...` in logs**

Note: Open a new terminal


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


 



After the installation is complete and your information appears, press `Ctrl + C`


## 6) creat file:

```
tmux new -s rl-node
```
```
cd rl-swarm
```


```
nano rl-node.sh
```

use this :

```
#!/bin/bash

RL_SWARM_DIR="/root/rl-swarm"
LOG_FILE="${RL_SWARM_DIR}/gensyn_node.log"
SESSION_NAME="gensyn"

# Create the screen session if it doesn't exist
if ! screen -list | grep -q "${SESSION_NAME}"; then
    screen -dmS $SESSION_NAME
fi

while true; do
    echo "=================================================="
    echo "$(date): Restarting RL Swarm inside screen session '${SESSION_NAME}'..."
    echo "Logging to: ${LOG_FILE}"
    echo "=================================================="

    # Run the command inside the existing screen session
    screen -S $SESSION_NAME -X stuff "cd ${RL_SWARM_DIR} && source .venv/bin/activate && printf '\n\n' | ./run_rl_swarm.sh 2>&1 | tee -a ${LOG_FILE}\n"

    # Wait for 3 hours
    sleep 10800

    # Send Ctrl+C to stop the running process
    screen -S $SESSION_NAME -X stuff $'\003'
done
```

## 7)run aiagn:

```
chmod +x rl-node.sh
```

```
./rl-node.sh
```


After that, to exit tmux, press `Ctrl + b`, then `d`


```
screen -ls
 ```

see gensyn screen if you need check 

```
screen -r <id>
```



-----

## Since your RAM might be limited, we add a swap space on the SSD to prevent programs from crashing too quickly.



# How to Add a 100GB Swap on Linux ✅

Adding swap in Linux is straightforward. To create a **100GB swap file** on your Ubuntu server, follow these steps:

---

### 1️⃣ Create the Swap File

```bash
fallocate -l 100G /swapfile
```

> If `fallocate` doesn’t work (some VPS providers restrict it), use:

```bash
dd if=/dev/zero of=/swapfile bs=1G count=100
```

---

### 2️⃣ Set Correct Permissions (Security)

```bash
chmod 600 /swapfile
```

---

### 3️⃣ Make the File a Swap

```bash
mkswap /swapfile
```

---

### 4️⃣ Enable Swap

```bash
swapon /swapfile
```

---

### 5️⃣ Verify Swap

```bash
swapon --show
free -h
```

You should see **100GB** added to your swap ✅.

---

### 6️⃣ Make Swap Permanent (After Reboot)

Edit `/etc/fstab`:

```bash
nano /etc/fstab
```

Add this line at the end:

```
/swapfile none swap sw 0 0
```

---

### ⚠️ Important Notes

* **100GB swap is huge!** Only do this if you have very little RAM or are running heavy workloads like ML/AI or simulations.
* Swap is **much slower than RAM** (even faster on SSDs, it’s still a turtle 🐢).
* If you just want **temporary swap**, step 4 is enough—no need to edit `/etc/fstab`.

---








