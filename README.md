# Smart Contract Tutorial in Neo Python

## Install NeoPython

```bash
sudo apt-get install python3.6 python3.6-dev python3.6-venv python3-pip libleveldb-dev libssl-dev g++
```

```bash
sudo pip3 install wheel neo-python
```

```bash

sudo docker pull cityofzion/neo-privatenet

sudo docker run --rm -d --name neo-privatenet -p 20333-20336:20333-20336/tcp -p 30333-30336:30333-30336/tcp cityofzion/neo-privatenet

git clone https://github.com/CityOfZion/neo-privatenet-docker.git

cd neo-privatenet-docker

sudo ./docker_run_and_create_wallet.sh

sudo chmod u=rwx,g=rwx,o=rwx neo-privnet.wallet
```

## Create HelloWorld Smart Contract

```bash
mkdir source_code
cd source_code
```

```bash
nano helloworld.py
```

and write the following code in that file

```python
def Main():
    print("Hello World")
    return True
```


I am not sure whether the following **2** commands are needed or not in docker settings. If later commands doesn't work then run them otherwise feel free to skip them.

```bash
np-bootstrap
np-bootstrap -n
```

then run the following command

```bash
np-prompt -p
```
once neo-prompt is open run the following commands.
```bash
config sc-events on

build helloworld.py

open wallet ../neo-privnet.wallet
```
Password is **coz**. After entering the password run the following commands

```bash
import contract ./helloworld.avm '' 01 False False False 
```
Note that 
1. the **' '** indicates that it does not take any parameters. 
2. **01** indicates that it returns boolean. 
3. 1st **False** indicates that it does not needs_storage.
4. 2nd **False** indicates that it does not need to be dynamically invoked.
5. 3rd **False** indicates that this smart contract is not payable.

It will ask you your smart contract name, author name, description etc. In Smart Contract Name enter **helloworld**. It will again ask for password which is **coz**. Now, your smart contract is deployed on your private blockchain. 

You can search your smart contract using the following command

```bash
contract search helloworld
```

Your output may look something like this

```bash
Found 1 results for helloworld
{
    "version": 0,
    "code": {
        "hash": "0x09a129673c61917593cb4b57dce066688f539d15",
        "script": "54c56b0b48656c6c6f20576f726c64680f4e656f2e52756e74696d652e4c6f67516c7566",
        "parameters": "10",
        "returntype": 1
    },
    "name": "helloworld",
    "code_version": "1.5",
    "author": "Meow",
    "email": "meow",
    "description": "",
    "properties": {
        "storage": false,
        "dynamic_invoke": false,
        "payable": false
    }
}
```

You can test invoke it using the following command
```bash
testinvoke 0x09a129673c61917593cb4b57dce066688f539d15
```

Note, the argument of testinvoke is hash of our smart contract. Its output
may look something like this

```bash
Used 0.016 Gas 

-------------------------------------------------------------------------------------------------------------------------------------
Test invoke successful
Total operations: 11
Results [{'type': 'Integer', 'value': '1'}]
Invoke TX GAS cost: 0.0
Invoke TX fee: 0.0001
-------------------------------------------------------------------------------------------------------------------------------------
```



## Notes

1. In Python, we usually don't mention data type of variables. However, We need to mention the types of our arguments when we are deploying the Smart Contract. For Example, we deploy the following Smart Contract **calc.py** using the following command

```bash
import contract ./calc.avm 070202 02 False False False 
```

Note the second argument **070202**. 07 refers to type of first argument which is **string** (operation). 

First 02 refers to type of second argument of Main() which is **int** (a). 

Second 02 refers to type of third argument of Main() which is **int** (b).


Note that the third argument **02** refers to return type of function which is **int**.
```python
# filename: calc.py

def Main(operation, a, b):

    if operation == 'add':
        return a + b

    elif operation == 'sub':
        return a - b

    elif operation == 'mul':
        return a * b

    elif operation == 'div':
        return a / b

    else:
        return -1
```

2. Solidity don't have builtin support for string comparison.



## Conversion of Neo Python to Solidity

## Hello World
```python
# Hello World in Neo Python

def Main():
    print("Hello World")
    return True
```

```solidity
// Hello World in Solidity

pragma solidity >=0.4.0 <0.6.0;

contract HelloWorld {

    function get() public pure returns (bool) {
        return true;
    }
}
```

## Calculator

```python
# Calculator in Neo Python

def Main(operation, a, b):

    if operation == 'add':
        return a + b

    elif operation == 'sub':
        return a - b

    elif operation == 'mul':
        return a * b

    elif operation == 'div':
        return a / b

    else:
        return -1
```

```solidity
// Calculator in Solidity

pragma solidity >=0.4.0 <0.6.0;

library StringUtilities {
    function hashCompareWithLengthCheck(bytes memory a, bytes memory b) internal pure returns (bool) {
        if(a.length != b.length) {
            return false;
        } else {
            return keccak256(a) == keccak256(b);
        }
    }
}


contract Calc {

    function Main(string memory operation, int a, int b) public pure returns (int) {
        bytes memory op = bytes(operation);
        if (StringUtilities.hashCompareWithLengthCheck(op, "add")) {
            return a + b;
        }
        else if (StringUtilities.hashCompareWithLengthCheck(op, "subtract")) {
            return a - b;
        }
        else if (StringUtilities.hashCompareWithLengthCheck(op, "multiply")) {
            return a * b;
        }
        else if (StringUtilities.hashCompareWithLengthCheck(op, "divide")) {
            return a / b;
        }
        return -1;
    }
}
```
