<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Limiti del pool elastico Basic

| eDTU per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di accessi simultanei per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opzioni per il numero minimo di eDTU per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opzioni per il numero massimo di eDTU per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Spazio di archiviazione massimo per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limiti del pool elastico Standard

| eDTU per pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Risorse di archiviazione incluse per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Opzioni per lo spazio di archiviazione massimo per pool (GB)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | 
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Numero massimo di accessi simultanei per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opzioni per il numero minimo di eDTU per database | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opzioni per il numero massimo di eDTU per database | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Spazio di archiviazione massimo per database (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limiti del pool elastico Standard (continua) 

| eDTU per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Risorse di archiviazione incluse per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Opzioni per lo spazio di archiviazione massimo per pool (GB)* | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | N/D | N/D | N/D | N/D | N/D | 
| Numero massimo di database per pool | 500 | 500 | 500 | 500 | 500 | 
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di accessi simultanei per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opzioni per il numero minimo di eDTU per database | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opzioni per il numero massimo di eDTU per database | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Opzioni per lo spazio di archiviazione massimo per database (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limiti del pool elastico Premium

| eDTU per pool | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Risorse di archiviazione incluse per pool (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Opzioni per lo spazio di archiviazione massimo per pool (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | 1 | 2 | 4 | 10 | 12 | 
| Numero massimo di database per pool | 50 | 100 | 100 | 100 | 100 | 
| Numero massimo di ruoli di lavoro simultanei per pool (richieste) | 200 | 400 | 800 | 1600 | 2400 | 
| Numero massimo di accessi simultanei per pool | 200 | 400 | 800 | 1600 | 2400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Numero massimo di eDTU per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Spazio di archiviazione massimo per database (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limiti del pool elastico Premium (continua) 

| eDTU per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Risorse di archiviazione incluse per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Opzioni per lo spazio di archiviazione massimo per pool (GB)* | 2048 | 2560 | 3072 | 3548 | 4096|
| Spazio di archiviazione OLTP in memoria massimo per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Numero massimo di database per pool | 100 | 100 | 100 | 100 | 100 | 
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di accessi simultanei per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opzioni per il numero minimo di eDTU per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Opzioni per il numero massimo di eDTU per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Spazio di archiviazione massimo per database (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||


> [!IMPORTANT]
> \* Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per informazioni dettagliate, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per informazioni dettagliate, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* Nel livello Premium sono attualmente disponibili più di 1 TB di risorse di archiviazione nelle aree seguenti: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Francia centrale, Germania centrale, Giappone orientale, Giappone occidentale, Corea centrale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Regno Unito orientale, Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia ed Europa occidentale. Vedere [Limitazioni correnti per P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
>

