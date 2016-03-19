
| **Identificatore limite** | **Limite** | **Commenti** |
|-----------------------------------------------------------------------------------------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Capacità totale (incluso il cloud) | Fino a 64 TB per dispositivo virtuale |
| Numero massimo di credenziali dell'account di archiviazione per dispositivo | 1 | |
| Numero massimo di volumi/condivisioni | 16 | |
| Dimensione minima di volumi/condivisioni a livelli | 500 GB | |
| Dimensione massima di volume/condivisione a livelli | 20 TB | |
| Dimensione minima di volume/condivisione aggiunto/a in locale | 50 GB | |
| Dimensione massima di volume/condivisione aggiunto/a in locale | 2 TB | |
| Numero massimo di connessioni iSCSI dagli iniziatori | 512 | |
| Numero massimo di record di controllo di accesso per dispositivo | 64 | |
| Numero massimo di backup conservati dal dispositivo virtuale nella cartella *.backups* per file server | 5 | Sono inclusi i backup pianificati (generati dal criterio di backup predefinito) e manuali più recenti. |
| Numero massimo di backup pianificati conservati dal dispositivo | 55 | 30 backup giornalieri<br>12 backup mensili<br>13 backup annuali |
| Numero massimo di backup manuali conservati dal dispositivo | 45 | |
| Numero massimo di volumi che possono essere elaborati parallelamente per backup o ripristino | 3 | Se sono presenti più di 3 volumi, questi vengono elaborati in sequenza man mano che gli slot di elaborazione si rendono disponibili. |
| Tempo di recupero del ripristino | Ripristino rapido | Il ripristino si basa sulla mappa termica ed è dipende dalla dimensione del volume.<br>Operazioni di backup possono verificarsi quando un'operazione di ripristino è in corso. |

<!---HONumber=AcomDC_0121_2016-->