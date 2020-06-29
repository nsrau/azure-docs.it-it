---
title: 'Servizio di peering di Azure: Come misurare la telemetria delle connessioni '
description: Questa esercitazione illustra come misurare i dati di telemetria delle connessioni.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "84870553"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Esercitazione: Misurare i dati di telemetria delle connessioni al Servizio di peering

 In questa esercitazione si apprenderà come misurare i dati di telemetria delle connessioni al servizio di peering.
 
 I dati di telemetria delle connessioni forniscono informazioni dettagliate raccolte per la connettività tra la località del cliente e la rete Microsoft. Questo articolo spiega come visualizzare il report di latenza per una specifica connessione al servizio peering di Azure. 

Per misurare i dati di telemetria delle connessioni al servizio di peering, è necessario registrare una connessione al servizio di peering nel portale di Azure. Per informazioni su come registrare una connessione, vedere [Registrare una connessione al servizio di peering - Portale di Azure](azure-portal.md).


## <a name="view-a-latency-report"></a>Visualizzare un report di latenza

Per visualizzare un report di latenza per una connessione al servizio di peering specifica, seguire questa procedura.

1. Selezionare **Tutte le risorse** nel riquadro sinistro e quindi selezionare la connessione al servizio di peering. Selezionare quindi **Apri** in **Prefissi**. 

   ![Selezionare la connessione al servizio di peering](./media/peering-service-measure/peering-service-measure-menu.png)

2. Viene visualizzata una pagina di report di latenza per tutti i prefissi associati alla connessione al servizio di peering. 

      ![Pagina del report di latenza](./media/peering-service-measure/peering-service-latency-report.png)

3. Per impostazione predefinita, il report viene aggiornato ogni ora nel periodo di tempo in cui viene visualizzato in questa pagina. Per visualizzare il report per periodi di tempo diversi, scegliere l'opzione appropriata in **Mostra dati per**. 

4. Per visualizzare gli eventi per un prefisso specifico, selezionare il nome del prefisso e quindi selezionare **Prefix Events** (Eventi prefisso) nel riquadro sinistro. Vengono visualizzati gli eventi acquisiti.


   ![Eventi prefisso](./media/peering-service-measure/peering-service-prefix-event.png)

 Di seguito sono visualizzati alcuni dei possibili eventi acquisiti nell'elenco **Prefix Events** (Eventi prefisso).

| **Eventi prefisso** | **Tipo di evento**|**Motivo**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Informazioni|È stato ricevuto un annuncio di prefisso|
| PrefixWithdrawalEvent|Avviso| È stato ricevuto un prelievo di prefisso |
| PrefixBackupRouteAnnouncementEvent |Informazioni|È stato ricevuto un annuncio della route di backup del prefisso |
| PrefixBackupRouteWithdrawalEvent|Avviso|È stato ricevuto un prelievo della route di backup del prefisso |
| PrefixActivePath |Informazioni| Route attiva del prefisso corrente   |
| PrefixBackupPath | Informazioni|Route di backup del prefisso corrente   |
| PrefixOriginAsChangeEvent|Critico| Prefisso esatto ricevuto con numero di sistema autonomo di origine diverso (per la route attiva)| 
| PrefixBackupRouteOriginAsChangeEvent  | Errore|Prefisso ricevuto con numero di sistema autonomo di origine diverso (per la route di backup)  |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connessione al servizio di peering, vedere [Connessione al servizio di peering](connection.md).
- Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).