# ... (Stage 1: Build Stage is assumed to be correct now) ...
# Stage 1: Build Stage
FROM node:20-alpine AS builder

WORKDIR /app

# 1. Copy package files and lock file for ALL dependencies
COPY package*.json ./
# 2. Install ALL dependencies (including devDependencies for 'tsc')
RUN npm install

# 3. Copy the rest of the application source code.
COPY . .

# 4. RUN THE BUILD STEP HERE!
RUN npm run build


# ---
# Stage 2: Production/Runtime Stage (The Fix is below)
# ---
FROM node:20-alpine

# Set the working directory
WORKDIR /app

# 1. Copy ONLY the files needed for the final production install.
#    ***CRUCIAL FIX: You must copy package-lock.json for 'npm ci' to work.***
COPY package.json ./
COPY package-lock.json ./

# 2. Install ONLY production dependencies using 'npm ci'
RUN npm ci --omit=dev

# 3. Copy the COMPILED application files (the 'dist' folder) from the 'builder' stage
COPY --from=builder /app/dist ./dist

# 4. Set Environment Variables
ENV DATABASE_URI=mongodb://mongo@mongo:27017/mydb?authSource=admin
ENV REDIS_HOST=redis
ENV REDIS_PORT=6379
ENV REDIS_PASSWORD=someredispassword
EXPOSE 8706 

# 5. Define the command to run the COMPILED application
# This assumes your compiled entry point is 'dist/server.js'
CMD ["node", "dist/server.js"]