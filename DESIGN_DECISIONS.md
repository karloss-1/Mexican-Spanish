# Decisiones de diseño y pedagogía de Mexican Spanish Flashcards

Estado: decisiones vigentes, sujetas a revisión. Actualización: 17 de septiembre de 2026, rama `main`.

## Cómo interpretar estas notas

Este documento permite retomar el proyecto sin depender de conversaciones anteriores. Las decisiones pueden revisarse según cambien los estudiantes, la evidencia pedagógica o los objetivos del proyecto. Conocer el motivo de una decisión no obliga a conservarla.

**Hecho** indica algo comprobado en el repositorio. **Razonamiento inferido** identifica una utilidad plausible del diseño, sin afirmar que sea la motivación histórica original. El repositorio no conserva todas las fuentes y debates pedagógicos; no se completan esos vacíos con supuestos presentados como hechos.

## 1. Propósito: producir vocabulario del español mexicano hablado

**Hecho.** La interfaz y el manifiesto describen “Active Spanish production with spaced repetition”. El anverso muestra la pista en inglés junto con su ejemplo en inglés; el reverso contiene la respuesta en español junto con el ejemplo correspondiente en español. No se incluyen aquí paradigmas completos de conjugación, corrección automática de respuestas ni audio.

Los datos incluyen palabras, expresiones y marcadores conversacionales, por ejemplo ya, pues, o sea, es que, ajá y güey. El objetivo de vocabulario mexicano cotidiano se refleja en los títulos de los mazos y en su contenido; no todas las entradas son exclusivas de México.

**Razonamiento inferido.** La dirección inglés→español favorece intentar producir la expresión antes de reconocerla. Mostrar el ejemplo inglés en el anverso aporta contexto semántico a la pista y reduce la ambigüedad de palabras con varias traducciones posibles; al revelar, el ejemplo español permite comparar cómo se expresa ese mismo significado en contexto. La palabra o expresión objetivo sigue siendo el foco de recuperación, no la reproducción literal de toda la oración. Incluir marcadores y expresiones coloquiales es coherente con conversaciones reales, además del vocabulario de manual.

**Límites y revisión.** Una traducción breve puede admitir varias respuestas válidas; no debe confundirse la respuesta guardada con la única formulación posible. Los ejemplos no constituyen por sí solos un sistema completo de registro, región o adecuación social. Revisar ambigüedades, lenguaje coloquial y contextos según los estudiantes; valorar audio, variantes aceptadas o pistas contextuales si ayudan a sus objetivos.

Fuentes: [index.html](index.html), [data/decks.js](data/decks.js), [manifest.webmanifest](manifest.webmanifest).

## 2. Corpus, listas y organización del contenido

**Hecho.** [data/decks.js](data/decks.js) contiene dos colecciones: 900 entradas en total.

| Colección | Alcance |
| --- | --- | --- |
| Essential Mexican Spanish Words | 600 tarjetas de vocabulario y expresiones |
| Essential Mexican Spanish Verbs | 300 tarjetas de verbos |

Estos son conteos de tarjetas, no una afirmación de 900 lemas distintos. El orden y los IDs de las tarjetas de los nueve bloques originales se conservan dentro de cada colección.

La cabecera de decks.js declara que se generó a partir de CSV suministrados y pide no editar manualmente el texto allí. Sin embargo, la rama actual no incluye esos CSV ni un generador. Tampoco contiene una referencia suficiente al corpus original, su versión, población, método de selección o cálculo de frecuencia. Los títulos “High-Frequency” y los números no bastan para demostrar un ranking corpus por corpus o un umbral estadístico.

**Razonamiento inferido.** Separar vocabulario general y verbos permite orientar la práctica por necesidad. Las colecciones eliminan divisiones visibles arbitrarias sin perder el orden de frecuencia existente.

**Revisión.** Recuperar y versionar CSV, procedencia, criterios de selección y conversión antes de una edición importante del contenido. Si aún no se recuperan, registrar explícitamente una nueva fuente editable y su relación con los datos existentes. La validación fija de dos colecciones y 900 tarjetas protege el conjunto actual. Al reordenar tarjetas, conservar identidad por contenido o planificar una migración, porque los IDs actuales son posicionales. No tratar un cambio de numeración como una edición puramente visual.

## 3. Recuperación activa y repetición espaciada con FSRS

