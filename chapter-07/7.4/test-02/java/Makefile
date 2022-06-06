CLIENT_BINARY_NAME := "Client"
SERVER_BINARY_NAME := "Server"

SERVER_IP := "192.168.0.1"
SERVER_PORT := "8090"

.PHONY: build-cli
build-cli:
	javac Client.java

.PHONY: run-cli
run-cli: build-cli
	java $(CLIENT_BINARY_NAME) $(SERVER_IP) $(SERVER_PORT)

.PHONY: build-srv
build-srv:
	javac Server.java

.PHONY: run-srv
run-srv: build-srv
	java $(SERVER_BINARY_NAME) 0.0.0.0 $(SERVER_PORT)

.PHONY: clean
clean:
	rm -f *.class
