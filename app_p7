import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from PIL import Image
from io import BytesIO
import requests

# --- Paleta de Colores ---
# Definición de colores en formato RGB (0-1) para Matplotlib
color_primario_1_rgb = (14/255, 69/255, 74/255) # 0E454A (Oscuro)
color_primario_2_rgb = (31/255, 255/255, 95/255) # 1FFF5F (Verde vibrante)
color_primario_3_rgb = (255/255, 255/255, 255/255) # FFFFFF (Blanco)

# Colores del logo de Sustrend para complementar
color_sustrend_1_rgb = (0/255, 155/255, 211/255) # 009BD3 (Azul claro)
color_sustrend_2_rgb = (0/255, 140/255, 207/255) # 008CCF (Azul medio)
color_sustrend_3_rgb = (0/255, 54/255, 110/255) # 00366E (Azul oscuro)

# Selección de colores para los gráficos
colors_for_charts = [color_primario_1_rgb, color_primario_2_rgb, color_sustrend_1_rgb, color_sustrend_3_rgb]

# --- Configuración de la página de Streamlit ---
st.set_page_config(layout="wide")

st.title('✨ Visualizador de Impactos - Proyecto P7')
st.subheader('Producto Fitosanitario a partir de Quillay (FungiBlock)')
st.markdown("""
    Ajusta los parámetros para explorar cómo las proyecciones de impacto ambiental y económico del proyecto
    varían con diferentes escenarios de volumen de producción, factor de sustitución de agroquímicos y porcentaje de materias primas secundarias.
""")

# --- 1. Datos del Proyecto (Línea Base) ---
# Datos línea base extraídos de Ficha_Tecnica_P7.docx o definidos para ejemplo
# Para P7:
# - Factor de emisión promedio de residuos orgánicos: 52 kg CH₄/ton
# - GWP (potencial de calentamiento global) CH₄: 28 (IPCC AR5)
# - Producción de bioinsumo: 2000 kg/año (asumiendo para ejemplo)
# - % de inclusión de materias primas secundarias: 40% (del avance del proyecto)
# - Material valorizado: 2000 kg * 0.40 = 800 kg
# - Ingresos generados: No hay un valor base directo en la ficha, se usará un estimado para la visualización.
# - Se asume una reducción de 1 kg de agroquímico por kg de bioinsumo producido para línea base
# La línea base de "agroquímicos evitados" e "ingresos generados" se establece como 0 antes de la implementación
# Material valorizado: Se indica "tasa preliminar de inclusión de materias primas secundarias del 40%",
# Si asumimos una producción base para esa tasa, por ejemplo, 2000 kg, entonces 2000 * 0.40 = 800 kg de material valorizado base.
# Para agroquímicos evitados, la ficha dice "disminución significativa en el uso de fungicidas sintéticos".
# Si asumimos que sin el proyecto no se evitan agroquímicos, la línea base es 0.
# Si el ejemplo en la ficha técnica P3 de GEI evitados por rellenos sanitarios es relevante:
# Material desviado: 200 ton -> 291.2 tCO2e. Esto es para residuos, no para producto final directamente.
# La ficha P7 menciona "disminución significativa en el uso de fungicidas sintéticos" y "incremento en el porcentaje de materias primas secundarias".
# Usaremos una línea base para material valorizado y 0 para agroquímicos evitados e ingresos generados.

base_agroquimicos_evitados = 0 # No se evitan agroquímicos sin el proyecto
base_material_valorizado = 800 # 40% de 2000 kg de producción (ejemplo de la ficha)
base_ingresos_generados = 0 # No se generan ingresos por la solución sin el proyecto

# --- 2. Widgets Interactivos para Parámetros (Streamlit) ---
st.sidebar.header('Parámetros de Simulación')

volumen_produccion = st.sidebar.slider(
    'Volumen total producido de FungiBlock (kg/año):',
    min_value=1000,
    max_value=20000,
    value=10000,
    step=1000,
    help="Volumen anual del bioinsumo producido."
)

factor_sustitucion = st.sidebar.slider(
    'Factor de sustitución de agroquímicos (kg agroquímico/kg FungiBlock):',
    min_value=0.1,
    max_value=1.0,
    value=0.2, # Valor basado en el ejemplo del Colab original
    step=0.05,
    help="Cantidad de agroquímico sintético que se evita por cada kg de FungiBlock producido."
)

porcentaje_materias_secundarias = st.sidebar.slider(
    'Porcentaje de materias primas secundarias en FungiBlock (%):',
    min_value=40.0, # Según la ficha, "tasa preliminar de inclusión de materias primas secundarias del 40%"
    max_value=70.0,
    value=60.0, # Valor de ejemplo, se puede ajustar para explorar escenarios mejorados
    step=1.0,
    format='%.1f%%',
    help="Porcentaje de la masa del producto final que proviene de materias primas secundarias (residuos)."
)

