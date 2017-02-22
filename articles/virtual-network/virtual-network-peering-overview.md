---
title: Peering reti virtuali di Azure | Documentazione Microsoft
description: Informazioni sul peering reti virtuali in Azure.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: 15afcad97941fc595478e36e826a73831f40475e
ms.openlocfilehash: eb05b504c5cf13cd852a5e01cc3bec79fd20d547


---
# <a name="virtual-network-peering"></a>Peering di rete virtuale
Il peering reti virtuali consente di connettere due reti virtuali nella stessa area tramite la rete backbone di Azure. Dopo avere eseguito il peering, le due reti virtuali vengono visualizzate come una sola dal punto di vista della connettività. Le due reti virtuali continuano a essere gestite come risorse separate, ma le macchine virtuali (VM) nelle reti virtuali con peering possono comunicare direttamente tra di esse usando gli indirizzi IP privati.

Il traffico tra le VM nelle reti virtuali con peering viene instradato tramite l'infrastruttura di Azure, in modo analogo al traffico instradato tra le macchine virtuali nella stessa rete virtuale. Ecco alcuni dei vantaggi offerti dall'uso del peering reti virtuali:

* Bassa latenza, connessione a larghezza di banda elevata tra le risorse in reti virtuali diverse.
* Possibilità di usare le risorse, ad esempio le appliance di rete virtuale, e i gateway VPN come punti di transito in rete virtuale con peering.
* Possibilità di eseguire il peering tra due reti virtuali create tramite il modello di distribuzione Azure Resource Manager o di eseguire il peering di una rete virtuale creata tramite Resource Manager a una rete virtuale creata tramite il modello di distribuzione classica. Per altre informazioni sulle differenze tra i due modelli di distribuzione di Azure, vedere l'articolo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

Requisiti e aspetti principali del peering reti virtuali:

* Le reti virtuali con peering devono esistere nella stessa area di Azure.
* Le reti virtuali con peering non devono avere spazi di indirizzi IP sovrapposti.
* Il peering reti virtuali viene eseguito tra due reti virtuali senza alcuna relazione transitiva derivata nei peering. Se, ad esempio, vengono eseguiti il peering della rete virtuale A con la rete virtuale B e il peering della rete virtuale B con la rete virtuale C, il peering della rete virtuale A con la rete virtuale C *non* viene eseguito.
* È possibile eseguire il peering di reti virtuali che esistono in due diverse sottoscrizioni, a condizione che un utente con privilegi di entrambe le sottoscrizioni autorizzi il peering e che le sottoscrizioni siano associate allo stesso tenant di Active Directory.
* È possibile eseguire il peering delle reti virtuali se entrambe vengono create tramite il modello di distribuzione Resource Manager o se una viene creata tramite il modello di distribuzione Resource Manager e l'altra viene creata tramite il modello di distribuzione classica. Non è tuttavia possibile eseguire il peering di due reti virtuali create tramite il modello di distribuzione classica. Quando si esegue il peering di reti virtuali create tramite modelli di distribuzione diversi, le reti virtuali devono esistere entrambe nella *stessa* sottoscrizione. È possibile eseguire il peering di reti virtuali create tramite modelli di distribuzione diversi che esistono in sottoscrizioni *diverse* nella release di **anteprima**. Per altre informazioni, vedere l'articolo [Creare un peering reti virtuali usando PowerShell](virtual-networks-create-vnetpeering-arm-ps.md).
* Anche se non esistono altre restrizioni per la larghezza di banda per la comunicazione tra VM in reti virtuali con peering, è tuttavia previsto un limite massimo per larghezza di banda di rete a seconda delle dimensioni delle macchine virtuali. Per altre informazioni sulla larghezza di banda di rete massima per le diverse dimensioni delle macchine virtuali, vedere gli articoli sulle dimensioni delle VM [Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [Linux](../virtual-machines/virtual-machines-linux-sizes.md).

![Peering reti virtuali di base](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Connettività
Dopo avere eseguito il peering di due reti virtuali, le VM o i ruoli dei servizi cloud nella rete virtuale possono connettersi direttamente alle altre risorse connesse alla rete virtuale con peering. Le due reti virtuali hanno la connettività completa a livello IP.

La latenza di rete per il round trip tra due VM in reti virtuali con peering è identica a quella di un round trip in una singola rete virtuale. La velocità effettiva della rete si basa sulla larghezza di banda consentita per la VM proporzionalmente alle relative dimensioni, senza altre restrizioni alla larghezza di banda consentita nel peering.

Il traffico tra le VM nelle reti virtuali con peering viene instradato direttamente tramite l'infrastruttura di back-end di Azure, non tramite un gateway.

Le VM connesse a una rete virtuale possono accedere agli endpoint con carico bilanciato interno nella rete virtuale con peering. È possibile applicare gruppi di sicurezza di rete a una delle reti virtuali per bloccare l'accesso alle altre reti virtuali o subnet, se necessario.

Quando si configura il peering, è possibile aprire o chiudere le regole dei gruppi di sicurezza di rete tra le reti virtuali. Se si apre la connettività completa tra le reti virtuali con peering (opzione predefinita), è possibile applicare gruppi di sicurezza di rete a subnet o VM specifiche per bloccare o negare un accesso specifico. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).

La risoluzione dei nomi DNS interni fornita da Azure per le VM non funziona tra le reti virtuali con peering. Le VM hanno nomi DNS interni che possono essere risolti solo nella rete virtuale locale. È tuttavia possibile configurare le VM connesse alle reti virtuali con peering come server DNS per una rete virtuale. Per altre informazioni, vedere l'articolo [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="service-chaining"></a>Concatenamento dei servizi
È possibile configurare route definite dall'utente che puntano alle VM nelle reti virtuali con peering come indirizzo IP dell'hop successivo, come illustrato nel diagramma più avanti in questo articolo. Ciò abilita il concatenamento dei servizi, che consente di indirizzare il traffico da una rete virtuale a un'appliance virtuale in esecuzione in una rete virtuale con peering, usando le route definite dall'utente.

È anche possibile creare in modo efficace ambienti di tipo hub e spoke, in cui l'hub può ospitare componenti dell'infrastruttura, come le appliance di rete virtuale, e tutte le reti virtuali spoke possono eseguire il peering con tali componenti, nonché un subset del traffico alle appliance in esecuzione nella rete virtuale dell'hub. In breve, il peering reti virtuali consente di usare l'indirizzo IP dell'hop successivo nella route definita dall'utente come indirizzo IP di una VM nella rete virtuale con peering. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).

