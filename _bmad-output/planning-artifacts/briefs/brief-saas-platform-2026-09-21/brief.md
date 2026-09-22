---
title: "Contrato vivo de mantenimiento"
status: ready
created: 2026-09-21
updated: 2026-09-21
---

# Product Brief: Contrato vivo de mantenimiento

> Modelo de bonos cerrado por Marta el 2026-09-21. Las decisiones de detalle que quedan están al
> final, agrupadas, y se resolvieron en el PRD. El PRD es posterior y manda sobre las partes
> explícitamente superadas que se indican a continuación.

> **Superado en parte por el PRD (2026-09-22).** En estos puntos manda
> `prds/prd-saas-platform-2026-09-21/prd.md`:
>
> 1. **Quién crea las peticiones:** la agencia. El cliente pide por su canal
>    habitual y no crea peticiones en la plataforma.
> 2. **Cómo aprueba el cliente:** mediante un enlace directo, sin necesidad de
>    navegar por la plataforma.
> 3. **Bonos:** también requieren la aprobación del cliente, y esa aprobación
>    es la que emite el documento de cargo.
> 4. **Roles del cliente:** hay un rol único, y cualquier usuario del cliente
>    puede aprobar o rechazar.
> 5. **Peticiones aprobadas:** se pueden reducir y ampliar mediante incrementos,
>    y existen horas imputadas no autorizadas.
>
> El resto del brief sigue vigente como explicación del *porqué*. Para cualquier
> requisito, consulta el PRD.

## Resumen ejecutivo

Las agencias digitales pequeñas venden mantenimiento web como bonos de horas. El bono se contrata
una vez y a partir de ahí nadie sabe cuánto queda: la agencia apunta las horas donde puede, el
cliente pide trabajo por el canal que tiene a mano, y cuando aparece la duda ambos descubren cifras
distintas. La discusión que sigue — *esto estaba incluido o no* — se resuelve por desgaste y por
memoria, no por datos.

**Contrato vivo de mantenimiento** convierte esos bonos en un objeto compartido que las dos partes
miran a la vez. El cliente pide trabajo declarando el resultado que espera; la agencia estima; la
aprobación del cliente **compromete** esas horas contra bonos concretos; y el trabajo se imputa
después contra ese compromiso. Las horas comprometidas dejan de estar disponibles en el momento en
que se autorizan, así que **nunca puede haber más trabajo autorizado que horas contratadas**.

El producto no intenta ser el sistema de gestión de la agencia. Hace una sola cosa: **fija el borde
del trabajo antes de empezarlo, y deja constancia de quién lo movió**.

## El problema

Una agencia de doce personas con cuarenta clientes en mantenimiento vive estas cuatro escenas:

- **El cliente pregunta cuántas horas le quedan.** Alguien abre una hoja de cálculo, la reconcilia a
  mano y contesta un número que ya está desactualizado cuando lo envía. Si el cliente tiene dos
  bonos contratados en momentos distintos, ni eso.
- **El cliente pide «un cambio pequeño».** Nadie escribe el alcance. Cuando resulta no ser pequeño,
  la agencia ya lo ha hecho y no sabe cómo cobrarlo sin que parezca que cobra de más.
- **El freelance externo recibe la tarea por mensaje.** No tiene el alcance por escrito, así que
  absorbe el «ya que estás» hasta que deja de rentarle.
- **Se agota un bono.** Nadie lo vio venir, el trabajo está a medias, y la conversación sobre
  contratar más llega en el peor momento posible: cuando el cliente ya está esperando algo.

El coste no es solo dinero: es la erosión de la relación. El cliente sospecha que le cobran de más;
la agencia sabe que regala horas. Ambos tienen razón, y ninguno puede demostrarlo.

## La solución

Un espacio compartido por cliente, donde agencia y cliente ven **el mismo objeto con permisos
distintos**:

1. El cliente contrata un **bono**: un número de horas, un importe, una fecha de inicio y una de
   caducidad. Cada bono conserva su identidad y su propio consumo. Los bonos no se amplían: cuando
   hace falta más capacidad, se contrata otro.
2. La contratación emite un **documento de cargo** por el importe del bono. Consumir esas horas
   después no genera ningún cargo nuevo.
3. La agencia invita a personas del cliente. El rol por defecto es el mínimo: ver.
4. El cliente crea una **petición de trabajo** declarando el resultado que espera. Crear siempre
   está permitido, haya horas o no: una petición no compromete nada.
5. La agencia la estima. Para aprobarla, el sistema mira las horas **disponibles sumando todos los
   bonos vigentes** del cliente.
6. Al aprobarla, esas horas quedan **comprometidas** contra bonos concretos. Una petición puede
   repartirse entre varios bonos, y el reparto es automático: **primero los bonos que caducan
   antes**, para que el cliente no pierda horas pagadas teniendo otras vivas. La comprobación y el
   compromiso ocurren de forma atómica, de modo que dos aprobaciones simultáneas no pueden
   comprometer las mismas horas.
