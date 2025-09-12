FROM node:20-alpine3.21
# we need to create system user and 
RUN addgroup -S roboshop && adduser roboshop -s roboshop -G roboshop
WORKDIR /opt/server
RUN chown -R roboshop:roboshop /opt/server
USER roboshop
COPY package.json .
COPY *.js .
RUN npm install
ENV MONGO="true"\
MONGO_URL="mongodb://mongodb:27017/catalogue"
CMD [ "node" ,"server.js"]