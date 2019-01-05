# Blockchain
Simple Blockchain written in Python using Flask.

## Necessary libraries:
  - flask
  - requests
  - postman (not required but useful)
  
## Blockchain module
  We will use localhsot 127.0.0.1 with port 5000 for testing. Sample:
  
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
  

## TODO:
  - Create cryptocurrency using blockchain
  - Create contract
