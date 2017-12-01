| Risorsa | Free | Shared (Anteprima) | Basic | Standard | Premium (anteprima)</th> |
| --- | --- | --- | --- | --- | --- |
| [App Web, per dispositivi mobili o per le API](https://azure.microsoft.com/services/app-service/) per [piano di servizio app](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |
| [App per la logica](https://azure.microsoft.com/services/app-service/logic/) per [piano di servizio app](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 per ogni core |20 per ogni core |
| [Piano di servizio app](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |
| Tipo di istanza di calcolo |Condiviso |Condiviso |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |
| [Scalabilità orizzontale](../articles/app-service/web-sites-scale.md) (numero massimo di istanze) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> |20 dedicati (50 nell'ambiente del servizio app)<sup>3,4</sup> |
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Tempo di CPU (5 min)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con tariffe standard |Senza limiti, pagamento con tariffe standard |
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con tariffe standard |Senza limiti, pagamento con tariffe standard |
| Memoria (1 ora) |1024 MB per ogni piano di servizio App |1024 MB per ogni applicazione |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Trasferimento dei dati applicate tariffe Unlimited |Trasferimento dei dati applicate tariffe Unlimited |Trasferimento dei dati applicate tariffe Unlimited |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web Socket per ogni istanza<sup>7</sup> |5 |35 |350 |Illimitato |Illimitato |
| [Connessioni di debugger](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |
| [Sottodominio azurewebsites.net con FTP/S e SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [dominio personalizzato](../articles/app-service/app-service-web-tutorial-custom-domain.md) | |X |X |X |X |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |
| Bilanciamento del carico integrato | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Backup pianificati](../articles/app-service/web-sites-backup.md) | | | |12 al giorno |Una volta ogni 5 minuti<sup>8</sup> |
| [Scalabilità automatica](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>9</sup> |X |X |X |X |X |
| [Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Slot di staging](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Domini personalizzati per applicazione</a> | |500 |500 |500 |500 |
| Contratto di servizio | |<p> |99,9% |99.95%<sup>10</sup> |99.95%<sup>10</sup> |

<sup>1</sup>Le quote per applicazioni e di archiviazione sono per ogni piano di servizio app se non diversamente specificato.  
<sup>2</sup>Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>3</sup>Le istanze dedicate possono essere di dimensioni diverse. Vedere [prezzi servizio App](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) per ulteriori dettagli.  
<sup>4</sup>Il livello premium consente fino a 50 istanze di calcolo (a seconda della disponibilità) e 500 GB di spazio su disco quando si utilizzano ambienti di servizio app e 20 istanze di calcolo e archiviazione di 250 GB in caso contrario.  
<sup>5</sup>Il limite di archiviazione è la dimensione totale del contenuto tra tutte le applicazioni nello stesso piano di servizio dell'applicazione. Altre opzioni di archiviazione sono disponibili nell'[ambiente del servizio app](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>Queste risorse sono vincolate dalle risorse fisiche nelle istanze dedicate (la dimensione dell'istanza e il numero di istanze).  
<sup>7</sup>Se si scala un'applicazione di livello base per due istanze, sono presenti 350 connessioni simultanee per ognuna delle due istanze.  
<sup>8</sup>Il livello premium consente gli intervalli di backup verso il basso fino a ogni 5 minuti quando si utilizza ambienti di servizio app e 50 volte al giorno in caso contrario.  
<sup>9</sup>Eseguire gli eseguibili e/o gli script personalizzati su richiesta, in base a una pianificazione o continuamente come attività in background nell'istanza di servizi app. Always On è richiesto per l'esecuzione continua di Processi Web. L'utilità di pianificazione di Azure di livello Free o Standard è richiesta per la funzionalità Processi Web pianificata. Non esiste alcun limite predefinito al numero di processi Web che è possibile eseguire in un'istanza del servizio app, ma vi sono limiti pratici che dipendono dall'operazione che sta tentando di eseguire il codice dell'applicazione.   
<sup>10</sup>SLA pari al 99,95% fornito per distribuzioni che utilizzano più istanze di gestione traffico di Azure configurato per il failover.  

