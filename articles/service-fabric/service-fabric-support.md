---
title: Informazioni sulle opzioni di supporto di Azure Service Fabric
description: Versioni dei cluster di Azure Service Fabric supportate e link ai ticket di supporto
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: afadd68b4c74ede522aa16809fab341e5802c627
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165928"
---
# <a name="azure-service-fabric-support-options"></a>Opzioni di supporto di Azure Service Fabric

Sono state create diverse opzioni di richiesta di supporto per soddisfare le esigenze di gestione dei cluster Service Fabric e dei carichi di lavoro dell'applicazione. A seconda dell'urgenza del supporto necessario e della gravità del problema, è possibile scegliere l'opzione più adatta alle proprie esigenze.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Segnalare problemi di produzione o richiedere supporto a pagamento per Azure

Per segnalare i problemi relativi al cluster Service Fabric in esecuzione in Azure, aprire un ticket di supporto [nel portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) o nel [portale del supporto tecnico Microsoft](https://support.microsoft.com/oas/default.aspx?prid=16146).

Altre informazioni su:
 
- [Supporto Microsoft per Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Supporto tecnico Premier Microsoft](https://support.microsoft.com/en-us/premier).

> [!Note]
> I cluster in esecuzione in un cluster di livello di affidabilità bronze o a nodo singolo consentiranno di eseguire solo i carichi di lavoro di test. Se si verificano problemi con un cluster in esecuzione su un cluster con affidabilità Bronze o a nodo singolo, il team di supporto di Microsoft fornirà assistenza per attenuare il problema, ma non eseguirà un'analisi della causa radice. Per ulteriori informazioni, consultare [le caratteristiche di affidabilità del cluster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Per altre informazioni su ciò che è necessario per un cluster pronto per la produzione, consultare l'[elenco di controllo per l'idoneità per la produzione](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Segnalare problemi di produzione o richiedere supporto a pagamento per i cluster di Service Fabric autonomi

Per segnalare problemi relativi a cluster Service Fabric eseguiti in locale o in altri cloud, è possibile aprire un ticket per il supporto professionale nel portale del [supporto tecnico Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Altre informazioni su:

- [Supporto professionale Microsoft per la distribuzione in locale](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Supporto tecnico Premier Microsoft](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Segnalare problemi di Azure Service Fabric

È disponibile un archivio GitHub per la segnalazione dei problemi relativi a Service Fabric.  Vengono inoltre monitorati attivamente i seguenti forum.

### <a name="github-repo"></a>Archivio GitHub 

Segnala i problemi del Service Fabric di Azure nel [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues). Questo repository è concepito per la creazione di report e il monitoraggio dei problemi, nonché per l'esecuzione di richieste di piccole dimensioni correlate ad Azure Service Fabric. Non **usare questo supporto per segnalare i problemi del sito Live**.

### <a name="stackoverflow-and-msdn-forums"></a>Forum StackOverflow e MSDN

Il [tag Service Fabric su StackOverflow][stackoverflow] e il [Forum di Service Fabric su MSDN][msdn-forum] sono particolarmente usati per porre domande generali sul funzionamento della piattaforma e su come usarlo per eseguire determinate attività.

### <a name="azure-feedback-forum"></a>Forum di commenti e suggerimenti su Azure

Il [Forum di commenti e suggerimenti su Azure per Service Fabric][uservoice-forum] è il posto migliore per inviare idee significative sulle funzionalità del prodotto. Vengono esaminate le richieste più diffuse e ne viene eseguito il factoring per la pianificazione da medio a lungo termine. Gli utenti sono incoraggiati a cercare sostegno per i suggerimenti presentati all'interno della community.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Versioni di anteprima di Service Fabric non supportate per l'uso in produzione

Occasionalmente vengono rilasciate versioni di anteprima speciali contenenti modifiche significative delle funzionalità per le quali si vuole analizzare tempestivamente il feedback. È consigliabile usare le versioni di anteprima solo in ambienti di test isolati che non servono carichi di lavoro di produzione. In un cluster di produzione deve essere sempre in esecuzione una versione di Service Fabric supportata e stabile. Non è disponibile un'opzione di supporto a pagamento per queste versioni di anteprima.

Una versione di anteprima inizia sempre con un numero di versione principale e secondario uguale a 255. Se, ad esempio, viene visualizzato un Service Fabric versione 255.255.5703.949, questa versione è in anteprima e deve essere usata solo nei cluster di test. Queste versioni di anteprima vengono annunciate anche nel [blog del team di Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) e includono informazioni dettagliate sulle funzionalità incluse. Usare una delle opzioni elencate in [Segnalare problemi di Azure Service Fabric](#report-azure-service-fabric-issues) per eventuali domande o commenti.

## <a name="next-steps"></a>Passaggi successivi

[Versioni di Service Fabric supportate](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
