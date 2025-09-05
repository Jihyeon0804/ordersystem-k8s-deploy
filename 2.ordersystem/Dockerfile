FROM openjdk:17-jdk-alpine AS stage1

WORKDIR /app

# 폴더 복사할 때는 폴더명 명시
# 파일 복사할 때는 복사할 위치 명시
COPY gradle gradle
COPY src src
COPY build.gradle .
COPY settings.gradle .
COPY gradlew .

# 파일 실행하기 위한 권한 설정
RUN chmod +x gradlew 
RUN ./gradlew bootJar

# 두번째 스테이지
# 이미지 경량화를 위해 스테이지 분리
FROM openjdk:17-jdk-alpine

WORKDIR /app

# stage1의 jar 파일을 stage2로 copy
COPY --from=stage1 /app/build/libs/*.jar app.jar

# 실행 : CMD 또는 ENTRIPOINT 를 통해 컨테이너 실행
ENTRYPOINT [ "java", "-jar", "app.jar" ]

# 도커 이미지 빌드 (빌드 위치 중요)
# docker build -t ordersystem:v1.0 .

# 도커 컨테이너 실행 (실행 위치 중요X)
# 내부 포트는 spring이 실행되는 포트 설정 (application.yml 의 servger.port 값)
# docker 내부에서 localhost 를 찾는 설정은 루프백(자기 자신을 찾게 되는) 문제 발생
# docker run --name my-ordersystem -d -p 8080:8080 ordersystem:v1.0

# 도커 컨테이너 실행 시점에 host.docker.internal을 환경 변수로 주입
# docker run --name my-ordersystem -d -p 8080:8080 -e SPRING_REDIS_HOST=host.docker.internal
#  -e SPRING_DATASOURCE_URL=jdbc:mariadb://host.docker.internal:3306/order ordersystem:v1.0