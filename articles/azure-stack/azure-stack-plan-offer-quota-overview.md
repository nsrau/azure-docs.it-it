---
title: Cenni preliminari di piano, offerta, quote e sottoscrizione di Azure Stack | Microsoft Docs
description: Come operatore di cloud, si desidera comprendere i piani, offerte, quote e le sottoscrizioni di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: 9d495087a0b3db5bba3b00fa9d0d40c91e5e9244
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497858"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Panoramica di piano, offerta, quote e sottoscrizione

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

[Azure Stack](azure-stack-poc.md) consente di distribuire un'ampia gamma di servizi, ad esempio le macchine virtuali, database di SQL Server, SharePoint, Exchange e persino [elementi di Azure Marketplace](azure-stack-marketplace-azure-items.md). Un operatore di Azure Stack, configurare e distribuire tali servizi in Azure Stack usando i piani, offerte e quote.

Offerte contengono uno o più piani e ogni piano include uno o più servizi. Creazione dei piani e combinandoli in offerte diverse, è possibile gestire:

- I servizi e gli utenti possono accedere alle risorse.
- La quantità di risorse che gli utenti possono utilizzare.
- Quali aree hanno accesso alle risorse.

Quando si distribuisce un servizio, seguire questi passaggi di alto livello:

1. Aggiungere un servizio che si desidera fornire agli utenti.
2. Creare un piano che dispone di uno o più servizi. Quando si crea un piano, selezionare o creare quote che definiscono i limiti delle risorse di ogni servizio nel piano.
3. Creare un'offerta che contiene uno o più piani. L'offerta può includere piani base e i piani aggiuntivi facoltativi.

Dopo aver creato l'offerta, gli utenti possono sottoscrivere per accedere ai servizi e risorse che fornisce l'offerta. Gli utenti possono sottoscrivere tante offerte che desiderano. La figura seguente mostra un esempio semplice di un utente che ha sottoscritto due offerte. Ogni offerta prevede un piano o a due e ogni piano consente loro di accedere ai servizi.

![Sottoscrizione con offerte e piani di tenant](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Piani

I piani sono raggruppamenti di uno o più servizi. Un operatore di Azure Stack, si [consente di creare piani](azure-stack-create-plan.md) per offrire agli utenti. A loro volta, gli utenti sottoscrivono le offerte per usare i piani e i servizi inclusi. Quando si creano i piani, assicurarsi di impostare le quote, definire i piani di base e provare a includere piani aggiuntivi facoltativi.

### <a name="quotas"></a>Quote

Per aiutarti a gestire la capacità del cloud, è possibile usare preconfigurati *quote*, o creare una nuova quota per ogni servizio in un piano. Le quote di definiscono i limiti di risorse superiore che può effettuare il provisioning o utilizzare una sottoscrizione utente. Ad esempio, una quota potrebbe consentire all'utente di creare fino a cinque macchine virtuali (VM).

> [!IMPORTANT]
> Può richiedere fino a due ore per le quote di nuovo a essere disponibile nel portale per gli utenti o prima viene applicata una quota modificata.

È possibile configurare le quote per area. Ad esempio, un piano che fornisce servizi di calcolo per l'area A potrebbe avere una quota di due macchine virtuali.

>[!NOTE]
>In Azure Stack Development Kit, una sola area (denominato *locale*) è disponibile.

Altre informazioni sulle [tipi di quote in Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Piano di base

Quando si crea un'offerta, l'amministratore del servizio può includere un piano base. Questi piani base sono inclusi per impostazione predefinita, quando un utente si iscrive per l'offerta. Quando un utente si iscrive, hanno accesso a tutti i provider di risorse specificato in tali piani base (con le quote corrispondenti).

### <a name="add-on-plans"></a>Piani aggiuntivi

Piani aggiuntivi sono facoltativi piani che si aggiunge a un'offerta. I piani aggiuntivi non sono inclusi per impostazione predefinita nella sottoscrizione. Componente aggiuntivo piani sono piani aggiuntivi (con quote) disponibili in un'offerta che è possibile aggiungere un sottoscrittore per le sottoscrizioni. Ad esempio, è possibile offrire un piano di base con risorse limitate per una versione di valutazione e un piano aggiuntivo con le risorse più sostanziali per i clienti che decide di adottare il servizio.

## <a name="offers"></a>Offerte

Le offerte sono gruppi di uno o più piani creati in modo che gli utenti possono sottoscriverli. Ad esempio, offerta alfa può contenere un piano, che fornisce un set di servizi di calcolo e il piano B, che fornisce un set di servizi di archiviazione e rete.

Quando si [creare un'offerta](azure-stack-create-offer.md), è necessario includere almeno un piano di base, ma è anche possibile creare piani aggiuntivi che gli utenti possono aggiungere alla propria sottoscrizione.

## <a name="subscriptions"></a>Sottoscrizioni

Una sottoscrizione è l'accesso delle offerte. Se si è un operatore di Azure Stack per un provider di servizi, i servizi agli utenti (tenant) acquistare, sottoscrivere le offerte. Se si ha un operatore di Azure Stack in corrispondenza di un'organizzazione, gli utenti (dipendenti) possono sottoscrivere i servizi che offri senza sostenere.

Ogni combinazione di un utente con un'offerta è una sottoscrizione univoca. Un utente può avere più offerte sottoscritte da, ma ogni sottoscrizione si applica solo a una sola offerta. I piani, offerte e quote si applicano solo a una sottoscrizione univoca, non possono essere condivisi tra le sottoscrizioni. Ogni risorsa che crea un utente è associato a una sottoscrizione.

### <a name="default-provider-subscription"></a>Sottoscrizione del provider predefinito

La sottoscrizione del provider predefinito viene creata automaticamente quando si distribuisce Azure Stack Development Kit. Questa sottoscrizione è utilizzabile per gestire Azure Stack, distribuire i provider di risorse aggiuntive e creare i piani e offerte per gli utenti. Per motivi di licenza e sicurezza, consigliabile non usarla per eseguire le applicazioni e carichi di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui piani e offerte, vedere [creare un piano](azure-stack-create-plan.md).
