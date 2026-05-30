## Quick Reference for OpenCode Sessions

**Setup MySQL (local)**
- Run the exact command from the root `readme.md` to start a MySQL container:
  ```
  docker run --name my-mysql -e MYSQL_ROOT_PASSWORD=atul -e MYSQL_DATABASE=test -p 3306:3306 -d mysql:latest
  ```
- In the `couponservice/docker-compose.yml` the MySQL service uses:
  - `MYSQL_ROOT_PASSWORD=test1234`
  - `MYSQL_DATABASE=mydb`
  - Health‑check runs `mysql --user=root --password=test1234 "SHOW DATABASES"`

**Build each service**
- All services are Maven projects with a wrapper (`mvnw` / `mvnw.cmd`).
- From the service directory (`couponservice`, `productservice`, `flightservices`):
  ```
  ./mvnw clean package   # Linux/macOS
  mvnw.cmd clean package # Windows
  ```
- The built JAR lands in `target/` (e.g. `target/couponservice-0.0.1-SNAPSHOT.jar`).
- Java versions differ:
  - `couponservice` uses **Java 21** (see `<java.version>21</java.version>`).
  - `productservice` and `flightservices` use **Java 1.8**.

**Run a service locally**
- Directly:
  ```
  java -jar target/<artifact>-0.0.1-SNAPSHOT.jar
  ```
- Or via the provided Dockerfile (expects the JAR at `target/`):
  ```
  docker build -t bharatht19/<service> .
  docker run --rm -p <hostPort>:<containerPort> bharatht19/<service>
  ```

**Docker‑Compose for the coupon ecosystem**
- From `couponservice/` run:
  ```
  docker-compose up -d   # starts product‑app, coupon‑app, and mysql
  ```
- Services wait for MySQL (`WAIT_HOSTS=mysql:3306`).
- Ports:
  - `product‑app` → `10666:9090`
  - `coupon‑app` → `10555:9091`
  - MySQL → `6666:3306`
- To stop:
  ```
  docker-compose down
  ```

**Kubernetes deployment**
- Manifests live under `kubernetes/`.
- Apply all microservice resources:
  ```
  kubectl apply -f kubernetes/microservices/
  ```
- Apply the webserver resources:
  ```
  kubectl apply -f kubernetes/webserver/
  ```
- Verify pods:
  ```
  kubectl get pods -n default
  ```

**Testing**
- Unit / integration tests are Maven‑managed:
  ```
  ./mvnw test   # or mvnw.cmd test on Windows
  ```
- No special test fixtures are required beyond the MySQL container.

**Common Gotchas**
- Ensure the correct Java version is active for the service you are building.
- The Dockerfile for `couponservice` expects the JAR name `couponservice-0.0.1-SNAPSHOT.jar`; do not rename the artifact.
- When using the Docker Compose file, the MySQL password is `test1234` (different from the root password in the README).
- Services depend on MySQL; start the DB first or rely on the `depends_on`/`WAIT_HOSTS` logic.
- The `docker-compose.yml` in the repo root (`dockercomposedemo`) is unrelated to the microservices; it demonstrates a generic compose setup.
