# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```
#include <stdio.h>

// structure for a point on the elliptic curve
struct Point {
    int x;
    int y;
};

// global curve parameters
int a = 2;
int b = 3;
int p = 97;   // prime number for finite field

// function to find modular inverse
int modInverse(int k) {
    k = k % p;
    for (int x = 1; x < p; x++) {
        if ((k * x) % p == 1)
            return x;
    }
    return -1;
}

// point addition
struct Point pointAdd(struct Point P, struct Point Q) {
    struct Point R;
    int slope;

    if (P.x == Q.x && P.y == Q.y) {
        slope = ((3 * P.x * P.x + a) * modInverse(2 * P.y)) % p;
    } else {
        slope = ((Q.y - P.y) * modInverse(Q.x - P.x)) % p;
    }

    if (slope < 0)
        slope += p;

    R.x = (slope * slope - P.x - Q.x) % p;
    if (R.x < 0) R.x += p;

    R.y = (slope * (P.x - R.x) - P.y) % p;
    if (R.y < 0) R.y += p;

    return R;
}

// point multiplication (repeated addition)
struct Point pointMultiply(struct Point P, int k) {
    struct Point R = P;

    for (int i = 1; i < k; i++) {
        R = pointAdd(R, P);
    }

    return R;
}

int main() {

    struct Point G = {3, 6}; // base point

    int d; // private key
    printf("Enter private key: ");
    scanf("%d", &d);

    // generate public key
    struct Point Q = pointMultiply(G, d);

    printf("\nPublic Key Q = (%d , %d)\n", Q.x, Q.y);

    // message represented as a point
    struct Point M = {10, 7};

    int k = 5; // random integer

    struct Point C1 = pointMultiply(G, k);
    struct Point temp = pointMultiply(Q, k);
    struct Point C2 = pointAdd(M, temp);

    printf("\nEncrypted Message:");
    printf("\nC1 = (%d , %d)", C1.x, C1.y);
    printf("\nC2 = (%d , %d)", C2.x, C2.y);

    // decryption
    struct Point decrypt = pointMultiply(C1, d);

    struct Point recovered;
    recovered.x = (C2.x - decrypt.x) % p;
    recovered.y = (C2.y - decrypt.y) % p;

    if (recovered.x < 0) recovered.x += p;
    if (recovered.y < 0) recovered.y += p;

    printf("\n\nDecrypted Message Point:");
    printf("\nM = (%d , %d)", recovered.x, recovered.y);

    return 0;
}
```


## Output:
<img width="1654" height="852" alt="image" src="https://github.com/user-attachments/assets/89c241a2-178e-4b62-a1b9-a425e406a366" />


## Result:
The program is executed successfully

