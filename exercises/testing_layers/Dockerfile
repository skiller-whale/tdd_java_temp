# Start with Gradle base image with JDK 21
FROM gradle:8.13-jdk21

# Switch to root to install additional packages
USER root

WORKDIR /app

# Install Node.js 20 and xz-utils for watchexec
RUN apt-get update && \
    apt-get install -y curl xz-utils && \
    curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Ensure node and npm are in PATH for all users (npm comes with Node.js from NodeSource)
RUN ln -sf /usr/bin/node /usr/local/bin/node && \
    ln -sf /usr/bin/npm /usr/local/bin/npm

# Set environment variables to ensure tools are found
ENV PATH="/usr/local/bin:/usr/bin:$PATH"
ENV JAVA_HOME="/opt/java/openjdk"

# Install dependencies for Playwright browsers
RUN apt-get update && \
    apt-get install -y libglib2.0-0t6 libnspr4 libnss3 libdbus-1-3 libatk1.0-0t64 \
                      libatk-bridge2.0-0t64 libcups2t64 libxcb1 libxkbcommon0 \
                      libatspi2.0-0t64 libx11-6 libxcomposite1 libxdamage1 \
                      libxext6 libxfixes3 libxrandr2 libgbm1 libcairo2 \
                      libpango-1.0-0 libasound2t64 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install watchexec (musl static binary)
RUN wget https://github.com/watchexec/watchexec/releases/download/v1.24.2/watchexec-1.24.2-x86_64-unknown-linux-musl.tar.xz && \
    tar -xf watchexec-1.24.2-x86_64-unknown-linux-musl.tar.xz && \
    mv watchexec-*/watchexec /usr/local/bin/watchexec && \
    rm -rf watchexec-* watchexec-1.24.2-x86_64-unknown-linux-musl.tar.xz

# Copy project files
COPY . .

# Pre-download Gradle dependencies to cache them in Docker layer
RUN gradle build -x test --no-daemon

# Verify tools are available
RUN echo "=== Tool Verification ===" && \
    java -version && \
    node --version && \
    npm --version && \
    gradle --version

EXPOSE 8080
