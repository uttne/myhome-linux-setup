# 1. Linux Mint の初期設定

## 1.1. ホームディレクトリの英語科 (Desktop, Downloads 等)

```bash
LANG=C xdg-user-dirs-gtk-update
```

* `Dont't ask me this again` をチェック
* `Update Names` をクリック
* 再起動
* また表示されるので `次回から表示しない` をチェック
* `古い名前のままにする` をクリック

## 1.2. アプリの更新

```bash
sudo apt update
sudo apt upgrade -y
```

## 1.3. .local でアクセス可能にする

```bash
sudo apt install -y libnss-mdns avahi-utils
sudo systemctl restart avahi-daemon
```

## 1.4. SSHのサーバーの設定

```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```

## 1.5. SSHのクライアントキーの登録

クライアントPCで

```bash
ssh username@pc-name.local
# パスワードでログインできることを確認
exit

# クライアントのカギを送信
scp ~/.ssh/xxxx.pub username@pc-name.local:~

ssh username@pc-name.local
mkdir -p ~/.ssh
cat xxxx.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh/
chmod 600 ~/.ssh/authorized_keys
rm xxxx.pub
# 一度切断
exit

# 鍵でアクセスできることを確認
ssh username@pc-name.local

sudo nano /etc/ssh/sshd_conig

# 以下の設定を行う
# パスワードの認証を拒否
# PasswordAuthentication no
# rootでの直接ログインを禁止
# PermitRootLogin no

sudo systemctl restart ssh

exit

# 設定が反映されてもアクセスできることを確認
ssh username@pc-name.local
```

## 1.6. 蓋を閉じたときにスリープしないようにする(ノートの場合)

```bash
sudo nano /etc/systemd/logind.conf

# 蓋を閉じても何もしない
# HandleLidSwitch=ignore
# HandleLidSwitchExternalPower=ignore
# HandleLidSwitchDocked=ignore


# 再起動で有効化
sudo shutdown -r now
# 以下でも有効化できるらしいが再起動が無難
# sudo systemctl restart systemd-logind
```

## 1.7. 仮想化ツールの導入

```bash
# 1. 仮想化ツール (KVM/QEMU)
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# 2. Dockerのインストール (Ubuntu Nobleリポジトリを使用)
sudo apt install -y apt-transport-https ca-certificates curl
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/ubuntu noble stable"

# https://docs.docker.com/engine/install/ubuntu/#installation-methods
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# sudoなしでDockerを実行できるようにする（反映には再ログインが必要）
sudo gpasswd -a $USER docker

eixt

# 動作することを確認
docker run hello-world
```

## 1.8. Node.js の導入

```bash
export NVS_HOME="$HOME/.nvs"
git clone https://github.com/jasongin/nvs "$NVS_HOME"
. "$NVS_HOME/nvs.sh" install
source ~/.bashrc

nvs add lts
nvs use lts
nvs link lts

# 以下を実行すると lts のバージョンのリストを取得可能
# nvs ls-remote lts
```
