<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->
 
### <a name="basic-elastic-pool-limits"></a>Limiti del pool elastico Basic

| Dimensioni pool (eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Spazio di archiviazione dati massimo per pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Spazio di archiviazione OLTP in memoria massimo per pool | N/D  | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di accessi simultanei per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Numero massimo di eDTU per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Spazio di archiviazione dati massimo per database | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limiti del pool elastico Standard

| Dimensioni pool (eDTU)  | **50** | **100** | **200**** | **300**** | **400**** | **800****| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Spazio di archiviazione dati massimo per pool* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Spazio di archiviazione OLTP in memoria massimo per pool | N/D  | N/D | N/D | N/D | N/D | N/D | 
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Numero massimo di accessi simultanei per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Numero massimo di eDTU per database** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Spazio di archiviazione dati massimo per database | 50 GB | 100 GB | 200 GB | 250 GB | 250 GB | 250 GB |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limiti del pool elastico Standard (continua) 

| Dimensioni pool (eDTU)  |  **1200**** | **1600**** | **2000**** | **2500**** | **3000**** |
|:---|---:|---:|---:| ---: | ---: |
| Spazio di archiviazione dati massimo per pool* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2,9 TB | 
| Spazio di archiviazione OLTP in memoria massimo per pool | N/D  | N/D | N/D | N/D | N/D | 
| Numero massimo di database per pool | 500 | 500 | 500 | 500 | 500 | 
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di accessi simultanei per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Numero massimo di eDTU per database** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Spazio di archiviazione dati massimo per database | 250 GB | 250 GB | 250 GB | 250 GB | 250 GB | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limiti del pool elastico Premium

| Dimensioni pool (eDTU)  | **125** | **250** | **500** | **1000** | **1500*****| 
|:---|---:|---:|---:| ---: | ---: | 
| Spazio di archiviazione dati massimo per pool* | 250 GB | 500 GB | 750 GB | 1 TB | 1,5 TB | 
| Spazio di archiviazione OLTP in memoria massimo per pool | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Numero massimo di database per pool | 50 | 100 | 100 | 100 | 100 |  
| Numero massimo di ruoli di lavoro simultanei per pool (richieste) | 200 | 400 | 800 | 1600 |  2400 | 
| Numero massimo di accessi simultanei per pool | 200 | 400 | 800 | 1600 |  2400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000 | 
| Numero massimo di eDTU per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000 |
| Spazio di archiviazione dati massimo per database | 250 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limiti del pool elastico Premium (continua) 

| Dimensioni pool (eDTU) | **2000***** | **2500***** | **3000***** | **3500***** | **4000*****|
|:---|---:|---:|---:| ---: | ---: | 
| Spazio di archiviazione dati massimo per pool* | 2 TB | 2,5 TB | 3 TB | 3,5 TB | 4 TB |
| Spazio di archiviazione OLTP in memoria massimo per pool | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Numero massimo di database per pool | 100 | 100 | 100 | 100 | 100 | 
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di accessi simultanei per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 |  0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Numero massimo di eDTU per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Spazio di archiviazione dati massimo per database | 500 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Limiti del pool elastico Premium RS

| Dimensioni pool (eDTU)  | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Spazio di archiviazione dati massimo per pool* | 250 GB| 500 GB | 750 GB | 750 GB |
| Spazio di archiviazione OLTP in memoria massimo per pool | 1 GB | 2 GB | 4 GB | 10 GB |
| Numero massimo di database per pool | 50 | 100 | 100 | 100 |
| Numero massimo di ruoli di lavoro simultanei (richieste) per pool | 200 | 400 | 800 | 1600 |
| Numero massimo di accessi simultanei per pool | 200 | 400 | 800 | 1600 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Numero massimo di eDTU per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Spazio di archiviazione dati massimo per database | 250 GB | 500 GB | 500 GB | 500 GB | 
||||||||

> [!IMPORTANT]
>\* Poiché i database in pool condividono lo spazio di archiviazione del pool, lo spazio di archiviazione dei dati è limitato allo spazio di archiviazione del pool rimanente o allo spazio di archiviazione massimo per database. 
>
>
>\*\* I numeri massimi e minimi di eDTU per database uguali o superiori a 200 sono disponibili in anteprima pubblica.
>
>\*\*\* Lo spazio di archiviazione dati massimo predefinito per ogni pool Premium con 500 o più eDTU è 750 GB. Per ottenere dimensioni di archiviazione dati massime per pool Premium superiori per 1000 o più eDTU, è necessario selezionare esplicitamente tali dimensioni usando il portale di Azure, [PowerShell](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-powershell), l'[interfaccia della riga di comando di Azure](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-azure-cli) o l'[API REST](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-rest-api). I pool Premium con spazio di archiviazione superiore a 1 TB sono attualmente in anteprima pubblica nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia, Europa occidentale, Germania centrale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale. Per tutte le altre aree, lo spazio di archiviazione massimo per pool è attualmente limitato a 750 GB.
>
