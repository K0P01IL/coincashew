# Upgrading a Node

## :tada: Introduction

{% hint style="success" %}
If you want to support this free educational Cardano content or found the content helpful, visit [cointr.ee](https://cointr.ee/coincashew) to find our donation addresses. Much appreciated in advance. :pray:

:ledger:Technical writing by [Change Pool \[CHG\]](https://change.paradoxicalsphere.com)
{% endhint %}

[Input-Output (IOHK)](https://iohk.io/) regularly releases new versions of Cardano Node via the `cardano-node` [GitHub repository](https://github.com/input-output-hk/cardano-node). Carefully review release notes available in the repository for new features, known issues, technical specifications, related downloads, documentation, changelogs, assets and other details of each new release.

{% hint style="info" %}
To receive notifications related to activity in the Cardano Node GitHub repository, configure [Watch](https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github/setting-up-notifications/configuring-notifications#automatic-watching) functionality.
{% endhint %}

The _Upgrading a Node_ topic describes how to upgrade a Cardano node to the latest version. Complete the following procedures on each block producer and relay node comprising your stake pool as needed to complete the upgrade.

{% hint style="info" %}
For instructions on upgrading your stake pool to a previous Cardano version, see the [archive](upgrading-a-node-archive.md).
{% endhint %}

## :revolving\_hearts: Upgrading Third-party Software

### CNCLI

If you use Andrew Westberg's [CNCLI](https://github.com/cardano-community/cncli) command line utilities, then upgrade to the latest version if a newer version is available.

{% hint style="danger" %}
Do not confuse Andrew Westberg's CNCLI utilities with the [`cncli.sh`](https://cardano-community.github.io/guild-operators/Scripts/cncli/) companion script for stake pool operators maintained by [Guild Operators](https://cardano-community.github.io/guild-operators/).
{% endhint %}

**To upgrade Andrew Westberg's CNCLI binary:**

1. In a terminal window, type the following commands:

```bash
RELEASETAG=$(curl -s https://api.github.com/repos/cardano-community/cncli/releases/latest | jq -r .tag_name)
VERSION=$(echo ${RELEASETAG} | cut -c 2-)
echo "Installing release ${RELEASETAG}"
curl -sLJ https://github.com/cardano-community/cncli/releases/download/${RELEASETAG}/cncli-${VERSION}-x86_64-unknown-linux-gnu.tar.gz -o /tmp/cncli-${VERSION}-x86_64-unknown-linux-gnu.tar.gz
sudo tar xzvf /tmp/cncli-${VERSION}-x86_64-unknown-linux-gnu.tar.gz -C /usr/local/bin/
```

1. To confirm that the new version of the CNCLI binary is installed, type:

```
cncli -V
```

If the command displays the latest version number, then the upgrade is successful.

### Guild LiveView

[Guild Operators](https://cardano-community.github.io/guild-operators/) maintains a set of tools to simplify operating a Cardano stake pool, including [Guild LiveView](https://cardano-community.github.io/guild-operators/Scripts/gliveview/). If you use the gLiveView script, then ensure that you are using the latest version prior to upgrading your Cardano node.

{% hint style="info" %}
In the [Common `env`](https://cardano-community.github.io/guild-operators/Scripts/env/) file for Guild Operator scripts, by default the `UPDATE_CHECK` user variable is set to check for updates to scripts when you start `gLiveView.sh`. If your implementation displays the message `Checking for script updates` when gLiveView starts and you install available updates when prompted, then you do NOT need to complete the following procedure.
{% endhint %}

**To upgrade the Guild LiveView tool manually:**

1. To back up existing Guild LiveView script files, type the following commands where `<gLiveViewFolder>` is the folder where the `gLiveView.sh` script is located on your computer:

```bash
cd <gLiveViewFolder>
mv gLiveView.sh gLiveView.sh.bak
mv env env.bak
```

{% hint style="info" %}
If you follow the Coin Cashew instructions for [Starting the Nodes](../part-iii-operation/starting-the-nodes.md), then you can type `$NODE_HOME` to replace
{% endhint %}

1. To download the latest Guild LiveView script files, type:

```bash
curl -s -o gLiveView.sh https://raw.githubusercontent.com/cardano-community/guild-operators/master/scripts/cnode-helper-scripts/gLiveView.sh
curl -s -o env https://raw.githubusercontent.com/cardano-community/guild-operators/master/scripts/cnode-helper-scripts/env
```

1. To set file permissions on the `gLiveView.sh` file that you downloaded in step 2, type:

```bash
chmod 755 gLiveView.sh
```

1. To set the `CONFIG` and `SOCKET` user variables in the `env` file that you downloaded in step 2, type:

```bash
sed -i env \
    -e "s/\#CONFIG=\"\${CNODE_HOME}\/files\/config.json\"/CONFIG=\"\${NODE_HOME}\/mainnet-config.json\"/g" \
    -e "s/\#SOCKET=\"\${CNODE_HOME}\/sockets\/node0.socket\"/SOCKET=\"\${NODE_HOME}\/db\/socket\"/g"
```

{% hint style="info" %}
For details on setting the `NODE_HOME` environment variable, see the topic [Installing Cabal and GHC](../part-i-installation/installing-cabal-and-ghc.md)
{% endhint %}

1. As needed to configure Guild LiveView for your stake pool, use a text editor to transfer additional user variable definitions from the `env.bak` file that you created in step 1 to the `env` file that you downloaded in step 2.
2. To test the upgrade, type:

```bash
gLiveView.sh
```

If the upgrade is successful, then the terminal window displays the Guild LiveView dashboard having the version number of the latest release.

## :nut\_and\_bolt:Setting GHC and Cabal Versions <a href="#setgcversions" id="setgcversions"></a>

For each Cardano Node release, Input-Output recommends compiling binaries using specific versions of GHC and Cabal. For example, refer to [Installing cardano-node and cardano-cli from source](https://developers.cardano.org/docs/get-started/installing-cardano-node/) in the [Cardano Developer Portal](https://developers.cardano.org/docs/get-started/) to determine the GHC and Cabal versions required for the current Cardano Node release. _Table 1_ lists GHC and Cabal version requirements for the current Cardano Node release.

_Table 1 Current Cardano Node Version Requirements_

|  Release Date | Cardano Node Version | GHC Version | Cabal Version |
| :-----------: | :------------------: | :---------: | :-----------: |
| June 25, 2022 |        1.35.0        |    8.10.7   |    3.6.2.0    |

**To upgrade the GHCup installer for GHC and Cabal to the latest version:**

* In a terminal window, type:

```
ghcup upgrade
ghcup --version
```

**To install other GHC versions:**

* In a terminal window, type the following commands where `<GHCVersionNumber>` is the GHC version that you want to install and use:

```
ghcup install ghc <GHCVersionNumber>
ghcup set ghc <GHCVersionNumber>
ghc --version
```

**To install other Cabal versions:**

* In a terminal window, type the following commands where `<CabalVersionNumber>` is the Cabal version that you want to install and use:

```
ghcup install cabal <CabalVersionNumber>
ghcup set cabal <CabalVersionNumber>
cabal --version
```

{% hint style="info" %}
To set GHCup, GHC and Cabal versions using a graphical user interface, type `ghcup tui` in a terminal window.
{% endhint %}

## :inbox\_tray:Downloading New Configuration Files

A new Cardano Node release may include updated configuration files. If configuration files are updated for a release, then you need to download and install the new configuration files when you upgrade a node.

**To download and install new Cardano Node configuration files:**

1. To stop your Cardano node, type the following command in a terminal window where `<CardanoServiceName>` is the name of the systemd service running your Cardano node:

```bash
sudo systemctl stop <CardanoServiceName>.service
```

{% hint style="info" %}
If you follow the Coin Cashew instructions for [Creating Startup Scripts](../part-ii-configuration/creating-startup-scripts.md), then `<CardanoServiceName>` is `cardano-node`
{% endhint %}

1. To back up the configuration files that your node currently uses, type the following commands where `<ConfigurationFileFolder>` is the path to the folder where the configuration files are located:

```bash
cd <ConfigurationFileFolder>
mv mainnet-config.json mainnet-config.bak
mv mainnet-byron-genesis.json mainnet-byron-genesis.bak
mv mainnet-shelley-genesis.json mainnet-shelley-genesis.bak
mv mainnet-alonzo-genesis.json mainnet-alonzo-genesis.bak
mv mainnet-topology.json mainnet-topology.bak
```

{% hint style="info" %}
If you follow the Coin Cashew instructions for [Preparing Configuration Files](../part-ii-configuration/preparing-configuration-files.md), then `<ConfigurationFileFolder>` is `$HOME/cardano-my-node` Alternately, you can type `$NODE_HOME` If needed, you can also use the environment variable `$NODE_CONFIG` to indicate the `mainnet` cluster in configuration file names.
{% endhint %}

1. Using a Web browser, navigate to the Cardano Node [GitHub repository](https://github.com/input-output-hk/cardano-node), then browse to the latest release, then click to expand the Downloads dropdown list in the Technical Specification section of the release notes, and then click the Configuration Files link.
2. On the Cardano Configurations page, click the following links to download configuration files for the `mainnet` cluster to the folder where you created backups of your current configuration files in step 2: `config`, `byronGenesis`, `shelleyGenesis`, `alonzoGenesis` and `topology`

{% hint style="info" %}
If you want to download new configuration files using the command line, then navigate to the folder where you created backups of your current configuration files in step 2 using a terminal window, and then type the following command where `<ConfigurationFileURL>` is the URL for the configuration file that you want to download: `wget <ConfigurationFileURL>`
{% endhint %}

1. Using [`diff`](https://www.man7.org/linux/man-pages/man1/diff.1.html) or a similar file comparison utility, identify and copy customizations as needed from the backup configuration files that you created in step 2 to each new configuration file that you downloaded in step 4.

## :zap:Building Cardano Node Binaries <a href="#buildingcn" id="buildingcn"></a>

{% hint style="danger" %}
Prior to building Cardano Node 1.35.0 binaries, [install libsecp256k1](https://github.com/coincashew/coincashew/blob/cb3c75e9e1099926dedb88cb49371d8542bcd848/coins/overview-ada/guide-how-to-build-a-haskell-stakepool-node/part-i-installation/installing-cabal-and-ghc.md#Installsecp256k1), and then type `sudo ldconfig`
{% endhint %}

**To build binaries for a new Cardano Node version:**

1. To create a clone of the Cardano Node [GitHub repository](https://github.com/input-output-hk/cardano-node), type the following commands in a terminal window on the computer you want to upgrade where `<NewFolderName>` is the name of a folder that does not exist:

```bash
# Navigate to the folder where you want to clone the repository
cd $HOME/git
# Download the Cardano Node repository to your local computer
git clone https://github.com/input-output-hk/cardano-node.git ./<NewFolderName>
```

{% hint style="info" %}
Cloning the GitHub repository to a new folder allows you to roll back the upgrade, if needed, by re-installing on your computer the `cardano-node` and `cardano-cli` binaries from a folder where you compiled a previous version of Cardano Node packages.
{% endhint %}

1. To build Cardano Node binaries using the source code that you downloaded in step 1, type the following commands where `<NewFolderName>` is the name of the folder you created in step 1 and `<GHCVersionNumber>` is the GHC version that you set in the section [Setting GHC and Cabal Versions](upgrading-a-node.md#SetGCVersions):

```bash
# Navigate to the folder where you cloned the Cardano Node repository
cd $HOME/git/<NewFolderName>
# Update the list of available packages
cabal update
# Download all branches and tags from the remote repository
git fetch --all --recurse-submodules --tags
# Switch to the branch of the latest Cardano Node release
git checkout $(curl -s https://api.github.com/repos/input-output-hk/cardano-node/releases/latest | jq -r .tag_name)
# Adjust the project configuration to disable optimization and use the recommended compiler version
cabal configure -O0 -w ghc-<GHCVersionNumber>
# Append the cabal.project.local file in the current folder to avoid installing the custom libsodium library
echo -e "package cardano-crypto-praos\n flags: -external-libsodium-vrf" >> cabal.project.local
# Compile the cardano-node and cardano-cli packages found in the current directory
cabal build cardano-node cardano-cli
```

{% hint style="info" %}
The time required to compile the `cardano-node` and `cardano-cli` packages may be a few minutes to hours, depending on the specifications of your computer.
{% endhint %}

1. When the compiler finishes, to verify the version numbers of the new `cardano-node` and `cardano-cli` binaries type the following commands where `<NewFolderName>` is the folder where you cloned the Cardano Node GitHub repository in step 1:

```bash
$(find $HOME/git/<NewFolderName>/dist-newstyle/build -type f -name "cardano-node") version
$(find $HOME/git/<NewFolderName>/dist-newstyle/build -type f -name "cardano-cli") version
```

## :open\_file\_folder:Installing New Cardano Node Binaries

**To install new `cardano-node` and `cardano-cli` binaries:**

1. If your Cardano node is running, then type the following command to stop the node where `<CardanoServiceName>` is the name of the systemd service running your node:

```bash
sudo systemctl stop <CardanoServiceName>.service
```

1. To replace the existing `cardano-node` and `cardano-cli` binaries, type the following commands where `<NewFolderName>` is the folder where you cloned the Cardano Node GitHub respository in the section [Building Cardano Node Binaries](upgrading-a-node.md#BuildingCN) and `<DestinationPath>` is the absolute file path to the folder where you install Cardano Node binaries on your local computer:

```bash
sudo cp $(find $HOME/git/<NewFolderName>/dist-newstyle/build -type f -name "cardano-node") <DestinationPath>/cardano-node
sudo cp $(find $HOME/git/<NewFolderName>/dist-newstyle/build -type f -name "cardano-cli") <DestinationPath>/cardano-cli
```

{% hint style="info" %}
If you follow the Coin Cashew instructions for [Compiling Source Code](../part-i-installation/compiling-source-code.md), then `<DestinationPath>` is `/usr/local/bin`
{% endhint %}

1. To verify that you installed the new Cardano Node binaries successfully, type:

```bash
cardano-node version
cardano-cli version
```

1. Optionally, to install the latest versions of all previously installed packages on your computer, and then reboot the computer, type:

```
sudo apt-get update && sudo apt-get upgrade -y && sudo reboot
```

1. If you need to restart your Cardano node manually, then type the following command where `<CardanoServiceName>` is the name of the systemd service running your Cardano node:

```bash
sudo systemctl start <CardanoServiceName>.service
```

1. Copy the new `cardano-node` and `cardano-cli` binaries to the air-gapped, offline computer that you use to sign transactions for your stake pool.

## :checkered\_flag:Verifying the Upgrade

To verify that the upgrade is successful, open gLiveView, journactl logs or Grafana dashboard.

{% tabs %}
{% tab title="gLiveView" %}
```bash
cd ${NODE_HOME}
./gLiveView.sh
```
{% endtab %}

{% tab title="journalctl" %}
```
journalctl -fu cardano-node
```
{% endtab %}
{% endtabs %}

Starting a node may take up to 30 minutes. Be patient.

{% hint style="success" %}
Congrats on completing the update. :sparkles:

Did you find our guide useful? Send us a signal with a tip and we'll keep updating it.

It really energizes us to keep creating the best crypto guides.

Use [cointr.ee](https://cointr.ee/coincashew) to find our donation addresses. :pray:

Any feedback and all pull requests much appreciated. :first\_quarter\_moon\_with\_face:

Hang out and chat with our stake pool community on Telegram @ [https://t.me/coincashew](https://t.me/coincashew)
{% endhint %}

## :rotating\_light:Troubleshooting

If your upgrade is unsuccessful, then try [Fixing a Corrupt Blockchain](../part-v-tips/fixing-a-corrupt-blockchain.md) and [Resetting an Installation](../part-v-tips/resetting-an-installation.md), as needed.
