SERVER_IP := "192.168.0.1"
SERVER_PORT := "8090"

.PHONY: run-cli
run-cli: 
	php client.php ${SERVER_IP} ${SERVER_PORT} 

.PHONY: run-srv
run-srv: 
	php server.php 0.0.0.0 ${SERVER_PORT}