7. El equipo imputa horas contra la petición, y esas horas pasan de comprometidas a **consumidas**.
   Si el trabajo se resuelve en menos horas de las aprobadas, se imputan las reales y el resto del
   compromiso se libera.
8. Todo lo anterior deja rastro en un registro **append-only** que nadie puede editar, ni siquiera
   el administrador de la agencia. Una imputación errónea no se corrige editando: se compensa con
   una operación nueva que registra quién la hizo, cuándo y por qué.

Tres magnitudes por bono, siempre separadas: **contratadas**, **comprometidas** y **consumidas**.
Las disponibles se calculan a partir de ellas y **nunca pueden ser negativas**. Esa es la garantía
central del producto: si el trabajo está autorizado, las horas existen.

La pieza que hace que esto funcione no es el contador: es que **las dos partes miran los mismos
números**. Un saldo que solo ve la agencia es una hoja de cálculo con mejor tipografía.

## A quién sirve

**Agencia digital de 5 a 25 personas** con cartera de mantenimiento recurrente. Tres roles:

- *Administración de la agencia* — registra bonos, define precios, invita, y gobierna la relación
  comercial con cada cliente.
- *Miembro de la agencia* — estima e imputa horas.
- *Cliente* — pide trabajo, aprueba gasto, consulta sus bonos y su histórico. No ve nada de otros
  clientes, ni costes internos.

El usuario que decide la compra es quien responde cuando un cliente discute un consumo:
normalmente el socio o el responsable de operaciones.

## Panorama competitivo y postura

El espacio **está ocupado**. Accelo (PSA para agencias) y HaloPSA (mundo MSP, contratos de *block
hours*) implementan este mecanismo de forma madura; Clientary y Retainero cubren buena parte a
precio bajo. Las herramientas de mantenimiento WordPress hacen informes técnicos, pero no gestionan
saldo ni aprobaciones — ese cruce concreto sigue vacío.

**Postura adoptada:** este proyecto es principalmente un vehículo de entrenamiento y la competencia
**no condiciona su alcance**. Si más adelante hubiera intención de convertirlo en producto real, la
única cuña defendible identificada combina la verticalización en mantenimiento web, el registro
inmutable como prueba en disputas y el precio de agencia pequeña — una cuña estrecha que exigiría
validar antes el dolor con evidencia primaria, que hoy no tenemos.

## Alcance de la primera versión

**Dentro:**

- Tenant de agencia con sus clientes dentro.
- Tres roles con permisos diferenciados, incluida la ocultación de campos sensibles al rol cliente.
- Invitación de usuarios por email con token de un solo uso. **El correo de invitación sí entra en
  el alcance**, pese a que las notificaciones queden fuera: sin él no hay forma de incorporar a
  nadie. En desarrollo basta con que el enlace quede visible en el log; configurar un envío real es
  un trabajo aparte.
- Bonos con horas, importe, fecha de inicio y fecha de caducidad. Varios bonos vivos por cliente.
- Documento de cargo emitido al contratar cada bono.
- Petición de trabajo con estados y resultado esperado declarado.
- Estimación, y aprobación del cliente que compromete horas de forma atómica.
- Reparto automático del compromiso entre varios bonos, por caducidad más próxima.
- Imputación de horas contra el compromiso, y liberación del remanente.
- Las tres magnitudes por bono, visibles para ambas partes.
- Registro append-only de contrataciones, peticiones, estimaciones, aprobaciones, compromisos,
  imputaciones, liberaciones y correcciones. Por «accesos» se entiende el alta y la baja de personas
  y los cambios de rol, **no** cada lectura de pantalla: registrar toda consulta multiplicaría el
  trabajo y el volumen sin aportar nada a la discusión que el producto quiere zanjar.

**Fuera, explícitamente:**

- Cierre mensual o global de periodo. Los bonos se gobiernan por sus propias fechas.
- Cobro real: pasarela, impuestos y reintentos.
- Modificación de una petición ya aprobada, al alza o a la baja.
- Bonos con ámbito: todos los bonos vigentes sirven para cualquier petición de ese cliente.
- Selección manual del bono contra el que se compromete.
- Bonos sin caducidad, y bonos con fecha de inicio futura.
- Contratación con efecto retroactivo.
- Límite de bonos activos por cliente.
- Límites de gasto por rol.
- Hilos de discusión por línea de cargo.
- Informes automáticos al cliente.
- Freelances externos como rol propio.
- Grupos con varias marcas y facturación consolidada.
- Notificaciones.
- Cualquier integración con WordPress.

