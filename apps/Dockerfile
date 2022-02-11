#Using ruby:alpine as base image due to image light weight.
FROM ruby:alpine 

#Run system updates.
RUN apk update -qq && apk upgrade && apk add curl

WORKDIR /home/myapp

#Create a new group and add a new user with the only permission to the working directory. 
RUN addgroup -g 1000 myapp && \
    adduser -u 1000 -G myapp -h /home/myapp -D myapp

#Change into user my app to copy files into the working directory, Expose port 80 and run final commands.
USER myapp

COPY ./http_server/http_server.rb /home/myapp/

EXPOSE 80

CMD ["ruby", "http_server.rb"]