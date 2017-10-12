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
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 082cd8a6cf50f76c89fe5995047396c734f83034
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-peering"></a>Peering di rete virtuale

La [rete virtuale di Azure](virtual-networks-overview.md) è lo spazio di rete privato in Azure che consente di connettere tra loro le risorse di Azure in modo sicuro.

Il peering reti virtuali consente di connettere facilmente le reti virtuali. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola dal punto di vista della connettività. Le macchine virtuali nelle reti virtuali con peering possono comunicare direttamente tra loro.
Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato tramite l'infrastruttura backbone di Microsoft, più o meno come il traffico viene instradato tra le macchine virtuali presenti nella stessa rete virtuale solo tramite indirizzi IP *privati*.

>[!IMPORTANT]
> È possibile eseguire il peering delle reti virtuali in aree di Azure differenti. Questa funzionalità è attualmente in anteprima. È possibile [registrare la sottoscrizione per l'anteprima.](virtual-network-create-peering.md) Il peering reti virtuali nelle stesse aree è disponibile a livello generale.
>

Il peering reti virtuali include i vantaggi seguenti:

* Il traffico che attraversa il peering reti virtuali è completamente privato. Passa attraverso la rete backbone di Microsoft, senza gateway o Internet pubblico.
* Connessione a bassa latenza e larghezza di banda elevata tra le risorse in reti virtuali diverse.
* Possibilità di usare le risorse in una rete virtuale da un'altra rete virtuale dopo aver eseguito il peering.
* Il peering reti virtuali consente di trasferire dati tra sottoscrizioni di Azure, modelli di distribuzione e aree di Azure (anteprima).
* Possibilità di eseguire il peering di reti virtuali create tramite il modello di distribuzione Azure Resource Manager o di eseguire il peering di una rete virtuale creata tramite Resource Manager a una rete virtuale creata con il modello di distribuzione classica. Per altre informazioni sulle differenze tra i due modelli di distribuzione di Azure, vedere l'articolo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informazioni sui modelli di distribuzione di Azure).

## <a name="requirements-constraints"></a>Requisiti e vincoli

* Il peering reti virtuali nella stessa area è disponibile a livello generale. Il peering reti virtuali in aree diverse è attualmente in anteprima negli Stati Uniti centro-occidentali, nel Canada centrale e negli Stati Uniti occidentali 2. È possibile [registrare la sottoscrizione per l'anteprima.](virtual-network-create-peering.md)
    > [!WARNING]
    > I peering reti virtuali creati in questo scenario potrebbero non avere lo stesso livello di disponibilità e affidabilità di quelli creati in scenari di rilascio con disponibilità generale. I peering reti virtuali possono presentare funzionalità limitate e potrebbero non essere disponibili in tutte le aree di Azure. Per ricevere le notifiche più aggiornate su disponibilità e stato della funzionalità, vedere la pagina [Aggiornamenti della rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network) .

* Le reti virtuali con peering non devono avere spazi di indirizzi IP sovrapposti.
* Non è possibile aggiungere o eliminare spazi di indirizzi da una rete virtuale dopo che ne è stato effettuato il peering con un'altra rete virtuale.
* Il peering viene eseguito tra due reti virtuali senza alcuna relazione transitiva derivata nei peering. Se, ad esempio, vengono eseguiti il peering della rete virtuale A con la rete virtuale B e il peering della rete virtuale B con la rete virtuale C, il peering della rete virtuale A con la rete virtuale C *non* viene eseguito.
* È possibile eseguire il peering di reti virtuali presenti in due sottoscrizioni diverse, a condizione che un utente con privilegi (vedere le [autorizzazioni specifiche](create-peering-different-deployment-models-subscriptions.md#permissions)) di entrambe le sottoscrizioni autorizzi il peering e che le sottoscrizioni siano associate allo stesso tenant di Azure Active Directory. È possibile usare un [gateway VPN ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) per connettere reti virtuali nelle sottoscrizioni associate a tenant diversi di Active Directory.
* È possibile eseguire il peering di reti virtuali se entrambe vengono create tramite il modello di distribuzione Resource Manager o se una rete virtuale viene creata tramite il modello di distribuzione Resource Manager e l'altra viene creata tramite il modello di distribuzione classica. Non è tuttavia possibile eseguire il peering di due reti virtuali create tramite il modello di distribuzione classica. È possibile usare un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) per collegare reti virtuali create tramite il modello di distribuzione classica.
* Anche se non esistono altre restrizioni di larghezza di banda nella comunicazione tra macchine virtuali in reti virtuali con peering, è tuttavia previsto un limite massimo per la larghezza di banda di rete, che varia a seconda delle dimensioni delle macchine virtuali. Per altre informazioni sulla larghezza di banda di rete massima per le diverse dimensioni delle macchine virtuali, vedere gli articoli sulle dimensioni delle macchine virtuali [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Peering di rete virtuale di base](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Connettività

Dopo avere eseguito il peering tra reti virtuali, le risorse di una delle due reti virtuali possono connettersi direttamente alle risorse nella rete virtuale con peering.

La latenza di rete tra le macchine virtuali in reti virtuali con peering nella stessa area è identica a quella presente in una singola rete virtuale. La velocità effettiva della rete si basa sulla larghezza di banda consentita per la macchina virtuale proporzionalmente alle relative dimensioni, senza altre restrizioni alla larghezza di banda consentita nel peering.

Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato direttamente attraverso l'infrastruttura backbone di Microsoft, non attraverso un gateway o Internet pubblico.

Le macchine virtuali in una rete virtuale possono accedere al bilanciamento del carico interno nella rete virtuale con peering della stessa area. Il supporto del bilanciamento del carico interno non si estende alle reti virtuali con peering globale in anteprima. La versione di disponibilità generale del peering reti virtuali globale supporterà il bilanciamento del carico interno.

È possibile applicare gruppi di sicurezza di rete a una delle reti virtuali per bloccare l'accesso alle altre reti virtuali o subnet, se necessario.
Quando si configura il peering di rete virtuale, è possibile aprire o chiudere le regole dei gruppi di sicurezza di rete tra le reti virtuali. Se si apre la connettività completa tra reti virtuali con peering (opzione predefinita), è possibile applicare gruppi di sicurezza di rete a subnet o reti virtuali specifiche per bloccare o negare un accesso specifico. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Panoramica dei gruppi di sicurezza di rete](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Concatenamento dei servizi

È possibile configurare route definite dall'utente che puntano a macchine virtuali di reti virtuali con peering come indirizzo IP dell'hop successivo. In questo modo viene abilitato il concatenamento dei servizi, che consente di indirizzare il traffico da una rete virtuale a un'appliance virtuale in esecuzione in una rete virtuale con peering usando le tabelle di route definite dall'utente.

È anche possibile creare in modo efficace ambienti di tipo hub e spoke, in cui l'hub può ospitare componenti dell'infrastruttura, come le appliance di rete virtuale, e tutte le reti virtuali spoke possono eseguire il peering con la rete virtuale dell'hub. Il traffico può quindi fluire attraverso le appliance di rete virtuale in esecuzione nella rete virtuale dell'hub. In breve, il peering di rete virtuale consente di usare l'indirizzo IP dell'hop successivo nella route definita dall'utente come indirizzo IP di una macchina virtuale nella rete virtuale con peering. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Panoramica delle route definite dall'utente](virtual-networks-udr-overview.md). Vedere come creare una [topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)

## <a name="gateways-and-on-premises-connectivity"></a>Gateway e connettività locale

Ogni rete virtuale, che ne sia stato eseguito o meno il peering con un'altra rete virtuale, può comunque avere un proprio gateway e usarlo per connettersi a una rete locale. È possibile anche configurare [connessioni da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) tramite gateway, anche se è stato eseguito il peering delle reti virtuali.

Quando vengono configurate entrambe le opzioni per la connettività tra reti virtuali, il flusso di traffico tra di esse seguirà la configurazione del peering, ovvero tramite il backbone di Azure.

Dopo aver eseguito il peering delle reti virtuali nella stessa area, gli utenti possono anche configurare il gateway nella rete virtuale con peering come punto di transito verso una rete locale. In questo caso, la rete virtuale che usa un gateway remoto non può avere un proprio gateway. Una rete virtuale può avere un solo gateway, che può essere locale o remoto (nella rete virtuale con peering), come illustrato nell'immagine seguente:

![Peering reti virtuali con transito](./media/virtual-networks-peering-overview/figure04.png)

Il transito gateway non è supportato nella relazione di peering tra le reti virtuali create con aree o modelli di distribuzione diversi. Perché un transito gateway possa funzionare, entrambe le reti virtuali nella relazione di peering devono essere state create tramite Resource Manager e trovarsi nella stessa area. Le reti virtuali con peering globale non supportano attualmente il transito gateway.

Quando viene eseguito il peering di reti virtuali che condividono una singola connessione Azure ExpressRoute, il traffico tra di esse passa attraverso la relazione di peering, ovvero tramite la rete backbone di Azure. È comunque possibile continuare a usare i gateway locali in ogni rete virtuale per connettersi al circuito locale. oppure usare un gateway condiviso e configurare il transito per la connettività locale.

## <a name="permissions"></a>Autorizzazioni

Il peering di rete virtuale è un'operazione con privilegi. È una funzione separata nello spazio dei nomi VirtualNetworks. È possibile assegnare a un utente diritti specifici per autorizzare il peering. Un utente con accesso in lettura e scrittura alla rete virtuale eredita questi diritti automaticamente.

Un utente amministratore o con privilegi per la funzionalità di peering può avviare un'operazione di peering in un'altra rete virtuale. Il livello minimo di autorizzazione necessaria è Collaboratore Rete. Se è presente una richiesta di peering corrispondente sull'altro lato e vengono soddisfatti altri requisiti, il peering verrà stabilito.

Per eseguire ad esempio il peering di reti virtuali denominate myvirtual networkA e myvirtual networkB, è necessario assegnare all'account il ruolo minimo o le autorizzazioni minime seguenti per ogni rete virtuale:

|Rete virtuale|Modello di distribuzione|Ruolo|Autorizzazioni|
|---|---|---|---|
|myvirtual networkA|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myvirtual networkB|Gestione risorse|[Collaboratore di rete](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classico|[Collaboratore reti virtuali classiche](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Monitorare

Quando si esegue il peering di due reti virtuali create tramite Resource Manager, deve essere configurato un peering per ogni rete virtuale nel peering.
È possibile monitorare lo stato della connessione peering. Lo stato del peering può avere uno dei valori seguenti:

* **Avviato**: quando si crea il peering alla seconda rete virtuale dalla prima rete virtuale, lo stato del peering è Avviato.

* **Connesso**: quando si crea il peering dalla seconda rete virtuale alla prima rete virtuale, lo stato del peering è Connesso. Se si visualizza lo stato del peering per la prima rete virtuale, si osserva che lo stato è cambiato da Avviato a Connesso. Il peering è stabilito correttamente solo quando lo stato del peering per entrambe le reti virtuali è Connesso.

* **Disconnesso**: se uno dei collegamenti per il peering viene eliminato dopo che è stata stabilita una connessione, lo stato del peering è Disconnesso.

## <a name="troubleshoot"></a>Risoluzione dei problemi

Per risolvere i problemi del traffico che passa attraverso la connessione peering, è possibile [verificare le route valide.](virtual-network-routes-troubleshoot-portal.md)

È anche possibile risolvere i problemi di connettività a una macchina virtuale in una rete virtuale con peering usando [Verifica della connettività](../network-watcher/network-watcher-connectivity-portal.md) di Network Watcher. Verifica della connettività consente di vedere come avviene il routing dall'interfaccia di rete della VM di origine all'interfaccia di rete della VM di destinazione.

## <a name="limits"></a>Limiti

Esistono limiti al numero di peering consentiti per una singola rete virtuale. Il numero predefinito di peering è 50. È possibile aumentare questo numero. Per altre informazioni, vedere [Limiti relativi alla rete di Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Prezzi

È previsto un addebito nominale per il traffico in ingresso e in uscita che usa una connessione con peering reti virtuali. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Passaggi successivi

* Completare un'esercitazione sul peering di reti virtuali. Un peering di reti virtuali viene generato tra reti virtuali create tramite modelli di distribuzione uguali o diversi esistenti in sottoscrizioni uguali o diverse. Completare un'esercitazione per uno degli scenari seguenti:

    |Modello di distribuzione di Azure  | Subscription  |
    |---------|---------|
    |Entrambi Resource Manager |[Uguale](virtual-network-create-peering.md)|
    | |[Diversa](create-peering-different-subscriptions.md)|
    |Uno di Resource Manager, uno della versione classica     |[Uguale](create-peering-different-deployment-models.md)|
    | |[Diversa](create-peering-different-deployment-models-subscriptions.md)|

* Vedere come creare una [topologia di rete hub-spoke.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)
* Informazioni su tutte le [impostazioni per il peering di reti virtuali e su come modificarle](virtual-network-manage-peering.md)
