---
layout: post
title: Socket Programming in Python
---

## Introduction

I took a look into python sockets and created a UDP calculator, and a TCP document statistic reporting tool. Collated below are my breif notes.
For better understanding i have included a table for the use of TCP vs UDP.

-----

## UDP Calculator

When we run the UDP Server the socket is started on port 8888 of our localhost, and it just waits for a client to send data. 
1. The client will send the first integer followed by the operator, followed by the second integer.
2. The server receives and reads those messages, calculates the sum based off the operator and will then send the answer back to the client. 
3. The client decodes and outputs the sum to the terminal. 

The working scenario can be seen in the screenshots below.

![placeholder](https://i.imgur.com/QyAS0om.png "UDP Client")

![placeholder](https://i.imgur.com/GVPoWZV.png "UDP Server")

UDP Client Code:

```python
import socket                                                  
                                                               
#Defines the server ip and port to connect to.                 
                                                               
serverName = 'Localhost'                                       
serverport = 8888                                              
clientSocket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
                                                               
print ('====================================')                      
print ('Welcome to Ironclads UDP Calculator!')                      
print ('====================================')                      
print ('\n')                                                   
                                                               
                                                               
number1 = input ('Input the first integer: ')                  
operator = input ('Input an operator (+ / * -) ')              
number2 = input ('Input the second integer: ')                 
                                                               
#Sends the integers and operator to the calculator server.     
                                                               
clientSocket.sendto(number1.encode(),(serverName,serverport))  
clientSocket.sendto(operator.encode(),(serverName,serverport)) 
clientSocket.sendto(number2.encode(),(serverName,serverport))  
                                                               
answer,serverAddress = clientSocket.recvfrom(2048)             
print ('The answer is: ', answer.decode())                     
                                                               
input()
clientSocket.close()
```

UDP Server Code:

```python
import socket

serverName = 'Localhost'
serverPort = 8888
serverSocket = socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
serverSocket.bind(('localhost',8888))

print ('The calculator is waiting...')
while 1:

#This is my text output to the server so i can see what is happening.

    number1, clientAddress = serverSocket.recvfrom(2048)
    print ('The user entered the number ', number1)
    
    operator, clientAddress = serverSocket.recvfrom(2048)
    print ('received operator', operator)

    number2, clientAddress = serverSocket.recvfrom(2048)
    print ('The user entered the number ', number2)
    
    print ('Answer sent to user ')
    operator = operator.decode()
#This is my calculations based off of the user input for the operators.
    if str(operator) == '+':
        answer = int(number1.decode()) + int(number2.decode())
        answer = str(answer)
        serverSocket.sendto(answer.encode(),clientAddress)

    elif str(operator) == '-':
        answer = int(number1.decode()) - int(number2.decode())
        answer = str(answer)
        serverSocket.sendto(answer.encode(),clientAddress)

    elif str(operator) == '/':
        answer = int(number1.decode()) / int(number2.decode())
        answer = str(answer)
        serverSocket.sendto(answer.encode(),clientAddress)

    elif str(operator) == '*':
        answer = int(number1.decode()) * int(number2.decode())
        answer = str(answer)
        serverSocket.sendto(answer.encode(),clientAddress)

serverSocket.close()
```

## TCP Document Statistic Reporting Tool

When the TCP Server is run, it opens on port 12546 on our localhost machine.
1. Three-way handshake occurs (SYN, SYN/ACK, ACK)
2. The client then encodes and sends the contents of our lazydog.txt file.
3. The server receives that data, decodes it, and counts the words and the characters within the string.
4. The server then encodes that and sends it back to the client.
5. The client receives that data, decodes it, and displays it through the terminal.

The working scenario can be seen in the screenshots below.

![placeholder](https://i.imgur.com/sfbla4K.png "TCP Client")

![placeholder](https://i.imgur.com/FtmotnT.png "TCP Server")

TCP Client Code:

```python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
host = 'localhost'
port = 12546
s.connect((host, port)) #Initialises the connection

msg = s.recv(1024)
print(msg.decode("utf-8"))

print("Uploading File...\n")
files = open("lazydog.txt", "r")
#After opening the file, the contents are sent from here
sendData = files.read(1024)
s.send(bytes(sendData, "utf-8"))
print("File Uploaded!\n\nDocument Statistics Received, Opening...\n")

print("===================\nDocument Statistics\n===================\n")
charmsg = s.recv(1024)
print("No. of Characters: ", charmsg.decode("utf-8"))
wordmsg = s.recv(1024)
print("No. of Words: ", wordmsg.decode("utf-8"))
input()
s.close()
```

TCP Server Code:

```python
import socket
import time

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
host = 'localhost'
port = 12546
s.bind((host, port))
s.listen(5)
print("Server is listening...")

while True:
    #Accepts the conneciton from Client
    clientsocket, address = s.accept()

    #Sends a welcome message
    print("Connection from", address, "has been established!\n")
    time.sleep(.500)
    clientsocket.send(bytes("Welcome to the server\n", "utf-8"))

    print ("File Received, Opening...\n")
    #Receives message from client
    recvData = clientsocket.recv(1024)
    
    #Counts words and characters to store as seperate variables
    wordcount = len(recvData.split())
    chars = len(recvData)
    print ("Sending Document Statistics...\n")
    
    #Sends message back to client
    clientsocket.send(bytes(str(chars), "utf-8"))
    #I put a pause here because it was sending too fast
    time.sleep(.500)
    clientsocket.send(bytes(str(wordcount), "utf-8"))

s.close()
```
