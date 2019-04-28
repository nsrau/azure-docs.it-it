---
title: Informazioni sulle opzioni di supporto di Azure Service Fabric | Documentazione Microsoft
description: Versioni dei cluster di Azure Service Fabric supportate e link ai ticket di supporto
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: a931de8be07d41cf4daab63aa7691973ee158452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545062"
---
# <a name="azure-service-fabric-support-options"></a>Opzioni di supporto di Azure Service Fabric

Per fornire il supporto appropriato per i cluster di Service Fabric in cui vengono eseguiti i carichi di lavoro delle applicazioni, sono disponibili diverse opzioni. A seconda del livello di supporto richiesto e della gravità del problema, è possibile selezionare le opzioni corrette. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Segnalare problemi di produzione o richiedere supporto a pagamento per Azure

Per la segnalazione di problemi nel cluster di Service Fabric distribuito in Azure, aprire un ticket per il supporto nel [portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) o nel [portale del supporto tecnico Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Altre informazioni su:
 
- [Supporto Microsoft per Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Supporto tecnico Premier Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> I cluster eseguiti in un livello di affidabilità bronze consentono di eseguire carichi di lavoro test solo. Se si verificano problemi con un cluster in esecuzione sull'affidabilità bronze, il team di supporto di Microsoft consente di ridurre il problema, ma non esegue un'analisi della causa radice. Consultare [le caratteristiche di affidabilità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) per altri dettagli.
>
> Per altre informazioni sui requisiti per un cluster pronto per la produzione, consultare il [elenco di controllo di produzione readiness](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Segnalare problemi di produzione o richiedere supporto a pagamento per i cluster di Service Fabric autonomi

Per la segnalazione di problemi nel cluster di Service Fabric distribuito in locale o in altri cloud, aprire un ticket per il supporto professionale nel [portale del supporto tecnico Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Altre informazioni su:

- [Supporto professionale Microsoft per la distribuzione in locale](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Supporto tecnico Premier Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Segnalare problemi di Azure Service Fabric

È disponibile un archivio GitHub per la segnalazione dei problemi relativi a Service Fabric.  Vengono inoltre monitorati attivamente i seguenti forum.

### <a name="github-repo"></a>Archivio GitHub 

Segnalare i problemi di Azure Service Fabric nell'[archivio Git dei problemi relativi a Service Fabric](https://github.com/Azure/service-fabric-issues). Questo archivio è destinato alla segnalazione e al rilevamento dei problemi di Azure Service Fabric e all'invio di richieste di funzionalità minori. **Non usarlo per segnalare problemi del sito live**.

### <a name="stackoverflow-and-msdn-forums"></a>Forum StackOverflow e MSDN

Il [tag Service Fabric su StackOverflow][stackoverflow] e il [forum di Service Fabric su MSDN][msdn-forum] vengono usati per inoltrare domande sul funzionamento della piattaforma e sull'esecuzione di specifiche operazioni.

### <a name="azure-feedback-forum"></a>Forum di commenti e suggerimenti su Azure

Il [forum di commenti e suggerimenti su Azure per Service Fabric][uservoice-forum] è il modo migliore per inoltrare i propri suggerimenti su funzionalità di rilievo del prodotto, dal momento che le richieste più diffuse vengono prese in considerazione nella pianificazione a medio-lungo termine di Microsoft. Gli utenti sono incoraggiati a cercare sostegno per i suggerimenti presentati all'interno della community.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versioni di anteprima di Service Fabric non supportate per l'uso in produzione

Periodicamente vengono rilasciate in anteprima versioni che includono funzionalità significative per cui è richiesto il feedback degli utenti. Queste versioni di anteprima devono essere usate solo a scopo di test. In un cluster di produzione deve essere sempre in esecuzione una versione di Service Fabric supportata e stabile. Una versione di anteprima inizia sempre con un numero di versione principale e secondario uguale a 255. Ad esempio, la versione di Service Fabric 255.255.5703.949 è una versione di anteprima e dovrà essere usata solo in cluster di test. Queste versioni di anteprima vengono annunciate anche nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) e includono informazioni dettagliate sulle funzionalità incluse.
Non esiste alcuna opzione di supporto a pagamento per queste versioni di anteprima. Usare una delle opzioni elencate in [Segnalare problemi di Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) per eventuali domande o commenti.

## <a name="next-steps"></a>Passaggi successivi

[Versioni di Service Fabric supportate](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples