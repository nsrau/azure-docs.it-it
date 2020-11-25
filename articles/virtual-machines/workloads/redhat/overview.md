---
title: Panoramica dei carichi di lavoro Red Hat in Azure | Microsoft Docs
description: Informazioni sulle offerte di prodotti Red Hat disponibili in Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 3e4a770a33f5cdb3c3b2de09cbcb8431b2fc3fbb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968656"
---
# <a name="red-hat-workloads-on-azure"></a>Carichi di lavoro Red Hat in Azure

I carichi di lavoro Red Hat sono supportati tramite un'ampia gamma di offerte in Azure. Le immagini Red Hat Enterprise Linux (RHEL) sono alla base dei carichi di lavoro RHEL, così come Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Immagini di Red Hat Enterprise Linux

In Azure è disponibile un'ampia gamma di immagini RHEL. Queste immagini sono rese disponibili tramite due modelli di licenza diversi, ovvero con pagamento in base al consumo e BYOS (Bring Your Own Subscription). Nuove immagini RHEL in Azure vengono pubblicate man mano che nuove versioni di RHEL vengono rilasciate e aggiornate nel corso del ciclo di vita in base alle necessità.

### <a name="pay-as-you-go-images"></a>Immagini con pagamento in base al consumo

Azure offre un'ampia gamma di immagini RHEL con pagamento in base al consumo. Queste immagini sono debitamente autorizzate per RHEL e sono associate a un'origine di aggiornamenti (Red Hat Update Infrastructure). Sono soggette a una tariffa Premium per gli aggiornamenti e l'entitlement RHEL. Le varianti di immagini RHEL con pagamento in base al consumo includono:

* RHEL Standard.
* RHEL for SAP.
* RHEL for SAP con disponibilità elevata e servizi di aggiornamento.

È consigliabile usare le immagini con pagamento in base al consumo se si vuole evitare di pagare separatamente per il numero appropriato di sottoscrizioni.

### <a name="red-hat-gold-images"></a>Immagini Red Hat Gold

Azure offre anche immagini Red Hat Gold (`rhel-byos`). Queste immagini possono essere utili per i clienti che vogliono usare le proprie sottoscrizioni Red Hat in Azure. Occorre però abilitare le sottoscrizioni di Red Hat esistenti per Red Hat Cloud Access prima di poterle usare in Azure. L'accesso a queste immagini viene concesso automaticamente quando le sottoscrizioni di Red Hat sono abilitate per Cloud Access e soddisfano i requisiti di idoneità. L'uso di queste immagini consente al cliente di evitare la fatturazione duplicata che può essere applicata alle immagini con pagamento in base al consumo.
* Per informazioni, vedere [come abilitare le sottoscrizioni di Red Hat per Cloud Access con Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Per informazioni, vedere come [individuare le immagini Red Hat Gold nel portale di Azure, con l'interfaccia della riga di comando di Azure o con il cmdlet PowerShell](./byos.md).

> [!NOTE]
> viene addebitata quando un utente paga due volte per le sottoscrizioni RHEL. Questo scenario si verifica in genere quando un cliente usa Red Hat Subscription-Manager per associare un entitlement a una VM RHEL con pagamento in base al consumo. Ad esempio, un cliente che usa Subscription-Manager per associare un entitlement per pacchetti SAP a un'immagine RHEL con pagamento in base al consumo riceve indirettamente una doppia fatturazione, in quanto paga due volte per RHEL, una volta tramite la tariffa Premium con pagamento in base al consumo e una volta tramite la sua sottoscrizione SAP. Questo scenario non si verifica per gli utenti di immagini BYOS.

### <a name="generation-2-images"></a>Immagini di seconda generazione

Le macchine virtuali di seconda generazione offrono alcune funzionalità più recenti rispetto a quelle di prima generazione. Per altre informazioni, vedere la [documentazione sulla seconda generazione](../../generation-2.md). La principale differenza dal punto di vista delle immagini RHEL è che le VM di seconda generazione usano un'interfaccia UEFI invece di un'interfaccia firmware del BIOS. Usano inoltre una tabella di partizione GUID (GPT) invece di un record di avvio master (MBR) in fase di avvio. L'uso di GPT consente, tra l'altro, di avere dimensioni del disco del sistema operativo superiori a 2 TB. In più, le [macchine virtuali della serie Mv2](../../mv2-series.md) vengono eseguite solo su immagini di seconda generazione.

Le immagini RHEL di seconda generazione sono disponibili in Azure Marketplace. Cercare "gen2" nello SKU nell'elenco di tutte le immagini visualizzato quando si usa l'interfaccia della riga di comando di Azure. Passare alla scheda **Avanzate** nel processo di distribuzione della macchina virtuale per distribuire una VM di seconda generazione.

## <a name="red-hat-update-infrastructure"></a>Red Hat Update Infrastructure

Azure fornisce Red Hat Update Infrastructure solo per le macchine virtuali RHEL con pagamento in base al consumo. RHUI funge da mirror delle reti CDN Red Hat, ma è accessibile solo alle VM RHEL di Azure con pagamento in base al consumo. Si avrà accesso ai pacchetti appropriati a seconda dell'immagine RHEL distribuita. Ad esempio, un'immagine RHEL for SAP avrà accesso ai pacchetti SAP oltre che ai pacchetti RHEL di base.

### <a name="rhui-update-behavior"></a>Comportamento degli aggiornamenti RHUI

Per impostazione predefinita, le immagini RHEL connesse a RHUI vengono aggiornate alla versione secondaria più recente di RHEL quando viene eseguito un comando `yum update`. In base a questo comportamento, una VM RHEL 7.4 può essere aggiornata a RHEL 7.7 se viene eseguita un'operazione `yum update` su di essa. Questo comportamento dipende dalla progettazione per RHUI. Per mitigare questo comportamento degli aggiornamenti, passare dai normali repository RHEL ai [repository EUS (Extended Update Support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [immagini RHEL in Azure](./redhat-images.md).
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md).
* Altre informazioni sull'offerta [Red Hat Gold Image (`rhel-byos`) ](./byos.md).