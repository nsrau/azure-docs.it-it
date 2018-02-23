---
title: Panoramica del piano, l'offerta, quote e sottoscrizione Stack Azure | Documenti Microsoft
description: Come operatore di cloud, si desidera conoscere i piani, offerte, le quote e le sottoscrizioni Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: erikje
ms.openlocfilehash: 083ca2f0a06625810d2f90a682ba0b3110032e60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Panoramica di piano, offerta, quote e sottoscrizione

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

[Azure Stack](azure-stack-poc.md) consente di fornire un'ampia gamma di servizi, come le macchine virtuali, database di SQL Server, SharePoint, Exchange e anche [gli elementi di Azure Marketplace](azure-stack-marketplace-azure-items.md). Come operatore di Stack di Azure, configurare e offrono servizi nello Stack di Azure tramite i piani, offerte e le quote.

Le offerte contengono uno o più piani e ogni piano include uno o più servizi. Creazione di piani e combinarle in diverse offerte, consente di controllare
- quali utenti servizi e le risorse possono accedere
- la quantità di tali risorse che gli utenti potranno utilizzare
- le aree di accedano accesso alle risorse

Quando si esegue un servizio, viene usata la seguente procedura seguente:

1. Aggiungere un servizio che si desidera distribuire agli utenti.
2. Creare un piano che contiene uno o più servizi. Quando si crea un piano, verrà selezionata o creata quote che definiscono i limiti delle risorse di ogni servizio nel piano.
3. Creare un'offerta che contiene uno o più piani (compresi i piani di base e i piani di componente aggiuntivo opzionale).

Dopo aver creato l'offerta, gli utenti possono sottoscrivere per l'accesso alle risorse che fornisce e i servizi. Gli utenti possano sottoscrivere tante offerte che desiderano. Nel diagramma seguente illustra un semplice esempio di un utente che ha sottoscritto due offerte. Ogni offerta è un piano o due e ogni piano concede l'accesso ai servizi.

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Piani

I piani sono raggruppamenti di uno o più servizi. Come operatore di Stack di Azure, si [creare piani](azure-stack-create-plan.md) per offrire agli utenti. A sua volta, gli utenti sottoscrivono le offerte di utilizzare i piani e i servizi che includono. Durante la creazione di piani, assicurarsi di impostare le quote, definire i piani di base e si consiglia di includere i piani di componente aggiuntivo opzionale.

### <a name="quotas"></a>Quote

Per gestire la capacità del cloud, si selezionare o creare una quota per ogni servizio in un piano. Le quote definiscono superiore i limiti delle risorse in grado di eseguire il provisioning o utilizzare una sottoscrizione utente. Ad esempio, una quota potrebbe consentire all'utente di creare fino a cinque macchine virtuali. Le quote possono limitare un'ampia gamma di risorse, ad esempio CPU, RAM e le macchine virtuali di limiti.

Quote possono essere configurate per area. Ad esempio, un piano che contiene i servizi di calcolo da un'area potrebbe avere una quota di due macchine virtuali, 4 GB di RAM e 10 core di CPU. In Azure Stack Development Kit, una sola area (denominato *locale*) è disponibile.

### <a name="base-plan"></a>Piano base

Quando si crea un'offerta, l'amministratore del servizio può includere un piano di base. Questi piani di base sono inclusi per impostazione predefinita, quando un utente sottoscrive l'offerta. Quando un utente esegue la sottoscrizione, hanno accesso a tutti i provider di risorse specificato in questi piani di base (con le quote corrispondenti).

### <a name="add-on-plans"></a>Piani aggiuntivi

È inoltre possibile includere i piani di componente aggiuntivo opzionale in un'offerta. I piani aggiuntivi non sono inclusi per impostazione predefinita nella sottoscrizione. Piani di componente aggiuntivo sono piani aggiuntivi (con quote) disponibili in un'offerta che è possibile aggiungere un sottoscrittore per le sottoscrizioni. Ad esempio, è possibile offrire ai clienti che decide di adottare il servizio di un piano di base con risorse limitate per una versione di valutazione e un piano del componente aggiuntivo con un numero più significativo di risorse.

## <a name="offers"></a>Offerte

Offerte sono gruppi di uno o più piani creati in modo che gli utenti possono eseguire la sottoscrizione a essi. Ad esempio, offrire alfa può contenere un piano che contiene un set di servizi di calcolo e pianificare B, che contiene un set di servizi di archiviazione e rete. 

Quando si [creare un'offerta](azure-stack-create-offer.md), è necessario includere almeno un piano di base, ma è anche possibile creare piani di componente aggiuntivo che gli utenti possono aggiungere alla propria sottoscrizione.


## <a name="subscriptions"></a>Sottoscrizioni

Una sottoscrizione è l'accesso le offerte. Se si è un operatore di Stack di Azure in un provider di servizi, gli utenti (tenant) acquistano i servizi sottoscrivendo le offerte. Se si è un operatore di Stack di Azure in un'organizzazione, gli utenti (dipendenti) possono sottoscrivere i servizi che offerti senza sostenere. Ogni combinazione di un utente con un'offerta è una sottoscrizione univoca. Di conseguenza, un utente può disporre di sottoscrizioni per le offerte più, ma ogni sottoscrizione si applica a sola offerta. I piani, offerte e le quote di si applicano solo a ogni sottoscrizione univoco, non possono essere condivisi tra le sottoscrizioni. Ogni risorsa che crea un utente è associata a una sottoscrizione.


### <a name="default-provider-subscription"></a>Sottoscrizione di provider predefinito

La sottoscrizione di Provider predefinito viene creata automaticamente quando si distribuisce il Kit di sviluppo dello Stack di Azure. Questa sottoscrizione è utilizzabile per gestire Azure Stack, distribuire ulteriormente i provider di risorse e creare i piani e le offerte per gli utenti. Per motivi di gestione delle licenze e di sicurezza, e non deve essere utilizzato per eseguire applicazioni e i carichi di lavoro di cliente. 

## <a name="next-steps"></a>Passaggi successivi

[Creare un piano](azure-stack-create-plan.md)
