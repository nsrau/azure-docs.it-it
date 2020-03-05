---
title: Panoramica dei carichi di lavoro Red Hat in Azure | Microsoft Docs
description: Informazioni sulle offerte di prodotti Red Hat disponibili in Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 424ef37885d685829a11d1864a72b043a562231c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920553"
---
# <a name="red-hat-workloads-on-azure"></a>Carichi di lavoro Red Hat in Azure
I carichi di lavoro Red Hat sono supportati tramite un'ampia gamma di offerte in Azure. Le immagini Red Hat Enterprise Linux (RHEL) sono alla base dei carichi di lavoro RHEL, così come Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Immagini Red Hat Enterprise Linux (RHEL)
In Azure è disponibile un'ampia gamma di immagini RHEL. Queste immagini sono rese disponibili tramite due diversi modelli di licenza, ovvero con pagamento in base al consumo e BYOS (Bring Your Own Subscription). Nuove immagini RHEL in Azure vengono pubblicate man mano che nuove versioni di RHEL vengono rilasciate e aggiornate nel corso del ciclo di vita in base alle necessità.

### <a name="pay-as-you-go-payg-images"></a>Immagini con pagamento in base al consumo
Azure offre un'ampia gamma di immagini RHEL con pagamento in base al consumo. Queste immagini sono debitamente autorizzate per RHEL e sono associate a un'origine di aggiornamenti (Red Hat Update Infrastructure). Sono soggette a una tariffa Premium per gli aggiornamenti e l'entitlement RHEL. Le varianti di immagini RHEL con pagamento in base al consumo includono:
* RHEL Standard
* RHEL for SAP
* RHEL for SAP con disponibilità elevata e servizi di aggiornamento.

È consigliabile usare le immagini con pagamento in base al consumo se si vuole evitare di pagare separatamente per il numero appropriato di sottoscrizioni.

### <a name="red-hat-gold-images-rhel-byos"></a>Immagini Red Hat Gold (`rhel-byos`)
Azure offre anche immagini Red Hat Gold. Queste immagini possono essere utili per i clienti che vogliono usare le proprie sottoscrizioni Red Hat in Azure. Occorre però abilitare le sottoscrizioni di Red Hat esistenti per Red Hat Cloud Access prima di poterle usare in Azure. L'accesso a queste immagini viene concesso automaticamente quando le sottoscrizioni di Red Hat sono abilitate per Cloud Access e soddisfano i requisiti di idoneità. L'uso di queste immagini consente al cliente di evitare la fatturazione duplicata che può essere applicata alle immagini con pagamento in base al consumo.
* [Informazioni su come abilitare le sottoscrizioni di Red Hat per Cloud Access con Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Informazioni su come individuare le immagini Red Hat Gold nel portale di Azure, con l'interfaccia della riga di comando o con il cmdlet PowerShell](./byos.md)

> [!NOTE]
> La fatturazione duplicata viene addebitata quando un utente paga due volte per le sottoscrizioni RHEL. Questo accade generalmente quando un cliente usa subscription-manager per associare un entitlement a una VM RHEL con pagamento in base al consumo. Ad esempio, a un cliente che usa subscription-manager per associare un entitlement per pacchetti SAP a un'immagine RHEL con pagamento in base al consumo verrà indirettamente addebitata una fatturazione duplicata, in quanto pagherà due volte per RHEL: una tramite la tariffa premium con pagamento in base al consumo e una tramite la sottoscrizione SAP. Questo non vale per gli utenti di immagini BYOS.

### <a name="generation-2-images"></a>Immagini di seconda generazione
Le macchine virtuali di seconda generazione offrono alcune funzionalità più recenti rispetto alle macchine virtuali di prima generazione. I dettagli sono illustrati nella [documentazione di Generazione 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). La differenza principale rispetto alla prospettiva di un'immagine RHEL è che le macchine virtuali di seconda generazione usano un'interfaccia UEFI anziché un firmware BIOS e usano una tabella di partizione GUID (GPT) invece di un record di avvio principale (MBR) al momento dell'avvio. Questo consente, tra le altre cose, dimensioni del disco del sistema operativo superiori a 2 TB. In più, le [macchine virtuali della serie Mv2](../../mv2-series.md) vengono eseguite solo su immagini di seconda generazione.

Le immagini di seconda generazione RHEL sono disponibili nel Marketplace. Cercare "gen2" nello SKU dell'immagine quando si elencano tutte le immagini usando l'interfaccia della riga di comando di Azure e passare alla scheda "Avanzate" nel processo di distribuzione della macchina virtuale per distribuire una macchina virtuale di seconda generazione.

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat Update Infrastructure (RHUI)
Azure fornisce Red Hat Update Infrastructure solo per le macchine virtuali RHEL con pagamento in base al consumo. RHUI funge da mirror delle reti CDN Red Hat, ma è accessibile solo alle VM RHEL di Azure con pagamento in base al consumo. Si avrà accesso ai pacchetti appropriati a seconda dell'immagine RHEL distribuita. Ad esempio, un'immagine RHEL for SAP avrà accesso ai pacchetti SAP oltre ai pacchetti RHEL di base.

### <a name="rhui-update-behavior"></a>Comportamento degli aggiornamenti RHUI
Per impostazione predefinita, le immagini RHEL connesse a RHUI vengono aggiornate alla versione secondaria più recente di RHEL quando viene eseguito un comando `yum update`. In base a questo comportamento, una VM RHEL 7.4 può essere aggiornata a RHEL 7.7 se viene eseguita un'operazione `yum update` su di essa. Si tratta di un comportamento legato alla progettazione di RHUI. È però possibile attenuare questo comportamento degli aggiornamenti passando dai normali repository RHEL ai [repository EUS (Extended Update Support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [immagini RHEL in Azure](./redhat-images.md)
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md)
* Altre informazioni sull'offerta [Red Hat Gold Image (`rhel-byos`) ](./byos.md)