El alcance de la facturación se reduce conscientemente a **documento de cargo sin cobro real**: la
pasarela es un proyecto en sí mismo y no cabe en el presupuesto.

## Criterios de éxito

Como vehículo de entrenamiento, que es su propósito principal:

- El flujo completo funciona de extremo a extremo: invitar, contratar un bono, pedir, estimar,
  aprobar, imputar y liberar el remanente.
- Las cinco capacidades obligatorias del proyecto — roles y permisos, aislamiento por tenant,
  invitaciones, facturación y auditoría — están ejercitadas de verdad, no simuladas: un usuario del
  cliente A no puede acceder a datos del cliente B ni por URL directa, y el rol cliente no puede
  leer costes internos aunque el registro que mira sea el mismo.
- **Las horas disponibles nunca son negativas**, ni siquiera con aprobaciones simultáneas sobre los
  mismos bonos. Hay una prueba que lo demuestra.
- El registro append-only resiste el intento de manipulación de un administrador, y ese intento
  también queda registrado.
- Cada historia entra por PR, con la CI en verde y la revisión superada.

Como producto, si algún día se mira con esos ojos: que un responsable de agencia pueda contestar
«cuántas horas me quedan» sin abrir una hoja de cálculo, y que un consumo discutido se resuelva
enseñando una pantalla.

## Restricciones

- **La estimación original de 80 horas se ha quedado corta.** Se hizo sobre un bono único con un
  contador; el modelo actual tiene cartera de bonos, tres magnitudes, reparto automático, compromiso
  atómico y liberación. El núcleo funcional cuesta bastante más, y el reparto de referencia de
  abajo ya lo refleja.
- **Ese número es la estimación del trabajo sin curva de aprendizaje, no un compromiso de entrega.**
  El stack es nuevo para quien lo implementa, y en esas condiciones la curva suele ser la mayor
  parte del coste: el tiempo real puede multiplicarlo por dos o por tres. La diferencia entre lo
  estimado y lo real es un dato de calibración que el programa quiere medir, no una desviación que
  corregir.
- Planificación sobre un objetivo de **20 h/semana**. La disponibilidad máxima es de 20–30 h/semana,
  pero ese margen es colchón para imprevistos, no capacidad a planificar.
- Una sola persona implementa todo el código.
- Stack: TypeScript, React, Node, MongoDB.
- Reparto de referencia, ~110 h: ~15 h de andamiaje; ~20 h de autenticación, tenancy y roles; ~60 h
  de núcleo funcional — bonos, peticiones, compromiso y reparto —; ~15 h de tests y ciclo de PR.
- **Línea de corte.** Si el calendario se alarga, el núcleo que ya se puede enseñar es tenancy,
  roles, invitaciones, bonos, petición, aprobación con compromiso e imputación. Lo primero que se
  aparta es el documento de cargo; después, las correcciones compensatorias. El reparto entre varios
  bonos **no** se aparta: es donde vive la parte interesante del modelo.
- **Decisión de arquitectura pendiente, no riesgo:** si las tres magnitudes se calculan a partir del
  registro cada vez que se consultan, o se mantienen en campos que se actualizan con cada operación.
  La prohibición de saldo negativo obliga a que la comprobación y el compromiso sean atómicos, y esa
  exigencia condiciona la elección. Corresponde a la fase de arquitectura.

## Decisiones confirmadas

Varias se han tomado pensando en qué conviene aprender con el presupuesto disponible, y no en cuál
sería el modelo de producto más completo. Donde ha sido así, queda dicho.

**Organizaciones**

1. **Un usuario puede pertenecer a varias organizaciones.** La pertenencia se resuelve por el
   contexto de organización de cada petición HTTP; no queda fijada únicamente en el token.
2. **El tenant es la agencia; el cliente es una organización dentro de ella.** *Decisión de
   alcance:* la alternativa — agencia y cliente como dos tenants que comparten un objeto — es
   arquitectónicamente más rica, y se descarta para que el MVP sea realizable.
3. **El sistema aloja varias agencias**, no una instalación por agencia.

**Bonos**

4. **Un cliente puede tener varios bonos vivos.** Los bonos no se amplían: cuando hace falta
   capacidad, se contrata uno nuevo e independiente.
5. **Cada bono conserva identidad, horas contratadas, importe, fecha de inicio, caducidad y su
   propio consumo.** No se funden en un saldo único indistinguible, porque hay que poder trazar qué
   bono aportó cada hora.
6. **La unidad es la hora**, no unidades de servicio ni paquetes.
7. **Todos los bonos vigentes del cliente sirven para cualquier petición suya.** Sin ámbitos por
   proyecto, sitio o tipo de trabajo.
8. **Todos los bonos tienen fecha de inicio y de caducidad.** No hay bonos perpetuos.
9. **Al caducar un bono, sus horas disponibles no comprometidas se pierden.** Las que ya estaban
   comprometidas por una petición aprobada sobreviven y pueden imputarse aunque el bono haya
   caducado. **Un bono caducado no genera compromisos nuevos.**

