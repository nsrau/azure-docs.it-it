---
title: Migrazione dal modello classico al modello Resource Manager con gateway VPN| Microsoft Docs
description: Questa pagina fornisce una panoramica della migrazione dal modello classico al modello Resource Manager con Gateway VPN.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Migrazione dal modello classico al modello Resource Manager con gateway VPN
I gateway VPN possono ora essere migrati dal modello di distribuzione classica a quello Resource Manager. Altre informazioni su [funzionalità e vantaggi di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Questo articolo descrive in dettaglio come eseguire la migrazione da distribuzioni classiche al modello più recente basato su Resource Manager. 

I gateway VPN vengono migrati come parte della migrazione della rete virtuale dal modello classico a quello Resource Manager. Viene eseguita la migrazione di una rete virtuale alla volta. Non esiste alcun requisito aggiuntivo in termini di strumenti o prerequisiti alla migrazione. I passaggi di migrazione sono identici alla migrazione di rete virtuale esistente e sono documentati nella [pagina sulla migrazione delle risorse IaaS](../virtual-machines/windows/migration-classic-resource-manager-ps.md). La migrazione non comporta un tempo di inattività del percorso di dati e quindi i carichi di lavoro esistenti continueranno a funzionare senza perdita della connettività locale durante la migrazione. L'indirizzo IP pubblico associato al gateway VPN non cambia durante il processo di migrazione. Non è quindi necessario riconfigurare il router locale dopo il completamento della migrazione.  

Il modello Resource Manager è diverso dal modello classico ed è composto da gateway di rete virtuale, gateway di rete locale e risorse di connessione. Questi rappresentano il gateway VPN stesso, il sito locale che rappresenta rispettivamente lo spazio di indirizzi locale e la connettività tra i due elementi. Dopo il completamento della migrazione, i gateway non saranno disponibili nel modello classico e tutte le operazioni di gestione nei gateway di rete virtuale, nei gateway di rete locale e gli oggetti di connessione devono essere eseguite usando il modello Resource Manager.

## <a name="supported-scenarios"></a>Scenari supportati
La migrazione dal modello classico a quello Resource Manager supporta gli scenari di connettività VPN più comuni. Gli scenari supportati includono:

* Connettività da punto a sito
* Connettività da sito a sito con il gateway VPN connesso a una posizione locale
* Connettività da rete virtuale a rete virtuale tra due reti virtuali usando i gateway VPN
* Più reti virtuali connesse alla stessa posizione locale
* Connessione multisito
* Reti virtuali abilitate per il tunneling forzato

Gli scenari non supportati includono:  

* La rete virtuale con il gateway ExpressRoute e Gateway VPN al momento non è supportata.
* Scenari di transito in cui le estensioni di VM sono connesse ai server locali. Le limitazioni relative alla connettività VPN di transito sono descritte di seguito.

> [!NOTE]
> La convalida CIDR nel modello Resource Manager è più rigida rispetto a quella nel modello classico. Prima di eseguire la migrazione verificare che gli intervalli di indirizzi classici specificati siano conformi al formato CIDR valido prima di iniziare la migrazione. È possibile convalidare CIDR usando qualsiasi validator CIDR comune. La rete virtuale o i siti locali con intervalli CIDR non validi durante la migrazione generano uno stato di errore.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migrazione della connettività da rete virtuale a rete virtuale
La connettività da rete virtuale a rete virtuale in modalità classica è stata ottenuta creando una rappresentazione nel sito locale della rete virtuale connessa. È stato richiesto ai clienti di creare due siti locali che rappresentano le due reti virtuali da collegare. Sono stati quindi connessi alle reti virtuali corrispondenti usando il tunnel IPsec per stabilire la connettività tra le due reti virtuali. Questo modello presenta problemi di gestibilità poiché le modifiche all'intervallo di indirizzi in una rete virtuale devono essere gestite anche nella rappresentazione del sito locale corrispondente. Nel modello Resource Manager questa soluzione non è più necessaria. La connessione tra le due reti virtuali può essere ottenuta direttamente usando il tipo di connessione "Vnet2Vnet" nella risorsa di connessione. 

![Screenshot della migrazione da rete virtuale a rete virtuale.](./media/vpn-gateway-migration/migration1.png)

Durante la migrazione della rete virtuale viene rilevato che l'entità connessa al gateway VPN della rete di virtuale corrente è un'altra rete virtuale; assicurarsi che al termine della migrazione di entrambe le reti virtuali, i due siti locali che rappresentano l'altra rete virtuale non siano più visualizzati. Il modello classico di due gateway VPN, due siti locali e due connessioni tra loro viene trasformato nel modello Resource Manager con due gateway VPN e due connessioni di tipo Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Connettività VPN di transito
È possibile configurare i gateway VPN in una topologia in modo da ottenere la connettività locale per una rete virtuale tramite la connessione a un'altra rete virtuale che è connessa direttamente alle risorse locali. Si tratta di connettività VPN di transito in cui le istanze nella prima rete virtuale vengono connesse alle risorse locali tramite il transito al gateway VPN nella rete virtuale connessa, che è connessa direttamente alle risorse locali. Per ottenere questa configurazione nel modello di distribuzione classica, è necessario creare un sito locale che dispone di prefissi aggregati che rappresentano sia la rete virtuale connessa sia lo spazio di indirizzi locali. Questo sito locale di rappresentazione viene quindi connesso alla rete virtuale per ottenere la connettività di transito. Anche il modello classico presenta problemi di gestibilità simili poiché qualsiasi modifica all'intervallo di indirizzi locali deve essere gestita anche nel sito locale che rappresenta l'aggregazione di rete virtuale e risorse locali. L'introduzione del supporto del protocollo BGP nei gateway supportati da Resource Manager semplifica la gestibilità poiché i gateway connessi possono apprendere le route da locale senza modificare manualmente i prefissi.

![Screenshot dello scenario di routing di transito.](./media/vpn-gateway-migration/migration2.png)

Poiché la connettività da rete virtuale a rete virtuale viene trasformata senza richiedere siti locali, lo scenario di transito perde la connettività locale per la rete virtuale connessa indirettamente alle risorse locali. Al termine della migrazione, la perdita di connettività può essere attenuata procedendo nei due modi seguenti: 

* Abilitare il protocollo BGP sui gateway VPN connessi tra loro e alle risorse locali. L'abilitazione del protocollo BGP ripristina la connettività senza apportare altre modifiche di configurazione poiché le route vengono apprese e annunciate tra i gateway di rete virtuale. Si noti che l'opzione BGP è disponibile solo per le unità SKU standard e versioni successive.
* Stabilire una connessione esplicita dalla rete virtuale interessata al gateway di rete locale che rappresenta la posizione locale. Per creare e configurare il tunnel IPsec, potrebbe essere necessario anche modificare la configurazione del router locale.

## <a name="next-steps"></a>Passaggi successivi
Dopo le informazioni sul supporto della migrazione di gateway VPN, leggere [Migrazione di risorse IaaS supportata dalla piattaforma dal modello classico a quello di Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) per iniziare.

