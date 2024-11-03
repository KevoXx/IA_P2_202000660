# Manual Técnico del Proyecto de Machine Learning

Este documento proporciona una guía técnica sobre la estructura y funcionalidad del proyecto de Machine Learning. Este proyecto permite cargar un archivo CSV y entrenar modelos de regresión lineal y polinomial para hacer predicciones y visualizar resultados.

## Estructura del Proyecto

El proyecto consiste en una interfaz web desarrollada en HTML, CSS y JavaScript. Los componentes principales incluyen:
- **HTML**: Estructura de la página y elementos de interacción.
- **JavaScript**: Funciones de procesamiento de datos, entrenamiento de modelos y visualización.

## Índice de Contenidos

- [Requerimientos](#requerimientos)
- [Funciones JavaScript](#funciones-javascript)
- [Ejemplo de Uso](#ejemplo-de-uso)

## Requerimientos

1. Navegador con soporte para HTML5 y JavaScript.
2. Conexión a Internet para cargar las bibliotecas externas:
   - `water.css` para los estilos.
   - Google Charts para la visualización de gráficos.

## Descripción de Componentes

### HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Proyecto de Machine Learning</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css" />
    <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
</head>
<body>
    <!-- Selección de modelo y carga de CSV -->
    <h1>Proyecto de Machine Learning</h1>
    <label for="modelSelect">Selecciona un modelo:</label>
    <select id="modelSelect">
        <option value="linear-regression">Regresión Lineal</option>
        <option value="polynomial-regression">Regresión Polinomial</option>
        <option value="decision-tree">Árbol de decisión</option>
    </select>
    <!-- Otros elementos de interfaz omitidos por brevedad -->
</body>
</html>
```


### JavaScript
Las funciones JavaScript están diseñadas para:

1. Procesar archivos CSV y cargar datos en la tabla.
2. Entrenar modelos de regresión y calcular predicciones.
3. Visualizar resultados en gráficos y tablas.


### Funciones JavaScript
#### clearResults
Limpia el contenido de los elementos de la tabla y el gráfico.

```javascript
function clearResults() {
    document.getElementById("output-table").innerHTML = "";
    document.getElementById("output-graph").innerHTML = "";
}
```
#### resetData
Limpia el archivo CSV, vacía los arrays de datos y restablece la tabla.

```javascript
function resetData() {
    document.getElementById("fileInput").value = "";
    dataX = [];
    dataY = [];
    const table = document.getElementById("parametersTable");
    while (table.rows.length > 0) {
        table.deleteRow(0);
    }
    clearResults();
}
```

#### processLinearRegression
Entrena un modelo de regresión lineal utilizando dataX y dataY y visualiza los resultados.

```javascript
function processLinearRegression() {
    let linearRegression = new LinearRegression();
    linearRegression.fit(dataX, dataY);
    let result = linearRegression.predict(dataX);
    // Generación de tabla y gráfico (omitidos por brevedad)
}
```

#### processPolynomialRegression
Entrena un modelo de regresión polinomial y calcula predicciones según el grado seleccionado.

```javascript
function processPolynomialRegression() {
    const degree = parseInt(document.getElementById("polynomialDegree").value);
    if (!degree || degree <= 0) {
        alert("Ingrese el grado del polinomio");
        return;
    }
    let polynomialModel = new PolynomialRegression();
    polynomialModel.fit(dataX, dataY, degree);
    let predictions = polynomialModel.predict(dataX);
    // Generación de tabla y gráfico (omitidos por brevedad)
}
```

### Gráficos

#### Código para Generar la Gráfica
Configuración del Dataset
El conjunto de datos para el gráfico se estructura en un array bidimensional, donde la primera fila representa los encabezados (X, Y, Predicted).

```javascript
let graphDataSet = [];
graphDataSet.push(["X", "Y", "Predicted"]);
for (let i = 0; i < dataX.length; i++) {
    graphDataSet.push([dataX[i], dataY[i], result[i]]);
}
```


### Configuración y Dibujado del Gráfico
La función drawChart toma el graphDataSet y lo convierte en un DataTable de Google Charts. Después, se configura el gráfico de líneas y se dibuja en el contenedor output-graph.

```javascript
google.charts.load("current", { packages: ["corechart"] });
google.charts.setOnLoadCallback(drawChart);

function drawChart() {
    var data = google.visualization.arrayToDataTable(graphDataSet);
    var options = {
        title: "Linear Regression",
        curveType: "function",
        legend: { position: "bottom" },
    };
    var chart = new google.visualization.LineChart(document.getElementById("output-graph"));
    chart.draw(data, options);
}
```


Ejemplo de Uso
1. Selecciona un modelo en el menú desplegable.
2. Carga un archivo CSV para entrenar el modelo.
3. Haz clic en el botón "Entrenar, predecir y graficar modelo".
4. Visualiza los resultados en la tabla y el gráfico.