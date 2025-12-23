# Documentación Fill rate 

Para el calculo de fill rate, consultamos una tabla de GCP, con la siguiente query:

```sql
SELECT case when  sum(articulos_solicitados)>1700000 then 1 else 0 end as val FROM `wmt-intl-adhoc-gcp-prod.BOD_AD_HOC_PRC.BOD_RD_BATOT_NBA` 

where fecha_entrega = DATE (CURRENT_DATE()-1)

and estatus_pedido='FINISHED'
 
SELECT nombre_tienda,fecha_entrega,

     sum(articulos_entregados) articulos_entregados, sum(articulos_solicitados) articulos_solicitados FROM `wmt-intl-adhoc-gcp-prod.BOD_AD_HOC_PRC.BOD_RD_BATOT_NBA` 

     where fecha_entrega between '2024-01-01' and '2025-12-31'

     --DATE_TRUNC(CURRENT_DATE()-1, MONTH) AND DATE (CURRENT_DATE()-1)

     and estatus_pedido='FINISHED'

    group by 1,2
 
```

# Ocupamos la siguiente fórmula para el cálculo 

```
sum([articulos_entregados])/ sum([articulos_solicitados])
```

# Documentación Capacidades 
Solicitar acceso a carpeta "CrowdsourcingData_bodega" - Pablo.Gerardo.Ramirez@walmart.com
ocupamos dos parquet para la obtención de la data:

* Disponibilidad_B3.parquet
* Disponibilidad_B3_1.parquet

# Anexamos la siguiente parte del código para generar DS N2H Y DS SD
```r
dispHist<-read_parquet(paste0("C:/Users/",user,"/OneDrive - Walmart Inc/CrowdsourcingData_bodega/Disponibilidad_B3.parquet")) 
  dispActual<-read_parquet(paste0("C:/Users/",user,"/OneDrive - Walmart Inc/CrowdsourcingData_bodega/Disponibilidad_B3_1.parquet"))
 
  disp <- rbind(dispHist,dispActual)
  disp<-disp %>% dplyr :: select (DELIVERY_DATE,STORE_NUMBER,SLOT_TOTALES,SLOTS_DISPONIBLES_2HORAS,SLOTS_DISPONIBLES_SAME_DAY)
  disp<-disp %>% dplyr:: rename(FECHA=DELIVERY_DATE,Det=STORE_NUMBER)
  disponibilidad<-disp%>% dplyr::group_by(FECHA,Det) %>% dplyr :: summarise(SLOT_TOTALES=sum(SLOT_TOTALES,na.rm = TRUE),SLOTS_2HORAS=sum(SLOTS_DISPONIBLES_2HORAS ,na.rm = TRUE),SLOTS_SD=sum(SLOTS_DISPONIBLES_SAME_DAY ,na.rm = TRUE))%>%
    ungroup()
 
```

# Validamos con el tablero de disponibilidad 
```
https://app.powerbi.com/groups/me/apps/2f394a42-4851-42c0-8f56-fddbff36abb4/reports/79ac4304-54d9-457f-8db8-5152408ecc7d/12c0c1082eaf5904150b?ctid=3cbcc3d3-094d-4006-9849-0d11d61f484d&experience=power-bi ```

# Las fórmulas que ocupamos para obtenerlo desde nuestros tableros son:



```(SUM(IFNULL([SLOTS_SD],0)))/(SUM(IFNULL([SLOT_TOTALES],0))) ```

```(SUM(IFNULL([SLOTS_2HORAS],0)))/(SUM(IFNULL([SLOT_TOTALES],0)))```







