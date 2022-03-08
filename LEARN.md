# Creating your own NFT Minting Machine and Website (v2)

Welcome to the Candy Machine Quest. This quest focuses on the well-known Candy Machine, a fully on-chain generative NFT distribution program. There are many improvements in this iteration over its predecessor. We don't require you to have any background on Solana development. If you're someone who loves creating NFTs and has a hard time due to the high prices of minting, you are in the right place. 

In this Quest, we'll be building our own Candy Machine which will be used to mint NFTs on the Solana Network without paying the minting price for each mint. Minting a bunch of NFTs is really pricy on other networks and thus we will be minting our NFTs on Solana Network. Even then, if we are going to mint a large number of NFTs (say 10000) it will be very costly. Thus, we will be building our own Candy Machine and the price will be paid by the buyer of the NFT. The candy machine here can be considered analogous to real world candy machine where we fill in the machine with candies (NFTs) beforehand & the customers can buy the candy (NFT) by paying the price set earlier.

By the end of this quest, you will be able to mint your own NFTs on the Solana network with just a bunch of commands and subtle configurations.

`NOTE:`
-  Throughout this guide, the newer Candy Machine v2 will be referred to as `CMv2` and the older version as `CMv1`.
- No prerequisite from the old version required. You can get started from scratch here. 


## Metaplex Introduction


Metaplex is a collection of tools, smart contracts, and more designed to make the process of creating and launching NFTs easier. 

Metaplex is a protocol built on top of Solana that allows:
- Creating/ Minting non-fungible tokens (NFTs)
- Starting a variety of auctions for primary/secondary sales
- visualizing NFTs in a standard way across wallets and applications

The candy-machine is an on chain Solana program (smart contract) that governs fair mints. It will help interact with the candy-machine program.  We will be using it to: 
- upload our images and metadata to arweave, then register them on the solana block-chain
- verify the state of our candy-machine 
- mint individual tokens

## New Features in CMv2

- **Unpredictable mint index** - In the `CMv1`, it was possible to estimate what item would be minted, since minting happened in a sequential order. This created opportunity to be able to choose which item to mint. The `CMv2` eliminates the possibility by using an unpredictable mint index, which it is not possible to determine in advance. 
- **Whitelist** - There is now possibility to create several different configurations for whitelists. You can allow whitelist users to mint before the start date, mint at a discount price or restrict the mint entirely to only whitelist users. 
- **Captcha Settings**- Integration with captcha to limit the mint to humans, a sad ending for bots.
- **Larger collections and hide-and-reveal drops** - It is possible to create super large and hide-and-reveal drops by specifying a single hash, which is used by all mints.

Along with all these above major features, there are also improvements to how one configures' the Candy Machine and new settings that allow the definition of rules to **pause the mint at a certain point**. On top of it, every configuration value can be **updated at any point**.

## Developer tools required

You will need to install the recent version of the following tools:
- **git**: to clone the metaplex repository
- **node**: Javascript runtime environment
- **yarn**: package manager for installing the required dependencies
- **ts-node**: Typescript execution environment

You can check the versions of the above mentioned packages by running the following command:
	`<toolName> version`
	
 *Ex:* For git, it will be : `git version	`. The output will be `git version 2.17.1`
 For you, the version number might be different. The latest the version, the more better it is.

| Tool Name  | Latest Version |
| ------------- | ------------- |
| git  | 2.32.0  |
| node  | v16.13.0  |
| yarn  | 1.22.17  |
| ts-node  | v10.4.0  |

## Metaplex Setup


