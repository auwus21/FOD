# 📚 Fundamentos de Organización de Datos (FOD) - Clase 2

> **Resumen de Algoritmia Clásica con Archivos** 🚀  
> Basado en las diapositivas oficiales de la UNLP - Facultad de Informática.

---

## 🎯 Agenda de la Clase
La clase repasa las operaciones usuales que se resuelven utilizando archivos secuenciales en Pascal:
1. **Agregar nuevos elementos** a un archivo.
2. **Actualización (Maestro-Detalle):** Modificar un archivo maestro en base a novedades.
3. **Corte de Control:** Generación de reportes agrupados.
4. **Merge:** Fusión de varios archivos en uno solo.

---

## 1️⃣ Agregar Datos a un Archivo Existente
- Se procesa un solo archivo que ya contiene información.
- Se incorporan datos nuevos posicionando el puntero directamente al final del archivo con `seek(archivo, filesize(archivo))`.

```pascal
Procedure agregar (Var Emp: Empleados); 
var E: registro;
begin
    reset(Emp); 
    seek(Emp, filesize(Emp)); // Nos posicionamos al final
    leer(E); 
    while E.nombre <> ' ' do begin
        write(Emp, E);     
        leer(E); 
    end;
    close(Emp);
end;
```

---

## 2️⃣ Actualización Maestro - Detalle
Este problema involucra sincronizar y utilizar al mismo tiempo varios archivos de datos.

- 👑 **Archivo Maestro:** Resume un determinado conjunto de datos (ej. stock actual).
- 📝 **Archivo Detalle:** Agrupa información de novedades/transacciones diarias que se usarán para modificar el maestro (ej. ventas del día).

> [!CAUTION]
> **Precondiciones Críticas:**
> - Ambos archivos (maestro y detalle) deben estar **ordenados por el mismo criterio**.
> - En el archivo detalle usualmente *solo aparecen elementos que existen en el maestro* (dependiendo de la variante del problema).

### 🛠️ Variantes Vistas:
1. **1 Maestro / 1 Detalle (Sin repetición):** Cada elemento del maestro aparece a lo sumo una vez en el detalle.
2. **1 Maestro / 1 Detalle (Con repetición):** Un mismo elemento puede aparecer varias veces en el archivo detalle a lo largo del día. Se suman las cantidades usando un corte por código, y luégo se reemplaza/graba en el maestro. *Es necesario retroceder el puntero del maestro usando `seek(mae, filepos(mae)-1)` antes de hacer el `write`.*
3. **1 Maestro / N Detalles:** Se generaliza el problema usando una función modular llamada `minimo()`. Esta función toma los registros de los diferentes detalles y devuelve el que tiene el código más chico, avanzando únicamente el archivo del cual provino ese registro mínimo.

---

## 3️⃣ Corte de Control
Es un problema clásico en el manejo de bases de datos para la **generación de reportes jerárquicos**.

- 📌 **Precondición fundamental:** El archivo debe encontrarse previamente **ordenado** por todos los campos sobre los cuales se va a realizar el "corte" (ej. Provincia -> Partido -> Ciudad).
- **Lógica principal:** Mientras se recorre secuencialmente, se acumulan contadores/totalizadores. Se utilizan variables provisorias (ej. `ant_prov`, `ant_partido`) para detectar un cambio. Cuando el dato actual no coincide con la variable `ant_...` **"se produce un corte"**, se imprimen los totales calculados y se enceran los contadores.

---

## 4️⃣ Merge (Fusión de Archivos)
Consiste en tomar múltiples archivos con contenido similar y resumirlos en 1 **único archivo nuevo**.

> [!CAUTION]
> **Precondiciones Críticas:**
> - Todos los archivos detalle tienen exactamente la **misma estructura**.
> - Todos están **ordenados por igual criterio**.

### 🛠️ Variantes Vistas:
1. **Merge de 3 archivos sin repetición:** Por ejemplo, tres archivos de inscripciones generados en tres PCs distintas. Se aplica la lógica de selección del mínimo y se graba directo al nuevo maestro.
2. **Merge de 3 archivos con repetición:** Se totalizan/sumarizan los registros con igual clave aplicando una lógica de "Corte temporal" (mientras que el código mínimo se mantenga igual) antes de grabarlo como un único registro en el maestro final.
3. **Merge de N archivos:** Escala la solución a N archivos.
   - Requiere manejar un `array of file` para los archivos físicos.
   - Requiere un `array of records` para almacenar el registro actual que fue leído de cada archivo.
   - La función `minimo()` hace una iteración `for` simple sobre el vector de registros para determinar el menor disponible.

---
*Hecho para ser visualizado en GitHub con Markdown.* 🚀
