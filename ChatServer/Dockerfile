FROM ubuntu:24.04

RUN apt-get update && \
    apt-get install -y \
        libqt6core6 \
        libqt6gui6 \
        libqt6widgets6 \
        libqt6network6 \
        libqt6sql6 \
        libqt6sql6-psql \
        libgl1 \
        libopengl0 \
        libxcb-xinerama0 \
        && apt-get clean

WORKDIR /app
COPY Server /app/Server

EXPOSE 8080
RUN chmod +x /app/Server

CMD ["./Server"]