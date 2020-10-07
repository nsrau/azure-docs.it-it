---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 557e20dcafabb2db0bc66c9dfda4977aca50de50
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745698"
---
| Risorsa | Gratuito | Condiviso | Basic | Standard | Premium (v3) | Isolato </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [App Web, per dispositivi mobili o per le API](https://azure.microsoft.com/services/app-service/) per [piano di servizio app di Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup>|
| [Piano di servizio app](../articles/app-service/overview-hosting-plans.md) |10 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse|
| Tipo di istanza di calcolo |Condiviso |Condiviso |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |Dedicato<sup>3</sup>|
| [Aumento delle istanze](../articles/app-service/manage-scale-up.md) (numero massimo di istanze) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> |30 dedicati<sup>3</sup>|100 dedicati<sup>4</sup>|
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Per dimensioni maggiori di 250 GB, inviare una richiesta di supporto. |1 TB<sup>5</sup> <br/><br/> La quota di archiviazione disponibile è di 999 GB. |
| Tempo di CPU (5 minuti)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |
| Memoria (1 ora) |1\.024 MB per ogni piano di servizio app |1\.024 MB per ogni app |N/D |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web Socket per ogni istanza<sup>7</sup> |5 |35 |350 |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |
| Connessioni IP | 600 | 600 | Dipende dalle dimensioni dell'istanza<sup>8</sup> | Dipende dalle dimensioni dell'istanza<sup>8</sup> | Dipende dalle dimensioni dell'istanza<sup>8</sup> | 16.000 |
| [Connessioni di debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |5 |
| Certificati del servizio app per sottoscrizione<sup>9</sup>| Non supportate | Non supportate |10 |10 |10 |10 |
| Domini personalizzati per applicazione</a> |0 (solo sottodominio azurewebsites.net)|500 |500 |500 |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/configure-ssl-certificate.md) |Non supportato. Certificato con caratteri jolly per \* disponibile per impostazione predefinita|Non supportato. Certificato con caratteri jolly per \* disponibile per impostazione predefinita|Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse|
| Connessioni ibride | | | 5 per piano | 25 per piano | 200 per app | 200 per app |
| [Integrazione della rete virtuale](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| Bilanciamento del carico integrato | |X |X |X |X |X<sup>10</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backup pianificati](../articles/app-service/manage-backup.md) | | | | Backup pianificati ogni 2 ore, massimo 12 backup al giorno (manuali e pianificati) | Backup pianificati ogni ora, massimo 50 backup al giorno (manuali e pianificati) | Backup pianificati ogni ora, massimo 50 backup al giorno (manuali e pianificati) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Processi Web](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slot di gestione temporanea](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| [Testing in Production](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Log di diagnostica](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Autenticazione e autorizzazione](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [Certificati gestiti del servizio app (anteprima pubblica)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| Contratto di servizio | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup>Le quote per applicazioni e di archiviazione sono per ogni piano di servizio app se non diversamente specificato.  
<sup>2</sup>Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>3</sup>Le istanze dedicate possono essere di dimensioni diverse. Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>Possibilità di aggiungerne su richiesta.  
<sup>5</sup>Il limite di archiviazione è la dimensione totale del contenuto tra tutte le applicazioni nello stesso piano di servizio app. Le dimensioni totali del contenuto di tutte le app in tutti i piani di servizio app in un unico gruppo di risorse e nell'area non può essere superiore a 500 GB.  
<sup>6</sup>Queste risorse sono vincolate dalle risorse fisiche nelle istanze dedicate (la dimensione dell'istanza e il numero di istanze).  
<sup>7</sup>Se si scala un'applicazione di livello base per due istanze, sono presenti 350 connessioni simultanee per ognuna delle due istanze. Per il livello Standard e per i livelli superiori non esistono limiti teorici per i socket Web, ma altri fattori possono limitare il numero di socket Web. Ad esempio, il numero massimo di richieste simultanee consentite (definite da `maxConcurrentRequestsPerCpu`) è: 7.500 per macchina virtuale di piccole dimensioni, 15.000 per macchina virtuale media (7.500 x 2 core) e 75.000 per macchina virtuale di grandi dimensioni (18.750 x 4 core).  
<sup>8</sup>Il numero massimo di connessioni IP viene calcolato per singola istanza e dipende dalle dimensioni dell'istanza: 1.920 per istanza B1/S1/P1V3, 3.968 per istanza B2/S2/P2V3, 8.064 per istanza B3/S3/P3V3.  
<sup>9</sup>Il limite di quota del certificato del servizio app per ogni sottoscrizione può essere aumentato fino a un massimo di 200 tramite una richiesta di supporto.  
<sup>10</sup>Gli SKU del servizio app Isolato possono applicare il bilanciamento del carico interno con Azure Load Balancer, ovvero senza connettività pubblica da Internet. Di conseguenza, alcune funzionalità di un servizio app isolato con carico interno bilanciato devono essere usate da macchine virtuali con accesso diretto all'endpoint di rete con bilanciamento del carico interno.  
<sup>11</sup>È possibile eseguire file eseguibili e/o script personalizzati su richiesta, in base a una pianificazione, oppure in modo continuativo come attività in background nell'istanza del servizio app. Always On è richiesto per l'esecuzione continua di Processi Web. Non esiste un limite predefinito per il numero di processi Web che possono essere eseguiti in un'istanza del servizio app. Esistono però limiti pratici che dipendono dalle operazioni che il codice dell'applicazione sta provando a eseguire.

<sup>12</sup>I domini di tipo naked non sono supportati. Solo emissione di certificati standard (i certificati con caratteri jolly non sono disponibili). Offerta limitata a un solo certificato gratuito per dominio personalizzato.
