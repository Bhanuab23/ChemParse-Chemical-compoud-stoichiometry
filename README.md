# Prompt the user to enter a chemical formula
print('Please enter the Chemical formula of the Compound and Element symbol correctly as per the IUPAC rules')
print('for example:H2SO4,NaCl,HCl,K4(NO(SO3)2)2,etc')
s = input("please enter the chemical formula(as mentioned in example):")
e = s  # Keep the original formula for display
z = s  # Working copy of the formula

# Remove digits and brackets from the formula to isolate element symbols
for c in s:
    if (c >= '1' and c <= '9') or c == '(' or c == ')':
        s = s.replace(c, '', 1)

# Convert to list of characters
p = list(s)

# Combine lowercase letters with preceding uppercase to get full element symbols (e.g., Cl, Na)
for i in range(len(p)):
    if p[i] >= 'a' and p[i] <= 'z':
        p[i - 1] += p[i]
        p[i] = 0  # Mark lowercase char for removal

# Remove all marked (0) entries
while 0 in p:
    p.remove(0)

w = p.copy()  # Save the list of element symbols for later use
p.sort()
B = {i: 0 for i in p}  # Initialize dictionary to hold element counts

# Recursive function to count atoms from the formula string
def atoms(z, q):
    if '(' not in z:
        # Handle simple formula without parentheses
        h = list(z)
        for i in range(len(h)):
            if h[i] >= 'a' and h[i] <= 'z':
                h[i - 1] += h[i]
                h[i] = 0
        while 0 in h:
            h.remove(0)

        # Combine multi-digit numbers
        for i in range(len(h)):
            if i < len(h) - 1 and h[i] >= '1' and h[i] <= '9' and h[i + 1] >= '1' and h[i + 1] <= '9':
                h[i] += h[i + 1]
                h[i + 1] = '+'
        while '+' in h:
            h.remove('+')

        p = h  # Reuse variable
        for i in range(len(p)):
            if p[i] in B:
                index = i
                if index != -1 and index < len(p) - 1:
                    nxt = p[index + 1]
                    if nxt >= 'A' and nxt <= 'Z':
                        B[p[i]] += q * 1
                    else:
                        B[p[i]] += q * int(nxt)
                else:
                    B[p[i]] += q * 1
        return

    # Handle formulas with parentheses (grouped atoms)
    l = {i: z[i] for i in range(len(z))}
    for key, val in enumerate(z):
        if val == '(':
            n = key + 1
            break
    for key, val in reversed(l.items()):
        if val == ')':
            m = key
            break
    j = z[n - 1:m + 2]  # Extract group including parentheses and multiplier
    x = z.replace(j, "")  # Remove this group from the formula

    # Process the rest of the formula outside parentheses
    h = list(x)
    for i in range(len(h)):
        if h[i] >= 'a' and h[i] <= 'z':
            h[i - 1] += h[i]
            h[i] = 0
    while 0 in h:
        h.remove(0)
    for i in range(len(h)):
        if i < len(h) - 1 and h[i] >= '1' and h[i] <= '9' and h[i + 1] >= '1' and h[i + 1] <= '9':
            h[i] += h[i + 1]
            h[i + 1] = '+'
    while '+' in h:
        h.remove('+')
    p = h.copy()

    for i in range(len(p)):
        if p[i] in B:
            index = i
            if index != -1 and index < len(p) - 1:
                nxt = p[index + 1]
                if nxt >= 'A' and nxt <= 'Z':
                    B[p[i]] += q * 1
                else:
                    B[p[i]] += q * int(nxt)
            else:
                B[p[i]] += q * 1

    # Recursively analyze the group inside parentheses
    k = z[n:m]
    q = q * int(z[m + 1])  # Apply multiplier to the group
    z = k
    atoms(z, q)  # Recurse

# Start processing formula with multiplier 1
q = 1
atoms(z, q)

