# MARU OS

## Sistema Operativo Cognitivo Offline impulsado por Gemma 4

> **M.A.R.U.** — *Memory · Agents · Reasoning · Universal*  
> La IA que sigue ayudando cuando Internet desaparece.

---

## Guion de pitch — 3 minutos (vender el producto)

| Tiempo | Qué decir / mostrar |
| :---: | :--- |
| **0:00–0:30** | *Hook:* “Imaginen un sismo en Lima. Cae Internet. ChatGPT muere. **MARU OS sigue vivo.**” Abrir `localhost:3000`, modo oscuro, sección **Impacto**. |
| **0:30–1:00** | *Problema:* La IA útil hoy exige nube, expone datos y falla en emergencia. En el Perú eso es crítico. |
| **1:00–1:45** | *Solución + Gemma:* “Gemma 4 **no es un plugin**: es el cerebro.” Mostrar chat con `gemma4:e2b-q4` local → respuesta streaming. Router elige agente y escala a 12B si hace falta. |
| **1:45–2:20** | *Demo multiagente + Perú:* **Tupac** (emergencia/IGP), **Pacha** (clima/SENAMHI), **Yaku** (Quechua/INEI), **Aya** (salud + pastillero), **Kipu** (código). Memoria RAG en Qdrant. |
| **2:20–2:45** | *Tracks & ODS:* Local/Edge + Agentes + Impacto Social con datos abiertos + multimodal (voz TTS/STT). ODS 3, 11 y 16. |
| **2:45–3:00** | *Cierre:* “Privacidad, resiliencia e impacto real. Gemma 4 como núcleo. MARU OS — el manantial de inteligencia que nunca se seca.” |

**Frase de cierre (memorizar):**  
*“No construimos otro chatbot. Construimos el sistema operativo cognitivo que Perú necesita cuando la nube no está.”*

---

## 1. Introducción

**MARU OS** es un Sistema Operativo Cognitivo **Offline-First** construido para demostrar que **Gemma 4** puede ser el núcleo de una plataforma de IA **privada, resiliente y accesible**.

A diferencia de los asistentes que envían cada consulta a la nube, MARU OS razona **en el dispositivo** del usuario (vía Ollama + modelos Gemma 4 cuantizados). Integra:

- **7 agentes especializados** coordinados por un *Cognitive Router*
- **Memoria local** (RAG en Qdrant + grafo Neo4j)
- **Base de conocimiento offline** (salud, legal, emergencia, clima, Perú, desarrollo)
- **Datos abiertos del Estado peruano** (IGP, SENAMHI / Open-Meteo, INEI, INDECI)
- **Voz** (STT del navegador + TTS Edge por agente)
- Interfaz completa (landing de impacto, onboarding, paneles de vida diaria)

El prototipo **funciona hoy** en Docker: frontend en `http://localhost:3000`, backend FastAPI en `http://localhost:8000`, Ollama en el host.

---

## 2. Contexto y motivación

La IA dominante es *cloud-first*:

1. **Dependencia de Internet** — sin red, no hay servicio  
2. **Riesgo de privacidad** — conversaciones y documentos salen del dispositivo  
3. **Desigualdad de acceso** — conexiones lentas o costosas en muchas regiones del Perú  

MARU OS invierte el modelo: **primero local, nube opcional**. La inteligencia permanece disponible en emergencias, viajes, zonas rurales o cuando el usuario decide no compartir datos.

---

## 3. La historia (escenario de impacto)

Un sismo de gran magnitud. Colapsan las comunicaciones. Miles de personas necesitan rutas, primeros auxilios y protocolos **ahora**.

Los asistentes en la nube se apagan.  
**MARU OS sigue operativo.**

Gemma 4 razona en local. **Tupac** guía con protocolos de emergencia. **Aya** orienta primeros auxilios (sin reemplazar al médico; prioriza SAMU 106). El corpus offline y la memoria local siguen disponibles sin conectividad.

Ese es el norte del proyecto: **IA que ayuda cuando más se necesita.**

---

## 4. Problema

| Desafío | Qué implica |
| :--- | :--- |
| Dependencia de Internet | La IA moderna falla en el peor momento |
| Privacidad | Datos sensibles viajan a terceros |
| Acceso desigual | Quien no tiene buena red queda fuera |

MARU OS ataca los tres con ejecución local, soberanía de datos y diseño para contextos reales del Perú.

---

## 5. Nuestra solución

Un **Sistema Operativo Cognitivo** (no un chatbot aislado) con:

