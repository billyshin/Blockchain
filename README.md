# Blockchain
Simple Blockchain written in Python using Flask.

## Necessary libraries:
  - flask
  - requests
  - postman (not required but useful)
  
## Blockchain module
  We will use localhsot 127.0.0.1 with port 5000 for testing. Sample:
  
      python blockchain.py
  
  - Mine a new block: 

        HTTP GET: http://127.0.0.1:5000/mine_block
        Body: 
            {
              "index": 2,
              "message": "Mined a block!",
              "previous_hash": "95c3115947a9bea5b965bb6230b20eb65542d07fc9b2db394f690c140f614eb0",
              "proof": 533,
              "timestamp": "2019-01-05 12:52:49.699568"
             }

  - Get all the chain in our blockchain:
  
        HTTP GET: http://127.0.0.1:5000/get_chain
        Body:
            {
              "chain": [
                  {
                    "index": 1,
                    "previous_hash": "0",
                    "proof": 1,
                    "timestamp": "2019-01-05 12:52:46.445953"
                  },
                  {
                    "index": 2,
                    "previous_hash": "95c3115947a9bea5b965bb6230b20eb65542d07fc9b2db394f690c140f614eb0",
                    "proof": 533,
                    "timestamp": "2019-01-05 12:52:49.699568"
                  }
              ],
              "length": 2
            }
        
  - Check if the blockchain is valid:

        HTTP GET: http://127.0.0.1:5000/is_valid
        Body:
            {
              "message": "The Blockchain is valid."
            }
  
## Cryptocurrency:
We built a cryptocurrency, Shincoin, based on the blockchain module we just implemented.

Important: All the node in the decentralized blockchain must have the same block, i.e. in our case, we the same previous_hash, proof, and timestamp (after applying consensus).

 We are going to use three nodes to show the demo of decentralized blockchain:
 
    python Shincoin_node_1.py
    python Shincoin_node_2.py
    python Shincoin_node_3.py
 
 ### Get the chain to see if the genesis block was well created
 
 Me (Port 5001):
 
    HTTP GET: http://127.0.0.1:5001/get_chain
    Body:
        {
          "chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:29.418209",
                "transactions": []
              }
          ],
          "length": 1
        }

 Billy (Port 5002):
 
    HTTP GET: http://127.0.0.1:5002/get_chain
    Body:
        {
          "chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:31.503097",
                "transactions": []
              }
          ],
          "length": 1
        }
        
 You (Port 5003):
 
    HTTP GET: http://127.0.0.1:5003/get_chain
    Body:
        {
          "chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:33.941248",
                "transactions": []
              }
          ],
          "length": 1
        }
        
### Use post request to connect the nodes to each other

Me (Port 5001):

    HTTP POST: http://127.0.0.1:5001/connect_node
    Body:
        {
          "nodes": ["http://127.0.0.1:5002",
                    "http://127.0.0.1:5003"]
        }
    
    Result Body:
        {
          "message": "All the nodes are now connected. The Shincoin Blockchain now contains the following nodes:",
          "total_nodes": [
              "127.0.0.1:5003",
              "127.0.0.1:5002"
          ]
        }

Billy (Port 5002):

    HTTP POST: http://127.0.0.1:5002/connect_node
    Body:
        {
          "nodes": ["http://127.0.0.1:5001",
                    "http://127.0.0.1:5003"]
        }
    
    Result Body:
        {
          "message": "All the nodes are now connected. The Shincoin Blockchain now contains the following nodes:",
          "total_nodes": [
              "127.0.0.1:5001",
              "127.0.0.1:5003"
          ]
        }
        
You (Port 5003):

    HTTP POST: http://127.0.0.1:5003/connect_node
    Body:
        {
          "nodes": ["http://127.0.0.1:5001",
                    "http://127.0.0.1:5002"]
        }
    
    Result Body:
        {
          "message": "All the nodes are now connected. The Shincoin Blockchain now contains the following nodes:",
          "total_nodes": [
              "127.0.0.1:5002",
              "127.0.0.1:5001"
          ]
        }        
        
Then we get the fully connected nodes.

### Test Consensus (what happens if on one node we get a chain that is larger than the chain on the other node?)

Me (port 5001):

    HTTP GET:  http://127.0.0.1:5001/mine_block
    Body:
        {
          "index": 2,
          "message": "You just mined a block successfully!",
          "previous_hash": "276b6a1a6f2e275d6c01b5eacda6b1a6b4afdc6fd147afd4405f385057cc43f0",
          "proof": 533,
          "timestamp": "2019-01-05 13:57:32.016718",
          "transactions": [
              {
                "amount": 1,
                "receiver": "Me",
                "sender": "395d25986fa844ff8152ce448d552b86"
              }
          ]
        }
    
Since I am the miner in this case, my node receives the coin from the node whose addrss is the value of sender.

    HTTP GET: http://127.0.0.1:5001/get_chain
    Body:
        {
          "chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:29.418209",
                "transactions": []
              },
              {
                "index": 2,
                "previous_hash": "276b6a1a6f2e275d6c01b5eacda6b1a6b4afdc6fd147afd4405f385057cc43f0",
                "proof": 533,
                "timestamp": "2019-01-05 13:57:32.016718",
                "transactions": [
                    {
                      "amount": 1,
                      "receiver": "Me",
                      "sender": "395d25986fa844ff8152ce448d552b86"
                    }
                ]
              }
          ],
          "length": 2
        }
        
