[Épica: Blue Velvet (BV)]
  │
  ├── [Feature 1: Catálogo Digital y Motor de Personalización]
  │     ├── Historia 1: Feat: Consulta de Carta y Restricción Mocktail
  │     │     ├── Subtarea 1.1: Modelar entidades de catálogo, graduación ABV y alérgenos
  │     │     ├── Subtarea 1.2: Construir endpoint GET /api/v1/menu/cocktails con filtro por stock
  │     │     └── Subtarea 1.3: Desarrollar validador de dominio para exclusión de alcohol en mocktails
  │     │
  │     └── Historia 2: Feat: Selección de Destilado Base y Modificación Única
  │           ├── Subtarea 2.1: Crear endpoint PATCH /api/v1/orders/{id}/items/{itemId}/spirit
  │           ├── Subtarea 2.2: Implementar regla de negocio de modificación única antes de preparación
  │           └── Subtarea 2.3: Configurar validador de límite de garnishes (máximo 2) y recargos
  │
  └── [Feature 2: Tablero KDS de Barra y Ciclo de Vida del Pedido]
        ├── Historia 3: Feat: Confirmación de Pedido y Despacho a Barra
        │     ├── Subtarea 3.1: Desarrollar endpoint POST /api/v1/orders/{id}/confirm con validación de destilados
        │     ├── Subtarea 3.2: Configurar servicio de notificación en tiempo real hacia el tablero de barra
        │     └── Subtarea 3.3: Implementar validación de cuenta única por mesa activa
        │
        └── Historia 4: Feat: Gestión de Estados del Pedido en KDS y Auditoría
              ├── Subtarea 4.1: Implementar máquina de estados finita (RECIBIDO -> EN PREPARACIÓN -> LISTO -> ENTREGADO)
              ├── Subtarea 4.2: Crear endpoint PUT /api/v1/orders/{id}/status con registro de auditoría
              └── Subtarea 4.3: Diseñar interfaz del tablero KDS de barra con tarjetas interactivas


# Épica: Blue Velvet (BV) 

| Nivel | Elemento | Descripción | Prioridad | Criterios de Aceptación | Subtareas |
|-------|----------|-------------|-----------|--------------------------|-----------|
| **Épica** | Blue Velvet (BV) | Agrupa todo el ciclo de vida del restaurante (carta, pedidos, barra y pagos). No cabe en un solo sprint. | — | — | — |
| **Feature** | Catálogo Digital y Motor de Personalización | Capacidad concreta dentro de la épica orientada a la exploración de bebidas y aplicación de reglas de mixología. | — | — | — |
| **Historia de Usuario** | Como cliente comensal de Blue Velvet, quiero consultar la carta digital interactiva con stock en tiempo real y filtrar por Mocktails (0.0% ABV), para elegir una bebida disponible de forma autónoma sin riesgo de consumir alcohol. | Entrega valor por sí sola y cabe en un sprint. | **Alta**: Puerta de entrada al servicio y evita consumo involuntario de alcohol. | 1. Dado que el insumo "Macerado artesanal de moras" tiene stock 0 ml, cuando el cliente consulta la carta, entonces el cóctel "Midnight Blackberry Gin Sour" se marca automáticamente como "NO DISPONIBLE" y bloquea su adición. *(Verificable con prueba automatizada)*<br><br>2. Dado que el comensal visualiza un "Mocktail (0.0% ABV)", cuando intenta interactuar con modificadores, entonces el sistema bloquea y desactiva cualquier opción o destilado con graduación alcohólica. *(Verificable: backend y frontend restringen adiciones según Regla de Negocio 2)* | • Modelar entidades de catálogo, graduación ABV e inventario (BD y JPA)<br>• Construir endpoint `GET /api/v1/menu/cocktails` con filtro por stock en tiempo real<br>• Desarrollar validador de dominio para exclusión de alcohol en mocktails (Specification Pattern) |
| **Historia de Usuario** | Como cliente comensal, quiero seleccionar obligatoriamente la marca del destilado de mi cóctel y poder cambiarlo una única vez antes de su preparación, para personalizar mi trago asegurando un cobro exacto. | Entrega valor por sí sola y cabe en un sprint. | **Alta**: Modela la regla diferencial del establecimiento y previene mermas de insumos premium. | 1. Dado que un cóctel requiere base espirituosa, cuando el cliente intenta agregarlo sin seleccionar la marca del destilado, entonces el sistema impide la adición y exige elegir una opción de la lista. *(Verificable con prueba unitaria que valide que el atributo de licor no sea nulo)*<br><br>2. Dado un cóctel en estado RECIBIDO con 0 cambios previos, cuando el cliente solicita un cambio de licor, entonces el sistema actualiza la marca, sube el contador a 1, congela el nuevo precio y bloquea modificaciones futuras. *(Verificable: permite comprobar la máquina de estados y la regla de mutación única)* | • Crear endpoint `PATCH /api/v1/orders/{id}/items/{itemId}/spirit`<br>• Implementar guarda de dominio para mutación única de licor y validación de estado RECIBIDO<br>• Configurar validador de límite de garnishes (máximo 2) y recargo tarifario congelado |


