# MARU OS

## Sistema Operativo Cognitivo Offline impulsado por Gemma 4

> **M.A.R.U.** — *Memory · Agents · Reasoning · Universal*  
> La IA que sigue ayudando cuando Internet desaparece.

---

## Entregables de la competencia

Este repositorio cumple lo solicitado, en este orden:

| # | Entregable | Dónde está en este README |
| :---: | :--- | :--- |
| 1 | Código fuente del proyecto | [Sección 1](#1-código-fuente-del-proyecto) |
| 2 | Documentación técnica | [Sección 2](#2-documentación-técnica) |
| 3 | Arquitectura de solución | [Sección 3](#3-arquitectura-de-solución) |
| 4 | Instrucciones para ejecutar y reproducir | [Sección 4](#4-instrucciones-para-ejecutar-y-reproducir-el-proyecto) |

Material adicional (pitch, impacto, rúbrica): [Sección 5](#5-contexto-del-proyecto-impacto-y-demo).

---

## 1. Código fuente del proyecto

Todo el código de MARU OS vive en este repositorio.

### Estructura principal

```text
MARU_OS/
├── src/                      # Frontend React + TypeScript (UI, chat, paneles, landing)
│   ├── components/           # Landing, chat, agentes, salud, clima, settings, etc.
│   ├── services/             # API, audio/TTS, tema, storage, música ambiente
│   ├── data/                 # Catálogos, KB frontend, agentes, ciudades PE
│   ├── context/              # Config del motor (router / modelos / agentes)
│   └── App.tsx               # Orquestación de vistas
├── backend/                  # Backend FastAPI (cerebro + agentes + memoria)
│   └── app/
│       ├── api/endpoints.py  # REST: chat, health, models, knowledge, TTS...
│       ├── core/             # Ollama, Qdrant, Neo4j, DB, config
│       └── services/         # Agentes, router, KB, datos Perú, tools
├── public/                   # Assets estáticos (landing, KB PDFs, multimedia)
├── scripts/                  # Utilidades (ej. generate_kb_pdfs.py)
├── docker-compose.yml        # Orquestación reproducible del stack
├── Dockerfile                # Imagen del frontend
├── backend/Dockerfile        # Imagen del backend
├── package.json              # Dependencias frontend
├── backend/requirements.txt  # Dependencias backend
└── README.md                 # Este documento
```

### Componentes de código más importantes

| Pieza | Ruta | Rol |
| :--- | :--- | :--- |
| Chat cognitivo | `backend/app/api/endpoints.py` | Streaming con Gemma 4 + agentes |
| Cliente Ollama / Gemma | `backend/app/core/ollama.py` | Inferencia local y embeddings |
| Router + agentes | `backend/app/services/agents.py` | Enrutamiento e identidad de agentes |
| Memoria RAG | `backend/app/core/vector_store.py` | Qdrant (`query_points`) |
| Grafo | `backend/app/core/graph_store.py` | Neo4j |
| KB offline | `backend/app/services/knowledge_base.py` | Corpus + sync |
| UI principal | `src/App.tsx`, `src/components/**` | Producto completo |
| Cliente API | `src/services/apiService.ts` | Health, chat, models, TTS |

El código fuente es el entregable #1: clonar este repo es suficiente para inspeccionarlo y reproducirlo.

---

## 2. Documentación técnica

### ¿Qué es MARU OS?

Sistema Operativo Cognitivo **Offline-First**. Gemma 4 (vía Ollama) es el **núcleo de razonamiento**. No es un wrapper de API remota: el chat, el router cognitivo, la traducción y la síntesis corren en local.

### Stack tecnológico

| Capa | Tecnología |
| :--- | :--- |
| Modelos | Gemma 4 (`e2b-q4`, `e4b-q4`, `12b-q4`) + `nomic-embed-text` en Ollama |
| Backend | Python 3.12, FastAPI, Uvicorn |
| Frontend | React, TypeScript, Vite |
| Memoria vectorial | Qdrant |
| Grafo | Neo4j |
| Persistencia | PostgreSQL, Redis, SQLite (datos offline PE) |
| Voz | STT del navegador + Edge TTS |
| Empaquetado | Docker Compose |

### Capacidades técnicas principales

- **Cognitive Router:** clasifica la consulta y elige agente / modelo Gemma.
- **7 agentes:** Aya (salud), Sumaq (bienestar), Kipu (código), Pacha (clima), Tupac (emergencias), Inti (legal), Yaku (Perú / Quechua / INEI).
- **RAG local:** embeddings + Qdrant; el chat recupera memoria relevante.
- **KB offline + PDFs** por especialidad (`/api/knowledge`, `/api/knowledge/pdfs`).
- **Datos abiertos PE:** IGP, SENAMHI/Open-Meteo, INEI, INDECI (corpus y paneles).
- **Privacidad:** onboarding con frase de recuperación, modo efímero, datos en dispositivo.
- **API clave:**
  - `GET /api/health` — estado Ollama + modelos Gemma
  - `GET /api/models` — catálogo instalado
  - `POST /api/chat` — chat cognitivo streaming
  - `GET /api/knowledge` — base de conocimiento
  - `POST /api/tts` — voz
  - `POST /api/translate` — traducción (Yaku / Gemma)

### Puertos por defecto

| Servicio | URL / puerto |
| :--- | :--- |
| Frontend | http://localhost:3000 |
| Backend API | http://localhost:8000 |
| Ollama (host) | http://localhost:11434 |
| PostgreSQL | 5432 |
| Redis | 6379 |
| Qdrant | 6333 |
| Neo4j | 7474 / 7687 |

### Monólogo corto para demo (opcional)

Ver archivo de texto: `docs/MARU_OS_Monologo_3min.txt`.

---

## 3. Arquitectura de solución

### Diagrama de flujo

```text
Usuario
  │
  ▼
Frontend React (localhost:3000)
  · Landing de impacto · Chat · Paneles (salud, clima, legal...)
  · STT / TTS · Tema claro/oscuro
  │
  ▼
Backend FastAPI (localhost:8000)
  · /api/chat · /api/health · /api/knowledge · /api/tts
  │
  ▼
Cognitive Router  ──────────────►  Gemma 4 (Ollama en el host)
  │                                    │
  ▼                                    ▼
Agente especializado            Razonamiento + streaming
(Aya / Sumaq / Kipu / Pacha /   Escalado e2b → e4b → 12b
 Tupac / Inti / Yaku)
  │
  ├──► KB offline (documentos + PDFs)
  ├──► Qdrant (memoria vectorial / RAG)
  ├──► Neo4j (grafo de conocimiento)
  ├──► PostgreSQL / Redis
  └──► Datos abiertos Perú (clima, sismos, demografía, emergencia)
  │
  ▼
Respuesta final al usuario (+ voz opcional)
```

### Contenedores Docker

```text
Ollama (host)  ←── Gemma 4 + embeddings
       ▲
       │ host.docker.internal:11434
       │
┌──────┴──────────────────────────────────────────┐
│ docker compose                                   │
│  maru_frontend  → UI                             │
│  maru_backend   → FastAPI + agentes + router     │
│  maru_postgres  → persistencia                   │
│  maru_redis     → cola / cache                   │
│  maru_qdrant    → RAG                            │
│  maru_neo4j     → grafo                          │
└──────────────────────────────────────────────────┘
```

### Por qué esta arquitectura

1. **Gemma 4 central:** toda inferencia cognitiva pasa por Ollama/Gemma.
2. **Offline-first:** KB y modelos locales; la nube es opcional.
3. **Modular:** nuevos agentes sin reescribir el núcleo.
4. **Reproducible:** un `docker compose up` levanta el sistema completo.

### Tracks que cubre

| Track | Cómo lo cubre MARU OS |
| :--- | :--- |
| IA Local / Edge | Gemma 4 en Ollama local |
| Agentes y automatización | 7 agentes + router + tools |
| Impacto social + datos abiertos PE | IGP, SENAMHI, INEI, INDECI |
| Multimodal | Texto, voz, PDFs, imágenes de landing |
| Tools para desarrolladores | Agente Kipu |

---

## 4. Instrucciones para ejecutar y reproducir el proyecto

### 4.1 Requisitos previos

1. **Docker Desktop** (o Docker Engine + Compose)
2. **Ollama** instalado y corriendo en el host: https://ollama.com
3. **Git**
4. (Opcional) Node.js 20+ solo si desarrollas el frontend sin Docker

### 4.2 Clonar el código fuente

```bash
git clone https://github.com/revilo3681/maru-os.git
cd maru-os
# Si trabajas desde este workspace:
# cd /Users/revilo/aqws/MARU_OS
```

### 4.3 Descargar modelos Gemma 4 (obligatorio para reproducir la IA)

```bash
# Modelo rápido por defecto (recomendado para la demo)
ollama pull gemma4:e2b-q4

# Embeddings para memoria RAG
ollama pull nomic-embed-text

# Opcionales (mejor calidad / agentes pesados)
ollama pull gemma4:e4b-q4
ollama pull gemma4:12b-q4
```

Verifica Ollama:

```bash
ollama list
curl http://localhost:11434/api/tags
```

### 4.4 Levantar todo el stack (reproducción recomendada)

Desde la raíz del proyecto (`MARU_OS/`):

```bash
docker compose up -d --build
```

Espera a que los contenedores estén healthy / Up:

```bash
docker compose ps
```

### 4.5 Abrir y verificar que funciona

1. Frontend: **http://localhost:3000**
2. Backend health: **http://localhost:8000/api/health**  
   Debe mostrar `"ollamaLocalActive": true` y modelos Gemma en `localModelsAvailable`.
3. Chat de prueba: en la app, envía un mensaje corto (ej. “Hola”). Debe responder con Gemma local.
4. Modelos: **http://localhost:8000/api/models** → `"ollamaConnected": true`

### 4.6 Comandos útiles de mantenimiento

```bash
docker compose logs -f backend
docker compose logs -f frontend
docker compose restart backend
docker compose down          # detener
docker compose up -d --build # reconstruir tras cambios
```

### 4.7 Desarrollo local (sin Docker, opcional)

**Backend:**

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

**Frontend:**

```bash
npm install --legacy-peer-deps
npm run dev
```

Necesitas Postgres, Redis, Qdrant y Neo4j accesibles (o usa solo Docker para esas dependencias).

### 4.8 Checklist de reproducción (jueces)

- [ ] Ollama corriendo en el host  
- [ ] `gemma4:e2b-q4` y `nomic-embed-text` descargados  
- [ ] `docker compose up -d --build` sin errores  
- [ ] http://localhost:3000 carga la portada  
- [ ] http://localhost:8000/api/health → Ollama activo  
- [ ] Chat responde en local (`is_local: true`)  

---

## 5. Contexto del proyecto (impacto y demo)

### Problema

La IA útil hoy suele depender de la nube: falla sin Internet, expone datos y deja fuera a quienes tienen mala conectividad. En emergencias (p. ej. un sismo) eso es crítico.

### Solución

MARU OS mueve el razonamiento al dispositivo con **Gemma 4**, agentes especializados, memoria local y datos abiertos del Perú.

### Gemma 4 es el componente central

| Uso | Descripción |
| :--- | :--- |
| Chat | Streaming local por agente |
| Router | Decide agente y complejidad |
| Traducción | Español ↔ Quechua |
| Escalado | e2b → e4b → 12b según tarea |

Sin Gemma 4 no hay producto.

### Los 7 agentes

| Agente | Especialidad | Modelo sugerido |
| :--- | :--- | :--- |
| Aya | Salud, alergias, medicamentos | `gemma4:12b-q4` |
| Sumaq | Bienestar y hábitos | `gemma4:e4b-q4` |
| Kipu | Programación | `gemma4:12b-q4` |
| Pacha | Clima / ambiente | `gemma4:e4b-q4` |
| Tupac | Emergencias / IGP | `gemma4:e2b-q4` |
| Inti | Legal Perú | `gemma4:e4b-q4` |
| Yaku | Cultura, INEI, Quechua | `gemma4:e4b-q4` |

### Demo sugerida (3 minutos)

1. Portada → sección Impacto → tema Oscuro  
2. Chat: “Guíame en un sismo sin Internet” → Tupac + Gemma local  
3. Clima en Chosica → Pacha  
4. Traducir al quechua → Yaku  
5. Ajustes: Ollama conectado y modelos listados  

### Rúbrica (resumen)

| Criterio | Evidencia |
| :--- | :--- |
| Innovación | OS cognitivo offline + multiagente + datos PE |
| Uso efectivo de Gemma | Núcleo de router, chat, traducción, escalado |
| Calidad técnica | Docker, FastAPI, RAG, grafo, streaming, voz |
| Impacto | Emergencia, salud, Quechua, educación, privacidad |

### Conclusión

MARU OS demuestra que **Gemma 4** puede ser el cerebro de un sistema cognitivo completo: privado, multiagente, con memoria y útil cuando la nube no está.

---

Desarrollado para demostrar el potencial de **Gemma 4** en escenarios reales de soberanía tecnológica e impacto social.
