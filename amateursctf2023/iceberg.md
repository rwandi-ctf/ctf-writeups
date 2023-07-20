# iceberg


So we are given an image, a script, and a modified image.

The script:

```py
from PIL import Image

def encode_lsb(image_path, message):
    # Open the image
    image = Image.open(image_path)
    pixels = image.load()

    # Check if the message can fit within the image
    if len(message) * 8 > image.width * image.height:
        raise ValueError("Message is too long to fit within the image.")

    # Convert the message to binary
    binary_message = ''.join(format(ord(char), '08b') for char in message)

    # Embed the message into the image
    char_index = 0
    for y in range(image.height):
        for x in range(image.width):
            r, g, b, a = pixels[x, y]

            if char_index < len(binary_message):
                # Modify the second least significant bit of the red channel
                # only if red is greater than green and blue
                if r > g and r > b:
                    r = (r & 0xFD) | (int(binary_message[char_index]) << 1)
                    char_index += 1

            pixels[x, y] = (r, g, b, a)

    # Save the modified image
    encoded_image_path = f"new-{image_path}"
    image.save(encoded_image_path)
    print("Message encoded successfully in the image:", encoded_image_path)


# Example usage
image_path = "rules-iceberg.png"

# extract flag from flag.txt
with open("flag.txt", "r") as f:
    flag = f.read().strip()

assert len(flag) == 54

encode_lsb(image_path, flag)
```

It's even commented for our convenience.

The script finds pixels with a red value greater than the green or blue value, then sets the second least significant bit to a bit in the binary of the flag.

So, to get the flag we do the same thing, but instead of modifying the image, we read from the modified image.

This means replace this line:
```py
r = (r & 0xFD) | (int(binary_message[char_index]) << 1)
```
with

```py
value = modified_r % 4 // 2
```

My code:
```py
im = np.array(Image.open('rules-iceberg.png'))
im2 = np.array(Image.open('new-rules-iceberg.png'))
count = 0
msg = ""
charindex = 0
for i in range(1205):
    for j in range(725):
        k = 0
        if im[i, j, 0] > im[i, j, 1] and im[i,j,0] > im[i,j,2]: #r > g and r > b
            charindex += 1
            msg += str(im2[i,j,0] % 4 // 2)
            if (len(msg)% 9 == 8):
                msg += " "
        if charindex == 432:
            break;
print(msg)
```


This gives you this binary:
```
01100001 01101101 01100001 01110100 01100101 01110101 01110010 01110011 01000011 01010100 01000110 01111011 00110011 01110110 00110011 01110010 01111001 00110000 01101110 00110011 01011111 01100100 00110011 01100110 00110001 01101110 00110001 01110100 00110011 01101100 01111001 01011111 01101100 00110000 01110110 00110011 01110011 01011111 01110011 01110100 00110011 01100111 00110000 01011111 01101101 01101000 01101101 01101000 01101101 01101000 01101101 01101000 01101101 01111101
```

Which translates to a flag
`amateursCTF{3v3ry0n3_d3f1n1t3ly_l0v3s_st3g0_mhmhmhmhm}`
