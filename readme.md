## run mysql

docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=atul -e MYSQL_DATABASE=test -p 3306:3306 -d mysql:latest

