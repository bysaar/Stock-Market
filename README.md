# Stock-Market

This is the final project of the course: "Computer Networks Design Laboratory".

## Introduction:

The idea behind this project is to implement a communication protocol that enables a digital stock-market platform.
In order to do so, it should support an instant communication channel for conducting instantaneous operations (eg. Buy/Sell) and in addition, a second communication channel for market database updates (eg. current prices, availability, etc).

The separation of the data streams over two different channels is necessary because of the protocol's scalability.
While the first data stream should be unique, the second data stream is generic and the same for every user, therefore in order to achieve more scalability the second data stream should be transmitted as multicast.


## Overview
The protocol resembles a real-time functioning stock market in the client-server model.

### Type Of Messages:
The protocol includes 4 different sections of messages which are as follows:

| Code | Message Description | Section | Direction
| :----: | :----: | :----: | :----: |
| **R** | Registration Request | Authentication | Client --> Server |
| **L** | Login Attempt | Authentication | Client --> Server |
| **X** | Auth. Failed | Authentication | Client <-- Server |
| **A** | Auth. Acknowledgment | Authentication | Client <-- Server |
| **F** | Server is Full | Authentication | Client <-- Server |
| **G** | Get Account Details | Display | Client --> Server |
| **Y** | Yes - Successful 'Get' Reply | Display | Client --> Server |
| **N** | No - Unsuccessful 'Get' Reply | Display | Client --> Server |
| **M** | Multicast Join Request | Updates | Client --> Server |
| **m** | Multicast Exit Notify | Updates | Client --> Server |
| **B** | Buy Offer Request | Transaction | Client --> Server |
| **S** | Sell Offer Request | Transaction | Client --> Server |
| **V** | Valid Offer | Transaction | Client <-- Server |
| **I** | Invalid Offer | Transaction | Client <-- Server |

### Design:
- Server-Side State Machine:
<img width="573" alt="צילום מסך 2022-09-02 ב-4 20 25" src="https://user-images.githubusercontent.com/90688449/188038884-a79b8fc0-62cd-4494-979e-35c4fcfb9afb.png">

- Client-Side State Machine:
<img width="648" alt="צילום מסך 2022-09-02 ב-4 22 05" src="https://user-images.githubusercontent.com/90688449/188038980-8fe84ce2-b854-4cc5-a416-f42aa55e956e.png">


## Functionality

### Authentication: (Over TCP)
Each client has his own private investment portfolio that can be accessed only by him using a unique username and password.

Authentication is divided into two steps:
#### Step 1 - Registration:
A new client sends a 'Registration Request' message that contains: username, password and amount of money to assign to the investment portfolio.

An 'Auth. Acknowledgment' message is sent if the username is free to use and the account created successfully, otherwise 'Auth. Failed' message is sent.

#### Step 2 - Login:
After registration, a client may connect to his account using the username and password he has chosen.

After examining the data an 'Auth. Ack/Failed' or 'Server is Full' message is sent back to the client, if successful (Auth. Ack) the client will receive the server's multicast IP address and will be logged in.


### Display: (Over TCP)
Enables proactive update requests and displays updated account details

A 'Get Account Details' message is sent from a client to the server, which sends back a full report on the client's current state including his balance, stock shares, and current profit.

The client will return a 'Yes' message for a successful 'Get Account Details' reply and a 'No' message otherwise.

### Updates: (Multicast over UDP, State change is done over TCP)
New up-to-date offers are sent periodically to all connected clients.

Each update is padded with a unique sequence number to prevent misordering of updates.

A 'Multicast Join Request' message is sent from a client to the server, indicating he is currently observing the multicast updates, when the client wishes to exit this state a 'Multicast Exit Notify' message is sent back to the server.

### Transactions: (Over TCP)
Enables the user to put an offer on the market.


A 'Buy/Sell Offer Request' message is sent to the server including the stock details: number, amount of units, and price per unit.

The server will check if the offer is valid and will reply with 'Valid Offer' message or 'Invalid Offer' message accordingly.

If the offer is valid it is added to the Bids/Asks array.

## Networking
- TCP - A reliable TCP connection is implemented with every client entering the server independently.

> The maximum number of clients is set to 50.

- Multicast - Each client connected to the server joins the multicast group and listens for incoming reports.
> Multicast IP address: 239.0.01

- All sockets are handled with care, clients connecting will be assigned a socket, and once left the socket will be closed.

- Each undefined message received by the client or server is handled by disconnecting the troublesome client or leaving the server depending on the receiving end.

- When an old update is received in the multicast socket (the old sequence number of update), it is discarded by the clients.

- Clients are not allowed to be idle for more than 45 seconds disregarding the case of observing upcoming updates, if so, a client will be kicked out.

- Both cases of a server or a client shutting down unexpectedly are handled.

- Our implementation used Select() function and multi-threads.


## Run Instructions
  ....
  ...
  ..
  .

## Screenshots

> An example of registration and login to the server:
<img width="600" alt="image" src="https://user-images.githubusercontent.com/90688449/188040595-a82b8b85-22c8-4959-9de2-55b4f2a5b615.png">



> Captured 'Registration Request' and 'Login Attempt' messages on Wireshark:
<img width="900" alt="image" src="https://user-images.githubusercontent.com/90688449/188040668-d898f1df-405a-4ce4-a727-88d4242105c8.png">
<img width="900" alt="image" src="https://user-images.githubusercontent.com/90688449/188040719-ad175528-f447-4ff5-a162-1111f46ae1a1.png">



> Investment portfolio example:
<img width="600" alt="image" src="https://user-images.githubusercontent.com/90688449/188040893-b77f50a6-eeb1-44be-972d-5bcaae18a5a0.png">



> Multicast offers update example:
<img width="600" alt="image" src="https://user-images.githubusercontent.com/90688449/188040984-786b25f3-50b6-4b13-aa0f-b157067da13b.png">



> Captured multicast offers update message on Wireshark:
<img width="900" alt="image" src="https://user-images.githubusercontent.com/90688449/188041085-c82080c5-53ac-42b5-a745-e1a6040fbcff.png">




> An example of offering a transaction:
<img width="600" alt="image" src="https://user-images.githubusercontent.com/90688449/188041191-fc265470-6f26-45c2-bf67-10c1dfdcaf45.png">








