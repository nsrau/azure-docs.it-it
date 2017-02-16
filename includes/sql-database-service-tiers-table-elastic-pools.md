<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-elastic-pool-limits"></a>Limiti del pool elastico Basic

| Dimensioni pool (eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Spazio di archiviazione massimo per pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Spazio di archiviazione OLTP in memoria max per pool* | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di accessi simultanei per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} |
| Numero massimo di eDTU per database | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limiti del pool elastico Standard

| Dimensioni pool (eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Spazio di archiviazione massimo per pool* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Spazio di archiviazione OLTP in memoria max per pool* | N/D | N/D | N/D | N/D | N/D | N/D | 
| Numero massimo di database per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Numero massimo di ruoli di lavoro simultanei per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Numero massimo di accessi simultanei per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Numero minimo di eDTU per database | {0,10,20,<br>50} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Numero massimo di eDTU per database | {10,20,<br>50} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limiti del pool elastico Standard (continua) 

| Dimensioni pool (eDTU)  |  **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Spazio di archiviazione massimo per pool* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2,9 TB | 
| Spazio di archiviazione OLTP in memoria max per pool* | N/D | N/D | N/D | N/D | N/D | 
| Numero massimo di database per pool | 500 | 500 | 500 | 500 | 500 | 500 |
| Numero massimo di ruoli di lavoro simultanei per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di accessi simultanei per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Numero massimo di eDTU per database | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limiti del pool elastico Premium

| Dimensioni pool (eDTU)  | **125** | **250** | **500** | **1000** | **1500** | 
|:---|---:|---:|---:| ---: | ---: | 
| Spazio di archiviazione massimo per pool* | 250 GB| 500 GB| 750 GB| 750 GB| 750 GB| 
| Spazio di archiviazione OLTP in memoria max per pool* | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Numero massimo di database per pool | 50 | 100 | 100 | 100 | 100 |  
| Numero massimo di ruoli di lavoro simultanei per pool | 200 | 400 | 800 | 1600 |  2400 | 
| Numero massimo di accessi simultanei per pool | 200 | 400 | 800 | 1600 |  2400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database | {0,25,50,75,<br>125} | {0,25,50,75,<br>125,250} | {0,25,50,75,<br>125,250,500} | {0,25,50,75,<br>125,250,500,<br>1000} | {0,25,50,75,<br>125,250,500,<br>1000,1500} | 
| Numero massimo di eDTU per database | {25,50,75,<br>125} | {25,50,75,<br>125,250} | {25,50,75,<br>125,250,500} | {25,50,75,<br>125,250,500,<br>1000} | {25,50,75,<br>125,250,500,<br>1000,1500} |  
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limiti del pool elastico Premium (continua) 

| Dimensioni pool (eDTU)  |  **2000** | **2500** | **3000** | **3500** | **4000** |
|:---|---:|---:|---:| ---: | ---: | 
| Spazio di archiviazione massimo per pool* | 750 GB | 750 GB | 750 GB | 750 GB | 750 GB |
| Spazio di archiviazione OLTP in memoria max per pool* | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Numero massimo di database per pool | 100 | 100 | 100 | 100 | 100 | 
| Numero massimo di ruoli di lavoro simultanei per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di accessi simultanei per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Numero massimo di sessioni simultanee per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Numero minimo di eDTU per database | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} |  {0,25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
| Numero massimo di eDTU per database | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
||||||||

\* Poiché i database in pool condividono lo spazio di archiviazione del pool, lo spazio di archiviazione del database è limitato allo spazio di archiviazione del pool rimanente o allo spazio di archiviazione massimo per database. Lo spazio di archiviazione massimo per pool fa riferimento allo spazio di archiviazione massimo per i file di dati nel pool e non include lo spazio usato dai file di log.



<!--HONumber=Jan17_HO3-->


