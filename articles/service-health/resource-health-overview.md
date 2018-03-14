---
title: "Panoramica di Integrità risorse di Azure | Microsoft Docs"
description: "Panoramica su Integrità risorse di Azure"
services: Resource health
documentationcenter: 
author: shawntabrizi
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: shawn.tabrizi
ms.openlocfilehash: 50a173a3d3a10ed59492b4a1d64173913f331639
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="azure-resource-health-overview"></a>Panoramica di Integrità risorse di Azure
 
Integrità risorse di Azure aiuta a diagnosticare gli eventuali problemi dei servizi di Azure con effetti negativi sulle risorse e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e offre il supporto tecnico necessario per attenuare i problemi.

A differenza di [Stato di Azure](https://status.azure.com) che visualizza informazioni sui problemi dei servizi che interessano numerosi clienti di Azure, Integrità risorse offre un dashboard personalizzato dell'integrità delle risorse. Integrità risorse visualizza tutte le volte in cui le risorse non sono state disponibili a causa di problemi dei servizi di Azure. È così semplice stabilire se è stato violato un contratto di servizio. 

## <a name="resource-definition-and-health-assessment"></a>Definizione di risorsa e valutazione dell'integrità
Una risorsa è un'istanza specifica di un servizio di Azure, ad esempio una macchina virtuale, un'app Web o un database SQL.

Integrità risorse si basa su segnali emessi dai diversi servizi di Azure per valutare se una risorsa è integra o meno. Se una risorsa non è integra, Integrità risorse analizza informazioni aggiuntive per determinare l'origine del problema. Identifica anche le azioni intraprese da Microsoft per risolvere il problema o le azioni da eseguire per eliminare la causa del problema. 

Per altri dettagli sulla modalità di valutazione dell'integrità, esaminare l'elenco completo dei tipi di risorse e dei controlli di integrità in [Integrità risorse di Azure](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Stato di integrità
L'integrità di una risorsa viene visualizzata come uno degli stati seguenti.

### <a name="available"></a>Disponibile
Lo stato **Disponibile** significa che il servizio non ha rilevato alcun evento con effetti sull'integrità della risorsa. Se la risorsa è stata ripristinata da un tempo di inattività non pianificato nelle ultime 24 ore, viene visualizzata la notifica **Risolto di recente**.

![Stato "Disponibile" per una macchina virtuale con notifica "Risolto di recente"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non disponibile
Lo stato **Non disponibile** indica che il servizio ha rilevato un evento piattaforma o non piattaforma in corso con effetti sull'integrità della risorsa.

#### <a name="platform-events"></a>Eventi piattaforma
Questi eventi vengono attivati da più componenti dell'infrastruttura di Azure. Includono le azioni pianificate (ad esempio, la manutenzione pianificata) e gli eventi imprevisti (ad esempio, il riavvio di un host non pianificato).

Integrità risorse fornisce dettagli aggiuntivi sull'evento e sul processo di ripristino. Consente inoltre di contattare il supporto tecnico, anche se non si dispone di un contratto di supporto Microsoft attivo.

![Stato "Non disponibile" per una macchina virtuale a causa di un evento piattaforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventi non piattaforma
Questi eventi vengono generati da azioni degli utenti, ad esempio, l'arresto di una macchina virtuale o il raggiungimento del numero massimo di connessioni a una cache Redis.

![Stato "Non disponibile" per una macchina virtuale a causa di un evento non piattaforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Sconosciuto
Lo stato **Sconosciuto** indica che Integrità risorse non ha ricevuto informazioni sulla risorsa per più di 10 minuti. Sebbene questo stato non sia un'indicazione definitiva dello stato della risorsa, è un punto dati importante nel processo di risoluzione dei problemi.

Se la risorsa viene eseguita come previsto, lo stato della risorsa verrà aggiornato in **Disponibile** dopo alcuni minuti.

Se si verificano problemi con la risorsa, lo stato di integrità **Sconosciuto** può indicare che la risorsa è stata interessata da un evento nella piattaforma.

![Stato "Sconosciuto" per una macchina virtuale](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Danneggiato
Lo stato di integrità **Danneggiato** indica che la risorsa ha rilevato una riduzione delle prestazioni, sebbene sia ancora disponibile per l'utilizzo.
Diverse risorse dispongono di propri criteri per specificare se una risorsa è danneggiata.

![Stato "Danneggiato" per una macchina virtuale](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Segnalazione di uno stato non corretto
Se si ritiene che lo stato di integrità corrente non sia corretto, è possibile inviare una segnalazione selezionando **Report stato integrità non corretto**. In presenza di un problema di Azure, è consigliabile contattare il supporto tecnico da Integrità risorse. 

![Casella per l'invio di informazioni su uno stato non corretto](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informazioni cronologiche
È possibile accedere a un massimo di 14 giorni di cronologia di informazioni sull'integrità selezionando **Visualizza cronologia** in Integrità risorse. 

![Elenco di eventi di Integrità risorse nelle ultime due settimane](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introduzione
Per aprire Integrità risorse per una risorsa:
1.  Accedere al portale di Azure.
2.  Passare alla risorsa.
3.  Nel menu della risorsa nel riquadro a sinistra selezionare **Integrità risorsa**.

![Apertura di Integrità risorse dalla visualizzazione delle risorse](./media/resource-health-overview/from-resource-blade.png)

È anche possibile accedere a Integrità risorse selezionando **Tutti i servizi** e digitando **Integrità risorse** nella casella di testo di filtro. Nel riquadro **Guida e supporto** selezionare [Integrità risorse](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Apertura di Integrità risorse da "Tutti i servizi"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere queste risorse per altre informazioni su Integrità risorse:
-  [Tipi di risorse e controlli di integrità in Integrità risorse di Azure](resource-health-checks-resource-types.md)
-  [Domande frequenti su Integrità risorse di Azure](resource-health-faq.md)