**Hecho.** Se carga la copia local de `ts-fsrs@5.4.1` y se usa su configuración predeterminada. Tras revelar se habilitan Again, Hard, Good y Easy; cada calificación actualiza el estado y guarda el progreso. Previous/Next y revelar solo cambian la vista: no califican ni consumen el cupo de tarjetas nuevas.

La cola prioriza Learning/Relearning vencidas, después Review vencidas y al final las New permitidas. El estudiante elige 5, 10 o 15 nuevas por colección y día natural de estudio; el conteo aumenta al calificar una tarjeta New por primera vez. Con 20 o más tarjetas no-New vencidas, no se ofrecen nuevas hasta reducir el atraso. FSRS sigue decidiendo los vencimientos de las tarjetas ya introducidas.

**Razonamiento inferido.** Pedir una producción antes de ver el reverso y espaciar futuras revisiones orienta la práctica al recuerdo. Los botones permiten autoevaluación sin añadir una interfaz de examen. Se confía en la valoración del estudiante; no se ha documentado en el repo una validación pedagógica específica del planificador o del orden de la cola.

**Revisión.** Evaluar con estudiantes si el límite, la prioridad de repasos y el umbral de atraso producen una carga sostenible. Cualquier cambio de planificador debe considerar los estados guardados. No importar automáticamente las reglas de autoevaluación por paradigma de ConjuFlow: aquí la unidad es una tarjeta léxica.

Fuentes: funciones de cola, calificación y persistencia en [index.html](index.html); [biblioteca local](vendor/ts-fsrs-5.4.1.umd.js).

## 4. IndexedDB y contenido separado del progreso

**Hecho.** La base es `mexican-spanish-flashcards-db`, versión 2. El almacén `deckProgress` usa `deckId` y guarda estados FSRS por ID de tarjeta, versión de contenido y del planificador, además del día y conteo de nuevas introducidas por colección. La colección y el límite seleccionados se recuerdan por separado en localStorage.

Al iniciar, `syncCanonicalContent` incorpora estados para tarjetas nuevas y conserva los existentes por ID. Cambiar la versión de contenido no reinicia por sí solo las tarjetas ya conocidas; una versión de planificador distinta sí vacía sus estados y los vuelve a crear. El texto se lee de los datos canónicos del repositorio, no del registro de progreso.

**Razonamiento inferido.** Separar texto y memoria permite actualizar contenido sin duplicarlo en cada navegador y conservar revisiones cuando la identidad permanece. Guardar localmente evita cuentas y un backend, a costa de no tener sincronización entre dispositivos.

El repositorio no contiene el progreso personal. No hay exportación, respaldo de progreso ni sincronización de cuenta implementados. Borrar datos del navegador puede perderlo.

**Revisión.** Mantener IDs si se corrige la misma tarjeta; si cambia el objetivo léxico o se reordena la lista, decidir explícitamente si corresponde conservar la memoria. Revisar migraciones y respaldo antes de cambiar esquemas o versiones de FSRS.

Fuente: persistencia y `syncCanonicalContent` en [index.html](index.html).

## 5. Relación y separación respecto a ConjuFlow

**Hecho.** ConjuFlow declara en su README que parte del código de esta app, pero usa otra base: `conjuflow-db`, con `cardProgress` por tarjeta verbo×tiempo. Esta app usa `deckProgress` por mazo. Las preferencias también tienen claves distintas. Ambas incluyen la misma versión de FSRS, pero sus colas, UI y unidades de aprendizaje no son idénticas.

**Razonamiento inferido.** Recordar el significado y uso de un verbo y producir todas sus formas son capacidades relacionadas que necesitan seguimiento propio. Compartir biblioteca no justifica compartir estado ni dar por aprendida una conjugación porque se conoce el significado.

