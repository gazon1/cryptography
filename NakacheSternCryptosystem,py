from functools import reduce
from random import randrange, getrandbits

# Code for generation big prime numbers
# borrowed from here https://medium.com/@prudywsh/how-to-generate-big-prime-numbers-miller-rabin-49e6e6af32fb

def is_prime(n, k=128):
    """ Test if a number is prime

        Args:
            n -- int -- the number to test
            k -- int -- the number of tests to do

        return True if n is prime
    """
    # Test if n is not even.
    # But care, 2 is prime !
    if n == 2 or n == 3:
        return True
    if n <= 1 or n % 2 == 0:
        return False
    # find r and s
    s = 0
    r = n - 1
    while r & 1 == 0:
        s += 1
        r //= 2
    # do k tests
    for _ in range(k):
        a = randrange(2, n - 1)
        x = pow(a, r, n)
        if x != 1 and x != n - 1:
            j = 1
            while j < s and x != n - 1:
                x = pow(x, 2, n)
                if x == 1:
                    return False
                j += 1
            if x != n - 1:
                return False

    return True

def generate_prime_candidate(length):
    """ Generate an odd integer randomly

        Args:
            length -- int -- the length of the number to generate, in bits

        return a integer
    """
    # generate random bits
    p = getrandbits(length)
    # apply a mask to set MSB and LSB to 1
    p |= (1 << length - 1) | 1

    return p

def generate_prime_number(length=1024):
    """ Generate a prime

        Args:
            length -- int -- length of the prime to generate, in          bits

        return a prime
    """
    p = 4
    # keep generating while the primality test fail
    while not is_prime(p, 128):
        p = generate_prime_candidate(length)
    return p


# Code implementing Chinese Remainder Theorem
# borrowed from https://www.geeksforgeeks.org/using-chinese-remainder-theorem-combine-modular-equations/

# function that implements Extended euclidean 
# algorithm 
def extended_euclidean(a, b): 
    if a == 0: 
        return (b, 0, 1) 
    else: 
        g, y, x = extended_euclidean(b % a, a) 
        return (g, x - (b // a) * y, y) 

# modular inverse driver function 
def modinv(a, m): 
    g, x, y = extended_euclidean(a, m) 
    return x % m 

# function implementing Chinese remainder theorem 
# list m contains all the modulii 
# list x contains the remainders of the equations 
def crt(m, x): 

    # We run this loop while the list of 
    # remainders has length greater than 1 
    while True: 
        # temp1 will contain the new value 
        # of A. which is calculated according 
        # to the equation m1' * m1 * x0 + m0' 
        # * m0 * x1 
        temp1 = modinv(m[1],m[0]) * x[0] * m[1] + modinv(m[0],m[1]) * x[1] * m[0] 

        # temp2 contains the value of the modulus 
        # in the new equation, which will be the 
        # product of the modulii of the two 
        # equations that we are combining 
        temp2 = m[0] * m[1] 

        # we then remove the first two elements 
        # from the list of remainders, and replace 
        # it with the remainder value, which will 
        # be temp1 % temp2 
        x.remove(x[0]) 
        x.remove(x[0]) 
        x = [temp1 % temp2] + x 

        # we then remove the first two values from 
        # the list of modulii as we no longer require 
        # them and simply replace them with the new 
        # modulii that we calculated 
        m.remove(m[0]) 
        m.remove(m[0]) 
        m = [temp2] + m 

        # once the list has only one element left, 
        # we can break as it will only contain 
        # the value of our final remainder 
        if len(x) == 1: 
            break

    # returns the remainder of the final equation 
    return x[0] 


class NakacheSternCryptosystem:
    def __init__(self, pk, a, b, g):
        """
        k - кол-во простых чисел p1, ..., pk - списка pk
        pk - список простых чисел p1, ..., pk
        a, b - простые числа. Используются в генерации p и q(см. алгоритм генерации ключа в криптосистеме
            Накаше-Штерна)
        g - основание, которое нужно возводить в степень шифруемого сообщения
        """
        self.g = g
        self.pk = pk
        k = len(pk)
        p = 2 * a * reduce(lambda x, y: x*y, pk[:int(k/2)]) + 1
        q = 2 * b * reduce(lambda x, y: x*y, pk[int(k/2):]) + 1
        self.sigma = reduce(lambda x, y: x*y, pk)
        self.n = p * q
        self.phi = (p - 1) * (q - 1)
        
    def encrypt(self, m):
        x = generate_prime_number(length=len(bin(self.n)) - 2)
        x = pow(x, self.sigma, self.n)
        g = pow(self.g, m, self.n)
        return pow(x * g, 1, n)


    def decrypt(self, c):
        mk = []
        for pi in self.pk:
            ci = pow(c, int(self.phi / pi), self.n)
            for j in range(0, pi):
                if ci == pow(self.g, int(self.phi * j / pi), self.n):
                    mk.append(j)

        return crt(list(pk), list(mk))
