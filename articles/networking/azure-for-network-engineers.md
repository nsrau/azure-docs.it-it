---
title: 'Azure ExpressRoute: Azure per i tecnici di rete'
description: Questa pagina illustra agli ingegneri di rete tradizionali il funzionamento delle reti in Azure.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 4f513da4e7883cd273098039c9c4a4645d849f0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516001"
---
# <a name="azure-for-network-engineers"></a>Azure per tecnici di rete
In qualità di progettista di rete convenzionale, le risorse fisiche, ad esempio router, commutatori, cavi e firewall per la creazione dell'infrastruttura, sono state distribuite. A un livello logico è stata configurata la LAN virtuale (VLAN), lo Spanning Tree Protocol (STP), i protocolli di routing (RIP, OSPF, BGP). La rete è stata gestita usando gli strumenti di gestione e l'interfaccia della riga di comando. La rete nel cloud è diversa, in cui gli endpoint di rete sono logici e l'uso dei protocolli di routing è minimo. Si collaborerà con Azure Resource Manager API, l'interfaccia della riga di comando di Azure e PowerShell per la configurazione e la gestione degli asset in Azure. Si inizierà il percorso di rete nel cloud comprendendo i tenant di base della rete di Azure. 
## <a name="virtual-network"></a>Rete virtuale
Quando si progetta una rete dal basso verso l'alto, si raccolgono alcune informazioni di base. Queste informazioni possono essere costituite da numero di host, dispositivi di rete, numero di subnet, routing tra subnet, domini di isolamento, ad esempio VLAN. Queste informazioni consentono di dimensionare i dispositivi di rete e di sicurezza, nonché di creare l'architettura per supportare le applicazioni e i servizi.

Quando si pianifica la distribuzione di applicazioni e servizi in Azure, si inizierà creando un limite logico in Azure, denominato rete virtuale. Questa rete virtuale è simile a un limite di rete fisica. Poiché si tratta di una rete virtuale, non è necessario l'ingranaggio fisico, ma è comunque necessario pianificare le entità logiche, ad esempio gli indirizzi IP, le subnet IP, il routing e i criteri.

Quando si crea una rete virtuale in Azure, questa viene preconfigurata con un intervallo di indirizzi IP (10.0.0.0/16). Questo intervallo non è corretto. è possibile definire un intervallo di indirizzi IP personalizzato. È possibile definire gli intervalli di indirizzi IPv4 e IPv6. Gli intervalli IP definiti per la rete virtuale non vengono annunciati a Internet. È possibile creare più subnet dall'intervallo di indirizzi IP. Queste subnet verranno utilizzate per assegnare gli indirizzi IP alle interfacce di rete virtuale (schede). I primi quattro indirizzi IP di ogni subnet sono riservati e non possono essere usati per l'allocazione IP. Non esiste un concetto di VLAN in un cloud pubblico. Tuttavia, è possibile creare un isolamento all'interno di una rete virtuale in base alle subnet definite.

È possibile creare una subnet di grandi dimensioni che includa tutto lo spazio di indirizzi della rete virtuale oppure scegliere di creare più subnet. Tuttavia, se si usa un gateway di rete virtuale, Azure richiede di creare una subnet con il nome "subnet del gateway". Azure utilizzerà questa subnet per assegnare indirizzi IP ai gateway di rete virtuale. 

## <a name="ip-allocation"></a>Allocazione IP

Quando si assegna un indirizzo IP a un host, si assegna effettivamente IP a una scheda di interfaccia di rete (NIC). È possibile assegnare due tipi di indirizzi IP a una scheda di interfaccia di rete in Azure:

- Indirizzi IP pubblici: usati per comunicare in ingresso e in uscita (senza Network Address Translation (NAT)) con Internet e altre risorse di Azure non connesse a una rete virtuale. L'assegnazione di un indirizzo IP pubblico a un'interfaccia di rete è facoltativa. Gli indirizzi IP pubblici appartengono allo spazio degli indirizzi IP Microsoft.
- Indirizzi IP privati: usati per la comunicazione all'interno di una rete virtuale, nella rete locale e in Internet (con NAT). Lo spazio degli indirizzi IP definito nella rete virtuale viene considerato privato anche se si configura lo spazio degli indirizzi IP pubblici. Microsoft non annuncia questo spazio a Internet. È necessario assegnare almeno un indirizzo IP privato a una VM.

Come per gli host o i dispositivi fisici, è possibile allocare un indirizzo IP a una risorsa dinamica o statica in due modi. In Azure il metodo di allocazione predefinito è dinamico, in cui un indirizzo IP viene allocato quando si crea una macchina virtuale (VM) o si avvia una VM arrestata. L'indirizzo IP viene rilasciato quando si arresta o si elimina la VM. Per far sì che l'indirizzo IP della VM rimanga invariato, è possibile impostare in modo esplicito il metodo di allocazione statico. In questo caso, un indirizzo IP viene assegnato immediatamente. Viene rilasciato solo quando si elimina la VM o si modifica il relativo metodo di allocazione in dinamico. 

Gli indirizzi IP privati vengono allocati dalle subnet definite all'interno di una rete virtuale. Per una macchina virtuale, è possibile scegliere una subnet per l'allocazione IP. Se una macchina virtuale contiene più schede di interfaccia di rete, è possibile scegliere una subnet diversa per ogni scheda di interfaccia di rete.

