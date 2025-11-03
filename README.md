# Laboratorio de Señales electromiográficas EMG
Universidad Militar Nueva Granada
Asignatura: Laboratorio de Procesamiento Digital de Señales
Estudiantes: [Maria Jose Peña Velandia, Joshara Valentina Palacios, Lina Marcela Pabuena]
Fecha: Noviembre 2025
Título de la práctica: Señales electromiográficas EMG 
## PARTE A – Captura de la señal emulada
## PARTE B - Captura de la señal de paciente
### a) Electrodos
En nuestro caso no contabamos c

## PARTE C – Análisis espectral mediante FFT
En esta sección se aplicó la Transformada Rápida de Fourier (FFT) a cada contracción registrada en la señal EMG real capturada durante el laboratorio. El objetivo fue observar la evolución del contenido espectral para detectar la aparición de fatiga muscular.

## Código utilizado

```python
from google.colab import drive 
drive.mount('/content/drive')

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from scipy.signal import butter, filtfilt

# 1. Cargar señal real desde Drive
ruta = '/content/drive/MyDrive/GITTHUB/Captura_1_REAL.txt'  
data = np.loadtxt(ruta, skiprows=1)

tiempo = data[:, 0]
voltaje = data[:, 1]

# 2. Calcular frecuencia de muestreo
fs = 1 / np.mean(np.diff(tiempo))
print(f"Frecuencia de muestreo estimada: {fs:.2f} Hz")

# 3. Filtrado pasa banda (20–450 Hz)
lowcut, highcut, order = 20, 450, 4
b, a = butter(order, [lowcut/(fs/2), highcut/(fs/2)], btype='band')
voltaje_filtrado = filtfilt(b, a, voltaje)

# 4. Segmentar la señal 
n_contr = 460         
segmentos = np.array_split(voltaje_filtrado, n_contr)

# 5. FFT por contracción + Pico espectral
picos = []

plt.figure(figsize=(13, 8))
for i, seg in enumerate(segmentos, start=1):
    N = len(seg)
    fft_vals = np.fft.fft(seg)
    fft_freq = np.fft.fftfreq(N, 1/fs)

    mask = fft_freq > 0
    frecs = fft_freq[mask]
    amplitud = np.abs(fft_vals[mask])

    # Calcular pico espectral
    pico = frecs[np.argmax(amplitud)]
    picos.append(pico)

    # Graficar solo 3 contracciones: primera, media y última
    if i in [1, int(n_contr/2), n_contr]:
        plt.plot(frecs, amplitud, label=f"Contracción {i} (pico={pico:.1f} Hz)")

plt.xlim([0, 450])
plt.title("FFT de primeras vs últimas contracciones")
plt.xlabel("Frecuencia (Hz)")
plt.ylabel("Amplitud")
plt.legend()
plt.grid()
plt.show()

# Tabla de picos para el informe
df_picos = pd.DataFrame({
    "Contracción": range(1, n_contr+1),
    "Pico espectral (Hz)": picos
})

display(df_picos.head())