## <a name="gateways-and-on-premises-connectivity"></a>Gateway e connettività locale
Ogni rete virtuale, che ne sia stato eseguito o meno il peering con un'altra rete virtuale, può comunque avere un proprio gateway e usarlo per una rete locale. Gli utenti possono anche configurare [connessioni da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) usando i gateway, anche se è stato eseguito il peering delle reti virtuali.

Quando vengono configurate entrambe le opzioni per la connettività tra reti virtuali, il flusso di traffico tra di esse segue la configurazione del peering, ad esempio tramite il backbone di Azure.

Quando è stato eseguito il peering delle reti virtuali, gli utenti possono anche configurare il gateway nella rete virtuale con peering come punto di transito a una rete locale. In questo caso, la rete virtuale che usa un gateway remoto non può avere un proprio gateway. Una rete virtuale può avere un solo gateway. Il gateway può essere locale o remoto (nella rete virtuale con peering), come illustrato nell'immagine seguente:

![Peering reti virtuali con transito](./media/virtual-networks-peering-overview/figure02.png)

Il transito gateway non è supportato nella relazione di peering tra le reti virtuali create tramite modelli di distribuzione diversi. Perché un transito gateway possa funzionare, entrambe le reti virtuali nella relazione di peering devono essere state create tramite Resource Manager.

Quando viene eseguito il peering di reti virtuali che condividono una singola connessione Azure ExpressRoute, il traffico tra di esse passa attraverso la relazione di peering, ovvero tramite la rete backbone di Azure. È possibile continuare a usare i gateway locali in ogni rete virtuale per connettersi al circuito locale oppure usare un gateway condiviso e configurare il transito per la connettività locale.

## <a name="provisioning"></a>Provisioning
Il peering reti virtuali è un'operazione con privilegi. È una funzione separata nello spazio dei nomi VirtualNetworks. È possibile assegnare a un utente diritti specifici per autorizzare il peering. Un utente con accesso in lettura e scrittura alla rete virtuale eredita questi diritti automaticamente.

Un utente amministratore o con privilegi per la funzionalità di peering può avviare un'operazione di peering in un'altra rete virtuale. Se è presente una richiesta di peering corrispondente sull'altro lato e vengono soddisfatti altri requisiti, il peering verrà stabilito.

Per informazioni su come stabilire il peering reti virtuali tra due reti virtuali, vedere gli articoli nella sezione [Passaggi successivi](#next-steps) di questo articolo.

## <a name="limits"></a>Limiti
Esistono limiti al numero di peering consentiti per una singola rete virtuale. Per altre informazioni, vedere [Limiti relativi alla rete di Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Prezzi
È previsto un importo minimo per il traffico in ingresso e in uscita che utilizza un peering reti virtuali. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="a-namenext-stepsanext-steps"></a><a name="next-steps"></a>Passaggi successivi
Informazioni su come creare un peering reti virtuali usando:

* [Il portale di Azure](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Modello di Azure Resource Manager](virtual-networks-create-vnetpeering-arm-template-click.md)



<!--HONumber=Feb17_HO1-->


