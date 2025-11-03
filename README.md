# Laboratorio de Señales electromiográficas EMG
Universidad Militar Nueva Granada
Asignatura: Laboratorio de Procesamiento Digital de Señales
Estudiantes: [Maria Jose Peña Velandia, Joshara Valentina Palacios, Lina Marcela Pabuena]
Fecha: Noviembre 2025
Título de la práctica: Señales electromiográficas EMG 
## PARTE A – Captura de la señal emulada
Se utilizó un generador de señales para emular la actividad eléctrica muscular (EMG). En el canal 1 se configuró el modo Arbitrary (Arb) con la función EMG, estableciendo una frecuencia portadora de 1 Hz y una frecuencia de modulación de 1 MHz en modo AM (amplitud modulada), con una profundidad de modulación del 1 %. La amplitud se fijó en 5 Vpp generando una señal que simula contracciones musculares periódicas.


<img width="856" height="387" alt="Captura de pantalla 2025-11-02 222632" src="https://github.com/user-attachments/assets/886aa137-89f8-435b-ad81-fded40fb52ad" />



Se segmento la señal en cinco contracciones simuladas lo que permite analizar individualmente cada evento muscular dentro del registro completo. En el contexto de la señal EMG emulada, cada contracción representa una activación independiente del músculo, con posibles variaciones en frecuencia, amplitud y duración.
Dividir la señal total en segmentos facilita el cálculo de parámetros específicos (como frecuencia media y frecuencia mediana) para cada contracción, lo que permite comparar su comportamiento y observar cómo evoluciona la actividad muscular simulada a lo largo del tiempo.

Posteriormente se realiza el cálculo de la frecuencia media y la frecuencia mediana para cada contracción lo cual permite caracterizar el contenido espectral de la señal EMG, es decir cómo se distribuye la energía en las diferentes frecuencias. Al calcular ambas frecuencias para cada una de las cinco contracciones simuladas, se obtiene una descripción cuantitativa de cómo varía el comportamiento frecuencial de la señal EMG a lo largo del tiempo. Esto permite analizar si existen cambios progresivos en la activación simulada del músculo o si la señal mantiene una respuesta estable en todas las contracciones.

En la tabla se presentan los valores de frecuencia media y frecuencia mediana correspondientes a cinco contracciones musculares extraídas de la señal EMG. Estos parámetros reflejan la distribución de energía en el espectro de la señal y están directamente relacionados con la actividad eléctrica de las fibras musculares durante la contracción. 

<img width="496" height="185" alt="Captura de pantalla 2025-11-02 222943" src="https://github.com/user-attachments/assets/7cdc4b55-aebf-48fc-b5c2-70169b52e5ed" />


Se observa que la frecuencia media aumenta ligeramente desde aproximadamente 84.9 Hz en la primera contracción hasta 91.4 Hz en la quinta. De forma similar, la frecuencia mediana también muestra un incremento leve, pasando de 33.2 Hz a 35.1 Hz. Este comportamiento sugiere que no hay una disminución significativa en las frecuencias características de la señal, lo cual indica que no se evidencia un proceso claro de fatiga muscular dentro del intervalo analizado. En condiciones de fatiga, se esperaría una tendencia descendente en ambas frecuencias, ya que la conducción de los potenciales de acción en las fibras musculares se hace más lenta con el cansancio. Por el contrario, el aumento progresivo que se evidencia podría deberse a una mayor activación de unidades motoras o a una mejora en la sincronización de las mismas a medida que avanza el registro, reflejando un patrón de adaptación o reclutamiento progresivo más que de fatiga.


En la gráfica se observa la señal EMG completa, la cual presenta una serie de picos regulares que representan las contracciones musculares registradas a lo largo del tiempo. Esta señal fue segmentada en cinco partes iguales para analizar de manera separada cada contracción y calcular sus respectivas frecuencias medias y medianas, cuyos resultados se presentan en la tabla.

<img width="682" height="391" alt="image" src="https://github.com/user-attachments/assets/06104c98-86ea-4b8c-9f7d-b6f2bb6e9753" />



Los valores obtenidos muestran que la frecuencia media se mantiene en un rango de aproximadamente 84 a 91 Hz, mientras que la frecuencia mediana oscila entre 33 y 35 Hz. Estos resultados reflejan una señal relativamente estable en cuanto a contenido frecuencial, sin descensos notables entre contracciones consecutivas.

Desde el punto de vista fisiológico, esto sugiere que no hay evidencia de fatiga muscular significativa. En condiciones de fatiga, se esperaría una disminución progresiva de las frecuencias media y mediana, producto de la reducción en la velocidad de conducción de las fibras musculares y la menor sincronización de las unidades motoras.
En este caso, la constancia o leve aumento en las frecuencias indica que el músculo mantiene un rendimiento estable y una actividad eléctrica sostenida a lo largo del tiempo, lo cual podría deberse a una señal emulada o a un registro corto en el que no se alcanza un nivel de agotamiento fisiológico.



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

<img width="645" height="305" alt="image" src="https://github.com/user-attachments/assets/6b974b5d-a7ad-4692-9bc7-f485d7ea78ac" />

La señal se dividió en 5 segmentos de misma duración `np.array_split`cada uno representando un bloque de contracciones, en cada uno de los bloques se buscó la frecuencia media y la frecuencia mediana. Aquí la tabla:
En esta tabla podemos ver la variación de la frecuencia media que indica en 47.54 Hz y finaliza en 47.42 Hz, esto es una ligera reducción lo que indica una fatiga muscular, pero de baja magnitud, es decir, una fatiga leve, pero no sostenida, en cuanto a la frecuencia mediana Oscila entre 48.83 Hz y 39.06 Hz esta caída en la frecuencia es normal en una fatiga muscular, pero el posterior aumento es índice de una recuperación o una mejora en la postura que permitió la activación de nuevas fibras.

Esto en interpretación fisiológica tendríamos que la disminución deambas señales nos indican un descenso en la velocidad e la consucción de las fibras musculares y una menor activación de las motoneuronas rápidas, normal en la fatiga muscular localizada.

### f) Resultados
La siguiente gráfica es la frecuencia media en azul y la frecuencia mediana en naranja, en esta gráfica se aprecia el descenso de ambas señales entre los cortes 2 y 4 que son normales en la fatiga muscular, despues se puede ver la compensación del musculo que es una posible redistribución de la carga entre fibras activas o un ajuste en la fuerza aplicada.

En conjunto, los resultados confirman que el método implementado de filtro es capaz de detectar cambios fisiológicos pequeños en la actividad muscular.

<img width="686" height="394" alt="image" src="https://github.com/user-attachments/assets/7d8cc55d-c0f4-4a2c-a2d6-ba25c954b457" />



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

### DIAGRAMA DE FLUJO PARTE B 
![My First Board](https://github.com/user-attachments/assets/e58a11de-7140-4272-b225-38ec7cbee7c1)
### DIAGRAMA DE FLUJO PARTE C 
<img width="904" height="1280" alt="image" src="https://github.com/user-attachments/assets/7def59b8-2302-4c6d-baa2-dac3692890dc" />

