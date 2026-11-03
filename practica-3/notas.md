## Principio Single Responsibility
1. La clase Pedido original tenía múltiples razones para cambiar, validaba stock, calculaba ISV, guardaba en BD, imprimía y enviaba WhatsApp.
2. Se aplicó la separación creando piezas enfocadas: ValidadorStock y CalculadoraTotal.
3. Ahora cada clase tiene una única responsabilidad clara y bien definida.
4. Esto eleva la cohesión del módulo, facilitando las pruebas unitarias aisladas.
5. Si mañana cambia la regla del ISV, solo modificamos CalculadoraTotal sin arriesgar el resto del flujo.

## Principio Dependency Inversion
1. El método original instanciaba directamente los detalles (new DatabaseConnection, new WhatsAppAPI) acoplándose a ellos.
2. Ahora la clase ProcesadorPedido recibe sus dependencias inyectadas desde afuera a través de su constructor.
3. El código de negocio depende de interfaces y no de implementaciones concretas.
4. Esto nos permite cambiar de proveedor de base de datos o de WhatsApp sin tocar la lógica del pedido.
5. Habilita el testing inyectando repositorios falsos en memoria, tal como se verá en el módulo 4.