# Dictionary of atomic masses (g/mol)
emm = {
    "H": 1.008, "He": 4.0026, "Li": 6.94, "Be": 9.0122, "B": 10.81, "C": 12.011, "N": 14.007, "O": 15.999,
    "F": 18.998, "Ne": 20.180, "Na": 22.990, "Mg": 24.305, "Al": 26.982, "Si": 28.085, "P": 30.974, "S": 32.06,
    "Cl": 35.45, "Ar": 39.948, "K": 39.098, "Ca": 40.078, "Sc": 44.956, "Ti": 47.867, "V": 50.942, "Cr": 51.996,
    "Mn": 54.938, "Fe": 55.845, "Co": 58.933, "Ni": 58.693, "Cu": 63.546, "Zn": 65.38, "Ga": 69.723, "Ge": 72.630,
    "As": 74.922, "Se": 78.971, "Br": 79.904, "Kr": 83.798, "Rb": 85.468, "Sr": 87.62, "Y": 88.906, "Zr": 91.224,
    "Nb": 92.906, "Mo": 95.95, "Tc": 98, "Ru": 101.07, "Rh": 102.91, "Pd": 106.42, "Ag": 107.87, "Cd": 112.41,
    "In": 114.82, "Sn": 118.71, "Sb": 121.76, "Te": 127.60, "I": 126.90, "Xe": 131.29, "Cs": 132.91, "Ba": 137.33,
    "La": 138.91, "Ce": 140.12, "Pr": 140.91, "Nd": 144.24, "Pm": 145, "Sm": 150.36, "Eu": 151.96, "Gd": 157.25,
    "Tb": 158.93, "Dy": 162.50, "Ho": 164.93, "Er": 167.26, "Tm": 168.93, "Yb": 173.05, "Lu": 174.97, "Hf": 178.49,
    "Ta": 180.95, "W": 183.84, "Re": 186.21, "Os": 190.23, "Ir": 192.22, "Pt": 195.08, "Au": 196.97, "Hg": 200.59,
    "Tl": 204.38, "Pb": 207.2, "Bi": 208.98, "Po": 209, "At": 210, "Rn": 222, "Fr": 223, "Ra": 226,
    "Ac": 227, "Th": 232.04, "Pa": 231.04, "U": 238.03, "Np": 237, "Pu": 244, "Am": 243, "Cm": 247,
    "Bk": 247, "Cf": 251, "Es": 252, "Fm": 257, "Md": 258, "No": 259, "Lr": 262, "Rf": 267,
    "Db": 270, "Sg": 271, "Bh": 270, "Hs": 277, "Mt": 276, "Ds": 281, "Rg": 282, "Cn": 285,
    "Nh": 286, "Fl": 289, "Mc": 290, "Lv": 293, "Ts": 294, "Og": 294
}


# Calculate total molar mass of the compound
mm = 0.0
for k, v in B.items():
    mm += emm[k] * v

# Menu-driven interaction loop
h = 1
while h:
    print('1. molar mass')
    print('2. percentage composition by mass of each element')
    print('3. number of elements in the compound')
    print('4. mass of elements in given amount of compound')
    print('5. number of moles of compound in given weight')
    d = int(input('enter your choice:'))

    if d == 1:
        print('Molar mass of compound {} is {}g'.format(e, round(mm, 4)))

    if d == 2:
        for k, v in B.items():
            o = ((v * emm[k]) / mm) * 100
            print('Percentage composition of {} is {}%'.format(k, round(o, 2)))

    if d == 99:
        # Option to get empirical formula (based on greatest common divisor)
        g = ''
        import math
        from functools import reduce
        nums = [v for v in B.values()]
        gcd_all = reduce(math.gcd, nums)
        for i in w:
            j = round((B[i] / gcd_all))
            if j == 1:
                j = ''
            g += i + str(j)
        print(g)

    if d == 3:
        for k, v in B.items():
            print("No of {}'s present in {} = {}".format(k, e, v))

    if d == 4:
        r = input('Enter given weight of compound (in grams):')
        if 'g' in r:
            r = float(r[:-1])
        r = float(r)
        f = input('Please enter the element present in the compound:')
        l = (B[f] * emm[f]) / mm
        u = round((l * r), 3)
        print('There are {}g of {} in {}g of {}'.format(u, f, r, e))

    if d == 5:
        r = input('Enter given weight of compound (in grams):')
        if 'g' in r:
            r = float(r[:-1])
        r = float(r)
        g = round((r / mm), 3)
        print('There are {} moles present in {}g of {}'.format(g, r, e))

    h = int(input('Please enter 1 to continue or 0 to quit:'))

