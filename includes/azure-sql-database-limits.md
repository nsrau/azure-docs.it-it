| Risorsa | Limite predefinito |
| --- | --- |
| Dimensioni database |Dipende dal livello di prestazioni <sup>1</sup> |
| Account di accesso |Dipende dal livello di prestazioni <sup>1</sup> |
| Utilizzo della memoria |Concessione di memoria di 16 MB per più di 20 secondi |
| Sessioni |Dipende dal livello di prestazioni <sup>1</sup> |
| Dimensioni di tempdb |5 GB |
| Durata della transazione |24 ore<sup>2</sup> |
| Blocchi per transazione |1 milione |
| Dimensioni di ciascuna transazione |2 GB |
| Percentuale di spazio totale di log utilizzato per transazione |20% |
| Numero massimo di richieste simultanee (thread di lavoro) |Dipende dal livello di prestazioni <sup>1</sup> |

<sup>1</sup>Database SQL con livelli di prestazioni, ad esempio Basic, Standard e Premium. Standard e Premium inoltre sono suddivise in livelli di prestazioni. Per i limiti dettagliati per ogni livello e livello di servizio, vedere [livelli di servizio del Database SQL Azure e livelli di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Se la transazione blocca una risorsa richiesta da un'attività di sistema sottostante, la durata massima è 20 secondi.

<!---HONumber=August15_HO7-->