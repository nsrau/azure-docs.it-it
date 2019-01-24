---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: d66f36e737e100a0d8e60b85b51f6dcf632e6d3e
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "54404681"
---
| Risorsa | Gratuito | Condiviso | Basic | Standard | Premium (v2) | Isolato </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [App Web, per dispositivi mobili o per le API](https://azure.microsoft.com/services/app-service/) per [piano di servizio app](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup>|
| [Piano di servizio app](../articles/app-service/overview-hosting-plans.md) |10 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse|
| Tipo di istanza di calcolo |Condiviso |Condiviso |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |Dedicato<sup>3</sup>|
| [Scalabilità orizzontale](../articles/app-service/web-sites-scale.md) (numero massimo di istanze) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> |20 dedicati<sup>3</sup>|100 dedicati<sup>4</sup>|
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo di CPU (5 min)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |
| Memoria (1 ora) |1024 MB per ogni piano di servizio App |1024 MB per ogni applicazione |N/D |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web Socket per ogni istanza<sup>7</sup> |5 |35 |350 |Illimitato |Illimitato |Illimitato |
| [Connessioni di debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |5 |
| Certificati del servizio app per sottoscrizione<sup>10</sup>| Non supportate | Non supportate |10 |10 |10 |10 |
| Domini personalizzati per applicazione</a> |0 (solo sottodominio azurewebsites.net)|500 |500 |500 |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Non supportati. Certificato con caratteri jolly per *.azurewebsites.net disponibile per impostazione predefinita.|Non supportati. Certificato con caratteri jolly per *.azurewebsites.net disponibile per impostazione predefinita.|Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse|
| Bilanciamento del carico integrato | |X |X |X |X |X<sup>9</sup> |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [Backup pianificati](../articles/app-service/manage-backup.md) | | | | Backup pianificati ogni 2 ore, massimo 12 backup al giorno (manuali e pianificati) | Backup pianificati ogni ora, massimo 50 backup al giorno (manuali e pianificati) | Backup pianificati ogni ora, massimo 50 backup al giorno (manuali e pianificati) |
| [Scalabilità automatica](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [Processi Web](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slot di staging](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Contratto di servizio | |  |99,9% |99,95%|99,95%|99,95%|  

<sup>1</sup> Le quote per app e archiviazione sono per piano di servizio app, se non diversamente specificato.  
<sup>2</sup> Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>3</sup> Le istanze dedicate possono essere di dimensioni diverse. Vedere [prezzi servizio App](https://azure.microsoft.com/pricing/details/app-service/) per ulteriori dettagli.  
<sup>4</sup> Possibilità di aggiungerne su richiesta.  
<sup>5</sup> Il limite di archiviazione è la dimensione totale del contenuto di tutte le app nello stesso piano di servizio app.  
<sup>6</sup> Queste risorse sono vincolate dalle risorse fisiche delle istanze dedicate (dimensione dell'istanza e numero di istanze).  
<sup>7</sup> Se si ridimensiona a due istanze un'app nel livello Basic, sono presenti 350 connessioni simultanee per ognuna delle due istanze.  
<sup>8</sup> Eseguire eseguibili e/o script personalizzati su richiesta, in base a una pianificazione, oppure continuamente come attività in background nell'istanza del servizio app. Always On è richiesto per l'esecuzione continua di Processi Web. L'utilità di pianificazione di Azure di livello Free o Standard è richiesta per la funzionalità Processi Web pianificata. Non esiste alcun limite predefinito al numero di processi Web che è possibile eseguire in un'istanza del servizio app, ma vi sono limiti pratici che dipendono dall'operazione che sta tentando di eseguire il codice dell'applicazione.  
<sup>9</sup> Gli SKU del servizio app Isolato sono in grado di applicare il bilanciamento del carico interno con Azure Load Balancer, ovvero senza connettività pubblica da Internet. Di conseguenza, alcune funzionalità di un servizio app isolato con carico interno bilanciato devono essere usate da macchine virtuali con accesso diretto all'endpoint di rete con bilanciamento del carico interno.  
<sup>10</sup> Il limite di quota del certificato del servizio app per ogni sottoscrizione può essere aumentato tramite una richiesta di supporto fino a un massimo di 200.  