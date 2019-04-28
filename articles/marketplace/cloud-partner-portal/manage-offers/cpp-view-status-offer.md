---
title: Visualizzare lo stato delle offerte di Marketplace - Azure Marketplace | Microsoft Docs
description: Visualizzare lo stato delle offerte in Azure Marketplace e nel marketplace di AppSource usando il portale Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: bdec2d699e8448c8e2303dfbabcb4d176a9ca389
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729792"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Visualizzare lo stato di pubblicazione delle offerte di Azure Marketplace e AppSource

Dopo aver creato un'offerta e in particolare durante il processo di pubblicazione, è possibile visualizzare lo stato dell'offerta nel portale Cloud Partner.  Lo stato di pubblicazione generale è disponibile nelle pagine [**Tutte le offerte**](../portal-tour/cpp-all-offers-page.md) e [**Approvazioni**](../portal-tour/cpp-approvals-page.md) del portale.  Per ogni offerta deve essere visualizzato uno degli indicatori di stato seguenti.  

|            Stato              |   DESCRIZIONE                                                           |
|            ------              |   -----------                                                           |
| **-**                          | L'offerta è stata creata, ma processo di pubblicazione non è iniziato.            |
| **Pubblicazione in corso**        | L'offerta sta attraversando le varie fasi del processo di pubblicazione.   |
| **Pubblicazione non riuscita**             | È stato rilevato un problema critico durante la convalida o la revisione da parte di Microsoft. |
| **Pubblicazione annullata**           | L'editore ha annullato il processo di pubblicazione dell'offerta.  Questo stato non esclude un'offerta esistente dall'elenco presente nel marketplace. | 
| **Awaiting publisher sign out** (In attesa della disconnessione dell'editore) | L'offerta è stata rivista da Microsoft ed è ora in attesa di una verifica finale da parte dell'editore. |
| **Delisted** (Escluso dall'elenco)                   | Un'offerta precedentemente pubblicata nel marketplace è stata rimossa.      | 
|  |  |


## <a name="publishing-status-details"></a>Dettagli dello stato di pubblicazione 

Altri dettagli sullo stato di un'offerta sottoposta al processo di pubblicazione sono disponibili nella scheda **Stato** della pagina **Nuova offerta**.  Questa pagina contiene l'elenco di tutti i passaggi da eseguire per la pubblicazione di un determinato tipo di offerta.  *Si noti che il numero e i passaggi specifici spesso variano in base ai diversi tipi di offerta.*  Questa pagina indica anche gli eventuali problemi irrisolti riscontrati durante le fasi di revisione e convalida di Microsoft, che spesso richiedono un intervento da parte dell'editore prima di continuare il processo di pubblicazione.  Ad esempio, l'immagine seguente mostra la scheda **Stato** relativa a una nuova offerta di macchina virtuale. 

![Scheda Stato relativa a un'offerta di macchina virtuale](./media/vm-offer-pub-steps1.png)

La scheda **Stato** dell'esempio successivo relativa a un servizio di consulenza mostra un errore segnalato nelle impostazioni per la gestione dei lead.  Poiché la gestione dei lead è necessaria per i servizi di consulenza, questo errore deve essere corretto prima di proseguire con la pubblicazione.

![Scheda Stato relativa a un servizio di consulenza contenente un errore](./media/consulting-service-error.png)

La scheda Stato dell'esempio finale relativo a un'applicazione di Azure mostra un problema di revisione Microsoft critico.  È presente un collegamento ipertestuale all'elemento VSTS che contiene informazioni dettagliate su questo problema di revisione.  Per altre informazioni, vedere [Pubblicare un'offerta di applicazione di Azure](cpp-publish-offer.md).

![Scheda Stato relativa a un'app di Azure contenente un problema di revisione](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Passaggi successivi

Per correggere problemi irrisolti o aggiornare le impostazioni di un'offerta, è necessario [aggiornare l'offerta](./cpp-update-offer.md). 
