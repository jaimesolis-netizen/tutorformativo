# Cocina CC-1

Tutor de aula para 20 minutos. Un clasificador de texto real, entrenado con 14 frases
de cocina, que el estudiante no consulta sino que audita y mide.

Curso DDC-1037, Sesión 7. Prototipo del trío Bustos / Sepúlveda / Solís-Pinilla.
Concepto de ML trabajado: **¿cómo se mide si un modelo "funciona"?**
Contenido disciplinar: cambio físico y cambio químico.
Destinatario: **8° año básico**.

---

## 1. Qué es

Un archivo HTML único, sin dependencias, sin red. Se abre con doble clic en cualquier
navegador y funciona igual con el wifi caído. Dentro hay un clasificador bayesiano
ingenuo que etiqueta frases como *físico* o *químico*, y toda su maquinaria está
expuesta: la base de entrenamiento, la tabla de pesos, la suma que produce cada
respuesta y el vocabulario que le falta.

La actividad no es "pregúntale al tutor". Es: clasifica tú, mira cómo arma su
respuesta, interrógalo sobre su propio funcionamiento, mídelo contra una referencia
revisada y decide si sirve.

## 2. Cómo se usa

- **Primero**: proyectar la pantalla "Cómo se usa" y leerla con el curso.
- **En clase**: abrir el archivo en el computador del aula y proyectar. También corre
  en el celular de cada estudiante si se comparte el archivo.
- **No requiere**: internet, cuenta, instalación, API key.
- **Sí requiere**: un navegador.
- El progreso se guarda en el navegador de cada estudiante. Al final hay un botón
  "Copiar ficha" que entrega el registro en texto para pegar en la guía del curso.

## 3. Las fases

La primera pantalla, **Cómo se usa**, va antes del cronómetro. Declara el propósito de
la actividad, explica en tres frases qué hace el programa y entrega la guía de uso paso
a paso, redactada para 8° básico. Está pensada para proyectarse y leerse en voz alta en
dos minutos antes de empezar. Además, cada fase abre con un recuadro verde
**Qué tienes que hacer aquí** con la instrucción concreta: qué botón apretar y dónde
mirar.

| Min | Fase | Qué hace el estudiante |
|---|---|---|
| antes | Cómo se usa | Lee el propósito, cómo funciona el programa y los cinco pasos |
| 0–4 | Tu clasificación | Etiqueta 8 casos de cocina sin ver el programa |
| 4–9 | Cómo arma la respuesta | Prueba frases, ve pesos por palabra, agrega ejemplos y reentrena |
| 9–13 | Interrogar al programa | 7 preguntas sobre su funcionamiento + prueba de la negación |
| 13–18 | Medir si funciona | Matriz de resultados, exactitud, tipos de error |
| 18–20 | Veredicto y pregunta | Decide, fija criterios de validez, formula pregunta investigable |

## 4. El modelo, en concreto

Naive Bayes multinomial sobre bolsa de palabras.

- **Tokenización**: minúsculas, se quitan tildes y signos, se descartan palabras de
  2 letras o menos y una lista corta de palabras funcionales.
- **Base**: 14 frases etiquetadas a mano (7 químicas, 7 físicas), 60 palabras distintas.
- **Peso de cada palabra**: `log((cQ+1)/(NQ+V)) − log((cF+1)/(NF+V))`, con suavizado
  de Laplace. Positivo tira a químico, negativo a físico.
- **Decisión**: se suman los pesos de las palabras presentes. Signo positivo → químico.
- **Confianza**: sigmoide del valor absoluto de la suma. Mide distancia al empate,
  **no** probabilidad de acierto. La interfaz lo dice con esas palabras.
- **Reentrenamiento**: al agregar un ejemplo, los pesos se recalculan en el navegador y
  todas las predicciones cambian. Es el mismo algoritmo, con otra base.

Nada de esto es una simplificación narrativa: es lo que el código hace, línea por línea.

## 5. Los 8 casos de prueba

Calibrados para que el modelo falle de forma diagnosticable, no al azar.

