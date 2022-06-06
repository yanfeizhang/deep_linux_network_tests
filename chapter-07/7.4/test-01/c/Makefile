CLIENT_BINARY_NAME := "test-client"
SERVER_BINARY_NAME := "test-server"

SERVER_IP := "192.168.0.1"
SERVER_PORT := "8090"

.PHONY: build-cli
build-cli:
	gcc client.c -o $(CLIENT_BINARY_NAME)

.PHONY: run-cli
run-cli: build-cli
	./$(CLIENT_BINARY_NAME) $(SERVER_IP) $(SERVER_PORT)

.PHONY: build-srv
build-srv:
	gcc server.c -o $(SERVER_BINARY_NAME)

.PHONY: run-srv
run-srv: build-srv
	./$(SERVER_BINARY_NAME) 0.0.0.0 $(SERVER_PORT)

.PHONY: clean
clean:
	rm -f ${CLIENT_BINARY_NAME}
	rm -f $(SERVER_BINARY_NAME)
