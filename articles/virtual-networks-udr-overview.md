<properties 
   pageTitle="Definito dall&#39;utente IP Cenni preliminari sull&#39;inoltro e route"
   description="Route (UDR) definito dall&#39;utente e Cenni preliminari sull&#39;inoltro IP"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Route e inoltro IP, definito dall'utente
Azure utilizza una tabella di route per decidere come inoltrare traffico IP in base alla destinazione di ogni pacchetto. Sebbene Azure offra una tabella di route predefinita in base alle impostazioni di rete virtuale, potrebbe essere necessario aggiungere route personalizzati a tale tabella. I più comuni necessari per una voce nella tabella della route personalizzata sono l'utilizzo di un dispositivo virtuale nell'ambiente Azure. Prendere in considerazione lo scenario illustrato nella figura riportata di seguito. Si supponga che si desidera garantire che tutto il traffico diretto a livello intermedio e sottoposti a subnet avviate dalla subnet front-end passano attraverso un accessorio virtuale firewall. Semplicemente aggiungendo l'accessorio alla rete virtuale e la connessione a subnet diverse non fornisce questa funzionalità. È inoltre necessario modificare la tabella di routing applicata alla subnet per garantire che i pacchetti vengono inoltrati al dispositivo virtuale firewall.

Lo stesso sarà true se è stato implementato un dispositivo NAT virtuale per controllare il traffico tra la rete virtuale di Azure e Internet. Per assicurarsi di utilizzata il dispositivo virtuale, è necessario creare una route specifica che tutto il traffico destinato a Internet deve essere inoltrato per il dispositivo virtuale.

## Routing
I pacchetti vengono inoltrati attraverso una rete TCP/IP basata su una tabella di route definita in ogni nodo nella rete fisica. Una tabella di route è un insieme di route singoli utilizzata per decidere dove inoltrare pacchetti in base a indirizzo IP di destinazione. Una route è costituita dai seguenti elementi:

- **Prefisso dell'indirizzo IP**. CIDR di destinazione a cui viene applicata la route, ad esempio 10.1.0.0/16.
- **Tipo hop successivo**. Il tipo di hop Azure il pacchetto deve essere inviato. I valori possibili sono:
	- **Locale**. Rappresenta la rete virtuale locale. Ad esempio, se si dispone di due subnet, 10.1.0.0/16 e 10.2.0.0/16 nella stessa rete virtuale, la route per ogni subnet nella tabella della route avrà un valore di hop successivo di *locale*.
	- **Gateway VPN**. Rappresenta un Gateway VPN S2S Azure. 
	- **Internet**. Rappresenta il gateway Internet predefinito fornito dall'infrastruttura di Azure 
	- **Dispositivo virtuale**. Rappresenta un dispositivo virtuale che aggiunto alla rete virtuale Azure.
	- **NULL**. Rappresenta un black hole. I pacchetti inoltrati a un black hole non verranno inoltrati affatto.
- **Valore Nexthop**. Il valore di hop successivo contiene l'indirizzo IP per inoltrare i pacchetti. I valori di hop successivo sono consentiti solo nelle route dove il tipo di hop successivo è *dispositivo virtuale*.

## Route predefinite
Ogni subnet creata in una rete virtuale viene associata automaticamente a una tabella di route che contiene le regole di route predefinite seguenti:- **regola di rete virtuale locale**: questa regola viene creata automaticamente per ogni subnet in una rete virtuale. Specifica che non vi è un collegamento diretto tra le macchine virtuali nella rete virtuale e non esiste alcun intermedia hop successivo. - **Locale regola**: questa regola si applica a tutto il traffico destinato all'intervallo di indirizzi locali e utilizza il gateway VPN come il successivo hop di destinazione. - **regola Internet**: questa regola gestisce tutto il traffico destinato a Internet pubblico e utilizza il gateway internet di infrastruttura come hop successivo per tutto il traffico destinato a Internet.

## Route BGP
Se si dispone di una connessione ExpressRoute tra la rete locale e Azure, è possibile abilitare BGP propagare route dalla rete locale in Azure. Queste route BGP vengono utilizzate nello stesso modo come route predefinite e le route definite dall'utente in ogni subnet di Azure. Per ulteriori informazioni vedere [ExpressRoute Introduzione](../expressroute-introduction).

[AZURE.IMPORTANT]È possibile configurare l'ambiente Azure per utilizzare il tunneling forzato attraverso la rete locale tramite la creazione di una route definita utente per 0.0.0.0/0 subnet che utilizza il gateway VPN come hop successivo. Tuttavia, funziona solo se si utilizza un gateway VPN, non ExpressRoute. Per ExpressRoute, il tunneling forzato viene configurato tramite BGP.

## Route definite dall'utente
Non è possibile visualizzare le route predefinite specificate in precedenza nell'ambiente Azure e per la maggior parte degli ambienti, questi sono solo le route che è necessario. Tuttavia, è necessario creare una tabella di route e aggiungere una o più route in casi specifici, ad esempio:

- Il tunneling forzato a Internet tramite la rete locale.
- Utilizzo di dispositivi virtuali nell'ambiente Azure.
- 
Negli scenari precedenti, è necessario creare una tabella di route e aggiungervi route definite dall'utente. È possibile avere più tabelle di routing e la stessa tabella di route può essere associata a una o più subnet. E ogni subnet può essere associato a una tabella singola route. Tutte le macchine virtuali e servizi cloud in uso una subnet la tabella di route associato a tale subnet.

Subnet si basano su route predefinite fino a quando non è associata alla subnet di una tabella di route. Una volta esiste un'associazione, routing viene eseguito in base su più lunga del prefisso corrispondenza \(LPM\) tra le route definite dall'utente e route predefinite. Se è presente più di una route con la stessa corrispondenza LPM una route viene selezionata in base alla sua origine nell'ordine seguente:

1. Route definita utente
1. Route BGP (quando viene utilizzato ExpressRoute)
1. Route predefinita

[AZURE.IMPORTANT]Route definite dall'utente vengono applicate solo a servizi cloud e macchine virtuali di Azure. Ad esempio, se si desidera aggiungere un dispositivo virtuale firewall tra la rete locale e Azure, è necessario creare una route definita dall'utente per le tabelle di Azure route che inoltra tutto il traffico verso lo spazio degli indirizzi locale per il dispositivo virtuale. Tuttavia, il traffico in ingresso dallo spazio degli indirizzi locale flusso attraverso il gateway VPN o il circuito ExpressRoute direttamente all'ambiente di Azure, ignorando il dispositivo virtuale.

## IP Forwarding
Come descritto sopra, uno dei motivi principali per creare una route definita utente è di inoltrare il traffico a un dispositivo virtuale. Un dispositivo virtuale non è altro che una macchina virtuale che esegue un'applicazione utilizzata per gestire il traffico di rete in qualche modo, ad esempio un firewall o un dispositivo NAT.

Questo dispositivo virtuale macchina virtuale deve essere in grado di ricevere traffico in ingresso non viene indirizzato a se stesso. Per consentire una macchina virtuale ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare l'inoltro IP nella macchina virtuale.

## Vedere anche

[Come creare route e attivare l'inoltro IP in Azure](../virtual-networks-udr-how-to)

[Indirizzi IP pubblici a livello di istanza](../virtual-networks-instance-level-public-ip)

[Panoramica di Rete virtuale](https://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=52-->
