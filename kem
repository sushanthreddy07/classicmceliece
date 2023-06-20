from sage.all import *
import hashlib

# Irreducible polynomial generation
m = 4
F = GF(2**m, 'a')
R.<x> = PolynomialRing(F)
t = 2
while True:
    irr_poly = R.random_element(t-1)
    irr_poly += x^t
    if irr_poly.is_irreducible():
        break
g = irr_poly
print("Irreducible polynomial g(x):", g)

# Key generation
t = 2
a = F.gen() # define the generator of the field F
L = [F(a**i) for i in range(2, 14)]
C = codes.GoppaCode(g, L)
H = C.parity_check_matrix()
n = H.ncols()
k = n - H.nrows()
print("Parity check matrix H:\n", H)
U = matrix(F, [H.columns()[i] for i in range(H.nrows())]).transpose()
H_ = U^(-1) * H
T = matrix(F, [H.columns()[i] for i in range(H.nrows(), H.ncols())]).transpose()
H_ = matrix.block([[matrix.identity(T.nrows()), T]])
print("Modified parity check matrix H:\n", H_)

# Fixed weight error generation using enumerating decoder
def invert_binomial(u, v):
    if u == 0:
        return v - 1
    x = (factorial(v) * u)^(1/v)
    z = x + ((v - 1)/2) + ((v^2 - 1)/24) * (1/x)
    return int(z)

def enum(x):
    E = vector(GF(Integer(2)), n)
    j = Integer(2)
    while j > Integer(0):
        i_j = invert_binomial(x, j)
        E[i_j] = Integer(1)
        x = x - binomial(i_j, j)
        j = j - 1
    return E

c = binomial(n, t)
l = floor(log(c, Integer(2)))
M = (GF(Integer(2))^l).random_element()
y = Integer(0)
for i in range(l):
    if M[i] == Integer(1):
        y = y + Integer(2)^i
e = enum(y)
print("Fixed weight error vector e:", e)
print("Random vector M:", M)

# Encapsulation
B_0 = H_ * e
seed1 = '2'
for ele in e:
    seed1 += str(ele)
B_1 = hashlib.sha256(seed1.encode()).hexdigest()
print("B_1:", B_1)
seed2 = '1'
for ele in e:
    seed2 += str(ele)
seed2 += str(B_1)
key = hashlib.sha256(seed1.encode()).hexdigest()
print("Key:", key)
B = (B_0, B_1)

# Decode using Sage's built-in decoder
A_ = H_.solve_right(B_0)
m_ = C.decode_to_code(A_)
e_ = A_ - m_
print("Decoded error vector e_:", e_)

# Decapsulation
seed2 = '2'
for ele in e_:
    seed2 += str(ele)
for ele in B_0:
    seed2 += str(ele)
seed2 += str(B_1)
K_ = hashlib.sha256(seed2.encode()).hexdigest()
print("Key and K_:", key, K_)
key==K_
