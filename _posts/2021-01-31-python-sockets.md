---
layout: post
title: Python Sockets
---

## Introduction

I took a look into python sockets and created a UDP calculator, and a TCP document statistic reporting tool. Collated below are my breif notes.

-----

## UDP Calculator

When we run the UDP Server the socket is started on port 8888 of our localhost, and it just waits for a client to send data. 
1. The client will send the first integer followed by the operator, followed by the second integer.
2. The server receives and reads those messages, calculates the sum based off the operator and will then send the answer back to the client. 
3. The client decodes and outputs the sum to the terminal. 
The working scenario can be seen in the screenshot below.

Client:

```python
import socket                                                  
                                                               
#Defines the server ip and port to connect to.                 
                                                               
serverName = 'Localhost'                                       
serverport = 8888                                              
clientSocket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
                                                               
print ('===============================')                      
print ('Welcome to Ironclads UDP Calculator!')                      
print ('===============================')                      
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

![placeholder](https://imgur.com/MHFxln6 "UDP Client")

Server:

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

![placeholder](https://imgur.com/GVPoWZV "UDP Server")
