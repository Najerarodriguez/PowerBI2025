1. Abrir Power BI Desktop.
2. Cargar el archivo CSV.
3. Confirmar que la columna **fecha** esté en tipo Date.
4. Ir a **Modelado → Nueva tabla**.
5. Crear una tabla de calendario con esta fórmula:

   ```
   Calendario = CALENDAR(MIN('CSV'[fecha]), MAX('CSV'[fecha]))
   ```
6. Ir al panel **Relaciones**.
7. Conectar **Calendario[Date]** con **CSV[fecha]**.
8. Ir a **Modelado → Nueva tabla**.
9. Crear una tabla llamada **CortesSnapshot**:

   ```
   CortesSnapshot = 
   ADDCOLUMNS(
       SUMMARIZE(Calendario, Calendario[Date]),
       "EsCorte", IF(DAY(Calendario[Date]) = DAY(EOMONTH(Calendario[Date],0)), 1, 0)
   )
   ```
10. Ir al panel **Relaciones**.
11. NO crear relación entre **CortesSnapshot** y las otras tablas.
12. Ir a **Modelado → Nueva medida**.
13. Crear una medida de ventas:

```
Ventas = SUMX('CSV', 'CSV'[cantidad] * 'CSV'[precio unidad])
```

14. Ir a **Modelado → Nueva medida**.
15. Crear una medida para obtener la fecha de corte seleccionada:

```
Fecha Corte Seleccionada = MAX('CortesSnapshot'[Date])
```

16. Ir a **Modelado → Nueva medida**.
17. Crear la medida de snapshot:

```
Ventas Snapshot =
CALCULATE(
    [Ventas],
    TREATAS( { [Fecha Corte Seleccionada] }, 'CSV'[fecha] )
)
```

18. Ir a la pestaña **Informe**.
19. Insertar una tabla visual.
20. Agregar columnas: provincia, vendedor, tienda, categoria, articulo.
21. Agregar la medida **Ventas Snapshot** como valor.
22. Insertar un slicer.
23. Asignar al slicer el campo **CortesSnapshot[Date]**.
24. Seleccionar una fecha de corte en el slicer.
25. La tabla mostrará los valores congelados al corte seleccionado.
