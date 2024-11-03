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


### JavaScript para Procesamiento y Visualización de Datos
El siguiente código JavaScript realiza las siguientes funciones clave:

1. Cargar y procesar archivos CSV: Los datos se extraen y organizan en arrays.
2. Entrenar modelos de regresión: Se procesan modelos de regresión lineal y polinomial, calculando predicciones en función de los datos.
3. Visualizar resultados: Los datos y las predicciones se presentan en tablas y gráficos interactivos.


### Funciones JavaScript
#### 1. Carga y Limpieza de Datos
clearResults: Elimina el contenido de la tabla de resultados y el gráfico.

```javascript
function clearResults() {
    document.getElementById("output-table").innerHTML = "";
    document.getElementById("output-graph").innerHTML = "";
}
```
#### 2. resetData
Reinicia la interfaz, limpia los arrays dataX y dataY, y elimina el contenido de la tabla de parámetros.

```javascript
function resetData() {
    document.getElementById("fileInput").value = "";
    dataX = [];
    dataY = [];
    document.getElementById("parametersTable").innerHTML = "";
    clearResults();
}
```

#### 3. Procesamiento de Archivos CSV
parseCSVData: Esta función procesa el archivo CSV cargado. Extrae los datos, organiza las columnas y los muestra en la tabla de parámetros. Los datos de la primera y segunda columna se almacenan en dataX y dataY, respectivamente.

```javascript
function parseCSVData(csvData) {
    const lines = csvData.trim().split("\n");
    const header = lines[0].split(",");
    const table = document.getElementById("parametersTable");

    // Crea el encabezado de la tabla
    const headerRow = table.insertRow();
    header.forEach(col => {
        const cell = headerRow.insertCell();
        cell.textContent = col;
    });

    // Llena la tabla y almacena los datos
    lines.slice(1).forEach(line => {
        const row = table.insertRow();
        const values = line.split(",");
        values.forEach(val => {
            const cell = row.insertCell();
            cell.textContent = val;
        });
        dataX.push(parseFloat(values[0]));
        dataY.push(parseFloat(values[1]));
    });
    document.getElementById("data-csv").style.display = "block";
}
```

#### 4. Selección y Entrenamiento del Modelo
processLinearRegression: Entrena un modelo de regresión lineal con dataX y dataY, y muestra los resultados.

``` javascript
function processLinearRegression() {
    const linearModel = new LinearRegression();
    linearModel.fit(dataX, dataY);
    displayResults(dataX, dataY, linearModel.predict(dataX), "Regresión Lineal");
}
```

#### 5. rocessPolynomialRegression
Entrena un modelo de regresión polinomial usando el grado ingresado y calcula las predicciones.

```javascript
function processPolynomialRegression() {
    const degree = parseInt(document.getElementById("polynomialDegree").value);
    if (!degree || degree <= 0) {
        alert("Ingrese un grado de polinomio válido");
        return;
    }
    const polyModel = new PolynomialRegression();
    polyModel.fit(dataX, dataY, degree);
    displayResults(dataX, dataY, polyModel.predict(dataX), "Regresión Polinomial");
}
```

#### 6. Visualización de Resultados
displayResults: Muestra los datos originales y las predicciones en una tabla y un gráfico.

```javascript
function displayResults(xData, yData, predictions, title) {
    const tableHTML = `
        <h2>Resultados</h2>
        <table>
            <thead>
                <tr><th>X</th><th>Y</th><th>Y-Predicción</th><th>Error %</th></tr>
            </thead>
            <tbody>
                ${xData.map((x, i) => `
                    <tr>
                        <td>${x}</td>
                        <td>${yData[i]}</td>
                        <td>${predictions[i]}</td>
                        <td>${calculateError(yData[i], predictions[i])}</td>
                    </tr>`).join('')}
            </tbody>
        </table>`;
    document.getElementById("output-table").innerHTML = tableHTML;
    drawGraph(xData, yData, predictions, title);
}
```

#### 7. drawGraph
Dibuja un gráfico interactivo con los datos originales y las predicciones.

```javascript
function drawGraph(xData, yData, predictions, title) {
    const graphData = [["X", "Y", "Predicción"]].concat(xData.map((x, i) => [x, yData[i], predictions[i]]));
    google.charts.load("current", { packages: ["corechart"] });
    google.charts.setOnLoadCallback(() => {
        const data = google.visualization.arrayToDataTable(graphData);
        const options = { title, curveType: "function", legend: { position: "bottom" } };
        const chart = new google.visualization.LineChart(document.getElementById("output-graph"));
        chart.draw(data, options);
    });
}
```

#### 8. calculateError
Calcula el error porcentual entre el valor actual y el valor predicho.

```javascript
function calculateError(actual, predicted) {
    return ((Math.abs(actual - predicted) / actual) * 100).toFixed(2);
}
```



Ejemplo de Uso
1. Selecciona un modelo en el menú desplegable.
2. Carga un archivo CSV para entrenar el modelo.
3. Haz clic en el botón "Entrenar, predecir y graficar modelo".
4. Visualiza los resultados en la tabla y el gráfico.