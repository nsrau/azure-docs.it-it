---
title: "Che cos'è l'Utilità di pianificazione di Azure? | Documentazione di Microsoft"
description: "L'Utilità di pianificazione di Azure consente di descrivere in modo dichiarativo le azioni da eseguire nel cloud e quindi pianifica ed esegue tali azioni automaticamente."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-scheduler"></a>Che cos'è l'Utilità di pianificazione di Azure?
L'Utilità di pianificazione di Azure consente di descrivere in modo dichiarativo le azioni da eseguire nel cloud e quindi pianifica ed esegue tali azioni automaticamente.  A tale scopo, l'utilità di pianificazione di Azure utilizza il [portale di Azure](scheduler-get-started-portal.md), il codice, l'[API REST](https://msdn.microsoft.com/library/mt629143.aspx) o Azure PowerShell.

L’Utilità di pianificazione crea, gestisce e richiama il lavoro programmato.  L’Utilità di pianificazione non ospita carichi di lavoro, né esegue codice. *Richiama* soltanto codice ospitato altrove, ad esempio in Azure, in locale o presso un altro provider. Richiama tramite HTTP, HTTPS, una coda di archiviazione, una coda del bus di servizio o un argomento del bus di servizio.

L'Utilità di pianificazione pianifica i [processi](scheduler-concepts-terms.md), mantiene una cronologia dei risultati dell'esecuzione dei processi che è possibile esaminare e pianifica in modo deterministico e attendibile i carichi di lavoro da eseguire. Azure WebJobs (parte della funzionalità App Web in Servizio app di Azure) e altre funzionalità di programmazione di Azure utilizzano l’Utilità di pianificazione in background. L' [API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/mt629143.aspx) consente di gestire le comunicazioni per queste azioni. Quindi, l’Utilità di pianificazione supporta facilmente [pianificazioni complesse e operazioni ricorrenti avanzate](scheduler-advanced-complexity.md) .

L'Utilità di pianificazione può essere usata in diversi scenari. Ad esempio: 

* *Azioni ricorrenti delle applicazioni*: raccolta periodica di dati da Twitter in un feed.
* *Manutenzione quotidiana*: eliminazione giornaliera dei registri, esecuzione di backup e altre attività di manutenzione. Ad esempio, un amministratore può scegliere di eseguire il backup del database alle ore 01:00 ogni giorno per i nove mesi successivi.

Con l'Utilità di pianificazione è possibile creare, aggiornare, eliminare, visualizzare e gestire processi e [raccolte di processi](scheduler-concepts-terms.md) a livello di codice, tramite script e nel portale.

## <a name="see-also"></a>Vedere anche 
 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)

