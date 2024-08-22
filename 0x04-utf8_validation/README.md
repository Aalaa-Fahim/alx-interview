# UTF-8 Validation

## Overview

This project involves writing a Python method that checks if a given data set is a valid UTF-8 encoding. The method will handle multiple characters, each represented by a list of integers, where each integer corresponds to one byte of data.

## Method Requirements

### Function Prototype
```python
def validUTF8(data):
```

### Input
- **data**: A list of integers, each representing 1 byte (8 bits) of data.

### Output
- **Return**: `True` if the data represents a valid UTF-8 encoding, otherwise return `False`.

### UTF-8 Encoding Basics
- UTF-8 characters can be 1 to 4 bytes long.
- The first byte of a character determines its length, with specific bit patterns indicating the number of bytes.

## Implementation Outline

1. **Bit Masking**:
   - Extract the 8 least significant bits from each integer in the list.

2. **Validation Logic**:
   - Check the number of leading 1s in the first byte to determine character length.
   - Validate that subsequent bytes start with the `10` pattern for multi-byte characters.

3. **Edge Cases**:
   - Handle scenarios where data might contain incomplete or overlong sequences.

### Example Code
```python
def validUTF8(data):
    num_bytes = 0

    for num in data:
        byte = num & 0xFF  # Extract the last 8 bits

        if num_bytes == 0:
            if (byte >> 5) == 0b110:
                num_bytes = 1
            elif (byte >> 4) == 0b1110:
                num_bytes = 2
            elif (byte >> 3) == 0b11110:
                num_bytes = 3
            elif (byte >> 7):
                return False
        else:
            if (byte >> 6) != 0b10:
                return False
            num_bytes -= 1

    return num_bytes == 0
```

## Conclusion

This project challenges you to understand and implement UTF-8 validation. The solution involves bitwise operations and logical checks, ensuring that the data set adheres to the rules of UTF-8 encoding.
