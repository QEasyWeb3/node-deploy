# Deployment tools of BSC


## Installation
Before proceeding to the next steps, please ensure that the following packages and softwares are well installed in your local machine: 
- nodejs: v16.15.0
- npm: 6.14.6
- go: 1.24+
- foundry
- python3 3.12.x
- poetry
- jq

### 安装命令（WSL/Linux环境）

#### 1. 安装Node.js和npm（使用nvm）
```bash
# 安装nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

# 激活nvm（添加到bash配置）
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# 安装Node.js v16.15.0
nvm install 16.15.0
nvm use 16.15.0

# 验证安装
node -v  # 应该显示 v16.15.0
npm -v   # 应该显示 6.14.6
```

#### 2. 安装Go
```bash
# 下载Go 1.24
wget https://golang.org/dl/go1.24.0.linux-amd64.tar.gz

# 解压安装
sudo tar -C /usr/local -xzf go1.24.0.linux-amd64.tar.gz

# 添加到环境变量
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.bashrc
source ~/.bashrc

# 验证安装
go version  # 应该显示 go1.24.0
```

#### 3. 安装Foundry
```bash
# 安装foundry
sudo curl -L https://foundry.paradigm.xyz | bash

# 激活并安装最新版本
source ~/.bashrc
foundryup

# 验证安装
forge --version
```

#### 4. 安装Python 3.12.x
```bash
# 更新软件包列表
sudo apt update

# 安装Python 3.12依赖
sudo apt install -y software-properties-common

# 添加deadsnakes PPA
sudo add-apt-repository ppa:deadsnakes/ppa -y

# 安装Python 3.12
sudo apt install -y python3.12 python3.12-venv python3.12-dev

# 验证安装
python3.12 --version
```

#### 5. 安装Poetry
```bash
# 安装poetry
curl -sSL https://install.python-poetry.org | python3 -n

# 添加到环境变量
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# 验证安装
poetry --version
```

#### 6. 安装jq
```bash
# 安装jq
sudo apt install -y jq

# 验证安装
jq --version
```

### Windows环境下的安装提示
如果在Windows环境下使用WSL，建议在WSL内安装以上所有依赖。也可以使用Windows原生工具，但可能需要额外配置环境变量。


## Quick Start
1. Clone this repository
```bash
git clone https://github.com/bnb-chain/node-deploy.git
```

2. For the first time, please execute the following command
```bash
pip3 install -r requirements.txt
```

3. build `create-validator`

```bash
# This tool is used to register the validators into StakeHub.
cd create-validator
go build
```

4. Configure the cluster
```
  You can configure the cluster by modifying the following files:
   - `config.toml`
   - `genesis/genesis-template.json`
   - `genesis/scripts/init_holders.template`
   - `.env`
```

5. Setup all nodes.
two different ways, choose as you like.
```bash
bash -x ./bsc_cluster.sh reset # will reset the cluster and start
# The 'vidx' parameter is optional. If provided, its value must be in the range [0, ${BSC_CLUSTER_SIZE}). If omitted, it affects all clusters.
bash -x ./bsc_cluster.sh stop [vidx] # Stops the cluster
bash -x ./bsc_cluster.sh start [vidx] # only start the cluster
bash -x ./bsc_cluster.sh restart [vidx] # start the cluster after stopping it
```

6. Setup a full node.
If you want to run a full node to test snap/full syncing, you can run:

> Attention: it relies on the validator cluster, so you should set up validators by `bsc_cluster.sh` firstly.

```bash
# reset a full sync node0
bash +x ./bsc_fullnode.sh reset 0 full
# reset a snap sync node1
bash +x ./bsc_fullnode.sh reset 1 snap
# restart the snap sync node1
bash +x ./bsc_fullnode.sh restart 1 snap
# stop the snap sync node1
bash +x ./bsc_fullnode.sh stop 1 snap
# clean the snap sync node1
bash +x ./bsc_fullnode.sh clean 1 snap
# reset a full sync node as fast node
bash +x ./bsc_fullnode.sh reset 2 full "--tries-verify-mode none"
# reset a snap sync node with prune ancient
bash +x ./bsc_fullnode.sh reset 3 snap "--pruneancient"
```

You can see the logs in `.local/fullnode`.

Generally, you need to wait for the validator to produce a certain amount of blocks before starting the full/snap syncing test, such as 1000 blocks.

## Background transactions
```bash
## normal tx
cd txbot
go build
./air-drops

## blob tx
cd txblob
go build
./txblob
```