| Caso | Referencia | Qué entrega el modelo | Por qué importa |
|---|---|---|---|
| Pan tostado que se enfría | Físico | Químico, 65% | La palabra *pan* carga peso químico porque apareció en un ejemplo quemado |
| Clara de huevo que cuaja | Químico | Físico, 54% | La palabra *sartén* lo arrastra; la desnaturalización no está en ninguna parte |
| Azúcar que se disuelve en té | Físico | Físico, 72% | Acierta, pero *azúcar* y *disuelve* casi se anulan entre sí |
| Cuchillo con costra naranja | Químico | Químico, 96% | Acierta con confianza alta: la frase se parece mucho a un ejemplo de la base |
| Queso derretido con bordes quemados | Los dos | Físico, 68% | No existe la categoría mixta: el modelo no puede acertar |
| Sal que se disuelve | Físico | Físico, 98% | Casi copia de un ejemplo de entrenamiento |
| Limón que corta la leche | Químico | Químico, 77% | Acierta por *leche* y *grumos* |
| Hielo que se achica (sublimación) | Físico | Físico, 54% | Acierta por razón equivocada: *congelador* es desconocida, la sublimación no entra |

Resultado típico: 5 de 7 casos binarios, 71%, con dos errores de tipo distinto y uno
fuera del espacio de salida. Ese reparto es el material de la discusión sobre medición.

## 6. La prueba de la negación

Botón dedicado en la fase 3. "El pan se quema en el horno" y "El pan **no** se quema en
el horno" producen suma 2.26, etiqueta químico, 91%, en los dos casos. La palabra *no*
no está en la tabla y el orden de las palabras no se usa.

## 7. Cómo responde a la rúbrica de la guía

- **Veracidad técnica**: ninguna afirmación sobre el modelo es falsa. No hay verbos de
  agencia (piensa, entiende, decide, sabe); hay separar, buscar y sumar. La confianza se
  nombra como distancia al empate.
- **Manipulabilidad**: el estudiante clasifica primero, prueba frases propias, agrega
  ejemplos a la base y reentrena viendo cambiar pesos y matriz. Construye y decide.
- **Límite explícito**: una fase completa es interrogatorio sobre el propio
  funcionamiento, más un bloque fijo "Lo que este programa no hace" que se arrastra
  hasta la ficha de entrega.
- **Pregunta científica**: cierra con pregunta investigable, con iniciadores de frase.
- **Robustez pedagógica**: sin wifi, sin IA conectada. El modelo es un recurso dentro de
  la actividad, no su condición de funcionamiento.

Checklist de trampas: sin analogía neurona-cerebro, sin verbos de agencia, sin
antropomorfización visual (el programa no tiene nombre propio, cara ni voz), con modos
de falla visibles en pantalla, sin dicotomía IA buena / IA mala.

## 8. Lo que esta actividad no explica

Declarado dentro del aplicativo y repetido aquí:

- No contiene química. No hay enlaces, átomos ni reacciones dentro del programa.
- No tiene la categoría "los dos a la vez".
- No usa el orden de las palabras ni las negaciones.
- No relaciona palabras emparentadas: *congela* y *congelador* son entradas distintas.
- No busca en internet, no tiene fuentes, no entrega links ni DOI.
- No explica *por qué* un cambio es físico o químico. Eso lo pone una persona.
- No es un modelo de lenguaje. Comparte con ellos el ajuste de números a partir de
  ejemplos, y poco más. La interfaz dice esa diferencia en vez de insinuar equivalencia.

## 9. Qué puede ir mal

El riesgo principal no es técnico: es que el curso concluya "la IA es mala". El antídoto
está en la granularidad. Este programa falla por razones que se señalan con el dedo
sobre la tabla de pesos, y mejora cuando el estudiante le agrega ejemplos. La conclusión
que se busca no es sobre la bondad de la herramienta, sino sobre qué le exigimos a una
fuente, incluida la propia.

Segundo riesgo: que alguien lea el porcentaje de confianza como porcentaje de acierto.
Por eso los dos errores del banco salen con confianza alta, y el panel docente propone
abrir la discusión con esa pregunta: ¿preferirías un programa que se equivoca mucho
avisando, o uno que se equivoca poco con 95% de confianza?

## 10. Archivos

- `tutor-cocina.html` — el aplicativo completo, autocontenido.
- `README.md` — este documento.

Para editar la base de entrenamiento o los casos de prueba, abrir el HTML en un editor
de texto: están al inicio del bloque `<script>`, en los arreglos `TRAIN_BASE` y `CASES`.
Cualquier frase agregada a `TRAIN_BASE` entra al entrenamiento al recargar la página.
