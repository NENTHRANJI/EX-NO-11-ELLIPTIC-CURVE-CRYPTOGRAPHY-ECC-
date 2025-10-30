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
~~~
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    long long int x, y;
    int is_infinity;  // Flag to represent point at infinity
} Point;

long long int modInverse(long long int a, long long int m) {
    long long int m0 = m, t, q;
    long long int x0 = 0, x1 = 1;
    if (m == 1) return 0;
    while (a > 1) {
        q = a / m;
        t = m;
        m = a % m, a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
    }
    if (x1 < 0) x1 += m0;
    return x1;
}

Point pointAddition(Point P, Point Q, long long int a, long long int p) {
    Point R;

    if (P.is_infinity) return Q;
    if (Q.is_infinity) return P;

    if (P.x == Q.x && (P.y != Q.y || P.y == 0)) {
        R.is_infinity = 1;  // Point at infinity (identity)
        return R;
    }

    long long int lambda;
    if (P.x == Q.x && P.y == Q.y) {
        lambda = (3 * P.x * P.x + a) * modInverse(2 * P.y, p) % p;
    } else {
        lambda = (Q.y - P.y) * modInverse(Q.x - P.x, p) % p;
    }

    R.x = (lambda * lambda - P.x - Q.x) % p;
    R.y = (lambda * (P.x - R.x) - P.y) % p;
    R.x = (R.x + p) % p;
    R.y = (R.y + p) % p;
    R.is_infinity = 0;
    return R;
}

Point scalarMultiplication(Point P, long long int k, long long int a, long long int p) {
    Point result = {0, 0, 1}; // Start with point at infinity
    while (k > 0) {
        if (k % 2 == 1)
            result = pointAddition(result, P, a, p);
        P = pointAddition(P, P, a, p);
        k /= 2;
    }
    return result;
}

int main() {
    long long int p, a, b, privateA, privateB;
    Point G, publicA, publicB, sharedA, sharedB;

    printf("Enter the prime number (p): ");
    scanf("%lld", &p);
    printf("Enter the curve parameters (a and b) for y^2 = x^3 + ax + b: ");
    scanf("%lld %lld", &a, &b);
    printf("Enter the base point G (x and y): ");
    scanf("%lld %lld", &G.x, &G.y);
    G.is_infinity = 0;

    printf("Enter Alice's private key: ");
    scanf("%lld", &privateA);
    printf("Enter Bob's private key: ");
    scanf("%lld", &privateB);

    publicA = scalarMultiplication(G, privateA, a, p);
    publicB = scalarMultiplication(G, privateB, a, p);

    printf("Alice's public key: (%lld, %lld)\n", publicA.x, publicA.y);
    printf("Bob's public key: (%lld, %lld)\n", publicB.x, publicB.y);

    sharedA = scalarMultiplication(publicB, privateA, a, p);
    sharedB = scalarMultiplication(publicA, privateB, a, p);

    printf("Shared secret computed by Alice: (%lld, %lld)\n", sharedA.x, sharedA.y);
    printf("Shared secret computed by Bob: (%lld, %lld)\n", sharedB.x, sharedB.y);

    if (sharedA.x == sharedB.x && sharedA.y == sharedB.y)
        printf("Key exchange successful. Both shared secrets match!\n");
    else
        printf("Key exchange failed. Shared secrets do not match.\n");

    return 0;
}
~~~



## Output:
<img width="1919" height="1199" alt="Screenshot 2025-10-30 090654" src="https://github.com/user-attachments/assets/3324c240-3a91-4ff7-bff0-d0a3e782f9c4" />

<img width="1906" height="1199" alt="Screenshot 2025-10-30 090715" src="https://github.com/user-attachments/assets/ebe3cb9b-4bad-4a26-9261-dffe88402096" />

<img width="1918" height="1199" alt="Screenshot 2025-10-30 090733" src="https://github.com/user-attachments/assets/89394b50-9a13-47e6-9191-e73762da8f8d" />

## Result:
The program is executed successfully

