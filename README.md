# Configurações e scripts diversos para execução da API desenvolvida durante a compweek

## Pré-requisitos

- JDK 11 ou superior
- Maven 3.6+ ou superior
- Docker

## Extensões do Quarkus

As seguintes extensões foram adicionadas ao projeto:

- `quarkus-hibernate-orm-panache`: Facilita o uso de Hibernate ORM com a API Panache.
- `quarkus-jdbc-h2`: Suporte ao banco de dados H2.
- `quarkus-resteasy-jackson`: Suporte para criação de APIs REST com JSON.
- `quarkus-smallrye-openapi`: Suporte à especificação OpenAPI para documentação de APIs.

## Configuração do Banco de Dados H2

O projeto está configurado para usar o banco de dados H2 em modo de arquivo, com as seguintes configurações no arquivo `application.properties`:

```properties
# Configuração do DataSource
quarkus.datasource.db-kind=h2
quarkus.datasource.username=sa
quarkus.datasource.password=sa
quarkus.datasource.jdbc.url=jdbc:h2:file:./data/h2db/test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
#quarkus.datasource.jdbc.url=jdbc:h2:/tmp/data/h2db/test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
quarkus.datasource.jdbc.max-size=10
quarkus.hibernate-orm.database.generation=update
quarkus.hibernate-orm.dialect=org.hibernate.dialect.H2Dialect
```

Habilitar OpenApi em PRD:

```
quarkus.swagger-ui.enable=true
quarkus.swagger-ui.always-include=true
```

## Scripts relacionados ao Kubernetes

Dependências:

```
- quarkus-smallrye-health
- quarkus-container-image-jib, quarkus-kubernetes
```

Configuração do Kubernetes:

```
quarkus.container-image.registry=docker.io
quarkus.container-image.group=REPO PUBLICO NO DOCKERHUB
quarkus.container-image.name=produto-api
quarkus.container-image.tag=1.0.0-SNAPSHOT
quarkus.kubernetes.service-type=NodePort
quarkus.kubernetes-client.trust-certs=true
```

Verificar Pods (realizar login antes, copy command):

```
kubectl get pods
```

Subir imagem no DockerHub 

```
mvn clean package -P kubernetes
```

Verificar PORT:

```
oc get svc produto-api
```

Request POST via terminal:

```
curl -X POST http://IP:PORT/api/produto \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Caneta",
    "descricao": "Bic Azul",
    "preco": 70.0
  }'
```

Request GET via terminal:

```
curl http://IP:PORT/api/produtos
```

Gerar Runner:

```
quarkus.package.type=uber-jar
```

pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>br.com.fatec</groupId>
    <artifactId>produto-api</artifactId>
    <version>1.0.0-SNAPSHOT</version>

    <properties>
        <compiler-plugin.version>3.13.0</compiler-plugin.version>
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <quarkus.platform.artifact-id>quarkus-bom</quarkus.platform.artifact-id>
        <quarkus.platform.group-id>io.quarkus.platform</quarkus.platform.group-id>
        <quarkus.platform.version>3.16.2</quarkus.platform.version>
        <skipITs>true</skipITs>
        <surefire-plugin.version>3.5.0</surefire-plugin.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>${quarkus.platform.group-id}</groupId>
                <artifactId>${quarkus.platform.artifact-id}</artifactId>
                <version>${quarkus.platform.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-jdbc-h2</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-smallrye-openapi</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-hibernate-orm-panache</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-resteasy-jackson</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-arc</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-hibernate-orm</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-resteasy</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-hibernate-validator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-smallrye-health</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-container-image-jib</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-kubernetes</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-junit5</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.34</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Plugin Quarkus para Gerar o Artefato e Deploy -->
            <plugin>
                <groupId>${quarkus.platform.group-id}</groupId>
                <artifactId>quarkus-maven-plugin</artifactId>
                <version>${quarkus.platform.version}</version>
                <extensions>true</extensions>
                <executions>
                    <execution>
                        <goals>
                            <goal>build</goal>
                            <goal>generate-code</goal>
                            <goal>generate-code-tests</goal>
                            <goal>native-image-agent</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

            <!-- Plugin Maven Compiler -->
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${compiler-plugin.version}</version>
                <configuration>
                    <parameters>true</parameters>
                </configuration>
            </plugin>

            <!-- Plugin Maven Surefire -->
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>${surefire-plugin.version}</version>
                <configuration>
                    <systemPropertyVariables>
                        <java.util.logging.manager>org.jboss.logmanager.LogManager</java.util.logging.manager>
                        <maven.home>${maven.home}</maven.home>
                    </systemPropertyVariables>
                </configuration>
            </plugin>

            <!-- Plugin Maven Failsafe -->
            <plugin>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>${surefire-plugin.version}</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>integration-test</goal>
                            <goal>verify</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <systemPropertyVariables>
                        <native.image.path>${project.build.directory}/${project.build.finalName}-runner</native.image.path>
                        <java.util.logging.manager>org.jboss.logmanager.LogManager</java.util.logging.manager>
                        <maven.home>${maven.home}</maven.home>
                    </systemPropertyVariables>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <profiles>
        <!-- Profile Kubernetes -->
        <profile>
            <id>kubernetes</id>
            <activation>
                <property>
                    <name>kubernetes</name>
                </property>
            </activation>
            <properties>
                <quarkus.kubernetes.deploy>true</quarkus.kubernetes.deploy>
                <quarkus.kubernetes.service-type>LoadBalancer</quarkus.kubernetes.service-type>
                <quarkus.kubernetes-client.trust-certs>true</quarkus.kubernetes-client.trust-certs>
            </properties>
        </profile>
    </profiles>
</project>
```
