#Use small Node.js image for dependency installation
FROM node:18-alpine AS deps

#Set working directory inside container
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./ 

#Install exact dependencies from package-lock.json
RUN npm ci 

#SECOND STAGE  Build React application,Use Node.js image to build React app
FROM deps AS build 		

WORKDIR /app			

#Copy installed node_modules from deps stage
COPY --from=deps /app/node_modules ./node_modules

#Copy all frontend source code
COPY . . 

#Build optimized production React app
RUN npm run build 

#THIRD STAGE  Nginx runtime server
#Use tiny Nginx image for serving static files
FROM nginx:alpine

#Nginx by default serves files from /usr/share/nginx/html,Nginx default directory for serving HTML
WORKDIR /usr/share/nginx/html

#Copy React build files from build stage
COPY --from=build /app/build ./

#Copy custom Nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

#Expose port 80 for HTTP access
EXPOSE 80 

#Start Nginx in foreground so container stays running
CMD ["nginx", "-g", "daemon off;"]
