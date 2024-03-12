FROM httpd:2.4-alpine
COPY init.sh .
RUN ["chmod", "+x", "./init.sh"]
ENTRYPOINT [ "./init.sh" ]
CMD ["apachectl", "-D", "FOREGROUND"]