## 后端存储字段定义
### 进货数据
```json
[
    {
        "company_id": 3,
        "order_id": 950,
        "tabno": "abc123",
        "client": "client1",
        "acc_time": 1257894000,
        "items": [
            {
                "spec_id": 1111,
                "price": 100,
                "how": 50,
                "money": 5000
            }
            ...
        ]
    }
    ...
]
```

> company_id - 分公司ID
order_id - 进货单ID
tabno - 进货单号
spec_id - 商品ID
client - 客户
acc_time - 记账时间
how - 数量
price - 单价
money - 金额

### 销售数据
```json
[
    {
        "company_id": 3,
        "order_id": 6495,
        "tabno": "abc123",
        "client": "client1",
        "acc_time": 1530438054,
        "items": [
            {
                "spec_id": 1111,
                "how": 10,
                "money": 1234
            }
            ...
        ]
    }
    ...
]
```

> company_id - 分公司ID
order_id - 销售单ID
tabno - 销售单号
client - 客户
send_time - 发起时间
out_time - 出库时间
acc_time - 记账时间
spec_id - 商品ID
items - 销售明细
how - 数量
money - 金额


## 部署
### 启动网络
> cd fabric-samples/chaincode-docker-devmode
sudo docker-compose -f docker-compose-simple.yaml up

## 接口测试
### Purchase
#### 启动chaincode
> docker exec -it chaincode bash
CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc2:0 ./purchase

#### 安装chaincode
> docker exec -it cli bash
peer chaincode install -p chaincodedev/chaincode/tyrechain/purchase -n mycc2 -v 0

#### Cmd
- init
> peer chaincode instantiate -n mycc2 -v 0 -c '{"Args":[]}' -C myc

- create
> peer chaincode invoke -n mycc2 -c '{"Args":["create", "10", "a1", "client1", "1257894000", "[{\"spec_id\": 1111, \"price\": 100, \"how\": 50, \"money\": 5000}, {\"spec_id\": 2222, \"price\": 10, \"how\": 50, \"money\": 500}, {\"spec_id\": 3333, \"price\": 300, \"how\": 50, \"money\": 15000}]"]}' -C myc

- query
> peer chaincode invoke -n mycc2 -c '{"Args":["query", "10"]}' -C myc 
peer chaincode query -n mycc2 -c '{"Args":["query", "9"]}' -C myc 

- getHistory
> peer chaincode invoke -n mycc2 -c '{"Args":["getHistory", "9"]}' -C myc

- delete
> peer chaincode invoke -n mycc2 -c '{"Args":["delete", "9"]}' -C myc

### Sell
#### 启动chaincode
> docker exec -it chaincode bash
CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc3:0 ./sell

#### 安装chaincode
> docker exec -it cli bash
peer chaincode install -p chaincodedev/chaincode/tyrechain/sell -n mycc3 -v 0

#### Cmd
- init
> peer chaincode instantiate -n mycc3 -v 0 -c '{"Args":[]}' -C myc

- create
> peer chaincode invoke -n mycc3 -c '{"Args":["create", "10", "a1", "kind1", "1",  "client1", "1257894000", "1257894001", "12578940002", "[{\"spec_id\": 1111, \"price\": 100, \"f_how\": 50, \"money\": 5000, \"discount\": 5}, {\"spec_id\": 2222, \"price\": 200, \"f_how\": 50, \"money\": 10000, \"discount\": 10}]"]}' -C myc

- modifyClient
> peer chaincode invoke -n mycc3 -c '{"Args":["modifyClient", "10", "client2"]}' -C myc

- query
> peer chaincode invoke -n mycc3 -c '{"Args":["query", "10"]}' -C myc 
peer chaincode query -n mycc3 -c '{"Args":["query", "9"]}' -C myc 

- getHistory
> peer chaincode invoke -n mycc3 -c '{"Args":["getHistory", "10"]}' -C myc

- delete
> peer chaincode invoke -n mycc3 -c '{"Args":["delete", "10"]}' -C myc


#### Rest API
##### Register and enroll new users in Organization - Org1
```bash
curl -s -X POST http://localhost:4000/users -H "content-type: application/x-www-form-urlencoded" -d 'username=Jim&orgName=Org1'
```

##### Create Channel request
```bash
curl -s -X POST \
  http://localhost:4000/channels \
  -H "authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MzQwMTIyOTksInVzZXJuYW1lIjoiSmltIiwib3JnTmFtZSI6Ik9yZzEiLCJpYXQiOjE1MzM5NzYyOTl9.1O-RXXuvz1PPngrkdU6sglxVcfyQRccBr973Hv_O43M" \
  -H "content-type: application/json" \
  -d '{
	"channelName":"mychannel",
	"channelConfigPath":"../artifacts/channel/mychannel.tx"
}'
```

##### Join Channel request
```bash
curl -s -X POST \
  http://localhost:4000/channels/mychannel/peers \
  -H "authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MzQwMTIyOTksInVzZXJuYW1lIjoiSmltIiwib3JnTmFtZSI6Ik9yZzEiLCJpYXQiOjE1MzM5NzYyOTl9.1O-RXXuvz1PPngrkdU6sglxVcfyQRccBr973Hv_O43M" \
  -H "content-type: application/json" \
  -d '{
	"peers": ["peer0.org1.example.com","peer1.org1.example.com"]
}'
```

##### Install chaincode
```bash
curl -s -X POST \
  http://localhost:4000/chaincodes \
  -H "authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MzQwMTIyOTksInVzZXJuYW1lIjoiSmltIiwib3JnTmFtZSI6Ik9yZzEiLCJpYXQiOjE1MzM5NzYyOTl9.1O-RXXuvz1PPngrkdU6sglxVcfyQRccBr973Hv_O43M" \
  -H "content-type: application/json" \
  -d '{
	"peers": ["peer0.org1.example.com","peer1.org1.example.com"],
	"chaincodeName":"mycc",
	"chaincodePath":"github.com/chaincode/purchase",
	"chaincodeType": "golang",
	"chaincodeVersion":"v0"
}'
```

##### Instantiate chaincode
```bash
curl -s -X POST \
  http://localhost:4000/channels/mychannel/chaincodes \
  -H "authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MzQwMTIyOTksInVzZXJuYW1lIjoiSmltIiwib3JnTmFtZSI6Ik9yZzEiLCJpYXQiOjE1MzM5NzYyOTl9.1O-RXXuvz1PPngrkdU6sglxVcfyQRccBr973Hv_O43M" \
  -H "content-type: application/json" \
  -d '{
	"peers": ["peer0.org1.example.com","peer1.org1.example.com"],
	"chaincodeName":"mycc",
	"chaincodeVersion":"v0",
	"chaincodeType": "golang",
	"args":[""]
}'
```