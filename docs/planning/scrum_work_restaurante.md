## PARTE 5 - Desglose del Trabajo del Restaurante    

**Épica:** Blue Velvet (BV)
**Descripción:** Agrupa todo el ciclo de vida del restaurante (carta, pedidos, barra y pagos). No cabe en un solo sprint.

---

### Feature: Catálogo Digital y Motor de Personalización

**Descripción:** Capacidad concreta dentro de la épica orientada a la exploración de bebidas y aplicación de reglas de mixología.

---

#### Historia de Usuario 1

**COMO** cliente comensal de Blue Velvet, **QUIERO** consultar la carta digital interactiva con stock en tiempo real y filtrar por Mocktails (0.0% ABV), **PARA** elegir una bebida disponible de forma autónoma sin riesgo de consumir alcohol.

- **Entrega de valor:** Entrega valor por sí sola y cabe en un sprint.
- **Prioridad:** Alta: Es la puerta de entrada al servicio y evita que comensales consuman alcohol involuntariamente.
- **Estimación:** 5 Puntos de Historia.

**Criterios de Aceptación:**

1. **Dado que** el insumo "Macerado artesanal de moras" tiene stock 0 ml, **cuando** el cliente consulta la carta, **entonces** el cóctel "Midnight Blackberry Gin Sour" se marca automáticamente como "NO DISPONIBLE" y bloquea su adición.  
   *Es verificable:* se puede escribir una prueba automatizada que compruebe la desactivación ante stock en cero.

2. **Dado que** el comensal visualiza un "Mocktail (0.0% ABV)", **cuando** intenta interactuar con modificadores, **entonces** el sistema bloquea y desactiva cualquier opción o destilado con graduación alcohólica.  
   *Es verificable:* se puede validar que el backend y frontend restrinjan adiciones alcohólicas según la Regla de Negocio 2.

**Subtareas:**

- Modelar entidades de catálogo, graduación ABV e inventario. Trabajo técnico de base de datos y modelos JPA, estimable en horas, con un solo responsable.
- Construir endpoint `GET /api/v1/menu/cocktails` con filtro por stock en tiempo real. Otra unidad técnica de backend de la misma historia.
- Desarrollar validador de dominio para exclusión de alcohol en mocktails. Unidad técnica de reglas de negocio (Specification Pattern) dentro de la misma historia.

---

#### Historia de Usuario 2

**COMO** cliente comensal, **QUIERO** seleccionar obligatoriamente la marca del destilado de mi cóctel y poder cambiarlo una única vez antes de su preparación, **PARA** personalizar mi trago asegurando un cobro exacto.

- **Entrega de valor:** Entrega valor por sí sola y cabe en un sprint.
- **Prioridad:** Alta: Modela la regla diferencial del establecimiento y previene mermas de insumos premium.
- **Estimación:** 5 Puntos de Historia.
- **Video de Planning Poker:** [Enlace de tu video de Planning Poker]

**Criterios de Aceptación:**

1. **Dado que** un cóctel requiere base espirituosa, **cuando** el cliente intenta agregarlo sin seleccionar la marca del destilado, **entonces** el sistema impide la adición y exige elegir una opción de la lista.  
   *Es verificable:* se puede escribir una prueba unitaria que valide que el atributo de licor no sea nulo.

2. **Dado** un cóctel en estado RECIBIDO con 0 cambios previos, **cuando** el cliente solicita un cambio de licor, **entonces** el sistema actualiza la marca, sube el contador a 1, congela el nuevo precio y bloquea modificaciones futuras.  
   *Es verificable:* permite comprobar la máquina de estados y la regla de mutación única.

**Subtareas:**

- Crear endpoint `PATCH /api/v1/orders/{id}/items/{itemId}/spirit`. Trabajo técnico de API REST, estimable en horas, con un solo responsable.
- Implementar guarda de dominio para mutación única de licor y validación de estado RECIBIDO. Unidad técnica de lógica de negocio y validación de transiciones dentro de la misma historia.
- Configurar validador de límite de garnishes (máximo 2) y recargo tarifario congelado. Otra unidad técnica encargada de reglas de decoraciones y precios fijos.

