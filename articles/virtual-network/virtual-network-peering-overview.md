<properties
   pageTitle="Peering reti virtuali di Azure | Microsoft Azure"
   description="Informazioni sul peering reti virtuali in Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="narayanannamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# Peering reti virtuali

Peering reti virtuale è un meccanismo che consente di connettere due reti virtuali nella stessa area tramite la rete backbone di Azure. Una volta eseguito il peering, le due reti virtuali appariranno come una sola per qualsiasi scopo di connettività. Continueranno a essere gestite come risorse separate, ma le macchine virtuali in queste reti virtuali potranno comunicare direttamente tra di esse usando l'indirizzo IP privato. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato tramite l'infrastruttura di Azure, in modo analogo al traffico tra le macchine virtuali nella stessa rete virtuale. Ecco alcuni dei vantaggi offerti dal peering reti virtuali:

- Bassa latenza, connessione a larghezza di banda elevata tra le risorse in reti virtuali diverse.
- Possibilità di usare le risorse, ad esempio le appliance di rete virtuale, e i gateway VPN nella rete virtuale con peering (transito).
- Connessione di una rete virtuale di Resource Manager a una rete virtuale classica e abilitazione della connettività completa tra le risorse in queste reti virtuali.

Requisiti e aspetti principali del peering reti virtuali:

- Le due reti virtuali con peering devono trovarsi nella stessa area di Azure.
- Le reti virtuali con peering non devono avere spazi di indirizzi IP sovrapposti.
- Il peering reti virtuali viene eseguito tra due reti virtuali senza alcuna relazione transitiva derivata. Ad esempio, se per la rete virtuale A è stato eseguito il peering con la rete virtuale B e se per quest'ultima è stato eseguito il peering con la rete virtuale C, non viene stabilito automaticamente il peering tra la rete virtuale A e la rete virtuale C.
- Il peering può essere stabilito tra reti virtuali in due diverse sottoscrizioni, a condizione che un utente con privilegi nelle rispettive sottoscrizioni autorizzi il peering.
- È possibile eseguire il peering di una rete virtuale di Resource Manager con un'altra rete virtuale di Resource Manager o una rete virtuale classica, ma non tra due reti virtuali classiche.
- Anche se la comunicazione tra le macchine virtuali nelle reti virtuali con peering non presenta restrizioni in termini di larghezza di banda aggiuntiva, verrà comunque applicato il limite di larghezza di banda in base alle dimensioni della macchina virtuale.


![Peering reti virtuali di base](./media/virtual-networks-peering-overview/figure01.png)

## Connettività 
Una volta eseguito il peering tra due reti virtuali, una macchina virtuale (con ruolo di lavoro o Web) nella rete virtuale può connettersi direttamente ad altre macchine virtuali nella rete virtuale con peering e tutte avranno la connettività completa a livello IP. La latenza di rete per il round trip tra due macchine virtuali in reti virtuali con peering sarà identica a quella interna alla rete virtuale locale. La velocità effettiva della rete si baserà sulla larghezza di banda consentita per la macchina virtuale proporzionalmente alle relative dimensioni, senza altre restrizioni alla larghezza di banda consentita. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato direttamente tramite l'infrastruttura di back-end di Azure, non tramite un gateway.

Le macchine virtuali in una rete virtuale potranno accedere agli endpoint con carico bilanciato interno (ILB) nella rete virtuale con peering. È possibile applicare gruppi di sicurezza di rete a una delle reti virtuali per bloccare l'accesso all'altra rete virtuale o subnet, se necessario. Quando l'utente configura il peering, può scegliere di aprire o chiudere le regole del gruppo di sicurezza di rete tra le reti virtuali. Se l'utente sceglie di aprire la connettività completa tra le reti virtuali con peering (opzione predefinita), potrà usare i gruppi di sicurezza di rete in subnet o macchine virtuali specifiche per bloccare o negare l'accesso in modo specifico.

La risoluzione dei nomi DNS interni fornita da Azure per le macchine virtuali non funzionerà tra le reti virtuali con peering. Le macchine virtuali avranno nomi DNS interni che possono essere risolti solo all'interno della rete virtuale locale. Gli utenti possono tuttavia configurare le macchine virtuali in esecuzione in reti virtuali con peering come server DNS per una rete virtuale.