precio_venta = st.sidebar.slider(
    'Precio de venta de FungiBlock (CLP/kg):',
    min_value=2000,
    max_value=10000,
    value=3000, # Valor del ejemplo del Colab original
    step=100,
    help="Precio de venta estimado por kilogramo de FungiBlock."
)

# --- 3. Cálculos de Indicadores ---
agroquimicos_evitados = volumen_produccion * factor_sustitucion
material_valorizado = volumen_produccion * (porcentaje_materias_secundarias / 100) # Convertir % a decimal
ingresos_generados = volumen_produccion * precio_venta

# Se mantienen estos como métricas fijas o se podrían hacer variables de entrada si se desea simular
alianzas_comerciales = 5
simbiosis_industrial = 6

st.header('Resultados Proyectados Anuales:')

col1, col2, col3 = st.columns(3)

with col1:
    st.metric(label="🧪 **Agroquímicos Sintéticos Evitados**", value=f"{agroquimicos_evitados:.2f} kg")
    st.caption("Cantidad de agroquímicos sintéticos que se dejan de usar.")
with col2:
    st.metric(label="♻️ **Material Valorizado**", value=f"{material_valorizado:.2f} kg")
    st.caption("Cantidad de residuos transformados en valor.")
with col3:
    st.metric(label="💰 **Ingresos Generados**", value=f"CLP {ingresos_generados:,.0f}")
    st.caption("Ingresos brutos estimados por la venta del bioinsumo.")

col4, col5 = st.columns(2)

with col4:
    st.metric(label="🤝 **Nuevas Alianzas Comerciales**", value=f"{alianzas_comerciales}")
    st.caption("Número de nuevas alianzas para la cadena de valor circular.")
with col5:
    st.metric(label="🔗 **Instancias de Simbiosis Industrial**", value=f"{simbiosis_industrial}")
    st.caption("Número de nuevas conexiones de simbiosis industrial generadas.")

st.markdown("---")

st.header('📊 Análisis Gráfico de Impactos')

# --- Visualización (Gráficos 2D con Matplotlib) ---
# Creamos una figura con 3 subplots (2D)
fig, (ax1, ax2, ax3) = plt.subplots(1, 3, figsize=(20, 7), facecolor=color_primario_3_rgb)
fig.patch.set_facecolor(color_primario_3_rgb)

# Definición de etiquetas y valores para los gráficos de barras 2D
labels = ['Línea Base', 'Proyección']
bar_width = 0.6
x = np.arange(len(labels))

# --- Gráfico 1: Agroquímicos Evitados (kg/año) ---
agroquimicos_values = [base_agroquimicos_evitados, agroquimicos_evitados]
bars1 = ax1.bar(x, agroquimicos_values, width=bar_width, color=[colors_for_charts[0], colors_for_charts[1]])
ax1.set_ylabel('Kilogramos/año', fontsize=12, color=colors_for_charts[3])
ax1.set_title('Agroquímicos Sintéticos Evitados', fontsize=14, color=colors_for_charts[3], pad=20)
ax1.set_xticks(x)
ax1.set_xticklabels(labels, rotation=15, color=colors_for_charts[0])
ax1.yaxis.set_tick_params(colors=colors_for_charts[0])
ax1.spines['top'].set_visible(False)
ax1.spines['right'].set_visible(False)
ax1.tick_params(axis='x', length=0)
max_val_agroquimicos = max(agroquimicos_values)
ax1.set_ylim(bottom=0, top=max(max_val_agroquimicos * 1.15, 1)) # Asegura al menos 1kg si es muy bajo
for bar in bars1:
    yval = bar.get_height()
    ax1.text(bar.get_x() + bar.get_width()/2, yval + 0.05 * yval, f"{yval:,.2f}", ha='center', va='bottom', color=colors_for_charts[0])

# --- Gráfico 2: Material Valorizado (kg/año) ---
material_values = [base_material_valorizado, material_valorizado]
bars2 = ax2.bar(x, material_values, width=bar_width, color=[colors_for_charts[2], colors_for_charts[3]])
ax2.set_ylabel('Kilogramos/año', fontsize=12, color=colors_for_charts[0])
ax2.set_title('Material Valorizado', fontsize=14, color=colors_for_charts[3], pad=20)
ax2.set_xticks(x)
ax2.set_xticklabels(labels, rotation=15, color=colors_for_charts[0])
ax2.yaxis.set_tick_params(colors=colors_for_charts[0])
ax2.spines['top'].set_visible(False)
ax2.spines['right'].set_visible(False)
ax2.tick_params(axis='x', length=0)
max_val_material = max(material_values)
ax2.set_ylim(bottom=0, top=max(max_val_material * 1.15, 100)) # 15% de margen superior o mínimo 100 kg
for bar in bars2:
    yval = bar.get_height()
    ax2.text(bar.get_x() + bar.get_width()/2, yval + 0.05 * yval, f"{yval:,.2f}", ha='center', va='bottom', color=colors_for_charts[0])