1. **Gemma 4 como motor** — e2b-q4 (rápido), e4b-q4 (equilibrio), 12b-q4 (alta precisión); fallback cloud opcional  
2. **Cognitive Router** — clasifica la intención y activa el agente / modelo adecuado  
3. **Ecosistema de 7 agentes** — salud, bienestar, código, clima, emergencia, legal, cultura Perú  
4. **Memoria + KB offline** — embeddings `nomic-embed-text`, Qdrant, Neo4j, PDFs por especialidad  
5. **Datos abiertos peruanos** — clima, sismos, demografía, emergencia  
6. **UX completa** — landing de impacto social, tema claro/oscuro, voz, pastillero, notas, calendario, correo asistido  

---

## 6. ¿Por qué Gemma 4? *(rúbrica: uso efectivo / componente central)*

Gemma 4 **no es un complemento**: es el **cerebro** de MARU OS.

| Rol de Gemma 4 | Cómo se usa |
| :--- | :--- |
| Razonamiento de chat | Streaming local por agente |
| Enrutamiento cognitivo | Decide agente + complejidad |
| Traducción | Español ↔ Quechua (Yaku) |
| Escalado inteligente | e2b → e4b → 12b según tarea |
| Embeddings (pipeline) | Memoria semántica con modelo local de embeddings |

**Evidencia en el prototipo:** `/api/health` reporta `ollamaLocalActive: true` y modelos Gemma instalados; `/api/chat` responde con `is_local: true` y el modelo real usado (ej. `gemma4:e2b-q4`).

---

## 7. Arquitectura

```text
Usuario
  → React (UI · voz · paneles)
    → FastAPI Backend
      → Cognitive Router (Gemma 4)
        → Agente especializado (Gemma 4)
          → KB offline · Qdrant RAG · Neo4j · datos Perú
            → Respuesta streaming + TTS
```

**Stack:** Gemma 4 (Ollama) · Python · FastAPI · React · Docker · PostgreSQL · Redis · Qdrant · Neo4j · Edge TTS

---

## 8. Ecosistema de agentes *(rúbrica: agentes + automatización)*

| Agente | Especialidad | Gemma sugerido |
| :--- | :--- | :--- |
| **Aya** | Salud integral, alergias, medicamentos, guías MINSA | `gemma4:12b-q4` |
| **Sumaq** | Bienestar, nutrición, hábitos, mente | `gemma4:e4b-q4` |
| **Kipu** | Programación, arquitectura, código | `gemma4:12b-q4` |
| **Pacha** | Clima, ambiente, SENAMHI / Open-Meteo | `gemma4:e4b-q4` |
| **Tupac** | Emergencias, sismos IGP, riesgo | `gemma4:e2b-q4` |
| **Inti** | Legal, Constitución y normas del Perú | `gemma4:e4b-q4` |
| **Yaku** | Cultura, INEI, sabiduría andina, Quechua | `gemma4:e4b-q4` |

El router puede escalar potencia (p. ej. a 12B) cuando la consulta lo exige, con confirmación de consumo en modo automático.

---

## 9. Tracks de la competencia — cómo encajamos

| Track | Evidencia en MARU OS |
| :--- | :--- |
| **IA Local e Inteligencia Edge** | Gemma 4 vía Ollama en el host; Docker local; modo offline-first |
| **Agentes de IA y Automatización** | 7 agentes + router + herramientas (KB, clima, pastillero, código) |
| **IA para Impacto Social + Datos Abiertos Perú** | IGP, SENAMHI, INEI, INDECI; escenarios de rescate, salud, Quechua |
| **Aplicaciones Multimodales** | Texto + voz (STT/TTS) + documentos/PDF + imágenes de impacto en landing |
| **Herramientas para Desarrolladores** | Kipu: asistencia de código y arquitectura local |

---

## 10. Rúbrica — cómo respondemos a los jueces

### ¿Uso efectivo de Gemma 4? ¿Es central?
**Sí.** Todo el chat cognitivo, el router, la traducción y la síntesis pasan por Gemma 4. Sin Gemma, no hay producto.

### ¿Problema relevante? ¿Creativo, útil, con impacto?
**Sí.** Resiliencia en desastres, privacidad, inclusión lingüística (Quechua) y acceso a IA sin nube — alineado a **ODS 3** (salud/bienestar), **ODS 11** (ciudades resilientes) y **ODS 16** (acceso a información / confianza digital).