**Peticiones, compromiso y consumo**

10. **Crear una petición siempre es posible y no compromete nada.** El cliente puede pedir con
    cualquier disponibilidad, incluida cero.
11. **La aprobación es lo que compromete.** Se evalúan las horas disponibles sumando todos los bonos
    vigentes; si no alcanzan, la petición no puede aprobarse hasta que exista un bono nuevo con
    capacidad.
12. **Una petición puede comprometer horas de varios bonos a la vez, y el reparto es automático
    priorizando la caducidad más próxima.** Sin selección manual. El compromiso se asigna a bonos
    concretos en el momento de aprobar, que es lo que permite saber qué compromiso sobrevive a qué
    caducidad.
13. **La comprobación de disponibilidad y el compromiso son atómicos**, de modo que dos aprobaciones
    simultáneas no puedan comprometer las mismas horas.
14. **Las horas disponibles nunca pueden ser negativas** como consecuencia de una operación válida.
15. **Una petición aprobada es inmutable.** Si hacen falta más horas no se modifica: se crea una
    petición nueva que sigue el mismo flujo. Las modificaciones quedan fuera del MVP.
16. **Si se trabajan menos horas de las aprobadas, se imputan las reales y el compromiso restante se
    libera**, volviendo a estar disponible según las reglas de su bono. Si ese bono ya caducó, esas
    horas se pierden.
17. **Las horas reales internas de la agencia pueden superar a las imputadas**, y esa diferencia no
    afecta al cliente.

**Cargo y registro**

18. **El documento de cargo corresponde a la contratación de un bono, no al consumo.** Un bono de
    20 h por 400 € genera su cargo de 400 €; consumir esas horas después no genera ningún cargo
    nuevo. Se emite dentro del producto; no se envía ni se cobra.
19. **No hay cierre mensual ni global.** Los bonos se gobiernan por sus propias fechas de vigencia.
20. **Las imputaciones erróneas se compensan, no se editan.** La corrección es una operación nueva
    que deja constancia de autor, momento y motivo. El registro original permanece intacto: el log
    es evidencia, no un historial técnico.

**Alcance general**

21. **El rol cliente ve** sus bonos, sus peticiones, el estado y las horas imputadas a cada una, y
    sus documentos de cargo. **No ve** coste interno, qué persona de la agencia hizo el trabajo, ni
    la existencia de otros clientes.
22. **Euros e interfaz en español**, sin internacionalización en el MVP.

## Decisiones de detalle pendientes, a resolver en el PRD

Ninguna contradice lo escrito arriba; todas concretan un hueco.

- **Cierre de una petición.** Alguien tiene que declarar que ha terminado para que se libere el
  compromiso remanente. Falta decidir quién, y si hay cierre automático por caducidad o inactividad.
  Sin esta regla, un compromiso puede quedar colgado indefinidamente.
- **Si «cancelar» existe como operación propia**, o basta con cerrar una petición sin horas
  imputadas. Y en su caso, quién puede hacerlo y si la otra parte debe consentir.
- **Quién contrata un bono:** si la agencia lo registra o el cliente lo solicita y aprueba. Siendo
  la operación que emite el cargo, importa.
- **Si se registran las horas reales de la agencia** en paralelo a las imputadas, o el margen por
  cliente queda fuera del MVP. Ver la nota de abajo.
- **Qué ve exactamente el cliente:** el desglose de las tres magnitudes bono a bono, o solo su total
  disponible.
- **Peticiones en espera y bono nuevo:** si al contratar capacidad las peticiones pendientes se
  desbloquean solas o hay que volver a aprobarlas.
- **Qué ve quien pierde una aprobación simultánea.**
- **Si un bono recién contratado se puede anular**, y qué ocurre entonces con su documento de cargo.
- **Si el documento de cargo tiene estados** más allá de emitido.

> **Nota sobre el margen.** La versión anterior de este brief decía que Administración «ve los
> márgenes». Con la decisión 17, las horas reales de la agencia no se registran en ninguna parte, de
> modo que no hay margen que mostrar. La promesa se ha retirado del rol hasta que se decida si esas
> horas se registran o si el margen sale del MVP.

## Visión

Si esto creciera más allá del ejercicio, la dirección natural no es convertirse en el sistema de
gestión de la agencia — ese espacio ya está ocupado y bien ocupado. Es volverse **la capa de
evidencia** entre una agencia y sus clientes: el sitio donde vive lo que se pidió, lo que se aprobó
y lo que se hizo, con un registro que ninguna de las dos partes puede alterar. El valor no está en
gestionar el trabajo, sino en que nadie pueda reescribir después lo que se acordó.