---

**Épica:** Blue Velvet (BV)    
**Descripción:** Agrupa todo el ciclo de vida del restaurante (carta, pedidos, barra y pagos). No cabe en un solo sprint.

---

### Feature: Tablero KDS de Barra y Ciclo de Vida del Pedido

**Descripción:** Capacidad concreta dentro de la épica encargada de sincronizar la orden con la estación de mixología y su seguimiento.

---

#### Historia de Usuario 3

**Como** mesero o cliente comensal, **QUIERO** confirmar el pedido de la mesa activa, **PARA** despachar la comanda detallada al tablero KDS de la barra en menos de 2 segundos.

- **Entrega de valor:** Entrega valor operacional directo y cabe en un sprint.
- **Prioridad:** Alta: Es el disparador que conecta la sala con la estación de preparación.
- **Estimación:** 5 Puntos de Historia.

**Criterios de Aceptación:**

1. **Dado que** la mesa tiene una comanda con bebidas válidas, **cuando** se acciona la confirmación del pedido, **entonces** la orden pasa a estado RECIBIDO y aparece en el tablero de barra en menos de 2 segundos.  
   *Es verificable:* se puede medir el tiempo de respuesta y la recepción del evento bajo el requisito RNF02.

2. **Dado que** una mesa ya tiene una cuenta abierta sin pagar, **cuando** se intenta crear una nueva orden independiente, **entonces** el sistema bloquea la acción indicando que la mesa ya tiene cuenta activa.  
   *Es verificable:* se puede probar la restricción de concurrencia e integridad en la base de datos.

**Subtareas:**

- Construir endpoint `POST /api/v1/orders/{id}/confirm` con validación de comanda completa. Trabajo técnico de backend y control transaccional, estimable en horas, con un único responsable.
- Configurar broker de eventos/WebSockets hacia el KDS de barra. Unidad técnica de infraestructura y comunicación reactiva de la misma historia.
- Implementar restricción de concurrencia de cuenta única por mesa. Otra unidad técnica de reglas de persistencia e índices únicos.

---

#### Historia de Usuario 4

**Como** bartender o jefe de barra, **QUIERO** actualizar el estado de cada pedido en el tablero KDS según avance su elaboración, **PARA** coordinar el despacho e impedir cambios en tragos ya preparados.

- **Entrega de valor:** Entrega valor al flujo operativo interno y cabe en un sprint.
- **Prioridad:** Media: Fundamental para el control de cocina, aunque depende de que la orden ya haya sido recibida.
- **Estimación:** 3 Puntos de Historia.

**Criterios de Aceptación:**

1. **Dado** un pedido en estado RECIBIDO en el KDS, **cuando** el bartender lo avanza a EN PREPARACIÓN, **entonces** el sistema bloquea inmediatamente cualquier edición o cancelación en la comanda.  
   *Es verificable:* se puede comprobar mediante pruebas de API que rechace mutaciones tras la transición.

2. **Dado que** un usuario de barra efectúa una transición de estado válida, **cuando** la operación finaliza, **entonces** el sistema inserta un registro inmutable en auditoría con ID de usuario, estados y timestamp UTC.  
   *Es verificable:* se puede auditar directamente la tabla de base de datos verificando el cumplimiento de RNF05.

**Subtareas:**

- Implementar máquina de estados finita del ciclo de la orden en backend. Trabajo técnico de arquitectura de software para controlar transiciones de estado, estimable en horas.
- Desarrollar endpoint `PUT /api/v1/orders/{id}/status` con log de auditoría. Unidad técnica de API y persistencia de trazas de seguridad dentro de la misma historia.
- Diseñar interfaz reactiva del tablero KDS para la estación de barra. Unidad técnica de frontend (componente Kanban interactivo) con un solo responsable asignado.