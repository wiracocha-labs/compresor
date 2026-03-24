# 🗜️ wiracocha-compress

> Compresor de alta eficiencia en Rust, optimizado específicamente para pesos de modelos de IA. La pieza que hace posible una red de IA descentralizada en hardware modesto.

[![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![Language: Rust](https://img.shields.io/badge/Language-Rust-orange.svg)](https://www.rust-lang.org/)
[![Status: Planeado](https://img.shields.io/badge/Status-Planeado-red.svg)]()
[![Org: Wiracocha Labs](https://img.shields.io/badge/Org-Wiracocha%20Labs-purple.svg)](https://github.com/wiracocha-labs)

---

## 🧭 ¿Por qué existe este proyecto?

Los compresores genéricos como ZIP, gzip o incluso zstd están optimizados para texto, código y archivos de uso general.

Los pesos de un modelo de IA son algo completamente distinto: **millones de números flotantes** con patrones matemáticos específicos que los compresores genéricos no aprovechan.

```
Modelo de 10GB con ZIP   →  ~9.5GB  (5% reducción)
Modelo de 10GB con zstd  →  ~7GB    (30% reducción)
Modelo de 10GB con       →  ???     (objetivo: >60% reducción)
wiracocha-compress           optimizando para estructura de redes neuronales
```

Sin una compresión eficiente, una red de IA descentralizada no puede correr en hardware modesto como Raspberry Pi. Este compresor es la pieza que lo hace posible.

---

## 🎯 Objetivos técnicos

### Lo que construye este repositorio

```
wiracocha-compress/
├── Quantización de pesos          → float32 → int8 (4x reducción inmediata)
├── Compresión de deltas           → solo envía cambios, no el modelo completo
├── Algoritmo LZ optimizado        → para patrones en datos numéricos flotantes
├── Codificación Huffman           → optimizada para distribución de pesos IA
└── API simple para quipu-ipfs     → comprimir antes de enviar, descomprimir al recibir
```

### Métricas objetivo

| Métrica | Meta |
|---|---|
| Ratio de compresión | >60% en modelos típicos |
| Velocidad de compresión | >500 MB/s en hardware modesto |
| Velocidad de descompresión | >1 GB/s |
| Overhead de memoria | <50 MB |
| Compatibilidad | Raspberry Pi 4+ |

---

## 🧠 ¿Cómo funciona?

### Paso 1 — Quantización

Antes de comprimir, reducimos la precisión matemática de los pesos:

```
Sin quantizar:   0.87394857  →  float32  →  4 bytes por valor
Quantizado:      87          →  int8     →  1 byte por valor

Resultado: 4x menos datos antes de comprimir
Pérdida de calidad: mínima en modelos bien entrenados
```

### Paso 2 — Compresión de deltas

En una red de nodos que entrenan en paralelo, no necesitas enviar el modelo completo cada vez:

```
Nodo A entrena → calcula qué cambió → envía solo el delta
Nodo B recibe delta → aplica cambio → modelo actualizado

En lugar de enviar 10GB → envías 50MB de cambios
```

### Paso 3 — Compresión optimizada para flotantes

Los pesos de modelos tienen patrones que los algoritmos genéricos ignoran. `wiracocha-compress` los aprovecha.

---

## 🔗 Relación con quipu-ipfs

Este compresor es una dependencia directa de [quipu-ipfs](https://github.com/wiracocha-labs/quipu-ipfs):

```
Nodo A                              Nodo B
  │                                   │
  ├── wiracocha-compress              │
  │   └── quantizar + comprimir       │
  │                                   │
  │──── enviar por quipu-ipfs ───────>│
  │                                   ├── wiracocha-compress
  │                                   │   └── descomprimir + dequantizar
  │                                   │
  │                                   └── modelo actualizado ✓
```

---

## ⚙️ Stack técnico

| Componente | Tecnología | Por qué |
|---|---|---|
| Lenguaje | **Rust** | Sin garbage collector, rendimiento predecible en nodos 24/7 |
| Algoritmo base | **LZ77 propio** | Optimizado para datos numéricos, no texto |
| Quantización | **Implementación propia** | Integrada con el pipeline de compresión |
| Serialización | **serde + bincode** | Formato binario eficiente |

---

## 🚀 Estado actual

- [x] Repositorio creado y licenciado (AGPL-3.0)
- [ ] Implementación de quantización float32 → int8
- [ ] Algoritmo LZ77 base en Rust
- [ ] Codificación Huffman para pesos
- [ ] Compresión de deltas entre versiones de modelos
- [ ] Benchmarks contra zstd y gzip
- [ ] API de integración con quipu-ipfs
- [ ] Tests en Raspberry Pi 4 y 5

---

## 🤝 Cómo contribuir

Este proyecto necesita personas con conocimientos en:

### Áreas donde más se necesita ayuda

- **Algoritmos de compresión** → LZ77, Huffman, compresión de datos numéricos
- **Machine Learning / matemáticas** → quantización, distribución de pesos en redes neuronales
- **Rust avanzado** → optimización de rendimiento, unsafe para operaciones críticas
- **Benchmarking** → comparativas de rendimiento en hardware variado
- **Embedded / ARM** → optimización para Raspberry Pi y hardware modesto

### Para empezar

```bash
# Clona el repositorio
git clone https://github.com/wiracocha-labs/wiracocha-compress.git
cd wiracocha-compress

# Asegúrate de tener Rust instalado
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Compila el proyecto
cargo build

# Corre los tests
cargo test
```

Revisa los [Issues abiertos](https://github.com/wiracocha-labs/wiracocha-compress/issues) para ver en qué puedes ayudar. Si tienes preguntas, abre una [Discussion](https://github.com/wiracocha-labs/wiracocha-compress/discussions).

---

## 📚 Referencias y lectura recomendada

- [Zstandard — Real-Time Data Compression Algorithm](https://facebook.github.io/zstd/)
- [GPTQ: Accurate Post-Training Quantization](https://arxiv.org/abs/2210.17323)
- [LLM.int8(): 8-bit Matrix Multiplication for Transformers](https://arxiv.org/abs/2208.07339)
- [The Rust Performance Book](https://nnethercote.github.io/perf-book/)

---

## 🧩 Ecosistema Wiracocha Labs

| Repositorio | Descripción | Estado |
|---|---|---|
| **quipu-ipfs** | Red P2P descentralizada | 🔨 En construcción |
| **wiracocha-compress** | Compresor para pesos de modelos IA *(este repo)* | 📋 Planeado |

---

## 📜 Licencia

Este proyecto está licenciado bajo [GNU Affero General Public License v3.0](LICENSE).

Cualquier modificación o uso en producción también debe ser de código abierto. Nadie puede tomar este trabajo y cerrarlo.

---

## 🌍 Sobre Wiracocha Labs

Organización de investigación y desarrollo open source enfocada en descentralización, privacidad y nuevas formas de comunicación digital.

[GitHub](https://github.com/wiracocha-labs) · [quipu-ipfs](https://github.com/wiracocha-labs/quipu-ipfs)
