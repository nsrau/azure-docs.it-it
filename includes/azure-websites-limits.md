---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: b84b503fad951b5a6998bf0b06d466ce41031aa8
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72526751"
---
| Gruppi | Gratis | Condivisione | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [App Web, per dispositivi mobili o](https://azure.microsoft.com/services/app-service/) per le API per [app Azure piano di servizio](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup>|
| [Piano di servizio app](../articles/app-service/overview-hosting-plans.md) |10 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse|
| Tipo di istanza di calcolo |Condivisione |Condivisione |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |Dedicato<sup>3</sup>|
| [Scalabilità orizzontale](../articles/app-service/manage-scale-up.md) (numero massimo di istanze) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> |20 dedicati<sup>3</sup>|100 dedicati<sup>4</sup>|
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo CPU (5 minuti)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |
| Memoria (1 ora) |1\.024 MB per piano di servizio app |1\.024 MB per app |N/D |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web socket per istanza<sup>7</sup> |5 |35 |350 |Illimitato |Illimitato |Illimitato |
| Connessioni IP | 600 | 600 | Dipende dalle dimensioni dell'istanza<sup>8</sup> | Dipende dalle dimensioni dell'istanza<sup>8</sup> | Dipende dalle dimensioni dell'istanza<sup>8</sup> | 64.000 |
| [Connessioni di debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |5 |
| Certificati del servizio app per sottoscrizione<sup>9</sup>| Supporto non disponibile | Supporto non disponibile |10 |10 |10 |10 |
| Domini personalizzati per applicazione</a> |0 (solo sottodominio azurewebsites.net)|500 |500 |500 |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Non supportato. il certificato con caratteri jolly per *. azurewebsites.net è disponibile per impostazione predefinita|Non supportato. il certificato con caratteri jolly per *. azurewebsites.net è disponibile per impostazione predefinita|Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse|
| Connessioni ibride per piano | | | 5 | 25 | 200 | 200 |
| Bilanciamento del carico integrato | |X |X |X |X |X<sup>10</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backup pianificati](../articles/app-service/manage-backup.md) | | | | Backup pianificati ogni 2 ore, un massimo di 12 backup al giorno (manuale + pianificato) | Backup pianificati ogni ora, un massimo di 50 backup al giorno (manuale + pianificato) | Backup pianificati ogni ora, un massimo di 50 backup al giorno (manuale + pianificato) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Processi](../articles/app-service/webjobs-create.md)Web<sup>11</sup> |X |X |X |X |X |X |
| [Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slot di staging](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Contratto di servizio | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup>Le quote per applicazioni e di archiviazione sono per ogni piano di servizio app se non diversamente specificato.  
<sup>2</sup>Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>3</sup>Le istanze dedicate possono essere di dimensioni diverse. Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Sono consentite altre richieste su richiesta.  
<sup>5</sup> Il limite di archiviazione è la dimensione totale del contenuto in tutte le app nello stesso piano di servizio app. La dimensione totale del contenuto di tutte le app in tutti i piani di servizio app in un singolo gruppo di risorse e nell'area non può essere superiore a 500 GB.  
<sup>6</sup>Queste risorse sono vincolate dalle risorse fisiche nelle istanze dedicate (la dimensione dell'istanza e il numero di istanze).  
<sup>7</sup>Se si scala un'applicazione di livello base per due istanze, sono presenti 350 connessioni simultanee per ognuna delle due istanze. Per il livello standard e versioni successive, non esistono limiti teorici per i socket Web, ma altri fattori possono limitare il numero di socket Web. Ad esempio, il numero massimo di richieste simultanee consentite (definite da `maxConcurrentRequestsPerCpu`) è: 7.500 per macchina virtuale piccola, 15.000 per macchina virtuale media (7.500 x 2 Core) e 75.000 per macchina virtuale di grandi dimensioni (18.750 x 4 core).  
<sup>8</sup> Le connessioni IP massime sono per istanza e dipendono dalle dimensioni dell'istanza: 1.920 per l'istanza B1/S1/P1V2, 3.968 per l'istanza B2/S2/P2V2, 8.064 per l'istanza B3/S3/P3V2.  
<sup>9</sup> Il limite di quota certificato del servizio app per sottoscrizione può essere aumentato tramite una richiesta di supporto fino al limite massimo di 200.  
<sup>10</sup> Servizio app Isolato SKU possono essere con bilanciamento del carico interno (ILB) con Azure Load Balancer, quindi non esiste una connettività pubblica da Internet. Di conseguenza, alcune funzionalità di un servizio app isolato con carico interno bilanciato devono essere usati da macchine virtuali con accesso diretto all'endpoint di rete con bilanciamento del carico interno.  
<sup>11</sup> Eseguire file eseguibili e/o script personalizzati su richiesta, in base a una pianificazione o in modo continuo come attività in background nell'istanza del servizio app. È necessario Always On per l'esecuzione dei processi Web in modo continuo. L'Utilità di pianificazione di Azure Gratuito o Standard è necessaria per i processi Web pianificati. Non esiste un limite predefinito per il numero di processi Web che possono essere eseguiti in un'istanza del servizio app. Esistono limiti pratici che dipendono dal codice dell'applicazione che sta tentando di eseguire.  