---
title: "Panoramica su Integrità risorse di Azure | Microsoft Docs"
description: "Panoramica su Integrità risorse di Azure"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d54979995ca97a70ba92c64915b919da09f548ec
ms.lasthandoff: 04/04/2017


---
# <a name="azure-resource-health-overview"></a>Panoramica su Integrità risorse di Azure
 
Integrità risorse consente di eseguire una diagnosi e di ottenere supporto quando un problema di Azure ha effetto sulle risorse. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.

A differenza di [Stato di Azure](https://status.azure.com) che visualizza informazioni sui problemi dei servizi che interessano numerosi clienti di Azure, Integrità risorse offre un dashboard personalizzato dell'integrità delle risorse. Integrità risorse visualizza tutte le volte in cui le risorse non sono state disponibili a causa di problemi dei servizi di Azure. Ciò consente di individuare in modo semplice se è stato violato un contratto di servizio. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>Qual è la definizione di "risorsa" e in che modo Integrità risorse stabilisce se una risorsa è integra o meno?
Una risorsa è un'istanza di un tipo di risorsa messo a disposizione da un servizio di Azure tramite Azure Resource Manager, ad esempio una macchina virtuale, un'app Web o un database SQL.

Integrità risorse si basa su segnali emessi dai diversi servizi di Azure per determinare se una risorsa è integra o meno. Se una risorsa non è integra, Integrità risorse analizza informazioni aggiuntive per determinare l'origine del problema. Identifica anche le azioni intraprese da Microsoft per risolvere il problema o le azioni da eseguire per eliminare la causa del problema. 

Esaminare l'elenco completo dei tipi di risorse e dei controlli di integrità in [Integrità risorse di Azure](resource-health-checks-resource-types.md) per ulteriori dettagli sulla modalità di valutazione dell'integrità.

## <a name="health-status-provided-by-resource-health"></a>Stato di integrità indicato da Integrità risorse
L'integrità di una risorsa è indicata da uno degli stati seguenti:

### <a name="available"></a>Disponibile
Il servizio non ha rilevato eventi che hanno effetto sull'integrità della risorsa. Se la risorsa è stata ripristinata da un tempo di inattività non pianificato nelle ultime 24 ore, viene visualizzata la notifica di **recupero recente**.

![Integrità risorse: macchina virtuale con stato Disponibile](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non disponibile
Il servizio ha rilevato un evento piattaforma o non piattaforma in corso che ha effetto sull'integrità della risorsa,.

#### <a name="platform-events"></a>Eventi piattaforma
Questi eventi vengono generati da più componenti dell'infrastruttura di Azure e includono azioni pianificate, ad esempio la manutenzione, ed eventi imprevisti, ad esempio un riavvio dell'host non pianificato.

Integrità risorse visualizza ulteriori dettagli sull'evento, il processo di recupero e consente di contattare il supporto tecnico anche se non si ha un contratto di supporto Microsoft.

![Integrità risorse: macchina virtuale con stato Non disponibile a causa di un evento piattaforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventi non piattaforma
Questi eventi vengono generati da azioni eseguite dagli utenti, ad esempio l'arresto di una macchina virtuale o il raggiungimento del numero massimo di connessioni a una cache Redis.

![Integrità risorse: macchina virtuale con stato Non disponibile a causa di un evento non piattaforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Sconosciuto
Questo stato indica che Integrità risorse non ha ricevuto informazioni sulla risorsa per più di 10 minuti. Sebbene questo stato non sia un'indicazione definitiva dello stato della risorsa, è un punto dati importanti nel processo di risoluzione dei problemi:
* Se la risorsa viene eseguita come previsto, lo stato della risorsa verrà aggiornato in Disponibile dopo alcuni minuti.
* Se si verificano problemi con la risorsa, lo stato di integrità Sconosciuto può indicare che la risorsa è stata interessata da un evento nella piattaforma.

![Integrità risorse: macchina virtuale con stato Sconosciuto](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>Segnalare uno stato non corretto
Se in qualsiasi momento si ritiene che lo stato di integrità corrente non sia corretto, è possibile inviare una segnalazione facendo clic su **Report incorrect health status** (Segnala stato integrità non corretto). Se si verifica un problema di Azure, è consigliabile contattare il supporto tecnico dal pannello di Integrità risorse. 

![Integrità risorse: report di stato non corretto](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informazioni sulla cronologia
È possibile accedere a un massimo di 14 giorni di dati cronologici sull'integrità facendo clic su **Visualizza cronologia** nel pannello di Integrità risorse. 

![Integrità risorse: report della cronologia](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introduzione
Per aprire Integrità risorse per una risorsa
1.    Accedere al portale di Azure.
2.    Passare alla risorsa.
3.    Nel menu della risorsa sul lato sinistro fare clic su **Integrità risorse**.

![Aprire Integrità risorse dal pannello della risorsa](./media/resource-health-overview/from-resource-blade.png)

È anche possibile accedere a Integrità risorse facendo clic su **Altri servizi** e digitando **Integrità risorse** nella casella di testo di filtro per aprire il pannello **Guida e supporto**. Infine fare clic su [**Integrità risorse**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Aprire Integrità risorse da Altri servizi](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Integrità risorse, vedere:
-  [Tipi di risorse e controlli di integrità in Integrità risorse di Azure](resource-health-checks-resource-types.md)
-  [Frequently asked questions about Azure resource health](resource-health-faq.md) (Domande frequenti su Integrità risorse di Azure)





