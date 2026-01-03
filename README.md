import numpy as np
import matplotlib.pyplot as plt

W = 1.0  # largeur du couloir
EPS = 0.005  # tolérance sécurité (5 mm)

# ----------------------------
# Rotation
# ----------------------------
def rotate(x, y, a):
    a = np.radians(a)
    return x*np.cos(a) - y*np.sin(a), x*np.sin(a) + y*np.cos(a)

# ----------------------------
# Collision complète (4 murs)
# ----------------------------
def collision(x, y):
    if np.any(x < -W + EPS): return True   # mur gauche
    if np.any(y >  W - EPS): return True   # mur haut
    if np.any(x >  0 + EPS): return True   # mur droit
    if np.any(y <  0 + EPS): return True   # mur bas
    return False

# ----------------------------
# Forme Hammersley (référence)
# ----------------------------
t = np.linspace(0, np.pi, 300)
R_in = 1.0
R_out = 4 / np.pi

x1 = R_out * np.cos(t)
y1 = R_out * np.sin(t)
x2 = R_in * np.cos(t[::-1])
y2 = R_in * np.sin(t[::-1])

x_shape = np.concatenate([x1, x2])
y_shape = np.concatenate([y1, y2])

# ----------------------------
# TEST DE ROTATION
# ----------------------------
plt.figure(figsize=(7,7))

blocked = False
max_dist = 0

for a in np.linspace(0, 90, 360):
    xr, yr = rotate(x_shape, y_shape, a)

    # distance maximale au pivot (ligne rouge)
    dist = np.max(np.sqrt(xr**2 + yr**2))
    max_dist = max(max_dist, dist)

    if collision(xr, yr):
        blocked = True
        plt.fill(xr, yr, 'red', alpha=0.3)
        break
    else:
        if a % 10 == 0:
            plt.fill(xr, yr, 'blue', alpha=0.08)

# murs
plt.plot([-1, -1], [0, 2], 'k', lw=3)
plt.plot([-1, 2], [1, 1], 'k', lw=3)
plt.plot([0, 0], [-1, 1], 'k', lw=3)
plt.plot([0, 1], [0, 0], 'k', lw=3)

plt.plot(0, 0, 'ro')
plt.title(f"Succès: {not blocked}\nDistance max au pivot ≈ {max_dist:.3f} m")
plt.gca().set_aspect('equal')
plt.grid(True)
plt.show()