# --- Gráfico 3: Ingresos Generados (CLP/año) ---
ingresos_values = [base_ingresos_generados, ingresos_generados]
bars3 = ax3.bar(x, ingresos_values, width=bar_width, color=[colors_for_charts[1], colors_for_charts[0]])
ax3.set_ylabel('CLP/año', fontsize=12, color=colors_for_charts[3])
ax3.set_title('Ingresos Generados', fontsize=14, color=colors_for_charts[3], pad=20)
ax3.set_xticks(x)
ax3.set_xticklabels(labels, rotation=15, color=colors_for_charts[0])
ax3.yaxis.set_tick_params(colors=colors_for_charts[0])
ax3.spines['top'].set_visible(False)
ax3.spines['right'].set_visible(False)
ax3.tick_params(axis='x', length=0)
max_val_ingresos = max(ingresos_values)
ax3.set_ylim(bottom=0, top=max(max_val_ingresos * 1.15, 100000)) # 15% de margen superior o mínimo 100,000 CLP
for bar in bars3:
    yval = bar.get_height()
    ax3.text(bar.get_x() + bar.get_width()/2, yval + 0.05 * yval, f"CLP {yval:,.0f}", ha='center', va='bottom', color=colors_for_charts[0])

plt.tight_layout(rect=[0, 0.05, 1, 0.95])
st.pyplot(fig)

# --- Funcionalidad de descarga de cada gráfico ---
st.markdown("---")
st.subheader("Descargar Gráficos Individualmente")

# Función auxiliar para generar el botón de descarga
def download_button(fig, filename_prefix, key):
    buf = BytesIO()
    fig.savefig(buf, format="png", bbox_inches="tight", dpi=300)
    st.download_button(
        label=f"Descargar {filename_prefix}.png",
        data=buf.getvalue(),
        file_name=f"{filename_prefix}.png",
        mime="image/png",
        key=key
    )

# Crear figuras individuales para cada gráfico para poder descargarlas
# Figura 1: Agroquímicos Evitados
fig_agroquimicos, ax_agroquimicos = plt.subplots(figsize=(8, 6), facecolor=color_primario_3_rgb)
ax_agroquimicos.bar(x, agroquimicos_values, width=bar_width, color=[colors_for_charts[0], colors_for_charts[1]])
ax_agroquimicos.set_ylabel('Kilogramos/año', fontsize=12, color=colors_for_charts[3])
ax_agroquimicos.set_title('Agroquímicos Sintéticos Evitados', fontsize=14, color=colors_for_charts[3], pad=20)
ax_agroquimicos.set_xticks(x)
ax_agroquimicos.set_xticklabels(labels, rotation=15, color=colors_for_charts[0])
ax_agroquimicos.yaxis.set_tick_params(colors=colors_for_charts[0])
ax_agroquimicos.spines['top'].set_visible(False)
ax_agroquimicos.spines['right'].set_visible(False)
ax_agroquimicos.tick_params(axis='x', length=0)
ax_agroquimicos.set_ylim(bottom=0, top=max(max_val_agroquimicos * 1.15, 1))
for bar in ax_agroquimicos.patches:
    yval = bar.get_height()
    ax_agroquimicos.text(bar.get_x() + bar.get_width()/2, yval + 0.05 * yval, f"{yval:,.2f}", ha='center', va='bottom', color=colors_for_charts[0])
plt.tight_layout()
download_button(fig_agroquimicos, "Agroquimicos_Evitados", "download_agroquimicos")
plt.close(fig_agroquimicos)

# Figura 2: Material Valorizado
fig_material, ax_material = plt.subplots(figsize=(8, 6), facecolor=color_primario_3_rgb)
ax_material.bar(x, material_values, width=bar_width, color=[colors_for_charts[2], colors_for_charts[3]])
ax_material.set_ylabel('Kilogramos/año', fontsize=12, color=colors_for_charts[0])
ax_material.set_title('Material Valorizado', fontsize=14, color=colors_for_charts[3], pad=20)
ax_material.set_xticks(x)
ax_material.set_xticklabels(labels, rotation=15, color=colors_for_charts[0])
ax_material.yaxis.set_tick_params(colors=colors_for_charts[0])
ax_material.spines['top'].set_visible(False)
ax_material.spines['right'].set_visible(False)
ax_material.tick_params(axis='x', length=0)
ax_material.set_ylim(bottom=0, top=max(max_val_material * 1.15, 100))
for bar in ax_material.patches:
    yval = bar.get_height()
    ax_material.text(bar.get_x() + bar.get_width()/2, yval + 0.05 * yval, f"{yval:,.2f}", ha='center', va='bottom', color=colors_for_charts[0])
