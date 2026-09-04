# DOSW_LABORATORIO_3_SebastianDavidNicolas

Integrantes:
- Juan David Munar
- Sebastian Granados
- Nicolas Alvarez

Objetivo:
- Aplicar herramientas de definición y análisis de requerimientos a partir de un caso de estudio práctico, y herramientas de planeación usando el framework Agile Scrum con Jira. El sistema que definan aquí será la base del proyecto de API que construirán durante el segundo corte.

=======
# Preguntas README

1. El pull request en GitHub es una solicitud para fusionar cambios de una rama hacia otra, típicamente desde una rama de feature hacia una rama principal como develop o main.

2. Es cuando, desde una rama propia se hace un commit y un push al origin, esto hace que se haga un pull request y pueda combinar los cambios de la rama de origen (que es la propia) a la rama destino, que en el contexto de laboratorio es la develop.

3. Desde la pagina de GitHub en la opción de Compare & Pull Request, esto hace que acepte esos commits al repositorio principal.

4.  GitHub. (s.f.). Acerca de las solicitudes de incorporación de cambios. GitHub Docs. Recuperado el 20 de agosto de
    2026, de https://docs.github.com/es/pull-requests/get-started/about-pull-requests

---
## GUIA DE MARCA (PARTE 4)

Concepto: Restaurante de coctelería.
Reglas de negocio:
1. Todo cóctel con base alcohólica debe registrar obligatoriamente la marca o tipo de destilado; sin este dato no pasa a la comanda de la barra, el cliente puede cambiar su licor 1 vez antes de preparar el coctel.
2. Si un cóctel se configura como Mocktail (sin alcohol), el sistema bloquea y desactiva cualquier modificador con graduación alcohólica.

### 1. Paleta 
La interfaz fue diseñada para un entorno nocturno, garantizando de forma estricta un contraste mínimo de 4.5:1 para cumplir con los criterios de accesibilidad visual.

| Elemento | Token de UI | Color (Hex) | Ratio vs Fondo Base | Nivel WCAG |
| :--- | :--- | :--- | :--- | :--- |
| **Fondo Base** | `bg-main` | `#0B132B` | Base | — |
| **Superficie** | `surface-card` | `#1C2541` | 1.3:1 (Separación visual) | — |
| **Acción Principal** | `accent-gold` | `#D4AF37` | **8.1:1** | AAA |
| **Acentos Interactivos** | `accent-cyan` | `#48CAE4` | **9.2:1** | AAA |
| **Badge Mocktail** | `mocktail-green`| `#2EC4B6` | **7.5:1** | AAA |
| **Texto Principal** | `text-primary` | `#FFFFFF` | **17.8:1** | AAA |
| **Texto Secundario** | `text-muted` | `#A0AAB2` | **6.1:1** | AA |
| **Alerta / Agotado** | `status-alert` | `#FF6B6B` | **6.3:1** | AA |

### 2. Criterios de Accesibilidad Cognitiva y Visual
Para asegurar la accesibilidad a usuarios con daltonismo, se aplica la regla de que ningún dato o estado del sistema se transmite únicamente a través del color.

* **Protocolo de Stock Agotado (Zero Color-Only Info):**
  * **Capa Textual:** Etiqueta explícita en mayúsculas con el mensaje `AGOTADO EN BARRA`.
  * **Capa Iconográfica:** Implementación del ícono de candado (`🔒`) o señal de bloqueo (`🚫`) junto al nombre del insumo faltante.
  * **Capa de Interacción:** Bloqueo nativo del componente mediante el atributo `disabled`, reducción de opacidad y cursor `not-allowed`.

### 3. Sistema Tipográfico
* **Títulos y Display:** *Playfair Display* (Serif, Bold 700 / Semi-Bold 600) — Transmite la sofisticación y elegancia clásica de la coctelería.
* **Cuerpo e Interfaz:** *Inter* (Sans-Serif, Regular 400 / Medium 500) — Garantiza máxima legibilidad en pantallas móviles bajo condiciones de baja iluminación.

### 4. Logo, favicon, mocks
- Se encuentran en docs/images