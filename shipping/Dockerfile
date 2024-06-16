#
# Build
#
FROM debian:10 AS build

# Update and install ca-certificates-java first to avoid issues
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates-java \
    && /var/lib/dpkg/info/ca-certificates-java.postinst configure

# Install Maven and other dependencies
RUN apt-get install -y openjdk-11-jdk-headless maven && apt-get clean

WORKDIR /opt/shipping

COPY pom.xml /opt/shipping/
RUN mvn dependency:resolve
COPY src /opt/shipping/src/
RUN mvn package

FROM openjdk:8-jdk

EXPOSE 8080

WORKDIR /opt/shipping

ENV CART_ENDPOINT=cart:8080
ENV DB_HOST=mysql

COPY --from=build /opt/shipping/target/shipping-1.0.jar shipping.jar

CMD ["java", "-Xmn256m", "-Xmx768m", "-jar", "shipping.jar"]


