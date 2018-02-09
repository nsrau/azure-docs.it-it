<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Livello di servizio Basic
| **Livello di prestazioni** | **Basic** |
| :--- | --: |
| Max DTU | 5 |
| Risorse di archiviazione incluse (GB) | 2 |
| Opzioni per lo spazio di archiviazione massimo (GB) | 2 |
| Spazio di archiviazione OLTP in memoria max (GB) |N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste) | 30 |
| Numero massimo di accessi simultanei | 30 |
| Numero massimo di sessioni simultanee | 300 |
|||

### <a name="standard-service-tier"></a>Livello di servizio Standard
| **Livello di prestazioni** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Numero max DTU** | 10 | 20 | 50 | 100 |
| Risorse di archiviazione incluse (GB) | 250 | 250 | 250 | 250 |
| Opzioni per lo spazio di archiviazione massimo (GB)* | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/D | N/D | N/D | N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 60 | 90 | 120 | 200 |
| Numero massimo di accessi simultanei | 60 | 90 | 120 | 200 |
| Numero massimo di sessioni simultanee |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Livello di servizio Standard (continua)
| **Livello di prestazioni** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Numero max DTU** | 200 | 400 | 800 | 1600 | 3000 |
| Risorse di archiviazione incluse (GB) | 250 | 250 | 250 | 250 | 250 |
| Opzioni per lo spazio di archiviazione massimo (GB)* | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | N/D | N/D | N/D | N/D |N/D |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 400 | 800 | 1600 | 3200 |6000 |
| Numero massimo di accessi simultanei | 400 | 800 | 1600 | 3200 |6000 |
| Numero massimo di sessioni simultanee |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Livello di servizio Premium 
| **Livello di prestazioni** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Max DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Risorse di archiviazione incluse (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opzioni per lo spazio di archiviazione massimo (GB)* | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Spazio di archiviazione OLTP in memoria max (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Numero massimo di accessi simultanei | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Numero massimo di sessioni simultanee | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

### <a name="premium-rs-service-tier"></a>Livello di servizio Premium RS 
| **Livello di prestazioni** | **PRS1** | **PRS2** | **PRS4** | **PRS6** |
| :--- |---:|---:|---:|---:|---:|---:|
| Max DTU | 125 | 250 | 500 | 1000 |
| Risorse di archiviazione incluse (GB) | 500 | 500 | 500 | 500 |
| Opzioni per lo spazio di archiviazione massimo (GB)* | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 |
| Spazio di archiviazione OLTP in memoria max (GB) | 1 | 2 | 4 | 8 |
| Numero massimo di ruoli di lavoro simultanei (richieste)| 200 | 400 | 800 | 1600 |
| Numero massimo di accessi simultanei | 200 | 400 | 800 | 1600 |
| Numero massimo di sessioni simultanee | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> \* Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* Nel livello Premium sono attualmente disponibili più di 1 TB di risorse di archiviazione nelle aree seguenti: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Francia centrale, Germania centrale, Giappone orientale, Giappone occidentale, Corea centrale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Regno Unito orientale, Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia ed Europa occidentale. Vedere [Limitazioni correnti per P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* Il numero massimo di DTU per database uguale o superiore a 200 nei siti Web Standard è disponibile in anteprima.
>
