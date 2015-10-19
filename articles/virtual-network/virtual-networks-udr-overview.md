<properties 
   pageTitle="Definito dall'utente IP Cenni preliminari sull'inoltro e route"
   description="Comprendere UDR e inoltro IP"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/04/2015"
   ms.author="telmos" />

# Route e inoltro IP, definito dall'utente
Quando si aggiungono macchine virtuali (VM) a una rete virtuale (VNet) in Azure, si noterà che le macchine virtuali sono in grado di comunicare con altri in rete, automaticamente. Non è necessario specificare un gateway, anche se le macchine virtuali si trovano in subnet diverse. Lo stesso vale per la comunicazione tra macchine virtuali e rete Internet pubblica e anche rete locale se è presente una connessione ibrida tra Azure e il proprio Data Center.

Questo flusso di comunicazione è possibile perché Azure utilizza una serie di route di sistema per definire il flusso di traffico IP. I route di sistema controllano il flusso delle comunicazioni negli scenari seguenti:

- All'interno della stessa subnet.
- Da una subnet a altra all'interno di una rete virtuale.
- Da macchine virtuali a Internet.
- Da una rete virtuale a un'altra rete virtuale tramite un gateway VPN.
- Da una rete virtuale alla rete locale tramite un gateway VPN.

Nella figura seguente viene illustrato un semplice programma di installazione con una rete virtuale, due subnet e alcune macchine virtuali, con le route di sistema che consentono il flusso del traffico IP.

![Route di sistema in Azure](./media/virtual-networks-udr-overview/Figure1.png)

Sebbene l'utilizzo di route del sistema faciliti il traffico automaticamente per la distribuzione, esistono casi in cui si desidera controllare il routing dei pacchetti tramite un dispositivo virtuale. Questo è possibile tramite la creazione di route definite dall'utente che specifichino l'hop successivo per i pacchetti che passano a una subnet specifica per accedere all'applicazione virtuale e tramite l’attivazione dell'inoltro IP per la macchina virtuale in esecuzione come dispositivo virtuale.

Nella figura seguente viene illustrato un esempio di route definite dall’utente e di inoltro IP per forzare i pacchetti che passano da una subnet front-end a Internet per passare attraverso un dispositivo virtuale e tutti i pacchetti che vanno dalla subnet front-end alla subnet back-end per passare attraverso un dispositivo diverso. Si noti che il traffico dalla subnet di back-end alla subnet front-end sta ancora attraversando la route di sistema, oltrepassando il dispositivo.

![Route di sistema in Azure](./media/virtual-networks-udr-overview/Figure2.png)

## Routing.
I pacchetti vengono inoltrati attraverso una rete TCP/IP basata su una tabella di route definita in ogni nodo nella rete fisica. Una tabella di route è un insieme di route singoli utilizzata per decidere dove inoltrare pacchetti in base a indirizzo IP di destinazione. Una route è costituita dai seguenti elementi:

- **Prefisso dell'indirizzo IP**. CIDR di destinazione a cui viene applicata la route, ad esempio 10.1.0.0/16.
- **Tipo hop successivo**. Il tipo di hop Azure il pacchetto deve essere inviato. I valori possibili sono:
	- **Locale**. Rappresenta la rete virtuale locale. Ad esempio, se si dispone di due subnet, 10.1.0.0/16 e 10.2.0.0/16 nella stessa rete virtuale, la route per ogni subnet nella tabella della route avrà un valore di hop successivo di *locale*.
	- **Gateway VPN**. Rappresenta un Gateway VPN S2S Azure. 
	- **Internet**. Rappresenta il gateway Internet predefinito fornito dall'infrastruttura di Azure 
	- **Dispositivo virtuale**. Rappresenta un dispositivo virtuale che aggiunto alla rete virtuale Azure.
	- **NULL**. Rappresenta un black hole. I pacchetti inoltrati a un black hole non verranno inoltrati affatto.
- **Valore Nexthop**. Il valore di hop successivo contiene l'indirizzo IP per inoltrare i pacchetti. I valori di hop successivo sono consentiti solo nelle route dove il tipo di hop successivo è *dispositivo virtuale*.

## Le route di sistema
Ogni subnet creata in una rete virtuale viene associata automaticamente a una tabella di route che contiene le seguenti regole di route di sistema:

