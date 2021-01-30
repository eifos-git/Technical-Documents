# Creating a witness account

For a given network, say MAINNET or TESTNET we need to first create a user account using a faucet. 

{% hint style="info" %}
**Faucet ?!**

In the blockchain world, account creation services are nicknamed as faucets. They create unique Network Address Identifiers.
{% endhint %}

To create a Peerplays MAINNET account, the easiest way is to use the GUI wallet available to download from [Github](https://github.com/peerplays-network/peerplays-core-gui/releases).  The GUI wallet will use a faucet to create an account for you using the username and password you've chosen.

Creating a user in the Peerplays GUI wallet is self explanatory.

For the Peerplays TESTNET there may not always be a faucet up and running. In that case ask one of the current witnesses to get you started.

#### 2. Upgrading the Peerplays account to a witness account

We can do this from the `cli_wallet` & this includes multiple steps.

1. Buy some PPY \(or ask your friendly whale!\) from an exchange
2. Upgrade your new account to a Life Time Member \(LTM\)
3. Register and update the witness to the network

For the first step, we need to access the newly created Peerplays account from the cli\_wallet. Instructions are given below:.

{% page-ref page="../obtaining-private-keys-for-use-in-cli\_wallet.md" %}



## CLI Wallet Setup

In its own terminal window, start the command-line wallet `cli_wallet`:

```text
./programs/cli_wallet/cli_wallet
```

To set your initial wallet password to `password` use:

```text
>>> set_password password
>>> unlock password
```

{% hint style="info" %}
**Tip**: A list of CLI wallet commands is available here:[ ](https://github.com/PBSA/peerplays/blob/master/libraries/wallet/include/graphene/wallet/api_documentation.hpp)[CLI Wallet commands](https://github.com/PBSA/peerplays/blob/master/libraries/wallet/include/graphene/wallet/api_documentation.hpp)
{% endhint %}


### Get Owner and Active Keys

To derive your owner and active keys use the `get_private_key_from_password` command:

```text
get_private_key_from_password your_witness_username owner the_password_youve_used_to_register_your_account
get_private_key_from_password your_witness_username active the_password_youve_used_to_register_your_account
```

This will return an array key pair in the form `["PPYxxx", "xxxx"].`
The first string starting with PPY is the public key, the second one is the private key.
You'll now have 2 sets of keys, one for the owner key(pair) and one for the active key(pair)

The owner permission has administrative powers over the whole account and should be considered for ‘backup’ strategies.
The active permission allows to access funds and some account settings, but cannot change the owner permission and is thus considered the 'online' permission.


### Import Keys into your CLI Wallet

To import your keys, derived from the password in the previous step, into your CLI wallet do the following:

1. Use the second value in the array for the private key.
2. Make sure your username is in quotes. 
3. Import the key using the following command 

{% hint style="warning" %}
**Note**: Substitute `your-witness-username` with your own username.
{% endhint %}

```text
import_key "your_witness_username" xxxx
```

This command returns `true` when succesful.
To check the keys that have been imported into the wallet you can use the command `dump_private_keys`
This should show both key pairs.


### Upgrade Your Account to Lifetime Membership

```text
upgrade_account your_witness_username true
```
The current (one-time) cost to run this command is 5 PPY.


### Register Yourself as a Witness

Make sure your URL is in quotes and starts with http:// or https://

{% hint style="warning" %}
**Note**: Substitute `"url"` with your witness URL.
{% endhint %}

```text
create_witness your_witness_username "url" true
```
The current (one-time) cost to run this command is 8 PPY.

This command, when successful, will return an object which also contains your public `block_signing_key` and in the background it will also import the private block_signing_key into your wallet.

example:
```
{
  "ref_block_num": 3896,
  "ref_block_prefix": 2300171430,
  "expiration": "2021-01-30T19:33:15",
  "operations": [[
      20,{
        "fee": {
          "amount": 800000,
          "asset_id": "1.3.0"
        },
        "witness_account": "1.2.xxxxx",
        "url": "https://yourawesomewitnesssite.com",
        "block_signing_key": "PPYxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "initial_secret": "081a2ff5e24d19fe87167344e1d64a0fea20db9b"
      }
    ]
  ],
  "extensions": [],
  "signatures": [
    "1f7b47e5f404121834ccaed4e17de10f22a9b060fda55a2a8f00114545f2b3020175d44b96724342a0fa4c8cc5a5f4f5f13fa4cc40d3f326dba033877650f32ed2"
  ]
}
```

{% hint style="danger" %}
**Important**: Be sure to take note of  your `block_signing_key`
{% endhint %}

Run the following command using your public `block_signing_key` from the previous step:

```text
get_private_key block_signing_key
```

To double-check we can run `dump_private_keys` again which will now show three permission key pairs imported into the wallet. Owner, Active and Block_signing


### Get Your Witness ID

{% hint style="warning" %}
**Note**: Substitute `username` with you Witness username.
{% endhint %}

```text
get_witness username 
```
This will return some info about your witness account including its id which will be in `1.6.xxx` form
Take note of this `id` for the next step.


### Modify Your Witness Node Configuration

Next the Witness node configuration file, `witness_node_data_dir/config.ini`, needs to be modified to include your Witness ID \(from the previous step\), and your private block signing key pair.

{% hint style="warning" %}
**Note**: Substitute `"your_witness_id"` with your Witness ID. 

Don't forget to enclose in quotes!
{% endhint %}

{% hint style="warning" %}
**Note**: Substitute `"block_signing_key"` with your block signing key pair.

Substitute `"private_key_for_your_block_signing_key"` with your private key.

Don't forget to enclose in quotes!
{% endhint %}

edit the file witness_node_data_dir/config.ini
```text
witness-id = "your_witness_id"
private-key = ["block_signing_key","private_key_for_your_block_signing_key"]
```


### Start Your Witness Node

```text
./programs/witness_node/witness_node
```

If your Witness node fails to start, try again with these flags:

{% hint style="danger" %}
**Important**: Not for permanent use.
{% endhint %}

```text
./programs/witness_node/witness_node --resync --replay
```


### Vote For Yourself

All Witnesses have to be voted in, so start by voting for yourself!

```text
vote_for_witness your_witness_account your_witness_account true true
```


### Ask to be Voted In

The following document gives information on the Peerplays Witness voting process and advice on how to get voted in:

{% page-ref page="../becoming-a-witness.md" %}

Once you've received enough votes to become an active Witness you'll start producing blocks at the next maintenance interval \(once per hour\). You can check your votes with:

```text
get_witness your_witness_account
```

### 

