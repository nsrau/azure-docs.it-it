---
title: Panoramica di Integrità risorse di Azure
description: Panoramica su Integrità risorse di Azure
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159252"
---
# <a name="resource-health-overview"></a>Panoramica di Integrità risorse
 
Integrità delle risorse di Azure consente di diagnosticare e ottenere supporto per i problemi di servizio che influiscono sulle risorse di Azure.Azure Resource Health helps you diagnose and get support for service problems that affect your Azure resources. Riporta lo stato di salute attuale e passato delle tue risorse.

[Report](https://status.azure.com) sullo stato di Azure sui problemi del servizio che interessano un'ampia gamma di clienti di Azure.Azure status reports on service problems that affect a broad set of Azure customers. Integrità delle risorse offre un dashboard personalizzato dell'integrità delle risorse. Integrità risorse mostra tutte le volte che le risorse non sono state disponibili a causa di problemi del servizio Azure.Resource Health shows all the times that your resources have been unavailable due to ad azure service problems. Questi dati consentono di vedere facilmente se un servizio di archiviazione è stato violato.

## <a name="resource-definition-and-health-assessment"></a>Definizione di risorsa e valutazione dell'integrità

Una *risorsa* è un'istanza specifica di un servizio di Azure, ad esempio una macchina virtuale, un'app Web o un database SQL. Integrità risorse si basa sui segnali provenienti da diversi servizi di Azure per valutare se una risorsa è integra. Se una risorsa non è integra, Integrità risorse analizza informazioni aggiuntive per determinare l'origine del problema. Segnala inoltre le azioni intraprese da Microsoft per risolvere il problema e identifica le operazioni che è possibile eseguire per risolverlo.

Per altre informazioni su come viene valutata l'integrità, vedere l'elenco dei tipi di risorse e dei controlli di integrità in Integrità delle risorse di Azure.For more information on how health is assessed, see the list of resource types and health checks at [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stato di integrità

L'integrità di una risorsa viene visualizzata come uno degli stati seguenti.

### <a name="available"></a>Disponibile

*Disponibile* significa che non sono rilevati eventi che influiscono sull'integrità della risorsa. Nei casi in cui la risorsa è stata ripristinata da tempi di inattività non pianificati nelle ultime 24 ore, verrà visualizzata una notifica "Risolto di recente".

![Stato di "Disponibile" per una macchina virtuale con una notifica "Risolto di recente"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non disponibile

*Non disponibile* indica che il servizio ha rilevato un evento piattaforma o non piattaforma in corso che influisce sull'integrità della risorsa.

#### <a name="platform-events"></a>Eventi piattaforma

Questi eventi vengono attivati da più componenti dell'infrastruttura di Azure. Includono sia azioni pianificate (ad esempio, manutenzione pianificata) che eventi imprevisti (ad esempio, un riavvio non pianificato dell'host o un hardware host danneggiato che si prevede non riesca dopo un intervallo di tempo specificato).

Integrità risorse fornisce ulteriori dettagli sull'evento e sul processo di ripristino. Consente inoltre di contattare il supporto tecnico Microsoft anche se non si dispone di un contratto di supporto attivo.

![Stato di "Non disponibile" per una macchina virtuale a causa di un evento della piattaforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventi non piattaforma

Gli eventi non della piattaforma vengono attivati dalle azioni dell'utente. Gli esempi includono l'arresto di una macchina virtuale o il raggiungimento del numero massimo di connessioni alla cache di Azure per Redis.Examples include stopping a virtual machine or reaching the maximum number of connections to Azure Cache for Redis.

![Stato di "Non disponibile" per una macchina virtuale a causa di un evento non di piattaformaStatus of "Unavailable" for a virtual machine because of a non-platform event](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

*Sconosciuto* significa che Integrità risorse non ha ricevuto informazioni sulla risorsa per più di 10 minuti. Anche se questo stato non è un'indicazione definitiva dello stato della risorsa, è un punto dati importante per la risoluzione dei problemi.

Se la risorsa viene eseguita come previsto, lo stato della risorsa verrà aggiornato in *Disponibile* dopo alcuni minuti.

Se si verificano problemi con la risorsa, lo stato di integrità *Sconosciuto* potrebbe indicare che un evento nella piattaforma interessa la risorsa.

![Stato di "Sconosciuto" per una macchina virtuale](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degraded

*Degraded* significa che la risorsa ha rilevato una perdita di prestazioni, anche se è ancora disponibile per l'uso.

Risorse diverse hanno i propri criteri per quando segnalano di essere degradate.

![Stato di "Degraded" per una macchina virtuale](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Segnalazione di uno stato non corretto

Se si ritiene che lo stato di integrità corrente non sia corretto, è possibile comunicarcelo selezionando **Segnala stato di integrità non corretto**. Nei casi in cui un problema di Azure interessa l'utente, è consigliabile contattare il supporto da Integrità risorse.

![Modulo per inviare informazioni su uno stato non corretto](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informazioni sulla cronologia

È possibile accedere fino a 30 giorni di cronologia nella sezione **Cronologia integrità** di Integrità risorse.

![Elenco di eventi di Integrità risorse nelle ultime due settimane](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Introduzione

Per aprire Integrità risorse per una risorsa:

1. Accedere al portale di Azure.
2. Passare alla risorsa.
3. Nel menu della risorsa nel riquadro a sinistra selezionare **Integrità risorsa**.

![Apertura di Integrità risorse dalla visualizzazione delle risorse](./media/resource-health-overview/from-resource-blade.png)

È anche possibile accedere a Integrità risorse selezionando **Tutti i servizi** e digitando **Integrità risorse** nella casella di testo di filtro. Nel riquadro **Guida e supporto** selezionare [Integrità risorse](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Apertura di Integrità risorse da "Tutti i servizi"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrità delle risorse, vedere questi riferimenti:Check out these references to learn more about Resource Health:
-  [Resource types and health checks in Azure Resource Health](resource-health-checks-resource-types.md)
-  [Domande frequenti su Integrità delle risorse di AzureFrequently asked questions about Azure Resource Health](resource-health-faq.md)
