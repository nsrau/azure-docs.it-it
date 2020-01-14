---
title: Panoramica dei carichi di lavoro Red Hat in Azure | Microsoft Docs
description: Informazioni sulle offerte di prodotti Red Hat disponibili in Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454975"
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

### <a name="bring-your-own-subscription-byos-images"></a>Immagini BYOS (Bring Your Own Subscription)
Azure offre anche immagini RHEL con modello di licenza BYOS. Queste immagini possono essere utili per i clienti che vogliono usare le proprie sottoscrizioni Red Hat in Azure. Occorre però convertire le sottoscrizioni esistenti in sottoscrizioni con accesso al cloud per poterle usare in Azure. L'accesso a queste immagini viene concesso solo dopo che Red Hat verifica che il cliente disponga di sottoscrizioni con accesso al cloud sufficienti. L'uso di queste immagini consente al cliente di evitare la fatturazione duplicata che può essere applicata alle immagini con pagamento in base al consumo. È possibile richiedere l'accesso alle immagini BYOS [qui](https://aka.ms/rhel-byos).

> [!NOTE]
> La fatturazione duplicata viene addebitata quando un utente paga due volte per le sottoscrizioni RHEL. Questo accade generalmente quando un cliente usa subscription-manager per associare un entitlement a una VM RHEL con pagamento in base al consumo. Ad esempio, a un cliente che usa subscription-manager per associare un entitlement per pacchetti SAP a un'immagine RHEL con pagamento in base al consumo verrà indirettamente addebitata una fatturazione duplicata, in quanto pagherà due volte per RHEL: una tramite la tariffa premium con pagamento in base al consumo e una tramite la sottoscrizione SAP. Questo non vale per gli utenti di immagini BYOS.

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat Update Infrastructure (RHUI)
Azure fornisce Red Hat Update Infrastructure solo per le macchine virtuali RHEL con pagamento in base al consumo. RHUI funge da mirror delle reti CDN Red Hat, ma è accessibile solo alle VM RHEL di Azure con pagamento in base al consumo. Si avrà accesso ai pacchetti appropriati a seconda dell'immagine RHEL distribuita. Ad esempio, un'immagine RHEL for SAP avrà accesso ai pacchetti SAP oltre ai pacchetti RHEL di base.

### <a name="rhui-update-behavior"></a>Comportamento degli aggiornamenti RHUI
Per impostazione predefinita, le immagini RHEL connesse a RHUI vengono aggiornate alla versione secondaria più recente di RHEL quando viene eseguito un comando `yum update`. In base a questo comportamento, una VM RHEL 7.4 può essere aggiornata a RHEL 7.7 se viene eseguita un'operazione `yum update` su di essa. Si tratta di un comportamento legato alla progettazione di RHUI. È però possibile attenuare questo comportamento degli aggiornamenti passando dai normali repository RHEL ai [repository EUS (Extended Update Support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [immagini RHEL in Azure](./redhat-images.md)
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md)
* Altre informazioni sull'[offerta BYOS per RHEL](./redhat-byos.md)