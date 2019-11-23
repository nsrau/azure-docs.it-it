---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: a05229f22e1682d73b627e3b4644ba64e653335f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414557"
---
| Gruppi | Gratis | Condivisione | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, mobile, or API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup>|
| [Piano di servizio app](../articles/app-service/overview-hosting-plans.md) |10 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse|
| Tipo di istanza di calcolo |Condivisione |Condivisione |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |Dedicato<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (maximum instances) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> |30 dedicated<sup>3</sup>|100 dedicati<sup>4</sup>|
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU time (5 minutes)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |
| Memoria (1 ora) |1,024 MB per App Service plan |1,024 MB per app |N/D |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web sockets per instance<sup>7</sup> |5 |35 |350 |Illimitato |Illimitato |Illimitato |
| IP connections | 600 | 600 | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | 64.000 |
| [Connessioni di debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |5 |
| App Service Certificates per subscription<sup>9</sup>| Supporto non disponibile | Supporto non disponibile |10 |10 |10 |10 |
| Domini personalizzati per applicazione</a> |0 (solo sottodominio azurewebsites.net)|500 |500 |500 |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/configure-ssl-certificate.md) |Not supported, wildcard certificate for *.azurewebsites.net available by default|Not supported, wildcard certificate for *.azurewebsites.net available by default|Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse|
| Hybrid connections per plan | | | 5 | 25 | 200 | 200 |
| Integrated load balancer | |X |X |X |X |X<sup>10</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Scheduled backups](../articles/app-service/manage-backup.md) | | | | Scheduled backups every 2 hours, a maximum of 12 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Staging slots](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Contratto di servizio | |  |99,95%|99,95%|99,95%|99,95%|  

<sup>1</sup>Le quote per applicazioni e di archiviazione sono per ogni piano di servizio app se non diversamente specificato.  
<sup>2</sup>Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.  
<sup>3</sup>Le istanze dedicate possono essere di dimensioni diverse. Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>More are allowed upon request.  
<sup>5</sup>The storage limit is the total content size across all apps in the same App service plan. The total content size of all apps across all App service plans in a single resource group and region cannot exceed 500GB.  
<sup>6</sup>Queste risorse sono vincolate dalle risorse fisiche nelle istanze dedicate (la dimensione dell'istanza e il numero di istanze).  
<sup>7</sup>Se si scala un'applicazione di livello base per due istanze, sono presenti 350 connessioni simultanee per ognuna delle due istanze. For Standard tier and above, there are no theoretical limits to web sockets, but other factors can limit the number of web sockets. For example, maximum concurrent requests allowed (defined by `maxConcurrentRequestsPerCpu`) are: 7,500 per small VM, 15,000 per medium VM (7,500 x 2 cores), and 75,000 per large VM (18,750 x 4 cores).  
<sup>8</sup>The maximum IP connections are per instance and depend on the instance size: 1,920 per B1/S1/P1V2 instance, 3,968 per B2/S2/P2V2 instance, 8,064 per B3/S3/P3V2 instance.  
<sup>9</sup>The App Service Certificate quota limit per subscription can be increased via a support request to a maximum limit of 200.  
<sup>10</sup>App Service Isolated SKUs can be internally load balanced (ILB) with Azure Load Balancer, so there's no public connectivity from the internet. Di conseguenza, alcune funzionalità di un servizio app isolato con carico interno bilanciato devono essere usati da macchine virtuali con accesso diretto all'endpoint di rete con bilanciamento del carico interno.  
<sup>11</sup>Run custom executables and/or scripts on demand, on a schedule, or continuously as a background task within your App Service instance. È necessario Always On per l'esecuzione dei processi Web in modo continuo. L'Utilità di pianificazione di Azure Gratuito o Standard è necessaria per i processi Web pianificati. There's no predefined limit on the number of WebJobs that can run in an App Service instance. There are practical limits that depend on what the application code is trying to do.  
