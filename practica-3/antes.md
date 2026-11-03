class Pedido {
  procesarPedido(cliente, items, direccion) {
    // 1. Validar stock
    for (let item of items) {
      if (item.stock < item.cantidad) {
        throw new Error("No hay stock suficiente");
      }
    }

    // 2. Calcular total con ISV (15%)
    let subtotal = 0;
    for (let item of items) {
      subtotal += item.precio * item.cantidad;
    }
    let isv = subtotal * 0.15;
    let total = subtotal + isv;

    // 3. Guardar en base de datos (simulado)
    const db = new DatabaseConnection("localhost", "user", "pass");
    db.query("INSERT INTO pedidos (cliente, total, direccion) VALUES (?, ?, ?)", [cliente, total, direccion]);
    
    // 4. Imprimir ticket
    const impresora = new Impresora("POS-58");
    impresora.imprimir("Cliente: " + cliente);
    impresora.imprimir("Total: " + total);
    impresora.cortarPapel();

    // 5. Enviar a WhatsApp
    const whatsapp = new WhatsAppAPI("token_secreto");
    whatsapp.enviarMensaje(cliente.telefono, "Su pedido ha sido procesado. Total: " + total);

    return total;
  }
}