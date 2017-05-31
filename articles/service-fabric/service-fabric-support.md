---
title: Informazioni sulle opzioni di supporto di Azure Service Fabric | Documentazione Microsoft
description: Versioni dei cluster di Azure Service Fabric supportate e link ai ticket di supporto.
services: service-fabric
documentationcenter: .net
author: pkc
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/05/2017
ms.author: pkc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: f87aec3698addae135d4c934bb7217d9fae0e571
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="azure-service-fabric-support-options"></a>Opzioni di supporto di Azure Service Fabric

Per fornire il supporto appropriato per i cluster di Service Fabric in cui vengono eseguiti i carichi di lavoro delle applicazioni, sono disponibili diverse opzioni. A seconda del livello di supporto richiesto e della gravità del problema, è possibile selezionare le opzioni corrette. 


<a id="getlivesitesupportonazure"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-azure"></a>Segnalare problemi di produzione o relativi al sito live o richiedere supporto a pagamento per Azure

Per la segnalazione di problemi relativi al sito live nel cluster di Service Fabric distribuito in Azure, aprire un ticket per il supporto professionale nel [portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) o nel [portale di supporto Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Altre informazioni su:
 
- [Supporto professionale Microsoft per Azure](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Supporto tecnico Premier Microsoft](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Segnalare problemi di produzione o relativi al sito live o richiedere supporto a pagamento per i cluster di Service Fabric autonomi

Per la segnalazione di problemi relativi al sito live nel cluster di Service Fabric distribuito in locale o in altri cloud, aprire un ticket per il supporto professionale nel [portale di supporto Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Altre informazioni su:

- [Supporto professionale Microsoft per la distribuzione in locale](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Supporto tecnico Premier Microsoft](https://support.microsoft.com/en-us/premier).


<a id="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>Segnalare problemi di Azure Service Fabric 
È disponibile un archivio GitHub per la segnalazione dei problemi relativi a Service Fabric.  Vengono inoltre monitorati attivamente i seguenti forum.

### <a name="github-repo"></a>Archivio GitHub 
Segnalare i problemi di Azure Service Fabric nell'[archivio Git dei problemi relativi a Service Fabric](https://github.com/Azure/service-fabric-issues). Questo archivio è destinato alla segnalazione e al rilevamento dei problemi di Azure Service Fabric e all'invio di richieste di funzionalità minori. **Non usarlo per segnalare problemi del sito live**.

### <a name="stackoverflow-and-msdn-forums"></a>Forum StackOverflow e MSDN

Il [tag Service Fabric su StackOverflow][stackoverflow] e il [forum di Service Fabric su MSDN][msdn-forum] vengono usati per inoltrare domande sul funzionamento della piattaforma e sull'esecuzione di specifiche operazioni.

### <a name="azure-feedback-forum"></a>Forum di commenti e suggerimenti su Azure

Il [forum di commenti e suggerimenti su Azure per Service Fabric][uservoice-forum] è il modo migliore per inoltrare i propri suggerimenti su funzionalità di rilievo del prodotto, dal momento che le richieste più diffuse vengono prese in considerazione nella pianificazione a medio-lungo termine di Microsoft. Gli utenti sono incoraggiati a cercare sostegno per i suggerimenti presentati all'interno della community.


<a id="releasesuport"></a>
## <a name="supported-service-fabric-versions"></a>Versioni di Service Fabric supportate

Verificare che il cluster esegua sempre una versione di Service Fabric supportata. Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Fare riferimento ai documenti seguenti per informazioni dettagliate su come assicurarsi che il cluster esegua una versione di Service Fabric supportata.

- [Aggiornare un cluster di Azure Service Fabric ](service-fabric-cluster-upgrade.md)
- [Aggiornare il cluster autonomo di Service Fabric in Windows Server ](service-fabric-cluster-upgrade-windows-server.md)
 
Di seguito è riportato un elenco delle versioni di Service Fabric supportate con le relative date di fine supporto.

| **Cluster runtime di Service Fabric** | **Data di fine supporto** |
| --- | --- |
| Tutte le versioni di cluster precedenti alla 5.3.121 |20 gennaio 2017 |
| 5.3.* |24 febbraio 2017 |
| 5.4.* |10 maggio 2017     |
| 5.5.* |10 luglio 2017    |
| 5.6.* |Versione corrente, nessuna data di fine supporto

<a id="previewversion"></a>
## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versioni di anteprima di Service Fabric non supportate per l'uso in produzione
Periodicamente vengono rilasciate in anteprima versioni che includono funzionalità significative per cui è richiesto il feedback degli utenti. Queste versioni di anteprima devono essere usate solo a scopo di test. In un cluster di produzione deve essere sempre in esecuzione una versione di Service Fabric supportata e stabile. Una versione di anteprima inizia sempre con un numero di versione principale e secondario uguale a 255. Ad esempio, la versione di Service Fabric 255.255.5703.949 è una versione di anteprima e dovrà essere usata solo in cluster di test. Queste versioni di anteprima vengono annunciate anche nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e includono informazioni dettagliate sulle funzionalità incluse.

Non esiste alcuna opzione di supporto a pagamento per queste versioni di anteprima. Usare una delle opzioni elencate in [Segnalare problemi di Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) per eventuali domande o commenti.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un cluster di Azure Service Fabric ](service-fabric-cluster-upgrade.md)
- [Aggiornare il cluster autonomo di Service Fabric in Windows Server ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples

