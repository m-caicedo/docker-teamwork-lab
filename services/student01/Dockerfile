FROM busybox:stable
WORKDIR /srv
RUN printf "Hello, I am Student01 👋\n" > index.html \
 && printf "ok\n" > health
EXPOSE 8080
CMD ["sh", "-c", "httpd -f -p 8080 -h /srv"]