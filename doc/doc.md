# Chaincode Dev Mode

## Run dev mode

```sh
cd ./dev-mode
```

### Requires `3` terminals:
1. `Terminal 1` : docker compose for fabric network
2. `Terminal 2` : build/run chaincode
3. `Terminal 3` : use chaincode
---
### Terminal 1
```sh
# If windows, run this first
export COMPOSE_CONVERT_WINDOWS_PATHS=1

# Create dev mode environment
docker-compose -f docker-compose-simple.yaml up
```
---
### Terminal 2
```sh
# enter chaincode execution container
docker exec -it chaincode bash

# go to chaincode dir
cd chaincode_example02/go

# build chaincode
go build -o chaincode_example02

# run chaincode
CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc:0 ./chaincode_example02
```
---
### Terminal 3
```sh
# enter CLI
docker exec -it cli bash

# install chaincode
peer chaincode install -p chaincodedev/chaincode/chaincode_example02/go -n mycc -v 0

# initialize chaincode
peer chaincode instantiate -n mycc -v 0 -c '{"Args":["init","a","100","b","200"]}' -C myc

# test: invoke
peer chaincode invoke -n mycc -c '{"Args":["invoke","a","b","10"]}' -C myc

# test: query
peer chaincode query -n mycc -c '{"Args":["query","a"]}' -C myc
```

在Terminal 3使用chaincode的時候，可在Terminal 2觀察到chaincode的console輸出

---

## 修改chaincode

在`terminal 2`重新build後，再執行一次chaincode即可

*不需要*再次install or instantiate chaincode

---

## 改變chaincode版本

在`Terminal 2`執行chaincode時，要指定正確的版本號

範例：執行`1.1`版的chaincode
```sh
CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc:1.1 ./chaincode_example02
```

在 `Terminal 3` 要重新 `install` chaincode

```sh
# install chaincode
peer chaincode install -p chaincodedev/chaincode/chaincode_example02/go -n mycc -v 1.1
```

且由於chaincode已經存在，所以要使用 `upgrade` 指令來升級chaincode的版本

```sh
# Upgrade chaincode verison
peer chaincode upgrade -n mycc -v 1.1 -c '{"Args":["init","a","100","b","200"]}' -C myc
```

>NOTE : Terminal 3 install / instantiate 使用的版本號一定要跟Terminal 2執行中的chaincode版本號一致

---

## Stop dev mode
切到`Terminal 1` 按 `Ctrl + C` 即可

---
References
- [Github chaincode dev mode sample](https://github.com/hyperledger/fabric-samples/tree/release-1.3/chaincode-docker-devmode)
- [Hyperledger document](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peer-chaincode-devmode.html)