# Épica: Blue Velvet (BV) — Digitalización Integral de Coctelería de Autor

## Tablero KDS de Barra y Ciclo de Vida del Pedido

| Nivel | Elemento | Descripción | Prioridad | Criterios de Aceptación | Subtareas |
|-------|----------|-------------|-----------|--------------------------|-----------|
| **Épica** | Blue Velvet (BV) — | Agrupa todo el ciclo de vida del restaurante (carta, pedidos, barra y pagos). No cabe en un solo sprint. | — | — | — |
| **Feature** | Tablero KDS de Barra y Ciclo de Vida del Pedido | Capacidad concreta dentro de la épica encargada de sincronizar la orden con la estación de mixología y su seguimiento. | — | — | — |
| **Historia de Usuario** | Como mesero o cliente comensal, quiero confirmar el pedido de la mesa activa, para despachar la comanda detallada al tablero KDS de la barra en menos de 2 segundos. | Entrega valor operacional directo y cabe en un sprint. | **Alta**: Disparador que conecta la sala con la estación de preparación. | 1. Dado que la mesa tiene una comanda con bebidas válidas, cuando se acciona la confirmación del pedido, entonces la orden pasa a estado RECIBIDO y aparece en el tablero de barra en menos de 2 segundos. *(Verificable: se puede medir el tiempo de respuesta y la recepción del evento bajo el requisito RNF02)*<br><br>2. Dado que una mesa ya tiene una cuenta abierta sin pagar, cuando se intenta crear una nueva orden independiente, entonces el sistema bloquea la acción indicando que la mesa ya tiene cuenta activa. *(Verificable: se puede probar la restricción de concurrencia e integridad en la base de datos)* | • Construir endpoint `POST /api/v1/orders/{id}/confirm` con validación de comanda completa<br>• Configurar broker de eventos/WebSockets hacia el KDS de barra<br>• Implementar restricción de concurrencia de cuenta única por mesa |
| **Historia de Usuario** | Como bartender o jefe de barra, quiero actualizar el estado de cada pedido en el tablero KDS según avance su elaboración, para coordinar el despacho e impedir cambios en tragos ya preparados. | Entrega valor al flujo operativo interno y cabe en un sprint. | **Media**: Fundamental para el control de cocina, aunque depende de que la orden ya haya sido recibida. | 1. Dado un pedido en estado RECIBIDO en el KDS, cuando el bartender lo avanza a EN PREPARACIÓN, entonces el sistema bloquea inmediatamente cualquier edición o cancelación en la comanda. *(Verificable: se puede comprobar mediante pruebas de API que rechace mutaciones tras la transición)*<br><br>2. Dado que un usuario de barra efectúa una transición de estado válida, cuando la operación finaliza, entonces el sistema inserta un registro inmutable en auditoría con ID de usuario, estados y timestamp UTC. *(Verificable: se puede auditar directamente la tabla de base de datos verificando el cumplimiento de RNF05)* | • Implementar máquina de estados finita del ciclo de la orden en backend<br>• Desarrollar endpoint `PUT /api/v1/orders/{id}/status` con log de auditoría<br>• Diseñar interfaz reactiva del tablero KDS para la estación de barra (componente Kanban interactivo) |