We can use the Metaplex github repo to get started. The link to the repository is [Metaplex Github](https://github.com/metaplex-foundation/metaplex)

Lets clone the repository on our device. Open the terminal in your desired location of project creation and type the following command.

```
git clone https://github.com/metaplex-foundation/metaplex.git

```
If you want to clone to a specific location, you can do so by  mentioning the location in the command too like:
```
git clone https://github.com/metaplex-foundation/metaplex.git <destination-location>
```


After a while, you will be having the metaplex folder on your machine. It will be in the folder where you did run the clone command. 

![Metaplex Folder Structure](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/0_Metaplex_Folder_Structure.png)



Let us now install the dependencies. We can do it from outside the metaplex directory itself by: 
```
yarn install --cwd ./metaplex/js
```

It will take a while based on your internet connection. After successful installation, you can if everything is working by running the Candy Machine CLI command:
```
ts-node ~/metaplex/js/packages/cli/src/candy-machine-v2-cli.ts --version
```
If giving the output as `0.0.2`, it has been setup correctly.

Congratulations. You are done with the initial Metaplex setup.


## Solana Environment Setup

For interacting with the Solana network, we need to install Solana CLI tool. Below is the installation commands:

>For MacOS A& Linux Users
```
sh -c "$(curl -sSfL https://release.solana.com/v1.8.2/install)"
```



After successful updation, the terminal will have the following message:

```
downloading v1.8.2 installer
Configuration: /home/solana/.config/solana/install/config.yml
Active release directory: /home/solana/.local/share/solana/install/active_release
* Release version: v1.8.2
* Release URL: https://github.com/solana-labs/solana/releases/download/v1.8.2/solana-release-x86_64-unknown-linux-gnu.tar.bz2
Update successful

```

> For Windows Users
- Open a Command Prompt as an Administrator.
- Copy and paste the following command, then press Enter to download the Solana installer into a temporary directory:
```
curl https://release.solana.com/v1.8.2/solana-install-init-x86_64-pc-windows-msvc.exe --output C:\solana-install-tmp\solana-install-init.exe --create-dirs

```

*You can replace v1.8.2 with the release tag matching the software version of your desired release.*

Here, you have succesfully installed, the Solana CLI tool. Please type the below command in the terminal:
```
solana --version
```

If the installation was correct and successful, you will be getting the version of cli installed.


## Wallet Creation & Setup

There are a couple of networks on the solana blockchain. Devnet serves as a playground for begineers. Mainnet beta is the real network.
Depending upon where you want to setup the project, you can set the the network to either devnet or mainnetbeta. 
> For devnet
```
solana config set --url https://api.devnet.solana.com 
```
> For mainnet-beta
```
solana config set --url https://api.mainnet-beta.solana.com 
```
We suggest you to connect to the devnet for initial playaround 
To check the configuration, you can type:
```
solana config get
```
And then will get the RPC URL of the network set above.

After succesfull configuration, we will need to create awallet for minting. Make sure to store its credentials for further usage. 
We can generate a new `KeyPair` wallet from the CLI itself by:

```
solana-keygen new --outfile ~/config/solana/devnet-test.json
```
--outfile flag will be storing the private key of the wallet by creating a file at the location mentioned at the end of the command. The name of the file here is devnet-test.json .
It will ask us for a passphrase to be used as a password for the wallet. After entering the pass phrase, you will be provided with the PubKey and the secret recovery phrase of the wallet in the terminal. The private key of the wallet is stored in the file stated in command & is available in the form of base64 . You have to store the PubKey of the wallet & the secret phrase for future reference.

If successfully created, you will have a prompt in the terminal like this. 

![Wallet Creation](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/1_Wallet_Creation.png)

With just a single command, you have created a new wallet. Its time to set this wallet as your default one. You can configure to set the keypair and provide the file location which you used initially during wallet creation.
```
solana config set --keypair ~/config/solana/devnet-test.json

```
Successful setup will prompt a message like this:

![Wallet Setup](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/2_Wallet_Setup.png)


The public address of your default wallet will be used to handle deployment, transactions, airdop SOLs,etc. To know the address of the wallet, you can type:
```
solana address 
```

For more details of your account, you can use the following command with the address that you got from the last command.
```
solana account <your address from the last command>
```

You can see the balance of the wallet too. To explicitly view just the balance, you can use `solana balance`.

Time to get some SOL for the project to be used in minting (just a small amount independent of the amount of NFTs) . For those who are connected to the devnet, we can airdrop SOLs to the wallet. For users who are connected to the mainnet-beta, they can use the wallet address of the default wallet and transfer real SOL to the wallet. 
For devnet users, the command is:
```
solana airdrop 2
```
Successful airdrop will be be prompting message like this.
![Balance and Airdrop](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/3_Balance_and_Airdrop.png)

## CONFIGURATION OF CANDY MACHINE

Instead of creating a candy machine and updating its configurations through commands like in `CMv1`, in this version, we can specify all the configurations in a single JSON file. This allows to save and reuse the configuration across multiple drops. 
There are many new features (Captcha, Hidden Settings, End Settings and Whitelist Settings) which can be specified in the configuration but we will be moving forward with the minimal configuration. 
Create a file named `config.json ` in the cloned metaplex directory. It is a JSON file whose contents are in a Object format(in Javascript). 
A minimal candy machine config settings will look like this:
```
{  
"price":  1.0,  
"number":  10,  
"gatekeeper":  null,  
"solTreasuryAccount":  "<YOUR WALLET ADDRESS>",  
"splTokenAccount":  null,  
"splToken":  null,  
"goLiveDate":  "15 Jan 2022 00:00:00 GMT",  
"endSettings":  null,  
"whitelistMintSettings":  null,  
"hiddenSettings":  null,  
"storage":  "arweave",  
"ipfsInfuraProjectId":  null,  
"ipfsInfuraSecret":  null,  
"awsS3Bucket":  null,  
"noRetainAuthority":  false,  
"noMutable":  false  
}
```
`NOTE`: All the settings which are not used are set to null to avoid errors from the CLI.

The settings that are specified in this example are:
-   **price**: The amount in SOL or SPL token for a mint
-   **number**: The number of items in the Candy Machine
-   **solTreasureAccount**: The SOL wallet address to receive proceedings from SOL Payments
-   **goLiveDate**: Timestamp when minting is allowed – the Candy Machine authority and whitelists can bypass this constraint. In current configuration, the goLive Date has been set to 15 Jan,2022. It means all the NFTs will be available for minting from that time.
-  **storage**: It specifies the storage type to upload images and metadata. In case you are working on devnet, make sure to set its value as `arweave`. But while you are on the mainnet-beta, keep the storage type as `arweave-sol`.
-  **noRetainAuthority**:  Indicates whether the candy machine authority has the update authority for each mint or not. 
-   **noMutable**: Indicated whether each mint is mutable or not

	

	Important configurations which you can try on are:
- **gatekeeper**: The configuration for enabling captcha settings. Configuring it will secure your mint from bot attacks.
- **hiddenSettings**: The configuration for allowing creation of larger drops, and also allowing the creation of hide-and-reveal-drops
- **endSettings**: The configuration providing a mechanism to stopnthe mint if a certain condition is met without ineraction.
- **whitelistMintSettings**: The configuration resolves around the idea of using custom SPL tokens to offer special rights to token holders. Features like providing discounts on mints, early access or exclusive sales.

## NFT METADATA

Every NFT minting on candy machine requires two information:
- The actual asset (image, gif, etc) of the NFT
- The metadata for that NFT (will be in the json format)

Now, we will be creating the actual assets for the minting. Create a folder named `assets` in the metaplex directory. This will contain the metadata for NFTs. 
You should familiarize yourself with the [Token Metadata Standard](https://docs.metaplex.com/token-metadata/v1.1.0/specification)

In this quest, we will be having a 10-item collection which will have 20 files in total:
| Image  | Metadata |
| ------------- | ------------- |
| 0.png  | 0.json  |
| 1.png  | 1.json  |
| 2.png  | 2.json  |
| 3.png  | 3.json  |
| 4.png  | 4.json  |
| 5.png  | 5.json  |
| 6.png  | 6.json  |
| 7.png  | 7.json  |
| 8.png  | 8.json  |
| 9.png  | 9.json  |


Depending upon the number of NFTs you want to have in the Candy Machine, you will need to have those many pairs of files.( an actual file and a JSON file for metadata).
>The first item in your collection must have the index `0`, the second `1` and so forth. In a `10000` NFT drop, will start with the pair `0.png` and `0.json`, and end with the pair `9999.png` and `9999.json`. The numbering must also be consecutive - i.e., should not have gaps in the numbering.

You can use this [sample collection](https://docs.metaplex.com/assets/files/assets-934a7281da49092b2a477733d067d8a0.zip) for your asset files. Extract the files into your assets folder. 

### Creating the NFT Metadata
All these attributes which will be stated now are must have attributes in the metadata of NFT while minting using Candy Machine. For better understanding, lets start from scratch. Create a file named `0.json` in the assets folder. JSON file has object like structure. Thus, initialize the file content as:
```
{
}
```
#####  Adding attibutes to the metadata
- Name, stands for the Name of the NFT. Lets name it as `Number #0001`
- Description- Human readable description of the NFT
- Image- URL to the image of the asset. PNG, GIF and JPG file formats are supported. Since, we have the image in the asset folder already, we can put the value as `0.png`.

After adding these attributes, the JSON file will look like this:
```
{
	"name":"Number #0001",
    "description":"Collection of 10 numbers on the blockchain. This is the number 1/10.",
    "image":"0.png"
}
    
```
- Symbol- The symbol of the asset. It can be empty if there is no specific symbol 
- Seller_fee_basis_points- Royalties percentage awarded to creators. A value of 250 means each creator will get a royalty fee of 2.5% per each NFT. We will be stating 0 royalty points. It means the creators won't be receiving any royalty for any transaction of the NFT. It will be an integer value. 

After adding these three attributes to the json object, the file will look like:
```
{
	"name":"Number #0001",
 	"symbol": "",
    "description":"Collection of 10 numbers on the blockchain. This is the number 1/10.",
 	"seller_fee_basis_points": 0
    "image":"0.png",
}
```
- Collection - Most popular NFTs are launched in collections. So, if your NFT belongs to any particular collection, the information can be provided here
- Properties
   - Files- This attribute is an Object array, where an object will contain the uri and type of the file that is part of the asset. The type should match the file extension. The array will also include files specified in image and animation_url fields, and any other that are associated with the asset.
   - Creators - This attribute will contain the  public address of the creators along with the share each creator has. Here, we have provided the address of the wallet we have generated recently.
 - Attributes- It is an object array, where an object will contain trait_type and value fields. value can be a string or a number. Different assets will have different values for the same trait_types in a collection. As the image used as an asset here has different traits , all the trait_types are listed here with their value in the asset.

Adding all the other mentioned attributes in the json will be resulting into:
```
{
	""name":"Number #0001",
 	"symbol": "",
    "description":"Collection of 10 numbers on the blockchain. This is the number 1/10.",
 	"seller_fee_basis_points": 0
    "image":"0.png",
     "attributes": [
       {"trait_type":  "Layer-1",  "value":  "0"},  
	   {"trait_type":  "Layer-2",  "value":  "0"},  
	   {"trait_type":  "Layer-3",  "value":  "0"},  
	   {"trait_type":  "Layer-4",  "value":  "1"}
    ],
    "properties": {
       "files": [
         {
           "url": "0.png",
           "type": "image/png"
         }
       ],
       "creators": [
         {
           "address": "PUBLIC ADDRESS OF THE CREATOR",
           "share": 100
         }
       ]
     },
    "collection": {
       "name": "numbers",
       "family": "numbers"
     },   
}
```
`NOTE: ` 
- The attributes of the NFT can be anything. The trait_type remains common in a collection of NFTs and the value is something which varies across different NFTs.  
- Please follow the above structure and make sure to have all the mentioned attributes in their actual format if you are creating the assets from scratch. Failure doing so will be resulting in errors during uploading assets.
- Please put in the correct public address of the creator in the json files so that you will  be able to receive payments made while minting.

Kudos, you now know how to create metadata for NFTs which will be minted using the Candy Machine. 

## CREATING THE CANDY MACHINE

Once you have your collection prepared, the next step is to upload your assets and create a Candy Machine. Unlike `CMv1`, it can be done with a single command via the Candy Machine CLI. 

Before moving forward, you will have to make sure that:
-  Your images and metadata are located in the same directory - in most cases this will be a directory named  `assets`
-   You have funds in your wallet - the command  `solana balance`  will tell your current balance
-   You have created your Candy Machine configuration file (e.g.,  `config.json`)

To proceed machine creation use the following command in your metplex directory:
```
ts-node ./js/packages/cli/src/candy-machine-v2-cli.ts upload -e devnet -k ~/.config/solana/devnet.json -cp ./config.json -c example ./assets
```

`NOTE`: To run the above command one folder directory above  your metaplex folder, use the following command:
```
ts-node ./metaplex/js/packages/cli/src/candy-machine-v2-cli.ts upload -e devnet -k ~/.config/solana/devnet.json -cp ./metaplex/config.json -c example ./metaplex/assets
```
![Candy Machine Creation](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/4_Candy_Machine_Creation.png)

Breaking down the command:
 - Using the `candy-machine-v2-cli` to create by the command `ts-node ./js/packages/cli/src/candy-machine-v2-cli.ts upload`.
 - `-e` flag specifies the environment we are uploading, `devnet` in this case.  
 - `-k` specifies the wallet keypair in use. It is  ` ~/.config/solana/devnet.json`  in our case and it is that wallet which was created during the initial setup.
- `-cp` f;ag specifies the location of the configuration file for the candy machine. It has been specified as `./config.json` in our case since we have created the config file within the metaplex folder
- Uploading the assets from the folder location `./assets` in our case

If successfully created, you will also have the `Candy Machine PublicKey` displayed on terminal. You can verify it on the [Solana Explorer](https://explorer.solana.com/) to validate upload.

### Verifying the Upload 

You can verify the NFTs upload and Candy machine creation, using the following command
```
ts-node ./js/packages/cli/src/candy-machine-v2-cli.ts verify_upload -e devnet -k ~/.config/solana/devnet.json -c example
```

![Verifying Upload](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/5_Verify_Upload.png)

And, your assets are open for sale. 

Congratulations, on successfully creating your own candy machine using metaplex. Now, you don't need to rely on applications to mint your NFTs. 

## FRONTEND INTERFACE FOR CANDY MACHINE V2

While the Candy Machine is ready to mint, in most cases you will want to provide a front end experience to allow your community the chance to mint, too.

You can use the Candy Machine v2 front end experience, which is already in the Metaplex repository and downloaded when you executed the  `git clone`  command.

You can find the UI within your metaplex folder in the following location: `/js/packages/candy-machine-ui`. 
It is a react application using typescript. It does have all the necessary files in place to create your basic frontend interface. 

You will only need to make a few changes before starting the frontend interface. You will find a file named `.env.example` in the candy-machine-ui folder. Follow the below stated steps:
 - Change the file name from `.env.example` to `.env`. In case of react applications, all the configuration keys are generally specified in a env file.
 - From the previous command of creating your candy machine, you would have got the `ID` of the candy machine. Put the value for the variable `REACT_APP_CANDY_MACHINE_ID` as the ID of your candy machine. If you missed out the ID from the previous command, you can find it out in the `./cache/devnet-example.json` file which gets created if the upload is successful.
 -  Specify the intended network you wish to use. In this quest, since we are using the `devnet`, the values for the other variables will be as:
 ```
 REACT_APP_SOLANA_NETWORK=devnet  
REACT_APP_SOLANA_RPC_HOST=https://api.devnet.solana.com
```

You are now ready to have the interface for your candy-machine. Open terminal in the folder location `./js/packages/candy-machine-ui`. Run the following command:

 ```
 yarn install && yarn start
 ```
 `yarn install` will install the dependencies for the front-end application and after installing all of them, `yarn start` will run your application locally. 
 
![Frontend Interface](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/6_Frontend_Website.png)


You can connect to any of your wallets containing SOL (and switch over to devnet). Clicking on mint will add a NFT token in your collectible. I am using Phantom wallet here. So, on clicking mint, my collectible has the new NFT token called Number #03.

`NOTE:` Make sure you have some SOL in the connected wallet. If not, you can airdrop some SOL since it is devnet. 

![Showing Collectibles](https://raw.githubusercontent.com/vamsisol/create_a_candy_machine_minting_nft_and_minting_website_v2/main/learn_src/learn_assets/7_Show_Collectible.png)


## UPDATING THE CANDY MACHINE

In case you want to change the configuration of your candy machine, you can do so by modifying the `config.json` file. Although there are a couple of exceptions:
-   `number`  of items in the Candy Machine can only be updated when  `hiddenSettings`  are being used.
    
-   switching to use  `hiddenSettings`  is only possible if the  `number`  of items is equal to  `0`. After the switch, you will be able to update the  `number`  of items.

Let us change our price per mint to 0.5 SOL. Copy the bottom configuration to your config file. 

```
{  
"price":  0.5,  
"number":  10,  
"gatekeeper":  null,  
"solTreasuryAccount":  "<YOUR WALLET ADDRESS>",  
"splTokenAccount":  null,  
"splToken":  null,  
"goLiveDate":  "15 Jan 2022 00:00:00 GMT",  
"endSettings":  null,  
"whitelistMintSettings":  null,  
"hiddenSettings":  null,  
"storage":  "arweave",  
"ipfsInfuraProjectId":  null,  
"ipfsInfuraSecret":  null,  
"awsS3Bucket":  null,  
"noRetainAuthority":  false,  
"noMutable":  false  
}
```
Awesome. We will now need to run the `update_candy_machine` command:
```
ts-node ./js/packages/cli/src/candy-machine-v2-cli.ts update_candy_machine -e devnet -k ~/.config/solana/devnet.json -cp ./config.json -c example
```

If successful, the command will output the message with the update transaction confirmation:
```
update_candy_machine finished 2zT344ZjS5FSJFsZRYE7Yu7Fg9sBtDQESSzPv1kNGezP7Mx8vDbf8geDQGvC3iMdbfo2GWCdPrZbsq58ZwmQ8136
```

## CHALLENGES

- Try to implement the advanced features in configuration like: Captcha , Hidden settings, End settings and Whitelist settings.

## WHAT'S NEXT

You can try hosting your frontend interface on to any server . Meanwhile, you can proceed to [Layer3](https://alpha.layer3.xyz/task/create-an-nft-candy-machine) and claim your reward NFT for succesfully completing this quest!
