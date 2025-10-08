# Shukur_Mini_Project
Mini Chat App (Socket Programming)
import socket

# Create a socket object
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Get local machine name and bind to a port
host = '8.8.8.8'  # localhost
port = 5685
server_socket.bind((host, port))

# Listen for incoming connections
server_socket.listen(1)
print(f"Server listening on {host}:{port}...")

# Accept a connection
client_socket, addr = server_socket.accept()
print(f"Connected to {addr}")

while True:
    message = client_socket.recv(1024).decode()
    if message.lower() == 'exit':
        print("Client disconnected.")
        break
    print(f"Client: {message}")
    reply = input("You: ")
    client_socket.send(reply.encode())

client_socket.close()
server_socket.close()
