# unit-zero-transaction

>Faucet link: UNIT0-Faucet  
>Dashboard : UNIT0

**راه اندازی محیط پایتون پروژه**
```
sudo apt update
sudo apt install python3 python3-pip
```

**ایجاد محیط مجازی و فعال سازی**
```
python3 -m venv unit0_env
source unit0_env/bin/activate
```
**نصب پکیج هایی که بهش نیاز دارید**
```
pip install web3 python-dotenv
```
**ایجاد ساختار پروژه**
```
mkdir unit0_python_automation
cd unit0_python_automation
touch .env
touch automate_transactions.py
```
**وارد فایل .env میشید و اطلاعات خودتون رو جایگزین میکنید**
```
nano .env
```
محتوی پوشه:
```
PRIVATE_KEY=your_private_key_here
RECIPIENT_ADDRESS=recipient_address_here
RPC_URL=https://rpc-testnet.unit0.dev
```
**ایجاد اسکریپت پایتون:**
```
nano automate_transactions.py
```
**کپی کد های زیر داخل فایل:**
```
import time
import random
from web3 import Web3
from dotenv import load_dotenv
import os

# Load environment variables
load_dotenv()

# Connect to Unit Zero network
w3 = Web3(Web3.HTTPProvider(os.getenv('RPC_URL')))

# Set up account
private_key = os.getenv('PRIVATE_KEY')
account = w3.eth.account.from_key(private_key)

def send_transaction(to_address, value):
    nonce = w3.eth.get_transaction_count(account.address)
    tx = {
        'nonce': nonce,
        'to': to_address,
        'value': w3.to_wei(value, 'ether'),
        'gas': 21000,
        'gasPrice': w3.eth.gas_price
    }
    signed_tx = account.sign_transaction(tx)
    tx_hash = w3.eth.send_raw_transaction(signed_tx.rawTransaction)
    return w3.eth.wait_for_transaction_receipt(tx_hash)

def automate_transactions(count, to_address, value):
    for i in range(count):
        print(f"Sending transaction {i+1} of {count}")
        try:
            receipt = send_transaction(to_address, value)
            print(f"Transaction confirmed in block {receipt['blockNumber']}")
            
            if i < count - 1:
                delay = random.uniform(10, 15)
                print(f"Waiting for {delay:.2f} seconds...")
                time.sleep(delay)
        except Exception as e:
            print(f"Error in transaction {i+1}: {str(e)}")

if __name__ == "__main__":
    recipient = os.getenv('RECIPIENT_ADDRESS')
    automate_transactions(500, recipient, 0.00001)
    print("All transactions completed")
```
**اجرای اسکریپت**
```
python automate_transactions.py
```
