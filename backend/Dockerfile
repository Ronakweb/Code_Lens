FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements first for better caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY . .

# Create necessary directories
RUN mkdir -p temp_clones fallback_indexes

# Expose the port that Render expects
EXPOSE 10000

# Command to run the application
# Render sets the PORT environment variable, but we default to 10000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "10000"]