# distributedsystems
AIM:

The objective of this project is to design a secure file system which allows distributed system nodes DSi to access the remote files stored on the remote file servers FSi in a secure manner using RPCs.

BASIC TERMS:
   
	 1. File System: A file system is a structure used to control how data is stored, accessed and manipulated. Usually, files can be accessed locally using the computer’s file system. But in distributed environments, it becomes necessary to have multiple file servers tasked with storing the files and some distributed nodes (clients) that want to access thosefiles.
	 2. RPC: A Remote Procedure Call is employed to perform subroutines whose implementation resides in some other machine. It basically emulates a scenario where the commands look like they’re running locally, whereas in reality, the internal message passing is hidden from the user.
	 3. Authentication: However, communication over a network has its downsides when it comes to confidentiality and integrity of the commands/data sent. This is why a system for secure RPC is needed. There are various protocols which can be effective in authenticating all the nodes participating in communication. One such protocol is the Needham-Schroeder protocol which is discussed in further sections.

COMPONENTS:
   
	 1. KDC: The Key Distribution Centre is responsible for
        a. Authenticating new clients by verifying if they exist using their username and passwords.
        b. Registering clients and file servers.
        c. Providing them with a unique ID as well as shared/session keys to communicate securely.
	 
	 2. Client: This is one of the distributed nodes which are interestedin accessing files from the file server. The client then registers with the KDC and then
	 proceeds to communicate with the desired file server provided that the file server is registered with the KDC.
	      a. Register- The client sends a registration request to the KDC. The KDC generates a unique ID and a shared key K(A) for communication between KDC and client, which is sent to the client.
        b. Authentication- The client is prompted to enter its username and password which are then sent to the KDC for verification. Once verified, the client can proceed with further steps.
        c. Communicate with Server- The client sends its own id and the id of the file server it wishes to communicate with, to the KDC.

   Consequently it receives the session key K(AB) required to communicate with the file server, along with its own id and session key encrypted using the shared key of the KDC and the file server K(B). This information is then sent to the file server to establish secure communication with it.
   
        d. Preventing Intrusion- With each request sent to either KDC or the file server, a random nonce is encrypted and sent along with it to ensure that whatever message is received by the client later, it should contain that random nonce or an arithmetic operation performed on the nonce so as to verify that the communication is secure and the receiver is authentic.
        e. Shell prompt to send file access/manipulation commands- Once the protocol has ensured that the server is authentic, the client is prompted with a new shell and enters a command that it wishes to perform and the file name (if required) which is then encrypted and sent to the file server for execution. The encrypted result of that command is received by the client.
	 
	 3. File Server: This is the node responsible for maintaining a logical structure of stored files and providing meaningful information whenever prompted by clients through secure RPCs. File servers first register with the KDC and once registered, they listen for connection requests from any of the clients.
        a. Register- The file server sends a registration request to the KDC. The KDC generates a unique ID and a shared key for communication between KDC and server, which is sent to the server.
        b. Communication with Client- While in the listening state, the file server receives a connection from a client along with a random nonce encrypted using session key K(AB), client id and session key K(AB), encrypted using shared key between KDC and server K(B). Once the server is able to decrypt the latter, it finally has the session key K(AB) which is then used to decrypt the random nonce and send back a confirmation of secure connection by performing an arithmetic operation on the nonce.
        c. Preventing Intrusion- With each request sent to either KDC or the client, a random nonce is encrypted and sent along with it to ensure that whatever message is received by the server later, it should contain that random nonce or an arithmetic operation performed on the nonce so as to verify that the communication is secure and the receiver is authentic.
        d. Performing file access/manipulation commands- Once the protocol has ensured that the client is authentic, the server listens for RPCs which direct it to execute a subroutine on its local filesystem and a possible filename. Once the command is executed, the result is encrypted and sent to the client.
