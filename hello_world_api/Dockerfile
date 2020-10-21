FROM node:8

WORKDIR /opt/helloworld

# install deps
COPY package.json /opt/helloworld
RUN npm install

# Setup workdir
COPY . /opt/helloworld

# run
EXPOSE 3000
CMD ["npm", "start"]