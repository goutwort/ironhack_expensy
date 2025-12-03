# Stage 1: Build Stage (Optional, but good practice for larger projects)
FROM node:20-alpine AS builder

# Set the working directory for subsequent steps
WORKDIR /app

# 1. Copy package files first for efficient Docker caching.
# This ensures npm install is only rerun if package.json or package-lock.json changes.
COPY package*.json ./

# 2. Install dependencies. Using 'ci' is faster and more reliable.
# --omit=dev ensures development dependencies are not in the final image.
RUN npm ci --omit=dev

# 3. Copy the rest of the application source code.
# The COPY . . command in the original was placed too early.
COPY . .

# Stage 2: Production/Runtime Stage
# Since we already ran 'npm ci' on an Alpine image, we can keep the same base for runtime
FROM node:20-alpine

# Set the working directory
WORKDIR /app

# Copy the installed dependencies and application code from the 'builder' stage
COPY --from=builder /app ./

# 4. Set Environment Variables
# NOTE: These should ideally be passed in at runtime (docker run -e) for secrets.
ENV DATABASE_URI=mongodb://mongo@mongo:27017/mydb?authSource=admin
ENV REDIS_HOST=redis
ENV REDIS_PORT=6379
ENV REDIS_PASSWORD=someredispassword
# You may also want to expose the application port if the app uses it
EXPOSE 8706 

# 5. Define the command to run the application
# This runs the 'start' script defined in your package.json
CMD ["npm", "start"]