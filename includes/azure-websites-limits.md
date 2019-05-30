---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238452"
---
| Resource | Gratuito | Condiviso | Basic | Standard | Premium (v2) | Isolato </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Le app Web, mobili o l'API](https://azure.microsoft.com/services/app-service/) per ogni [piano di servizio App di Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup>|
| [Piano di servizio app](../articles/app-service/overview-hosting-plans.md) |10 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse|
| Tipo di istanza di calcolo |Condiviso |Condiviso |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |Dedicato<sup>3</sup>|
| [Scalare in orizzontale](../articles/app-service/web-sites-scale.md) (numero massimo di istanze) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> |20 dedicati<sup>3</sup>|100 dedicati<sup>4</sup>|
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo di CPU (5 minuti)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |
| Memoria (1 ora) |1024 MB per ogni piano di servizio App |1024 MB per ogni app |N/D |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web socket per ogni istanza<sup>7</sup> |5 |35 |350 |Illimitato |Illimitato |Illimitato |
| [Connessioni di debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |5 |
| Certificati del servizio App per ogni sottoscrizione<sup>10</sup>| Non supportate | Non supportate |10 |10 |10 |10 |
| Domini personalizzati per applicazione</a> |0 (solo sottodominio azurewebsites.net)|500 |500 |500 |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Non supportato, il certificato con caratteri jolly per *. azurewebsites.net disponibile per impostazione predefinita|Non supportato, il certificato con caratteri jolly per *. azurewebsites.net disponibile per impostazione predefinita|Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse|
| Servizio di bilanciamento del carico integrati | |X |X |X |X |X<sup>9</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backup pianificati](../articles/app-service/manage-backup.md) | | | | Backup pianificati ogni 2 ore, massimo 12 backup al giorno (manuale e pianificati) | Backup pianificati ogni ora, un massimo di 50 backup al giorno (manuale e pianificati) | Backup pianificati ogni ora, un massimo di 50 backup al giorno (manuale e pianificati) |
| [Autoscale](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [Processi Web](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slot di staging](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Contratto di servizio | |  |99,9% |99,95%|99,95%|99,95%|  

<sup>1</sup>Le quote per applicazioni e di archiviazione sono per ogni piano di servizio app se non diversamente specificato.  
<sup>2</sup>Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>3</sup>Le istanze dedicate possono essere di dimensioni diverse. Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>siano consentite più su richiesta.  
<sup>5</sup>Il limite di archiviazione è la dimensione totale del contenuto tra tutte le applicazioni nello stesso piano di servizio dell'applicazione.  
<sup>6</sup>Queste risorse sono vincolate dalle risorse fisiche nelle istanze dedicate (la dimensione dell'istanza e il numero di istanze).  
<sup>7</sup>Se si scala un'applicazione di livello base per due istanze, sono presenti 350 connessioni simultanee per ognuna delle due istanze.  
<sup>8</sup>Eseguire gli eseguibili e/o gli script personalizzati su richiesta, in base a una pianificazione, oppure continuamente come attività in background nell'istanza del servizio app. Always On è richiesto per l'esecuzione continua di Processi Web. L'utilità di pianificazione di Azure di livello Free o Standard è richiesta per la funzionalità Processi Web pianificata. Non sono previsti limiti predefiniti per il numero di processi Web eseguibili in un'istanza del servizio App. Vi sono limiti pratici che dipendono da ciò che sta provando a eseguire il codice dell'applicazione.  
<sup>9</sup>agli SKU isolati del servizio app ha la possibilità di essere internamente carico bilanciato (bilanciamento del carico interno) con Azure Load Balancer, in modo che non è disponibile connettività pubblica da internet. Di conseguenza, alcune funzionalità di un servizio app isolato con carico interno bilanciato devono essere usate da macchine virtuali con accesso diretto all'endpoint di rete con bilanciamento del carico interno.  
<sup>10</sup>limite di quota del certificato di servizio App per ogni sottoscrizione può essere aumentato tramite una richiesta di supporto per un limite massimo di 200.  