## <a name="routing"></a>Routing
Quando si crea una rete virtuale, Azure crea una tabella di routing per la rete. Questa tabella di routing contiene i tipi di route seguenti.
- Route di sistema
- Route predefinite subnet
- Route da altre reti virtuali
- Route BGP
- Route endpoint servizio
- Route definite dall'utente (UDR)

Quando si crea una rete virtuale per la prima volta senza definire alcuna subnet, Azure crea le voci di routing nella tabella di routing. Queste route sono denominate route di sistema. Le route di sistema sono definite in questa posizione. Non è possibile modificare queste route. Tuttavia, è possibile eseguire l'override delle route di sistema configurando UdR.

Quando si creano una o più subnet all'interno di una rete virtuale, Azure crea voci predefinite nella tabella di routing per consentire la comunicazione tra le subnet all'interno di una rete virtuale. Queste route possono essere modificate usando route statiche, ovvero route definite dall'utente (UDR) in Azure.

quando si crea un peering di rete virtuale tra due reti virtuali, viene aggiunta una route per ogni intervallo di indirizzi nello spazio degli indirizzi di ogni rete virtuale per la quale viene creato un peering.

Se il gateway di rete locale scambia route con un gateway di rete virtuale di Azure tramite Border Gateway Protocol (BGP), viene aggiunta una route per ogni route propagata dal gateway di rete locale. Queste route vengono visualizzate nella tabella di routing come route BGP.

Azure aggiunge gli indirizzi IP pubblici per alcuni servizi alla tabella di route quando si abilita un endpoint di servizio per il servizio. Gli endpoint di servizio sono abilitati per le singole subnet all'interno di una rete virtuale. Quando si Abilita un endpoint di servizio, la route viene aggiunta solo alla tabella di route di per la subnet che appartiene a questo servizio. Azure gestisce automaticamente gli indirizzi nella tabella di route quando gli indirizzi cambiano.

Le route definite dall'utente sono anche denominate route personalizzate. È possibile creare UDR in Azure per eseguire l'override delle route di sistema predefinite di Azure o per aggiungere altre route alla tabella di route di una subnet. In Azure è possibile creare una tabella di route e quindi associare la tabella di route alle subnet della rete virtuale.

Quando si hanno voci in competizione in una tabella di routing, Azure seleziona l'hop successivo in base alla corrispondenza del prefisso più lungo simile ai router tradizionali. Tuttavia, se sono presenti più voci di hop successivo con lo stesso prefisso di indirizzo, Azure seleziona le route nell'ordine seguente.
1. Route definite dall'utente (UDR)
1. Route BGP
1. Route di sistema

## <a name="security"></a>Sicurezza

È possibile filtrare il traffico di rete da e verso le risorse in una rete virtuale usando gruppi di sicurezza di rete. Si usano anche appliance virtuali di rete, ad esempio firewall di Azure o firewall di altri fornitori. È possibile controllare come Azure instrada il traffico proveniente da subnet. È inoltre possibile limitare gli utenti dell'organizzazione che possono usare le risorse nelle reti virtuali.

Un gruppo di sicurezza di rete (NSG) contiene una serie di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete verso le subnet, le interfacce di rete o entrambe. I gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete connesse a una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole ACL si applicano a tutte le VM in tale subnet. Il traffico verso una singola interfaccia di rete può essere limitato associando un gruppo di sicurezza di rete direttamente a un'interfaccia di rete.

I gruppi di sicurezza di rete includono due tipi di regole: In ingresso e In uscita. La priorità per una regola deve essere univoca all'interno di ogni set. Ogni regola ha proprietà di protocollo, intervalli di porte di origine e destinazione, prefissi degli indirizzi, direzione del traffico, priorità e tipo di accesso. Tutti i gruppi di sicurezza di rete contengono un set di regole predefinite. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente.

## <a name="verification"></a>Verifica
### <a name="routes-in-virtual-network"></a>Route nella rete virtuale
La combinazione di route create dall'utente, route predefinite di Azure e route propagate dalla rete locale tramite un gateway VPN di Azure (se la rete virtuale è connessa alla rete locale) con BGP (Border Gateway Protocol), rappresenta le route valide per tutte le interfacce di rete in una subnet. È possibile visualizzare queste route valide passando a NIC tramite il portale, PowerShell o l'interfaccia della riga di comando.
### <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Le regole di sicurezza applicate a un'interfaccia di rete sono una combinazione tra le regole di sicurezza presenti nel gruppo di sicurezza associato all'interfaccia di rete e quelle della subnet in cui l'interfaccia di rete si trova. È possibile visualizzare tutte le regole di sicurezza effettive da gruppi applicate alle interfacce di rete della macchina virtuale passando alla scheda di interfaccia di rete tramite il portale, PowerShell o l'interfaccia della riga di comando.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla [rete virtuale][VNet].

Informazioni sul [routing di rete virtuale][vnet-routing].

Informazioni sui [gruppi di sicurezza di rete][network-security].

<!--Link References-->
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[vnet-routing]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview
[network-security]: https://docs.microsoft.com/azure/virtual-network/security-overview

