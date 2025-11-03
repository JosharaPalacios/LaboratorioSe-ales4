# Laboratorio de Señales electromiográficas EMG
Universidad Militar Nueva Granada
Asignatura: Laboratorio de Procesamiento Digital de Señales
Estudiantes: [Maria Jose Peña Velandia, Joshara Valentina Palacios, Lina Marcela Pabuena]
Fecha: Noviembre 2025
Título de la práctica: Señales electromiográficas EMG 
## PARTE A – Captura de la señal emulada

## PARTE B - Captura de la señal de paciente

### a) Electrodos
Ya que no contábamos con el modulo de EMG, se implementó el modulo AD8232 de ECG para poder hacer la captura de la señal electromiografía superficial por medio de electrodos, aprovechando que este modulo tiene la capacidad de detectar el diferencial de potenciales eléctricos en la piel.
Los electrodos se colocaron de manera superficial sobre el brazo izquierdo de una paciente sana, naturalmente diestra de 19 años, siguiendo esta disposición:
- Electrodo verde (GND): Se ubicó sobre el codo, haciendo de este una referencia eléctrica.
- Electrodo rojo: sobre la parte proximal del musculo braquiorradial, cerca al codo.
- Electrodo amarillo: posición distal mas proximal a la muñeca.

  <img width="409" height="600" alt="image" src="https://github.com/user-attachments/assets/d2cce0eb-db82-445f-83fb-528671bf93db" />


El antebrazo izquierdo, al ser no dominante, presenta menor entrenamiento motor, lo que puede reflejarse en una menor amplitud de señal y una aparición más lenta de la fatiga. Lo cual en ese momento no teníamos conocimiento, pero es un dato importante para la amplitud de la señal.

### b) Adquisición de la señal
A continuación, vamos a hacer un recuento de los materiales que implementamos para la adquisición de nuestra señal:

- Modulo AD8232 de ECG
- Microcontrolador STM32 y ST- Link para la alimentación de 3,3 V
- DAQ con entradas análogas AI
- Protoboard y cables de conexión
- Computador con NI Package Manager y Python (Spyder / Anaconda)

Para la configuración del circuito se dio de esta manera:

- La alimentación de 3,3 V provino directamente del stm32 para evitar dañar el módulo con la alimentación natural de 5 V del DAQ
- El GND del modulo se conecto a la primera entrada del DAQ
- La salida OUTPUT del modulo se conectó a la siguiente entrada análoga del DAQ
- Antes de la captura de la señal se verificó mediante el Test Device del DAQ

La señal se adquirió en tiempo real mediante un código en Python, mientras la voluntaria realizaba las contracciones con la pelota antiestrés aproximadamente 50 por minuto hasta llegar a la fatiga que fue aproximadamente a las 460 contracciones.

### c) Filtro Pasa banda
Este filtrado tiene como objetivo eliminar las componentes que no corresponden a la actividad muscular.
- <20 Hz son por lo general respiración o movimientos de la voluntaria
- 450 Hz ruido natural de la corriente eléctrica

<img width="1010" height="393" alt="image" src="https://github.com/user-attachments/assets/6bd74926-3b6e-4716-9d25-eebc8b835611" />


El filtro implementado es de tipo Butterworth, este se implementó por su respuesta plana, sin ondulaciones y su atenuación progresiva, de cuarto orden ya que es el mejo para este caso para no generar desequilibrios.
El uso de `filtfilt` es para que se filtre sin que haya un desfase, manteniendo así la alineación temporal de la señal.

<img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/746ec064-09b1-45b7-9da6-1f85926fa794" />


### d) Transformada Rápida de Fourier (FFT)
Se implementó la FFT para convertir la señal del dominio del tiempo al dominio de la frecuencia, esto permitió identificar las secciones en donde se concentraba la energía muscular.
En señales EMG, la mayoría de energía se concentra entre 40 y 150 Hz, con otros picos que dependen del musculo y la contracción que se esté haciendo.

<img width="990" height="490" alt="image" src="https://github.com/user-attachments/assets/f3a5623b-c681-4998-85aa-03ee4975c92d" />


### e) Segmentación y cálculo de frecuencias

<img width="859" height="372" alt="image" src="https://github.com/user-attachments/assets/fc839fcf-1699-4fc5-99bc-30933c893d21" />