La separación también evita interferencias de esquema cuando ambas apps se sirven bajo el mismo origen: rutas URL distintas por sí solas no separan los nombres de IndexedDB. El historial de ConjuFlow documenta la corrección en [8bd901d](https://github.com/karloss-1/Conjuflow/commit/8bd901d).

**Revisión.** Se pueden añadir enlaces o una experiencia común si los objetivos lo requieren. Una integración del progreso necesitaría definir correspondencias y migración; no basta renombrar bases ni copiar estados FSRS entre tipos de tarjeta.

Fuentes: [index.html](index.html), [README de ConjuFlow](https://github.com/karloss-1/Conjuflow/blob/main/README.md).

## 6. Arquitectura estática, PWA y alcance real sin conexión

**Hecho.** La app concentra estructura, estilos y lógica en [index.html](index.html), carga contenido y FSRS localmente y no incluye backend ni un proceso de compilación. El [manifiesto](manifest.webmanifest) define inicio y alcance relativos, modo standalone e iconos; hay metadatos móviles y de Apple.

La app registra [sw.js](sw.js), que precarga la estructura de la aplicación, la política de estudio, los datos, la biblioteca FSRS y los iconos. Después de una primera carga correcta con conexión, puede volver a abrirse sin conexión con esos recursos. Su caché se llama `mexican-spanish-flashcards-v2`; al actualizarse, elimina cachés anteriores con ese prefijo. Cuando hay conexión usa primero la red para evitar servir una versión obsoleta. El service worker no lee, escribe ni elimina IndexedDB.

**Razonamiento inferido.** Servir archivos estáticos y mantener la dependencia local facilita alojamiento y reduce dependencias de infraestructura. Una caché propia permite uso sin conexión sin compartir estado ni archivos con ConjuFlow.

**Revisión.** Incrementar la versión del caché cuando cambien recursos precargados y comprobar la carga offline. Mantener su prefijo y alcance propios para preservar el aislamiento respecto a ConjuFlow.

## 7. Decisiones de interfaz

**Hecho.** La versión actual usa una tarjeta central, selectores de colección y límite diario, pista visible para revelar y etiquetas English/Spanish. El anverso presenta la palabra o expresión inglesa con su ejemplo en inglés; el reverso presenta la respuesta española con su ejemplo en español. En ambos lados, la palabra o expresión objetivo tiene mayor peso visual que el ejemplo. La interfaz resume repasos vencidos y nuevas disponibles o avisa cuando el atraso pausa material nuevo. La navegación es secundaria respecto a los cuatro botones de calificación. La ayuda explica la práctica, la repetición espaciada, el límite diario, la prioridad de repasos y el almacenamiento local.

La paleta usa superficies claras, verde como identidad y colores diferenciados para las calificaciones, acompañados de texto. En pantallas pequeñas los cuatro botones se distribuyen en dos columnas; la tarjeta tiene espacio estable y desplazamiento interior para contenido largo. Incluye foco visible, interacción de teclado, mensajes de estado y reducción de animaciones según la preferencia del sistema.

El indicador `posición / tamaño de cola` y su barra representan ubicación en la cola actual. Navegar puede moverlos; no son un conteo acumulado de tarjetas calificadas ni una medida de dominio. Esta semántica es distinta de la barra por revisiones de ConjuFlow.

**Razonamiento inferido.** Una jerarquía clara centra la atención en producir y comprobar. Emparejar cada idioma con su propio ejemplo mantiene el contexto disponible antes y después de revelar sin mostrar anticipadamente la respuesta española. Mantener navegación secundaria ayuda a distinguirla de calificar. El diseño móvil favorece sesiones breves, pero no hay un estudio de usabilidad documentado.

**Revisión.** Comprobar legibilidad, contraste, contenido largo, accesibilidad y comprensión de la barra con usuarios. Valorar instrucciones de calificación más precisas si hay confusión. Los colores y la disposición son decisiones revisables, no identidad inalterable.

Fuentes: [index.html](index.html), rediseño [6220da6](https://github.com/karloss-1/Mexican-Spanish/commit/6220da6) y ajuste de paleta [ed6625c](https://github.com/karloss-1/Mexican-Spanish/commit/ed6625c).

## 8. Cómo continuar el proyecto

Antes de una modificación, contrastar estas notas con el código vigente y los datos; distinguir comportamiento comprobado de intención inferida. Registrar fecha, problema del alumnado, alternativas consideradas, decisión, consecuencias y señales que justificarían revisarla.

Los vacíos principales son procedencia del corpus/listas, CSV y generación original, y evidencia de aprendizaje/usabilidad. Recuperar esas fuentes mejoraría la continuidad; no hace falta mantener decisiones actuales solo porque se perdió una conversación.

La inspección no encontró AGENTS.md ni instrucciones de una rama documental alternativa. Estas notas se añaden a main; la rama de rediseño no sustituye a la principal como referencia vigente.