- **Regola di rete virtuale locale**: questa regola viene creata automaticamente per ogni subnet in una rete virtuale. Specifica che vi è un collegamento diretto tra le macchine virtuali nella rete virtuale e che non esiste alcun hop successivo intermediario.
- **Regola Locale**: questa regola si applica a tutto il traffico destinato ad un intervallo di indirizzi locali e utilizza il gateway VPN come destinazione hop successiva.
- **Regola Internet**: questa regola gestisce tutto il traffico destinato all’Internet pubblico e utilizza il gateway internet di infrastruttura come hop successivo per tutto il traffico destinato a Internet.

## Route definite dall'utente
Per la maggior parte degli ambienti saranno necessarie solo le route di sistema già definite da Azure. Tuttavia, è necessario creare una tabella di route e aggiungere una o più route in casi specifici, ad esempio:

- Il tunneling forzato a Internet tramite la rete locale.
- Utilizzo di dispositivi virtuali nell'ambiente Azure.

Negli scenari precedenti, è necessario creare una tabella di route e aggiungervi route definite dall'utente. È possibile avere più tabelle di routing e la stessa tabella di route può essere associata a una o più subnet. E ogni subnet può essere associato a una tabella singola route. Tutte le macchine virtuali e servizi cloud in uso una subnet la tabella di route associato a tale subnet.

Le subnet si basano su route predefinite fino a quando una tabella di route viene associata alla subnet. Una volta che esiste un’associazione,il routing viene eseguito in base alla corrispondenza più lunga del prefisso (LPM) tra le route definite dall'utente e le route predefinite. Se è presente più di una route con la stessa corrispondenza LPM una route viene selezionata in base alla sua origine nell'ordine seguente:

1. Route definita utente
1. Route BGP (quando viene utilizzato ExpressRoute)
1. La route di sistema

Per informazioni su come creare route definite dall'utente, vedere[come creare route e abilitare l'inoltro dell'IP in Azure](../virtual-networks-udr-how-to#How-to-manage-routes).

>[AZURE.IMPORTANT]Route definite dall'utente vengono applicate solo a servizi cloud e macchine virtuali di Azure. Ad esempio, se si desidera aggiungere un dispositivo virtuale firewall tra la rete locale e Azure, è necessario creare una route definita dall'utente per le tabelle di Azure route che inoltra tutto il traffico verso lo spazio degli indirizzi locale per il dispositivo virtuale. Tuttavia, il traffico in ingresso dallo spazio degli indirizzi locale flusso attraverso il gateway VPN o il circuito ExpressRoute direttamente all'ambiente di Azure, ignorando il dispositivo virtuale.

## Route BGP
Se si dispone di una connessione ExpressRoute tra la rete locale e Azure, è possibile abilitare BGP propagare route dalla rete locale in Azure. Queste route BGP vengono utilizzate nello stesso modo delle route predefinite e delle route definite dall'utente in ogni subnet di Azure. Per ulteriori informazioni vedere [ExpressRoute Introduzione](../expressroute-introduction).

>[AZURE.IMPORTANT]È possibile configurare l'ambiente Azure per utilizzare il tunneling forzato attraverso la rete locale tramite la creazione di una route definita utente per 0.0.0.0/0 subnet che utilizza il gateway VPN come hop successivo. Tuttavia, funziona solo se si utilizza un gateway VPN, non ExpressRoute. Per ExpressRoute, il tunneling forzato viene configurato tramite BGP.

## IP Forwarding
Come descritto sopra, uno dei motivi principali per creare una route definita utente è di inoltrare il traffico a un dispositivo virtuale. Un dispositivo virtuale non è altro che una macchina virtuale che esegue un'applicazione utilizzata per gestire il traffico di rete in qualche modo, ad esempio un firewall o un dispositivo NAT.

Questo dispositivo virtuale macchina virtuale deve essere in grado di ricevere traffico in ingresso non viene indirizzato a se stesso. Per consentire a una macchina virtuale di ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare l'inoltro IP per la macchina virtuale. Si tratta di un'impostazione di Azure e non del sistema operativo guest.

Per informazioni su come abilitare l’inoltro IP per una macchina virtuale in Azure, vedere[come creare route e abilitare l'inoltro dell'IP in Azure](../virtual-networks-udr-how-to#How-to-Manage-IP-Forwarding).

## Passaggi successivi

- Informazioni su come [creare route nel modello di distribuzione di Gestione risorse](../virtual-network-create-udr-arm-template) e associarle alle subnet. 
- Informazioni su come [creare route nel modello di distribuzione classica](../virtual-network-create-udr-classic-ps) e associarle a subnet.

<!---HONumber=Oct15_HO2-->