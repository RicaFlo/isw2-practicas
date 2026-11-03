
// Aplicando S y D

//Interfaces para inyección
class IBaseDeDatos { guardarPedido(cliente, total, direccion) {} }
class INotificador { enviarWhatsApp(telefono, mensaje) {} }
class IImpresora { imprimirTicket(cliente, total) {} }

//Responsabilidad Única
class ValidadorStock {
  validar(items) {
    for (let item of items) {
      if (item.stock < item.cantidad) throw new Error("Sin stock");
    }
  }
}

class CalculadoraTotal {
  calcular(items) {
    let subtotal = items.reduce((acc, item) => acc + (item.precio * item.cantidad), 0);
    return subtotal + (subtotal * 0.15);
  }
}

//Clase Orquestadora
class ProcesadorPedido {
  constructor(validador, calculadora, db, notificador, impresora) {
    this.validador = validador;
    this.calculadora = calculadora;
    this.db = db;
    this.notificador = notificador;
    this.impresora = impresora;
  }

  procesar(cliente, items, direccion) {
    this.validador.validar(items);
    const total = this.calculadora.calcular(items);
    
    this.db.guardarPedido(cliente, total, direccion);
    this.impresora.imprimirTicket(cliente, total);
    this.notificador.enviarWhatsApp(cliente.telefono, "Pedido procesado");

    return total;
  }
}