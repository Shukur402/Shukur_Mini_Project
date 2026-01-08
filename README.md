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




# client.py
import socket
import threading
import sys

SERVER_HOST = "127.0.0.1"  # Change to server IP if remote
SERVER_PORT = 5000

def receive_loop(sock):
    """Continuously receive messages from server and print them."""
    try:
        while True:
            data = sock.recv(1024)
            if not data:
                print("\n[Disconnected from server]")
                break
            print(data.decode(errors="ignore"), end="")
    except Exception as e:
        print(f"\n[Receive error] {e}")
    finally:
        try:
            sock.shutdown(socket.SHUT_RDWR)
        except Exception:
            pass
        sock.close()

def send_loop(sock):
    """Read user input and send messages to server."""
    try:
        for line in sys.stdin:
            msg = line.strip()
            if not msg:
                continue
            sock.sendall((msg + "\n").encode())
            if msg.lower() == "/quit":
                break
    except Exception as e:
        print(f"[Send error] {e}")
    finally:
        try:
            sock.shutdown(socket.SHUT_RDWR)
        except Exception:
            pass
        sock.close()

def main():
    print(f"Connecting to {SERVER_HOST}:{SERVER_PORT} ...")
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.connect((SERVER_HOST, SERVER_PORT))
    print("Connected! Type messages. Use /quit to exit.")

    # Start receiver thread
    t_recv = threading.Thread(target=receive_loop, args=(sock,), daemon=True)
    t_recv.start()

    # Run sender loop in main thread (so Ctrl+C works nicely)
    send_loop(sock)
    print("Client closed.")

if __name__ == "__main__":
    main()
