# Use an official base image
FROM node:14

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json for installing dependencies
COPY package*.json ./

# Install the dependencies
RUN npm install

# Copy all other application files
COPY . /app

# Expose the port on which the app will run
EXPOSE 3000

# Command to start the application
CMD ["npm", "start"]
