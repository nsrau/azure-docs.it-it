---
title: Panoramica del piano, l'offerta, quote e sottoscrizione Stack Azure | Documenti Microsoft
description: Come operatore di cloud, si desidera conoscere i piani, offerte, le quote e le sottoscrizioni Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: d8aef778807d3a8a61cf9eedaae24abce84a19ab
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248759"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Panoramica di piano, offerta, quote e sottoscrizione

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

[Azure Stack](azure-stack-poc.md) consente di fornire un'ampia gamma di servizi, come le macchine virtuali, database di SQL Server, SharePoint, Exchange e anche [gli elementi di Azure Marketplace](azure-stack-marketplace-azure-items.md). Come operatore di Stack di Azure, configurare e offrono servizi nello Stack di Azure tramite i piani, offerte e le quote.

Le offerte contengono uno o più piani e ogni piano include uno o più servizi. Creazione di piani e sulla relativa combinazione in diverse offerte, è possibile gestire:

- Servizi e le risorse di cui gli utenti possono accedere.
- La quantità di risorse che gli utenti potranno utilizzare.
- Le aree che hanno accesso alle risorse.

Quando si esegue un servizio, attenersi alla procedura seguente:

1. Aggiungere un servizio che si desidera distribuire agli utenti.
2. Creare un piano che dispone di uno o più servizi. Quando si crea un piano, selezionare o creare le quote che definiscono i limiti delle risorse di ogni servizio nel piano.
3. Creare un'offerta che contiene uno o più piani. L'offerta può includere i piani di base e i piani di componente aggiuntivo opzionale.

Dopo aver creato l'offerta, gli utenti possono sottoscrivere per accedere ai servizi e risorse che fornisce l'offerta. Gli utenti possano sottoscrivere tante offerte che desiderano. Nel diagramma seguente illustra un semplice esempio di un utente che ha sottoscritto due offerte. Ogni offerta è un piano o due e ogni piano concede l'accesso ai servizi.

![Sottoscrizione con offerte e piani di tenant](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Piani

I piani sono raggruppamenti di uno o più servizi. Come operatore di Stack di Azure, si [creare piani](azure-stack-create-plan.md) per offrire agli utenti. A sua volta, gli utenti sottoscrivono le offerte di utilizzare i piani e i servizi che includono. Durante la creazione di piani, assicurarsi di impostare le quote, definire i piani di base e si consiglia di includere i piani di componente aggiuntivo opzionale.

### <a name="quotas"></a>Quote

Per gestire la capacità del cloud, è possibile utilizzare le quote preconfigurate o creare una nuova quota per ogni servizio in un piano. Le quote definiscono superiore i limiti delle risorse in grado di eseguire il provisioning o utilizzare una sottoscrizione utente. Ad esempio, una quota potrebbe consentire all'utente di creare fino a cinque macchine virtuali (VM). Le macchine virtuali, ad esempio core CPU e RAM, impostare quote aggiuntive.

È possibile configurare le quote per area geografica. Ad esempio, un piano che offre servizi di calcolo per l'area A potrebbe avere una quota di due macchine virtuali con 4 GB di RAM e 8 core di CPU.

>[!NOTE]
>In Azure Stack Development Kit, una sola area (denominato *locale*) è disponibile.

Altre informazioni, vedere [tipi di quote nello Stack di Azure](azure-stack-quota-types.md).

### <a name="base-plan"></a>Piano base

Quando si crea un'offerta, l'amministratore del servizio può includere un piano di base. Questi piani di base sono inclusi per impostazione predefinita, quando un utente sottoscrive l'offerta. Quando un utente esegue la sottoscrizione, hanno accesso a tutti i provider di risorse specificato in questi piani di base (con le quote corrispondenti.)

### <a name="add-on-plans"></a>Piani aggiuntivi

I piani di componente aggiuntivo sono piani facoltativi che si aggiunge a un'offerta. I piani di componente aggiuntivo non sono inclusi per impostazione predefinita nella sottoscrizione. Piani di componente aggiuntivo sono piani aggiuntivi (con quote) disponibili in un'offerta che è possibile aggiungere un sottoscrittore per le sottoscrizioni. Ad esempio, è possibile offrire ai clienti che decide di adottare il servizio di un piano di base con risorse limitate per una versione di valutazione e un piano del componente aggiuntivo con un numero più significativo di risorse.

## <a name="offers"></a>Offerte

Offerte sono gruppi di uno o più piani creati in modo che gli utenti possono eseguire la sottoscrizione a essi. Ad esempio, Alpha offrono può contenere un piano, che fornisce un set di servizi di calcolo e pianificare B, che fornisce un set di servizi di archiviazione e rete.

Quando si [creare un'offerta](azure-stack-create-offer.md), è necessario includere almeno un piano di base, ma è anche possibile creare piani di componente aggiuntivo che gli utenti possono aggiungere alla propria sottoscrizione.

## <a name="subscriptions"></a>Sottoscrizioni

Una sottoscrizione è l'accesso le offerte. Se si è un operatore di Stack di Azure per un provider di servizi, gli utenti (tenant) acquistano i servizi sottoscrivendo le offerte. Se si è un operatore di Stack di Azure in un'organizzazione, gli utenti (dipendenti) possono sottoscrivere i servizi che offerti senza sostenere.

Ogni combinazione di un utente con un'offerta è una sottoscrizione univoca. Un utente può disporre di sottoscrizioni alle offerte più, ma ogni sottoscrizione si applica solo a una sola offerta. I piani, offerte e le quote di si applicano solo a una sottoscrizione univoca: non può essere condivisa tra le sottoscrizioni. Ogni risorsa che crea un utente è associata a una sottoscrizione.

### <a name="default-provider-subscription"></a>Sottoscrizione di provider predefinito

La sottoscrizione di Provider predefinito viene creata automaticamente quando si distribuisce il Kit di sviluppo dello Stack di Azure. Questa sottoscrizione è utilizzabile per gestire Azure Stack, distribuire i provider di risorse aggiuntive e creare i piani e le offerte per gli utenti. Per motivi di gestione delle licenze e sicurezza, non devono essere usata per eseguire applicazioni e i carichi di lavoro di cliente.

## <a name="next-steps"></a>Passaggi successivi

[Creare un piano](azure-stack-create-plan.md)
