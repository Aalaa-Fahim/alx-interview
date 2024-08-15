# Log Parsing

This project involves writing a Python script that reads log data from standard input (`stdin`) line by line, processes the data to compute metrics, and outputs the results periodically. The script is designed to handle log entries in a specific format, and it calculates and displays the total file size and the count of HTTP status codes encountered.

## Input Format

The expected input format for each log entry is:

```
<IP Address> - [<date>] "GET /projects/260 HTTP/1.1" <status code> <file size>
```

- **IP Address**: The IP address of the client.
- **date**: The date and time of the request.
- **status code**: The HTTP status code returned (e.g., 200, 404).
- **file size**: The size of the requested file in bytes.

### Example Input
```
123.456.789.000 - [2024-08-15 14:23:45] "GET /projects/260 HTTP/1.1" 200 1024
```

### Input Handling
- If a line does not match the expected format, it will be skipped.

## Output

The script prints statistics after every 10 lines of input or upon receiving a keyboard interruption (CTRL + C). The output includes:

1. **Total File Size**: The cumulative sum of all file sizes encountered so far.
    - **Format**: `File size: <total size>`

2. **Number of Lines by Status Code**: The count of occurrences of each status code.
    - Only the following status codes are tracked: `200, 301, 400, 401, 403, 404, 405, 500`.
    - **Format**: `<status code>: <number>`
    - Status codes should be printed in ascending order.

### Example Output
```
File size: 2048
200: 5
404: 2
500: 1
```

## Implementation Details

### Script Structure
The script processes input data line by line, updating the total file size and counting the occurrences of valid status codes. It handles two main events for outputting the metrics:
- After processing every 10 lines.
- When a keyboard interruption (CTRL + C) is detected.

### Key Functions
- **Processing Lines**: Extracts the IP address, status code, and file size from each line and updates the respective metrics.
- **Handling Interruptions**: Captures the keyboard interruption signal to print the final statistics before exiting.

### Example Code

```python
import sys
import signal

def print_stats(total_size, status_codes):
    print(f"File size: {total_size}")
    for code in sorted(status_codes.keys()):
        if status_codes[code] > 0:
            print(f"{code}: {status_codes[code]}")

def process_line(line, total_size, status_codes):
    try:
        parts = line.split()
        status_code = int(parts[-2])
        file_size = int(parts[-1])
        
        total_size += file_size
        if status_code in status_codes:
            status_codes[status_code] += 1
            
    except Exception:
        pass
    
    return total_size

def main():
    total_size = 0
    status_codes = {200: 0, 301: 0, 400: 0, 401: 0, 403: 0, 404: 0, 405: 0, 500: 0}
    line_count = 0
    
    def signal_handler(sig, frame):
        print_stats(total_size, status_codes)
        sys.exit(0)

    signal.signal(signal.SIGINT, signal_handler)
    
    try:
        for line in sys.stdin:
            total_size = process_line(line, total_size, status_codes)
            line_count += 1
            
            if line_count % 10 == 0:
                print_stats(total_size, status_codes)
                
    except Exception:
        pass

    print_stats(total_size, status_codes)

if __name__ == "__main__":
    main()
```

### How It Works
- **Processing Lines**: Each line is split into its components, and the script attempts to parse the status code and file size. If successful, it updates the cumulative file size and the relevant status code count.
- **Handling Interruptions**: When the script is interrupted (CTRL + C), it outputs the current metrics before gracefully exiting.

## Conclusion

This script efficiently processes log entries and provides useful metrics on file size and HTTP status codes. It’s designed to be robust, handling incorrect formats gracefully and providing periodic updates to the user.
