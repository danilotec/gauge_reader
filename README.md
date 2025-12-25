# gauge_reader

📟 **Leitura automática de manômetros analógicos** usando **YOLO (detecção)** e **regressão de ângulo**, com conversão para **porcentagem, pressão e volume**.

Projetado para aplicações **industriais, hospitalares e IoT**, eliminando a necessidade de leitura manual.

---

## ✨ Principais recursos

* Detecção do manômetro via **YOLOv8**
* Regressão precisa do **ângulo do ponteiro**
* Conversão de ângulo → porcentagem
* Cálculo de **pressão** e **volume**
* API simples e reutilizável
* Compatível com pipelines de visão computacional

---

## 📦 Instalação

```bash
git clone https://github.com/danilotec/gauge_reader.git
pip install -r gauge_reader/requirements.txt
```

> ⚠️ O pacote **inclui modelos treinados**.
> Porem você pode fornecer seus próprios arquivos `.pt`.

---

## 🔧 Requisitos

* Python **3.9+**
* PyTorch
* Ultralytics (YOLOv8)
* OpenCV
* NumPy

---

## 📁 Arquivos necessários

Você precisa informar:

* **Modelo YOLO treinado** (`best.pt`)
* **Modelo de regressão de ângulo** (`regressor.pt`)

Exemplo:

```
gauge_reader/models/best.pt
gauge_reader/models/regressor.pt
```

---

## 🚀 Uso rápido

### Exemplo completo

```python
from gauge_reader.reader import Manometer, angle_to_percent, get_volume, CropImage, YOLO

yolo = YOLO("gauge_reader/models/best.pt")

def get_crop():
    crop = CropImage(
        yolo=yolo,
        imput_dir='./',
        output_dir='./crops'
    )
    crop.generate_crop('image2.jpg')


def get_vol():
    man = Manometer(
        yolo=yolo,
        regressor="gauge_reader/models/regressor.pt"
    )

    angles = man.get_angle(
        filename='./crops/image2_0.jpg'
    )


    if angles:
        
        print("ângulos:", angles)
        
        man_volume = angles[0]

        vol_percent = angle_to_percent(man_volume)
        print("porcentagem volume:", round(vol_percent, 2))

        print("volume:", round(get_volume(vol_percent, 800), 2))

```

---

## 🧠 API

### `Manometer`

Classe principal responsável pela inferência.

```python
Manometer(model: str, regressor: str)
```

**Parâmetros**

* `model`: caminho para o modelo YOLO (`.pt`)
* `regressor`: caminho para o modelo de regressão de ângulo (`.pt`)

---

### `get_angle()`

```python
angles = man.get_angle(filename: str)
```

**Retorno**

```python
[angulo_1, angulo_2]
```

* Valores em **graus**
* Retorna `None` se não detectar o manômetro

---

### `angle_to_percent()`

```python
percent = angle_to_percent(angle)
```

Converte o ângulo do ponteiro em **porcentagem (0–100%)**, considerando a escala do manômetro.

---

### `get_volume()`

```python
value = get_volume(percent, max_value)
```

Usado para calcular:

* Pressão (ex: `25 bar`)
* Volume (ex: `800 L`)

---

## 🏭 Casos de uso

* Monitoramento de oxigênio hospitalar
* Leitura remota de tanques pressurizados
* Automação industrial
* Integração com ESP32, APIs REST e MQTT
* Dashboards e sistemas SCADA

---

## ⚠️ Observações importantes

* O modelo YOLO deve ser **treinado especificamente** para seu tipo de manômetro apesar de ja pussuir uma boa base
* A regressão depende de **imagens bem enquadradas**
* A escala angular precisa estar configurada corretamente no projeto

---

## 📄 Licença

MIT License

---
