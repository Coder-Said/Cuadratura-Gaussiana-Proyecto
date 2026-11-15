# Distribución de sustentación en el ala de un avión usando cuadratura gaussiana

Proyecto de la **Facultad de Ingeniería en Sistemas** que aplica la **cuadratura de Gauss-Legendre** para calcular la sustentación total generada a lo largo de la envergadura de un ala.

## Autores

- Said Josue Bravo González
- Jhon Alexander Solorzano Mendoza
- Deysi Abigail Guachamin Guanuña
- Alisson Quimbita

## Descripción

Un ala no genera la misma sustentación en toda su extensión: es máxima en el centro (junto al fuselaje) y disminuye hasta anularse en las puntas. En alas reales (con torsión o flaps) la distribución de carga no tiene antiderivada analítica sencilla, por lo que la sustentación total debe calcularse numéricamente.

La envergadura se normaliza al intervalo $[-1, 1]$ (`x = -1` punta izquierda, `x = 0` centro, `x = 1` punta derecha). Este dominio coincide con el de los polinomios de Legendre, así que la cuadratura se aplica **directamente**, sin cambio de variable ni escalado.

### Modelo utilizado

$$
L = \int_{-1}^{1} \sqrt{1-x^2}\; e^{-x^2/2}\, dx
$$

- `√(1 - x²)`: perfil elíptico de carga, que se anula en las puntas.
- `exp(-x²/2)`: modulación suave que simula efectos de torsión/flaps.

## Fundamento matemático

La cuadratura de Gauss-Legendre aproxima la integral con una suma ponderada:

$$
\int_{-1}^{1} f(x)\,dx \approx \sum_{i=1}^{n} w_i\, f(x_i)
$$

- **Nodos** $x_i$: raíces del polinomio de Legendre $P_n(x)$.
- **Pesos**:

$$
w_i = \frac{2}{(1-x_i^2)\,\left[P_n'(x_i)\right]^2}
$$

- **Recurrencia de Legendre**: $(k+1)P_{k+1}(x) = (2k+1)\,x\,P_k(x) - k\,P_{k-1}(x)$, con $P_0 = 1$, $P_1 = x$.
- **Derivada**: $P_n'(x) = \dfrac{n\,(x P_n(x) - P_{n-1}(x))}{x^2 - 1}$.

Con $n$ nodos, el método integra de forma exacta polinomios de grado hasta $2n-1$.

## Implementación

El código **no usa librerías externas** (solo el módulo estándar `math`). Los pasos son:

1. `carga_ala(x)`: función a integrar.
2. `legendre(n, x)`: evalúa $P_n$ y $P_{n-1}$ por recurrencia.
3. `nodos_pesos(n)`: calcula nodos por Newton-Raphson y pesos con la fórmula cerrada.
4. `gauss_legendre(f, n)`: evalúa la suma ponderada.
5. `referencia()`: valor de comparación independiente de `scipy`, usando el cambio $x = \sin t$ (integrando suave) y Simpson compuesto.

El programa imprime una tabla con el resultado y el error absoluto para `n = 2, 4, 8, 16, 32`.

> **Nota:** como $\sqrt{1-x^2}$ tiene derivada singular en $\pm 1$, el error de Gauss-Legendre disminuye de forma algebraica (no exponencial) al aumentar $n$. Es el comportamiento esperado para este integrando.

## Estructura del repositorio

| Archivo | Contenido |
|---|---|
| `Proyecto.ipynb` | Informe en Quarto (render a PDF): introducción, problema, solución y objetivos. Las secciones de fundamento matemático, implementación, solución e interpretación y referencias están pendientes. |
| `Proyecto_Comp_Aplicación.ipynb` | Código completo de Gauss-Legendre desde cero (formato Google Colab). |
| `ref.bib` | Bibliografía usada por el informe (referenciada en `Proyecto.ipynb`). |
| `before-body.tex` | Plantilla LaTeX parcial usada por el informe (referenciada en `Proyecto.ipynb`). |

## Requisitos

- **Código (`Proyecto_Comp_Aplicación.ipynb`)**: Python 3.8+ y Jupyter o Google Colab. No requiere instalar paquetes.
- **Informe (`Proyecto.ipynb`)**: [Quarto](https://quarto.org/), una distribución de LaTeX (por ejemplo TinyTeX) y un kernel de Jupyter con Python 3.

## Cómo ejecutarlo

**Código**

1. Abre `Proyecto_Comp_Aplicación.ipynb` en Jupyter o súbelo a Google Colab.
2. Ejecuta la celda para ver la tabla de nodos, resultado y error absoluto.

**Informe en PDF**

```bash
quarto render Proyecto.ipynb --to pdf
```

## Objetivos

- Entender qué representa la cuadratura de Gauss-Legendre.
- Comprender sus diferencias con otros métodos de integración.
- Aplicar métodos teóricos a un problema real para obtener soluciones eficientes.

## Estado del proyecto

- [x] Implementación de Gauss-Legendre sin librerías
- [x] Valor de referencia independiente de `scipy`
- [ ] Completar el fundamento matemático y el desarrollo de ecuaciones en el informe
- [ ] Incluir la solución, su interpretación y las referencias
- [ ] Añadir `ref.bib` y `before-body.tex` al repositorio

## Referencias

Ver `ref.bib` y la sección *Referencias* del informe.
