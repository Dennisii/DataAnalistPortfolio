import numpy as np
import matplotlib.pyplot as plt

# ========================
# 1 Datos del problema
# ========================
P_Google = 0.60
P_Facebook = 0.40

P_Compra_Google = 0.05
P_Compra_Facebook = 0.03

# ========================
# 2 Probabilidad teórica (fórmula de la probabilidad total)
# ========================
P_Compra_Total = (P_Google * P_Compra_Google) + (P_Facebook * P_Compra_Facebook)

# ========================
# 3 Simulación base con NumPy
# ========================
np.random.seed(42)
n_visitas = 100_000

fuentes = np.random.choice([1, 0], size=n_visitas, p=[P_Google, P_Facebook])
compras = np.zeros(n_visitas, dtype=int)

compras[fuentes == 1] = np.random.rand(np.sum(fuentes == 1)) < P_Compra_Google
compras[fuentes == 0] = np.random.rand(np.sum(fuentes == 0)) < P_Compra_Facebook

P_Compra_Simulada = compras.mean()

# ========================
# 4 Simulación Monte Carlo
# ========================
n_simulaciones = 500
resultados = []

for _ in range(n_simulaciones):
    fuentes_mc = np.random.choice([1, 0], size=n_visitas, p=[P_Google, P_Facebook])
    compras_mc = np.zeros(n_visitas, dtype=int)
    compras_mc[fuentes_mc == 1] = np.random.rand(np.sum(fuentes_mc == 1)) < P_Compra_Google
    compras_mc[fuentes_mc == 0] = np.random.rand(np.sum(fuentes_mc == 0)) < P_Compra_Facebook
    resultados.append(compras_mc.mean())

resultados = np.array(resultados)

# ========================
# 5 Gráfico comparativo
# ========================
plt.style.use("seaborn-v0_8-whitegrid")
fig, axs = plt.subplots(1, 2, figsize=(14, 5))

# --- Barra comparativa ---
colores = ["#2E86AB", "#F6AE2D"]
barras = axs[0].bar(
    ["Teórica", "Simulada"],
    [P_Compra_Total * 100, P_Compra_Simulada * 100],
    color=colores,
    edgecolor="black",
    linewidth=1.2,
    width=0.5
)

axs[0].axhline(P_Compra_Total * 100, color="red", linestyle="--", linewidth=1.5, alpha=0.7, label="Referencia Teórica")
axs[0].set_ylabel("Probabilidad de Compra (%)", fontsize=12, fontweight="bold")
axs[0].set_title("Probabilidad Total: Teoría vs Simulación", fontsize=14, fontweight="bold", pad=15)
axs[0].legend()

for barra in barras:
    altura = barra.get_height()
    axs[0].text(
        barra.get_x() + barra.get_width()/2, altura + 0.2,
        f"{altura:.2f}%",
        ha="center", va="bottom",
        fontsize=11, fontweight="bold",
        color="#333333"
    )

axs[0].yaxis.grid(True, linestyle="--", alpha=0.7)
for spine in ["top", "right"]:
    axs[0].spines[spine].set_visible(False)

# --- Histograma Monte Carlo ---
axs[1].hist(resultados * 100, bins=20, color="#6A4C93", edgecolor="black", alpha=0.85)
axs[1].axvline(P_Compra_Total * 100, color="red", linestyle="--", linewidth=1.5, label="Referencia Teórica")
axs[1].set_title("Variabilidad por Azar (Monte Carlo)", fontsize=14, fontweight="bold", pad=15)
axs[1].set_xlabel("Probabilidad de Compra (%)", fontsize=12)
axs[1].set_ylabel("Frecuencia", fontsize=12)
axs[1].legend()
axs[1].yaxis.grid(True, linestyle="--", alpha=0.7)
for spine in ["top", "right"]:
    axs[1].spines[spine].set_visible(False)

plt.tight_layout()
plt.show()

# ========================
# 6 Resumen tipo reporte
# ========================
compradores_estimados = n_visitas * P_Compra_Total
compradores_simulados = compras.sum()
variacion_simulada = (P_Compra_Simulada - P_Compra_Total) * 100

print("RESUMEN EJECUTIVO")
print("="*40)
print(f"Probabilidad teórica calculada: {P_Compra_Total:.4f} ({P_Compra_Total*100:.2f}%)")
print(f"Probabilidad simulada: {P_Compra_Simulada:.4f} ({P_Compra_Simulada*100:.2f}%)")
print(f"Visitantes: {n_visitas:,}")
print(f"Compradores esperados (teoría): {compradores_estimados:,.0f}")
print(f"Compradores reales (simulación): {compradores_simulados:,}")
print(f"Diferencia entre teoría y simulación: {variacion_simulada:.2f} puntos porcentuales")
print("\nInterpretación:")
print("- La probabilidad teórica se calcula con la fórmula de probabilidad total considerando las dos fuentes de tráfico.")
print("- La simulación muestra que, debido al azar, el resultado real puede variar ligeramente.")
print("- El histograma Monte Carlo demuestra que incluso con las mismas probabilidades iniciales, los resultados fluctúan.")
