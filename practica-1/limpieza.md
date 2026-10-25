# Práctica 1: Limpieza de Código

## Código Original
```html
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" media="screen" href="cualeslaruta.html" />
    <script src="cualeslaruta.html"></script>
</head>
<body>
    <script>
        var id;
        id=prompt("Ingrese su numero de identidad")
        if(id=="0501199914878"||"0501200003497"||"0501200108673"||"0501200311233"||"profe")
        {
            location.href="cualeslaruta.html" 
        }
        else
        {
         document.write("Datos no validos")
        }
    </script>
</body>
</html>

rror de lógica: El if con múltiples || siempre da verdadero porque los strings no vacíos evalúan como true.

Números mágicos: Los números de identidad están hardcodeados en el código.

Nombres crípticos: La variable id no dice qué contiene.

Mezcla de responsabilidades: Hay HTML, CSS y JS revueltos (y apuntando a un archivo .html en vez de .css y .js).

Uso de var y document.write: Prácticas obsoletas.

// Lista de identidades permitidas extraída a una constante
const IDENTIDADES_PERMITIDAS = [
    "0501199914878",
    "0501200003497",
    "0501200108673",
    "0501200311233",
    "profe"
];

// Nombre descriptivo para la variable
const numeroIdentidadIngresado = prompt("Ingrese su número de identidad");

// Uso de .includes() para validar correctamente
if (IDENTIDADES_PERMITIDAS.includes(numeroIdentidadIngresado)) {
    window.location.href = "cualeslaruta.html";
} else {
    alert("Datos no válidos"); // Evitamos document.write
}
