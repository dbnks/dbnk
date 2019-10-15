## 0. dBnk Welcome Screen
- User chooses to either signup or login to the application

## 1. dBnk Signup
###  Loading SCREEN Appears
---- Background Process Begins:
---- Random Entropy is generated (128 bits)
---- Generated Entropy should be hashed by the SHA256 hashing algorithm
---- Generated Entropy is given a checksum suffix (first 4 bits of the SHA256 hash of the generated entropy) This creates a 132 bit sequence. 
---- The 132 bit sequence is converted into 12 segments of 11 bits each
---- Using the methods from BIP-39 (mnemonics), each segment is used to identically match a specific word within a 2,048 world list. 
---- These twelve words will form a "mnemonic phrase" (Example: "army van defense carry jealous true garbage claim echo media make crunch"). 
### - CoinPin SCREEN Appears
---- User is asked to create a 6 digit pin number known as a ```CoinPin``` and then asked to confirm the entered ```CoinPin```. This number is temporarily saved to memory.
### - CoinPass SCREEN Appears
---- User is asked to create a password (all alphanumeric characters are acceptable, up to 32 characters), known as a ```CoinPass```. This password is temporarily saved to memory.
### - Mnemonic SCREEN Appears
---- User is asked to save the mnemonic phrase
### - Confirm Mnemonic SCREEN Appears
---- User is asked to confirm the mnemonic phrase they just saved.
### - Generating Keys SCREEN Appears
---- The mnemonic phrase is combined with a ```salt```, which is a combination of the string ```mnemonic```, the ```CoinPin``` and the ```CoinPass``` and using the ```PBKDF2 key-stretching function```, is converted into a 512 bit seed using the ```HMAC-SHA512``` algorithm.
---- The mnemonic phrase is stored in a file locally on the device that is encrypted with a ```SHA256``` of the ```CoinPin``` and the ```CoinPass```.
---- The ```CoinPin``` and the ```CoinPass``` are not saved anywhere and will be used to regenerate the 512 bit seed for recovery and signature measures in the future.
---- Using the 512-bit seed and the [arisenjs-ecc](https://github.com/arisenio/arisenjs-ecc) library, two Arisen keypairs are generated. One is for the ```owner``` permission level and the other is for the ```active``` permission level. The prefix ```A``` is added to the 512 bit seed, in order to generate a different ```active``` key. 
### - Saving Arisen Keys SCREEN Appears
---- Popup showing both keypairs is shown, asking the user to save both the ```owner``` and the ```active``` keypairs. 
---- Both public keys (owner and active) are saved to an unencrypted local file. 
### - Create Username SCREEN Appears 
---- User is prompted to create a username on the Arisen network. 
---- Account creation process utilizes [arisenjs](https://github.com/arisenio/arisenjs) to create an account using two locally stored public keys. 
---- Account creation uses the [dbnkaccount](https://github.com/dbnks/dbnkaccount-contract) contract for account creation.
---- [dbnkaccount](https://github.com/dbnks/dbnkaccount-contract) contract will stake the necessary RSN for account creation.
### - Region/Location SCREEN Appears
---- User is asked for their region/country in order to set a default government currency for balance valuations.
### - Peeps Private Membership Agreement
---- User is asked to join as a private member of Peeps and agree to the private membership agreement
### - Creating Initial Bank Accounts SCREEN Appears
---- User is asked to type the mnemonic phrase to confirm mnemonic was properly saved. 
---- A Deterministic Wallet (HD) of each integrated ```blockchain_type``` is created using the 512-bit seed, ultimately creating a master key for each blockchain type. Basically, a ```HMAC-SHA512```-based hash (512 bits) is taken of the 512-bit seed. The left 256 bits becomes what is known as the ```master private key``` aka ```m```. The ```master private key``` is 256 bits, since it is technically the left half of the ```HMAC-SHA512```-based hash of the 512-bit seed. The right half of the ```HMAC-SHA512```-based hash of the 512-bit seed is converted into the ```master chain code```. From the ```master private key``` a ```master public key``` aka ```M``` is generated, that is 264 bits in size. This process is ran for each ```blockchain_type```. 
---- Each one of these wallets is stored as a record on the Arisen blockchain, by way of the [dbnk contract](https://github.com/dbnks/dbnk-contract) smart contract that is stored on-chain. Each deterministic wallet's ```blockchain_type```, ```created_time```, ```account_type```, ```key_type```, ```account_identifier```, ```public_key``` and ```account_label``` are stored along with the ```dbnk_user``` that created it, all within the ```accounts``` **SCOPE** of the ```dbnk``` contract. This data can easily be filtered by the [dBnk dApp](https://github.com/dbnks/dbnk) via the [Arisen network](https://github.com/arisenio/arisen) itself.

## 2. dBnk Login
---- User has to enter ```CoinPin``` and ```CoinPass``` to decrypt mnemonic phrase, in order to derive the 512 bit seed.
---- 512 bit seed is used to recreate the ```owner``` keys and the 512 bit seed with ```A``` as a prefix is used to recreate the ```active``` keys.
---- If both keys match for the username, then the user is successfully authenticated. If not, either the ```CoinPass```, the ```CoinPin``` or both the ```CoinPass``` and ```CoinPin``` are wrong. 

### 2.1 dBnk Login Process
```
LOGIN -> COINPASS/COINPIN ->
|
COINPIN + COINPASS INPUTTED ->
| 
DECRYPT LOCAL MNEMONIC FILE ->
|
COINPIN + COINPASS + MNEMONIC PHRASE + "mnemonic"  == 512 Bit Seed ->
| 
512 BIT SEED -> ARISEN OWNER PRIVATE KEY 
|
"A" + 512 BIT SEED -> ARISEN ACTIVE PRIVATE KEY 
|
AUTHENTICATE ARISEN USER
```

## 3. dBnk Intro  (New User Onboarding)
--- **We're Not A Bank, You Are.** (Informs users that dBnk is not a bank, that they are in fact their own bank)
--- **dBnk Accounts** (Explains a crypto account vs. a bank account) 
--- **Digital Money** (Explains cryptocurrency and digital currencies within dBnk)
--- **Security** (Explains cryptographic security) 
--- **Add-Ons** (Explains dBnk Extensions Marketplace and a future addons)
--- **Experience The Future Of Banking** - (Button leading to dBnk's Home screen)

##  4. dBnk Home
### Balance Header
---- Shows combined balance (using dBnkJS), of all current wallets (accounts)
---- Pulls all wallet addresses for each ```blockchain_type``` for a given username found within the ```accounts``` **SCOPE** of the ```dbnk``` contract.
---- Using ```dBnkJS``` and its ```Multichain API```, each individual addresses' balance is retrieved from each respective blockchain every 30 seconds.
---- Balance is then converted to a government-backed currency value (chosen in the settings and at startup, by the user). Default currency is chosen during signup when user selections ```country/region```. Government-backed currency values derive from ```CoinMarketCap's API```.

### Transactions
---- Below the balance header, are the latest transactions from all account addresses. 
---- Pulls all wallet addresses for each ```blockchain_type``` for a given ```dbnk_username``` found within the ```accounts``` **SCOPE** of the ```dbnk``` contract.
---- Pulls the last 5 transactions from each wallet address of each respective ```blockchain_type```. 
---- This is shortened to the last ten transactions from all combined wallets and shown on the home screen.
 
## 5. dBnk Menu
---- The dBnk Menu will appear in the top left of the screen using a hamburger-style icon. The menu slides in from the left and will cover half of the screen, blurring the other half. 
---- At the top, an dBnk Gravatar will be used (more about the gravatar process in section #13), along with the dBnk username, as well as the user's region or country (chosen at startup).
---- The following menu items will be displayed:
```
====
- dBnk Home
- Accounts
-- Create Account 
---- Business 
---- Personal
---- Savings
-- View Accounts
- Send Money 
- Addon Store
- Addons 
--- dAix (coming soon)
--- dExg (coming soon) 
--- dPay (coming soon)
--- aCharts (coming soon)
--- dPay (coming soon)
--- {Inserts other installed add-ons automatically}
- Settings
====
```
---- The menu will use dropdowns that use an accordion-style animation, to save space and keep things modern and minimal.

## 6. Create Account (Business, Savings & Personal)
---- User is asked to enter a label for the account.
---- User is asked to choose ```account_type``` and ```blockchain_type``` ***(Bitcoin, Ethereum, EOS, BitShares, etc)***.
---- Query for the ```master_pubkey```  for this particular ```dbnk_user``` and ```blockchain_type``` is made to the ```accounts``` **SCOPE** of the ```dbnk``` contract.
---- Once the ```master_pubkey``` for the ```blockchain_type``` is recovered, the ```master_pubkey``` will be used to derive the child public key ```(m/0)``` if it's the first child and ```(m/1)```  if it's the second child and so on. The child's private keys are not stored or needed, considering dBnk's automated recovery process will be able to derive it from the ```CoinPass```, ```CoinPin``` and the decrypted mnemonic file. 
---- Now that the ```child_public_key``` is generated, the ```dbnk_user```, ```blockchain_type```, ```created_time```, ```account_type```, ```key_type ``` ***(child)***, ```account_identifier``` ***(child public key)*** and the ```account_label``` are stored with in the ```accounts``` **SCOPE** of the ```dbnk``` contract.
---- The account is created and BitShares stored on the [Arisen network](https://github.com/arisenio/arisen).

### 6.1 Data Stored On-Chain During Account Creation Process:
```dbnk_user``` ***(string)*** - dBnk user creating the account
```blockchain_type``` ***(string)*** -  The blockchain type being used to create the account
```created_time``` ***(integer)*** - The time the dBnk account was created
```account_type``` ***(string)*** - Business, Personal or Savings.
``` key_type``` ***(string)*** - Master or Child key.
```account_identifier``` ***(string)*** - Public Key related to the account.
```account_label``` ***(string)*** - The label for the account.

## 7. View Accounts
---- A popup is presented to the user to choose whether they want to view business, savings or personal accounts (```account_type```).
---- A logo-based filter of ```blockchain_type``` logos are presented to the user to choose which ```blockchain_type```-based accounts to show.
---- Query via Arisen Network's [dbnk contract](https://github.com/dbnks/dbnk-contract) of **ALL** accounts for ```dbnk_user``` are filtered for ```account_type``` and ```blockchain_type``` and displayed to the user.
---- For each account on the screen, the ```account_identifier``` is shown, the logo for the ```blockchain_type```, the ```account_label``` as well as the government-backed currency-based balance, which will be pulled from CoinMarketCap's API. 
---- When clicking on account, user is taken to the "View Account" view, which allows a user to view individual accounts. It's important to note that this shouldn't be confused with the current view known as "View Accounts", which shows a snapshot of all accounts, based on the initial filtering of the user.

## 8. View Account
---- Top section shows balance of account ***(same design as the home screen but only the balance of the individual account, rather than a combined balance)***
---- Bottom section shows all account-related transactions. The last ten transactions are shown by default, although, a "show more" button will allow another ten to be shown. 
---- Each transaction shows the ```transactionID```, ```the amount```, ```the date``` and an ***optional*** ```memo```
---- Each transaction can be clicked, which will ultimately take the user to the ```View Transaction``` View.
---- At the bottom, there are two buttons, each spanning 50% of the screen's width. One says "send" and the other says "receive". 
---- The ```send button``` sends the user to the ```Send View```, where the ```account_identifier``` and ```blockchain_type``` are transferred to the ```Send View```. 
---- The ```receive button```, takes the user to the ```Receive View```, where the ```account_identifier``` and ```blockchain_type``` are transferred to the receive view.

## 9. Send
---- The ```Send View``` is either populated by the account page the user was sent from or by the user themselves, if they go straight to the ```Send View```.
---- A dropdown menu showing each ```blockchain_type``` is shown first. Once a ```blockchain_type``` has been selected, another dropdown is shown, showing each ```account_label``` that is available for the ```dbnk_user``` and the previously chosen ```blockchain_type```. 
---- An amount is now shown, to match the correct standards & ticker symbol of the associated ```blockchain_type```. 
---- A dropdown is presented showing the user of choosing whether they want to send to an ```dbnk_user``` or an outside user. 
---- If an ```dbnk_user``` is entered, a query is performed automatically to retrieve all accounts related to that ```dbnk_user``` of that ```blockchain_type```. ```dbnk_user```-ready QR codes can also be generated and/or scanned.
---- A dropdown of the receiving ```dbnk_user``` accounts, displayed by their ```account_label```. 
---- Users can then choose the ```account_label``` they want to send to, enter the amount and send the transaction.
---- Transaction fee should be CORRECTLY calculated for each transaction, based on the fee algorithm for the particular ```blockchain_type```'s currency, that a transaction fee is being calculated for.
**NOTE:** ***Regular QR codes can be scanned for outside addresses.***

## 10. Receive 
---- The ```Receive View``` is always populated by the account page it came from.
---- A QR code is generated for other dBnk users to use when sending to other dBnk users.
---- A custom QR code can be created based around a custom amount

## 11. View Transaction 
---- This view is always populated by the previous view. Whether it's the transactions shown on the ```dBnk Home View``` or the transactions shown on an individual account page. The data from the transactions clicked, populates this view.
---- Using the ```account_identifier``` and ```blockchain_type``` columns ```full transaction``` and ```raw transaction``` data can be retrieved from various blockchains using [dbnkjs](https://github.com/dbnks/dbnkjs)'s MultiChain API.

## 12. Settings
---- {Automatically inherits addon settings}
---- User has the option to select region/country
---- User has the option to select specific government-backed currency (used for CoinMarketCap-based API valuations)
---- User **CANNOT CHANGE** ```CoinPin``` or ```CoinPass``` because seed that derived from all ```blockchain_type```-based deterministic wallets were generated from it (both masters and their children).
---- Gives user the ability to save an enlarged version of their Gravatar
---- Has a "logout" button
---- Shows current dBnk Version

## Important
See LICENSE for copyright and license terms. Peeps makes its contribution on a voluntary basis as a member of the Arisen and dBnk communities and is not responsible for ensuring the overall performance of the software or any related applications. We make no representation, warranty, guarantee or undertaking in respect of the software or any related documentation, whether expressed or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose and noninfringement. In no event shall we be liable for any claim, damages or other liability, whether in an act ion of contract, tort or otherwise, arising from, out of or in connection with the software or documentation or the user or other dealings in the software or documentation. Any test results or performance figures are indicative and will not reflect performance under all conditions. Any reference to any third party or third-party product, service or other resource is not an endorsement  or recommendation by Peeps. We are not responsible, and disclaim any and all responsibility and liability, for your user of or reliance on any of these resources. Third-party resources may be updated, changed or terminated at any time, so the information here may be out of date or inaccurate. Any person using or offering this software in connect ion with providing software, goods or services to third parties shall advise such third parties of these license terms, disclaimers and exclusions of liability. Peeps, Peeps Labs, dBnk, Arisen and associated logos are copyright of Peeps. 

Wallets and related components are complex software that requires the highest levels of security. If incorrectly built or used, they may compromise users' private keys and digital assets. Wallet applications and related components should undergo thorough security evaluations before being used. Only experienced developers should work with this software.
