---
title: Opzioni di supporto di Azure Service Fabric | Documentazione Microsoft
description: Versioni dei cluster di Azure Service Fabric supportate e link ai ticket di supporto.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: d848bebea962e8ba883266188cd0bafe991dd804
ms.openlocfilehash: 5d27e6622faeee7cf9f4cdb2911171ef29d94b5c


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
| 5.3.121.* |24 febbraio 2017 |
| 5.3.204.* |24 febbraio 2017 |
| 5.3.301.* |24 febbraio 2017 |
| 5.3.311.* |24 febbraio 2017 |
| 5.4. *. * |Versione corrente, nessuna data di fine supporto |


## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un cluster di Azure Service Fabric ](service-fabric-cluster-upgrade.md)
- [Aggiornare il cluster autonomo di Service Fabric in Windows Server ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples



<!--HONumber=Dec16_HO2-->


