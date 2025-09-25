# CS454/554 Project 1 — EC2 REST Service (lbs → kg)

Overview
This project implements a simple RESTful service running on AWS EC2.  
Endpoint: `GET /convert?lbs=<number>`

The service returns JSON:
```json
{"lbs":<number>,"kg":<converted>,"formula":"kg = lbs * 0.45359237"}
```

Error codes:
- 400 if param is missing or not numeric
- 422 if value is negative or non-finite

Deployment Steps
1. Launch Amazon Linux 2 EC2 (`t3.micro`).
2. Install Node.js and Git.
3. Clone repo and install dependencies:
   ```bash
   git clone <your-repo-url> ~/p1
   cd ~/p1
   npm install
   ```
4. Copy systemd unit:
   ```bash
   sudo cp systemd/p1.service /etc/systemd/system/
   sudo systemctl daemon-reload
   sudo systemctl enable --now p1
   ```
5. Verify:
   ```bash
   sudo systemctl status p1
   ```

Test Cases
- Success:
  ```bash
  curl http://<PUBLIC_IP>:8080/convert?lbs=150
  # {"lbs":150,"kg":68.039,"formula":"kg = lbs * 0.45359237"}
  ```
- Missing:
  ```bash
  curl -i http://<PUBLIC_IP>:8080/convert
  # HTTP/1.1 400
  ```
- Negative:
  ```bash
  curl -i http://<PUBLIC_IP>:8080/convert?lbs=-5
  # HTTP/1.1 422
  ```
- Decimal:
  ```bash
  curl http://<PUBLIC_IP>:8080/convert?lbs=0.1
  # {"lbs":0.1,"kg":0.045,"formula":"kg = lbs * 0.45359237"}
  ```

Security Group
Inbound rules:
- SSH (22) → my IP only
- HTTP (80) / Custom TCP (8080) → open for testing

Cleanup
- Terminate instance in AWS Console.
- Delete Security Group & Key Pair if not reused.