La señal se dividió en 5 segmentos de misma duración `np.array_split`cada uno representando un bloque de contracciones, en cada uno de los bloques se buscó la frecuencia media y la frecuencia mediana. Aquí la tabla:
En esta tabla podemos ver la variación de la frecuencia media que indica en 44.72 Hz y finaliza en 43.83 Hz, esto es una ligera reducción lo que indica una fatiga muscular, pero de baja magnitud, en cuanto a la frecuencia mediana es de 39.06 Hz lo que quiere decir que la resolución frecuencial fue demasiado baja.

Esto en interpretación fisiológica tendríamos que la frecuencia media esta relacionada con la velocidad y conducción que tienen las fibras musculares y las unidades motoras, a medida que el musculo se fatiga disminuye la contracción eléctrica, las fibras rápidas dejan de activarse y baja la frecuencia, nuestros resultados arrojan que las muestras no fueron suficientes para el análisis. 

### f) Resultados
El descenso de la frecuencia media nos indica una tendencia leve a la fatiga muscular, aunque no pronunciada, esto en cuanto al análisis, pero en realidad si se llegó a la fatiga muscular. En los músculos que no son dominantes como lo hicimos en nuestro caso, la fatiga es más lenta, sin embargo, en nuestro caso experimental, nuestra voluntaria llegó a la fatiga ya que la pelota antiestrés era bastante dura por lo cual también requería de fuerza, pero se puede apreciar que las contracciones iniciales fueron más potentes y al final disminuyeron.

<img width="686" height="394" alt="image" src="https://github.com/user-attachments/assets/8878e72c-de02-45bd-a7e8-aceff9c64432" />


### g) Parte Fisiológica
Debido a la repetición de contracciones se acumularon productos sub y metabólicos como lo son principalmente iones de hidrógeno, la reducción de ATP afectó a la propagación del potencial de acción por las fibras musculares (sarcolema)  y la disminución del potencial de membrana en la fibra muscular debido a la acumulación de potasio K+ extracelular.
Esta disminución en la velocidad de conducción es la que provoca el desplazamiento del EMG hacia frecuencias más bajas, por lo cual no se presencia una fatiga clara, esto puede ser porque el ejercicio haya sido de muy baja intensidad o de poca duración para inducir cambios metabólicos y de velocidad de conducción.

## PARTE C – Análisis espectral mediante FFT
En esta sección se aplicó la Transformada Rápida de Fourier (FFT) a cada contracción registrada en la señal EMG real capturada durante el laboratorio. El objetivo fue observar la evolución del contenido espectral para detectar la aparición de fatiga muscular.

### Código utilizado
<pre>
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
</pre>
### RESULTADOS
<img width="301" height="179" alt="image" src="https://github.com/user-attachments/assets/086af699-2f9a-485b-bfb2-4ba1d78e4060" />

### GRÁFICA – FFT DE PRIMERAS VS ÚLTIMAS CONTRACCIONES
<img width="1096" height="702" alt="image" src="https://github.com/user-attachments/assets/404ce025-26dd-46bc-8b03-d02d7de0ad3e" />

### ANÁLISIS E INTERPRETACIÓN
•	El pico espectral de las contracciones iniciales se ubicó alrededor de 60 Hz, mientras que en las últimas contracciones descendió hacia ~38–40 Hz.

•	Este desplazamiento hacia frecuencias más bajas representa una pérdida de contenido de alta frecuencia, lo cual es típico del proceso de fatiga muscular.

•	Fisiológicamente, la fatiga reduce la velocidad de conducción de las fibras musculares, provocando que los potenciales de acción sean más lentos y que la energía espectral se concentre en frecuencias menores.

### CONCLUSIONES
•	Se comprobó que la Transformada Rápida de Fourier (FFT) es una herramienta eficaz para analizar la evolución del contenido frecuencial de una señal EMG.

•	Se observó un desplazamiento del pico espectral hacia bajas frecuencias con el aumento del esfuerzo sostenido, lo cual confirma la presencia de fatiga muscular.

•	El análisis espectral resulta útil como herramienta diagnóstica y de monitoreo en electromiografía, permitiendo evaluar objetivamente el estado de fatiga de un músculo.

•	En futuras aplicaciones se recomienda complementar con frecuencia mediana y frecuencia media como métricas más robustas frente a ruido y variabilidad individual.