### ¿El prototipo funciona? ¿La demo cumple el objetivo?
**Sí.** Stack Docker verificable: salud Ollama, chat local streaming, RAG Qdrant, agentes, clima, TTS, landing de impacto. Demo en 3 minutos con flujo real (ver guion arriba).

### ¿Writeup claro?
Este documento estructura **problema → solución → Gemma → arquitectura → agentes → demo → impacto → roadmap**.

---

## 11. Datos abiertos del Perú

- **IGP** — contexto sísmico / emergencia (Tupac)  
- **SENAMHI / Open-Meteo** — clima por ciudad (Pacha)  
- **INEI** — demografía y contexto territorial (Yaku)  
- **INDECI / protocolos** — gestión de riesgo en corpus offline  
- **MINSA (orientación)** — reglas de seguridad en Aya (p. ej. dengue: no AINEs)

---

## 12. Funcionalidades del prototipo (actualizado)

- Router cognitivo + catálogo de modelos (manual / router)  
- Chat streaming local con agentes  
- Memoria vectorial (Qdrant) + grafo (Neo4j)  
- KB offline + PDFs descargables por especialidad  
- Pastillero avanzado e interacciones  
- Clima por ciudad, traducción Quechua, voz  
- Landing “Impacto real” (rescate, programar desde cualquier dispositivo, Maru niños, traductor Quechua)  
- Tema claro / oscuro, onboarding con soberanía (frase de 12 palabras, modo efímero)

---

## 13. Demo sugerida (prueba viva)

1. Abrir portada → sección **Impacto** → alternar **Oscuro**  
2. Chat: “Guíame en un sismo sin Internet” → **Tupac** + Gemma local  
3. “¿Qué clima hay en Chosica?” → **Pacha**  
4. “Traduce al quechua: buenos días” → **Yaku**  
5. Aya: medicamento del catálogo → aparece en memoria  
6. Ajustes: Ollama conectado + modelos Gemma listados  

---

## 14. Retos técnicos resueltos

- Coordinar multiagente sin saturar GPU (cola + modelos Q4)  
- Compatibilidad Qdrant cliente/servidor para RAG real  
- Autoplay de audio / UX de portada  
- Contraste y sistema de diseño en modo noche  
- Mantener experiencia usable en cold-start de Ollama  

---

## 15. Impacto esperado

| Público | Valor |
| :--- | :--- |
| Ciudadanía / emergencia | Orientación local cuando cae la red |
| Estudiantes y docentes | Tutor privado sin nube |
| Comunidades Quechua | Puente lingüístico con Gemma |
| Desarrolladores | Kipu como copiloto local |
| Organizaciones | Privacidad por diseño |

---

## 16. Hoja de ruta

- Multimodal más profundo (visión Gemma sobre documentos/imágenes clínicas no diagnósticas)  
- Memoria de largo plazo más rica  
- Más fuentes oficiales y agentes de dominio  
- Empaquetado edge (instalador 1-click)  

---

## 17. Conclusión

**MARU OS demuestra que Gemma 4 puede ser el núcleo de un sistema operativo cognitivo completo**: local, multiagente, con memoria, datos abiertos del Perú e impacto social medible.

No es solo un experimento técnico. Es una propuesta de futuro: **IA que respeta la privacidad, sobrevive a la desconexión y acerca inteligencia útil a más personas.**

> *El manantial de inteligencia universal — siempre cerca de ti.*

---

## Apéndice A — Arranque rápido (jueces / demo)

### Requisitos
Docker Desktop · Ollama · Modelos Gemma (`gemma4:e2b-q4`, opcional `e4b-q4`, `12b-q4`) · `nomic-embed-text`

### Levantar
```bash
cd MARU_OS
ollama pull gemma4:e2b-q4
ollama pull nomic-embed-text
docker compose up -d --build
```

Abrir: **http://localhost:3000** · API: **http://localhost:8000/api/health**

### Comandos útiles
```bash
docker compose ps
docker compose logs -f backend
docker compose down
```

---

## Apéndice B — Criterios de evaluación (checklist interno)

| Criterio | Evidencia lista |
| :--- | :--- |
| ⭐ Innovación | OS cognitivo offline + 7 agentes + datos abiertos PE |
| ⭐ Uso efectivo de Gemma | Cerebro de router, chat, traducción, escalado |
| ⭐ Calidad técnica | Docker, FastAPI, RAG, grafo, streaming, voz |
| ⭐ Impacto y utilidad | Emergencia, salud, Quechua, educación, privacidad |

---

Desarrollado para demostrar el potencial de **Gemma 4** en escenarios reales de soberanía tecnológica e impacto social.