## Concatenamento dei servizi
Gli utenti possono configurare tabelle di route definite dall'utente che puntano alle macchine virtuali nelle reti virtuali con peering come hop successivo, come illustrato nel diagramma seguente. Ciò consente agli utenti di ottenere il concatenamento dei servizi mediante il quale possono indirizzare il traffico da una rete virtuale a un'appliance virtuale in esecuzione in una rete virtuale con peering, usando le tabelle di route definite dall'utente. Gli utenti possono anche creare in modo efficace ambienti di tipo hub e spoke in cui l'hub può ospitare componenti dell'infrastruttura, come le appliance di rete virtuale, e tutte le reti virtuali spoke possono eseguire il peering con tali componenti e indirizzare un subset del traffico alle appliance in esecuzione nella rete virtuale dell'hub. In breve, il peering reti virtuali consente di usare l'indirizzo IP di una macchina virtuale, nella rete virtuale con peering, come indirizzo IP dell'hop successivo nella "tabella di route definita dall'utente"'.

## Gateway e connettività locale
Ogni rete virtuale, che ne sia stato eseguito o meno il peering con un'altra rete virtuale, può comunque avere un proprio gateway e usarlo per la connessione locale. Gli utenti possono anche configurare la connessione da rete virtuale a rete virtuale (specificare il collegamento) tramite gateway, anche se è stato eseguito il peering delle reti virtuali. Quando vengono configurate entrambe le opzioni per la connettività tra reti virtuali, il flusso di traffico tra di esse seguirà la configurazione del peering, ad esempio tramite il backbone di Azure.

Quando è stato eseguito il peering delle reti virtuali, gli utenti possono anche configurare l'uso del gateway nella rete virtuale con peering come punto di transito all'ambiente locale. In questo caso, la rete virtuale che usa un gateway remoto non può avere un proprio gateway. Per semplificare, una rete virtuale può avere un solo gateway, che può essere locale o remoto (nella rete virtuale con peering), come illustrato nell'immagine seguente. Il transito gateway non è supportato tra una rete virtuale di Resource Manager e una rete virtuale classica. Per il funzionamento del transito gateway, è necessario che entrambe le reti virtuali nella relazione di peering siano reti virtuali di Resource Manager. Quando viene eseguito il peering di reti virtuali che condividono un singolo circuito ER, il traffico tra di esse passerà attraverso la relazione di peering, ad esempio tramite la rete backbone di Azure. Gli utenti possono continuare a usare i gateway locali in ogni rete virtuale per connettersi al circuito locale o usare un gateway condiviso e configurare il transito per la connettività locale.

![Peering reti virtuali con transito](./media/virtual-networks-peering-overview/figure02.png)

## Provisioning
Il peering reti virtuali è un'operazione con privilegi. È una funzione separata nello spazio dei nomi della rete virtuale. È possibile assegnare a un utente diritti specifici per autorizzare il peering. Un utente con accesso in lettura e scrittura nella rete virtuale erediterà questi diritti automaticamente. Un utente amministratore o con privilegi per la funzionalità di peering può avviare un'operazione di peering in un'altra rete virtuale. Se è presente una richiesta di peering corrispondente sull'altro lato e vengono soddisfatti altri requisiti, il peering verrà stabilito.

Per altre informazioni su come stabilire il peering reti virtuali tra due reti virtuali, vedere gli articoli sulle procedure.

## Limiti
Esistono limiti al numero di peering consentiti per una singola rete virtuale. Per altre informazioni, vedere la sezione [Limiti relativi alla rete](../azure-subscription-service-limits.md#networking-limits).

## Prezzi
Non è previsto l'addebito di costi per il peering reti virtuali durante il periodo di verifica. Una volta rilasciato e disponibile a livello generale, al traffico in ingresso e in uscita che usa il peering verrà applicata una tariffa nominale. Per altre informazioni, vedere la pagina [Prezzi di Rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network).


## Passaggi successivi
- [Configurare il peering tra reti virtuali](virtual-networks-create-vnetpeering-arm-portal.md).
- Informazioni sui [gruppi di sicurezza di rete](virtual-networks-nsg.md).
- Informazioni su [route e inoltro IP definiti dall'utente](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0803_2016-->