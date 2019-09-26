---
title: Che cos'è l'Utilità di pianificazione di Azure? | Microsoft Docs
description: Informazioni su come creare, pianificare ed eseguire i processi automatizzati che chiamano servizi all'interno o all'esterno di Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300894"
---
# <a name="what-is-azure-scheduler"></a>Che cos'è l'Utilità di pianificazione di Azure?

> [!IMPORTANT]
> [App](../logic-apps/logic-apps-overview.md) per la logica di Azure sostituisce l'utilità di pianificazione di Azure, che sta per [essere ritirata](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile.

L'[Utilità di pianificazione di Azure](https://azure.microsoft.com/services/scheduler/) consente di creare [processi](../scheduler/scheduler-concepts-terms.md) eseguiti nel cloud descrivendo le azioni in modo dichiarativo. Quindi, il servizio pianifica ed esegue tali azioni automaticamente. È possibile, ad esempio, chiamare servizi quali endpoint HTTP o HTTPS all'interno e all'esterno di Azure, e inserire messaggi nelle code di Archiviazione di Azure e nelle code o negli argomenti del bus di servizio di Azure. È possibile eseguire i processi immediatamente o in un secondo momento. L’Utilità di pianificazione supporta con facilità [pianificazioni complesse e operazioni ricorrenti avanzate](../scheduler/scheduler-advanced-complexity.md). L'Utilità di pianificazione specifica quando eseguire i processi, mantiene una cronologia dei risultati dei processi che è possibile esaminare e pianifica quindi l'esecuzione dei carichi di lavoro in modo prevedibile e affidabile.

Sebbene sia possibile usare l'Utilità di pianificazione per creare, gestire ed eseguire carichi di lavoro pianificati, l'utilità di pianificazione non ospita i carichi di lavoro né esegue il codice. Il servizio si limita a *richiamare* i servizi o il codice ospitato altrove, ad esempio in Azure, in locale o presso un altro provider. L'Utilità di pianificazione può richiamare tramite HTTP, HTTPS, una coda di archiviazione, una coda o un argomento del bus di servizio. Per creare, gestire e pianificare i processi, è possibile usare il [portale di Azure](../scheduler/scheduler-get-started-portal.md), il codice, l'[API REST dell'utilità di pianificazione](https://docs.microsoft.com/rest/api/scheduler/) o [il riferimento di cmdlet di PowerShell dell'utilità di pianificazione Azure](scheduler-powershell-reference.md). Ad esempio, a livello di programmazione è possibile creare, visualizzare, aggiornare, gestire o eliminare i processi e le [raccolte di processi](../scheduler/scheduler-concepts-terms.md) tramite script e nel portale di Azure.

Altre funzionalità di pianificazione di Azure utilizzano l'Utilità di pianificazione in background, come ad esempio [Processi Web di Azure](../app-service/webjobs-create.md), una funzionalità [app Web](https://azure.microsoft.com/services/app-service/web/) nel Servizio app di Azure. L'[API REST dell'Utilità di pianificazione](https://docs.microsoft.com/rest/api/scheduler/) consente di gestire le comunicazioni per queste azioni. consente di gestire le comunicazioni per queste azioni.

Ecco alcuni scenari in cui l'Utilità di pianificazione consente di:

* **Eseguire le azioni ricorrenti delle app**: ad esempio, raccogliere periodicamente i dati da Twitter in un feed.

* **Effettuare la manutenzione quotidiana**: eseguire operazioni quali l'eliminazione giornaliera dei log, l'esecuzione di backup e altre attività di manutenzione. 

  Ad esempio, come amministratore, è possibile eseguire il backup del database ogni giorno all'1:00 per i nove mesi successivi.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso dell'Utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)
* Informazioni sui [piani e la fatturazione per l'Utilità di pianificazione di Azure](scheduler-plans-billing.md)
* Informazioni su [come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)