Billy (port 5002):

    HTTP GET: http://127.0.0.1:5002/get_chain
    Body:
        {
          "chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:31.503097",
                "transactions": []
              }
          ],
          "length": 1
        }

Since the genesis block is not consistent, we need to use replace_block

    HTTP GET: http://127.0.0.1:5002/replace_chain
    Body:
        {
          "message": "The nodes had different chains so the chain was replaced by the longest one.",
          "new_chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:29.418209",
                "transactions": []
              },
              {
                "index": 2,
                "previous_hash": "276b6a1a6f2e275d6c01b5eacda6b1a6b4afdc6fd147afd4405f385057cc43f0",
                "proof": 533,
                "timestamp": "2019-01-05 13:57:32.016718",
                "transactions": [
                    {
                        "amount": 1,
                        "receiver": "Me",
                        "sender": "395d25986fa844ff8152ce448d552b86"
                    }
                ]
              }
          ]
        }

You (port 5003):

Do the same thing as Billy

     HTTP GET: http://127.0.0.1:5003/replace_chain
    
### Transaction

Me (port 5001):

    HTTP POST: http://127.0.0.1:5001/add_transaction
    Body:
        {
          "sender": "Me",
          "receiver": "Billy",
          "amount": 10000
         }
         
     Result Body:
         {
            "message": "This transaction will be added to Block 3"
          }
          
This transaction is not yet added to the block, so we need to mine a block first to welcome this transaction and add it to the blockchain.

    HTTP GET:  http://127.0.0.1:5001/mine_block
    Body:
        {
          "index": 3,
          "message": "You just mined a block successfully!",
          "previous_hash": "661327f55aaaddce6964d12c57840647753231ded3e8da0483e76d73d475c9d5",
          "proof": 45293,
          "timestamp": "2019-01-05 14:09:04.602729",
          "transactions": [
              {
                "amount": 10000,
                "receiver": "Billy",
                "sender": "Me"
              },
              {
                "amount": 1,
                "receiver": "Me",
                "sender": "395d25986fa844ff8152ce448d552b86"
              }
          ]
        }
        
    HTTP GET: http://127.0.0.1:5001/get_chain
    Body:
        {
          "chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:29.418209",
                "transactions": []
              },
              {
                "index": 2,
                "previous_hash": "276b6a1a6f2e275d6c01b5eacda6b1a6b4afdc6fd147afd4405f385057cc43f0",
                "proof": 533,
                "timestamp": "2019-01-05 13:57:32.016718",
                "transactions": [
                    {
                      "amount": 1,
                      "receiver": "Me",
                      "sender": "395d25986fa844ff8152ce448d552b86"
                    }
                ]
              },
              {
                "index": 3,
                "previous_hash": "661327f55aaaddce6964d12c57840647753231ded3e8da0483e76d73d475c9d5",
                "proof": 45293,
                "timestamp": "2019-01-05 14:09:04.602729",
                "transactions": [
                    {
                      "amount": 10000,
                      "receiver": "Billy",
                      "sender": "Me"
                    },
                    {
                      "amount": 1,
                      "receiver": "Me",
                      "sender": "395d25986fa844ff8152ce448d552b86"
                    }
                ]
              }
          ],
          "length": 3
        }
        
Billy (port 5002):

    HTTP GET: http://127.0.0.1:5002/replace_chain
    Body:
        {
          "message": "The nodes had different chains so the chain was replaced by the longest one.",
          "new_chain": [
              {
                "index": 1,
                "previous_hash": "0",
                "proof": 1,
                "timestamp": "2019-01-05 13:51:29.418209",
                "transactions": []
              },
              {
                "index": 2,
                "previous_hash": "276b6a1a6f2e275d6c01b5eacda6b1a6b4afdc6fd147afd4405f385057cc43f0",
                "proof": 533,
                "timestamp": "2019-01-05 13:57:32.016718",
                "transactions": [
                    {
                      "amount": 1,
                      "receiver": "Me",
                      "sender": "395d25986fa844ff8152ce448d552b86"
                    }
                ]
              },
              {
                "index": 3,
                "previous_hash": "661327f55aaaddce6964d12c57840647753231ded3e8da0483e76d73d475c9d5",
                "proof": 45293,
                "timestamp": "2019-01-05 14:09:04.602729",
                "transactions": [
                    {
                      "amount": 10000,
                      "receiver": "Billy",
                      "sender": "Me"
                    },
                    {
                      "amount": 1,
                      "receiver": "Me",
                      "sender": "395d25986fa844ff8152ce448d552b86"
                    }
                ]
              }
          ]
        }

You (port 5003):
Do the same thing as Billy

     HTTP GET: http://127.0.0.1:5003/replace_chain
     
Our transaction is done successfully.


## TODO:
  - Create contract