plt.tight_layout()
download_button(fig_material, "Material_Valorizado", "download_material")
plt.close(fig_material)

# Figura 3: Ingresos Generados
fig_ingresos, ax_ingresos = plt.subplots(figsize=(8, 6), facecolor=color_primario_3_rgb)
ax_ingresos.bar(x, ingresos_values, width=bar_width, color=[colors_for_charts[1], colors_for_charts[0]])
ax_ingresos.set_ylabel('CLP/año', fontsize=12, color=colors_for_charts[3])
ax_ingresos.set_title('Ingresos Generados', fontsize=14, color=colors_for_charts[3], pad=20)
ax_ingresos.set_xticks(x)
ax_ingresos.set_xticklabels(labels, rotation=15, color=colors_for_charts[0])
ax_ingresos.yaxis.set_tick_params(colors=colors_for_charts[0])
ax_ingresos.spines['top'].set_visible(False)
ax_ingresos.spines['right'].set_visible(False)
ax_ingresos.tick_params(axis='x', length=0)
ax_ingresos.set_ylim(bottom=0, top=max(max_val_ingresos * 1.15, 100000))
for bar in ax_ingresos.patches:
    yval = bar.get_height()
    ax_ingresos.text(bar.get_x() + bar.get_width()/2, yval + 0.05 * yval, f"CLP {yval:,.0f}", ha='center', va='bottom', color=colors_for_charts[0])
plt.tight_layout()
download_button(fig_ingresos, "Ingresos_Generados", "download_ingresos")
plt.close(fig_ingresos)


st.markdown("---")
st.markdown("### Información Adicional:")
st.markdown(f"- **Estado de Avance y Recomendaciones:** El Proyecto FungiBlock ha mostrado un progreso significativo en el desarrollo de un fitosanitario a base de extractos de Quillay y otros materiales naturales valorizados. Se han completado pruebas de laboratorio que validan el efecto fungicida de la solución frente a patógenos de interés agrícola. Se recomienda profundizar en la cuantificación de impactos ambientales y la escalabilidad del modelo de negocio.")

st.markdown("---")
# Texto de atribución centrado
st.markdown("<div style='text-align: center;'>Visualizador Creado por el equipo Sustrend SpA en el marco del Proyecto TT GREEN Foods</div>", unsafe_allow_html=True)

# Aumentar el espaciado antes de los logos
st.markdown("<br>", unsafe_allow_html=True)
st.markdown("<br>", unsafe_allow_html=True)

# --- Mostrar Logos ---
col_logos_left, col_logos_center, col_logos_right = st.columns([1, 2, 1])

with col_logos_center:
    sustrend_logo_url = "https://drive.google.com/uc?id=1vx_znPU2VfdkzeDtl91dlpw_p9mmu4dd"
    ttgreenfoods_logo_url = "https://drive.google.com/uc?id=1uIQZQywjuQJz6Eokkj6dNSpBroJ8tQf8"

    try:
        sustrend_response = requests.get(sustrend_logo_url)
        sustrend_response.raise_for_status()
        sustrend_image = Image.open(BytesIO(sustrend_response.content))

        ttgreenfoods_response = requests.get(ttgreenfoods_logo_url)
        ttgreenfoods_response.raise_for_status()
        ttgreenfoods_image = Image.open(BytesIO(ttgreenfoods_response.content))

        st.image([sustrend_image, ttgreenfoods_image], width=100)
    except requests.exceptions.RequestException as e:
        st.error(f"Error al cargar los logos desde las URLs. Por favor, verifica los enlaces: {e}")
    except Exception as e:
        st.error(f"Error inesperado al procesar las imágenes de los logos: {e}")

st.markdown("<div style='text-align: center; font-size: small; color: gray;'>Viña del Mar, Valparaíso, Chile</div>", unsafe_allow_html=True)

st.sidebar.markdown("---")
st.sidebar.markdown(f"<div style='text-align: center; font-size: smaller; color: gray;'>Versión del Visualizador: 1.0</div>", unsafe_allow_html=True)
st.sidebar.markdown(f"<div style='text-align: center; font-size: x-small; color: lightgray;'>Desarrollado con Streamlit</div>", unsafe_allow_html=True)
