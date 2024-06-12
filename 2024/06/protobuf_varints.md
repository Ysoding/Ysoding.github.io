[Protobuf Encoding](https://protobuf.dev/programming-guides/encoding/#simple)

[Code](https://github.com/Ysoding/cs/blob/main/computer-system/bits%26bytes/varint/varints.py)

## Encode

- **Start with the integer you want to encode.**
- **Break it into 7-bit chunks.** These chunks are extracted from the least significant bits of the number.
- **Set the most significant bit (MSB) of each chunk to 1 if there are more chunks to follow, and to 0 for the last chunk.**
- **Combine these chunks into bytes and output them in order.**

```sh
300 in binary: 100101100

100101100 (original)
00101100 (first 7 bits: 44 in decimal)
00000010 (remaining bits: 2 in decimal)


00101100  ->  10101100 (since there are more chunks, MSB is 1)
00000010  ->  00000010 (last chunk, MSB is 0)

First byte:  10101100 (172 in decimal)
Second byte: 00000010 (2 in decimal)

0xAC 0x02
```

## Decode

- **Read bytes one by one.**
- **For each byte, strip the MSB and collect the remaining 7 bits.**
- **If the MSB is 1, continue to the next byte. If the MSB is 0, this is the last byte.**
- **Combine all 7-bit groups into the original number.**

```sh

10010110 00000001        // Original inputs.
 0010110  0000001        // Drop continuation bits.
 0000001  0010110        // Convert to big-endian.
   00000010010110        // Concatenate.
 128 + 16 + 4 + 2 = 150  // Interpret as an unsigned 64-bit integer.
```