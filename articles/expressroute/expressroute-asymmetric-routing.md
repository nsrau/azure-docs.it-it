<properties
   pageTitle="Routing asimmetrico | Microsoft Azure"
   description="Questo articolo illustra i problemi che un cliente può incontrare con il routing asimmetrico in una rete con più collegamenti a una destinazione."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>  

# Routing asimmetrico con più percorsi di rete

Questo articolo illustra come il traffico inoltrato e quello di ritorno possano seguire route differenti quando sono disponibili più percorsi tra origine e destinazione.

Per comprendere il routing asimmetrico occorre avere chiari due concetti. Uno è l'impatto della presenza di più percorsi di rete. L'altro è il comportamento dei dispositivi che mantengono lo stato, ad esempio i firewall. Questi sono detti dispositivi con stato. Una combinazione di questi due fattori crea scenari in cui il traffico viene eliminato da un dispositivo con stato perché non ha rilevato il traffico originato dal dispositivo stesso.

## Più percorsi di rete

Quando una rete aziendale ha un solo collegamento a Internet tramite il provider di servizi Internet, tutto il traffico da e verso Internet passa per lo stesso percorso. Spesso le aziende acquistano più circuiti, per avere percorsi ridondanti e migliorare i tempi di attività della rete. In questi casi il traffico in uscita dalla rete verso Internet e il traffico di ritorno potrebbero passare per collegamenti diversi. Questo fenomeno è comunemente noto come routing asimmetrico, perché il traffico di ritorno segue un percorso diverso rispetto al flusso originale.

![Routing3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)  

La descrizione precedente si riferisce a Internet, ma è applicabile anche ad altre combinazioni di più percorsi, ad esempio nel caso di un percorso Internet e un percorso privato per la stessa destinazione, di più percorsi privati per la stessa destinazione e così via.

Per calcolare il percorso migliore per raggiungere la destinazione, ogni router lungo il tragitto dall'origine alla destinazione si basa su un calcolo proprio. La determinazione del miglior percorso possibile si basa su due fattori principali.

1.	Il routing tra le reti esterne è basato sul protocollo di routing noto come BGP, che sta per Border Gateway Protocol. Il protocollo BGP riceve annunci dai vicini ed esegue alcuni passaggi per determinare il percorso migliore per la destinazione, che poi installa nella relativa tabella di routing.
2.	La lunghezza della subnet mask associata a una route. Se vengono ricevuti più annunci per lo stesso indirizzo IP ma con subnet mask diverse, viene preferito l'annuncio con la subnet mask più lunga perché è considerata una route più specifica.

## Dispositivi con stato

I router esaminano l'intestazione del pacchetto IP per il routing. Alcuni dispositivi, però, vanno più in profondità all'interno del pacchetto. In genere vengono esaminate le intestazioni fino al livello 4, TCP/UDP, ma a volte questi dispositivi arrivano fino al livello 7, ovvero il livello applicazione. Si tratta di dispositivi di sicurezza o di dispositivi di ottimizzazione della larghezza di banda. Un esempio comune di dispositivo con stato è rappresentato dal firewall. Quest'ultimo consente o nega il passaggio di un pacchetto attraverso le relative interfacce in base a diversi campi quali protocollo, porta TCP/UDP e intestazioni degli URL. Il controllo del pacchetto comporta un carico di elaborazione pesante per il dispositivo. Per migliorare le prestazioni, il firewall controlla il primo pacchetto di un flusso. Se il passaggio del pacchetto è consentito, conserva le informazioni sul flusso nella tabella dello stato. Il passaggio di tutti i pacchetti successivi correlati a questo flusso viene consentito in base alla decisione iniziale. Quando un pacchetto, che fa parte di un flusso esistente, arriva al firewall e questo non ha informazioni sullo stato precedenti, il firewall elimina il pacchetto.

## Routing asimmetrico con ExpressRoute

Quando ci si connette a Microsoft tramite ExpressRoute, nella rete si verificano i cambiamenti seguenti.

1.	Sono presenti più collegamenti a Microsoft. Un collegamento è rappresentato dalla connessione Internet esistente, mentre l'altro avviene tramite ExpressRoute. Parte del traffico diretto a Microsoft può passare per Internet ma tornare tramite ExpressRoute o viceversa.
2.	Tramite ExpressRoute si ricevono indirizzi IP più specifici. Il traffico dalla rete a Microsoft per i servizi offerti tramite ExpressRoute, quindi, preferisce sempre ExpressRoute.

Di seguito vengono proposti alcuni scenari per illustrare l'impatto di questi fattori. Si supponga di avere un solo circuito per Internet e di utilizzare tutti i servizi Microsoft tramite Internet. Il traffico dalla rete a Microsoft e viceversa attraversa lo stesso collegamento Internet e passa per il firewall. Il firewall registra il flusso non appena rileva il primo pacchetto e ai pacchetti di ritorno viene consentito il passaggio perché il flusso è già presente nella tabella dello stato.

![Routing1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)  


A questo punto si abilita ExpressRoute e si utilizzano i servizi offerti da Microsoft tramite ExpressRoute. Tutti gli altri servizi Microsoft vengono utilizzati tramite Internet. Si distribuisce un firewall di confine separato che si connette a ExpressRoute. Tramite ExpressRoute, Microsoft annuncia alla rete prefissi più specifici per servizi specifici. Per tali prefissi l'infrastruttura di routing sceglierà ExpressRoute come percorso preferito. Se gli indirizzi IP pubblici non vengono annunciati a Microsoft tramite ExpressRoute, Microsoft comunica con gli indirizzi IP pubblici tramite Internet. Quindi, il traffico inoltrato dalla rete a Microsoft userà ExpressRoute mentre il traffico di ritorno da Microsoft userà Internet. Quando il firewall di confine rileva un pacchetto di risposta per un flusso non presente nella tabella dello stato, elimina il traffico di ritorno.

Se si sceglie di usare lo stesso pool NAT sia per ExpressRoute che per Internet, si avranno problemi simili con i client negli indirizzi IP privati della rete. La richiesta di servizi come Windows Update passerà per Internet, dato che gli indirizzi IP per questi servizi non vengono annunciati tramite ExpressRoute. Tuttavia, il traffico di ritorno passerà per ExpressRoute. Se Microsoft riceve un indirizzo IP con la stessa subnet mask da Internet e da ExpressRoute, preferisce ExpressRoute a Internet. Se un firewall o un altro dispositivo con stato al confine della rete, connesso a ExpressRoute, non ha informazioni precedenti sul flusso, elimina i pacchetti che appartengono a tale flusso.

## Soluzioni per il routing asimmetrico

Il problema del routing asimmetrico può essere risolto principalmente in due modi. Uno tramite il routing e l'altro tramite Source NAT (SNAT).

1. Routing

    - È necessario assicurarsi che gli indirizzi IP pubblici vengano annunciati ai collegamenti WAN appropriati. Ad esempio, se si vuole usare Internet per il traffico di autenticazione ed ExpressRoute per il traffico di posta elettronica, gli indirizzi IP pubblici ADFS non devono essere annunciati tramite ExpressRoute. Analogamente, il server ADFS locale non deve essere esposto agli indirizzi IP ricevuti tramite ExpressRoute. Le route ricevute tramite ExpressRoute sono più specifiche. Questo fa di ExpressRoute il percorso preferito per il traffico di autenticazione diretto a Microsoft dando luogo al routing asimmetrico.

    - Per usare ExpressRoute per l'autenticazione, è necessario assicurarsi che gli indirizzi IP pubblici ADFS vengano annunciati tramite ExpressRoute senza NAT. In questo modo il traffico proveniente da Microsoft e diretto al server ADFS locale passerà per ExpressRoute mentre il traffico di ritorno dal cliente a Microsoft preferirà ExpressRoute a Internet.

2. Source NAT

	Un altro metodo per risolvere i problemi di routing asimmetrico è il Source NAT (SNAT). Si supponga, ad esempio, di non aver annunciato l'indirizzo IP pubblico del server SMTP locale tramite ExpressRoute perché si intende usare Internet per questa comunicazione. Una richiesta originata da Microsoft e diretta al server SMTP locale attraversa Internet. Tramite il Source NAT la richiesta in ingresso viene inviata a un indirizzo IP interno. Il traffico di ritorno dal server SMTP passerà per il firewall di confine usato per il NAT, anziché attraverso ExpressRoute. In questo modo il traffico di ritorno passerà per Internet.


![Routing2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)  

## Rilevamento del routing asimmetrico

Il modo migliore per verificare che il traffico attraversi il percorso previsto è il comando traceroute. Se il traffico dal server SMTP locale a Microsoft deve passare per Internet, eseguire il comando traceroute dal server SMTP a Office 365. Il risultato permetterà di verificare che il traffico in uscita dalla rete sia effettivamente diretto verso Internet e non verso ExpressRoute.

<!---HONumber=AcomDC_0824_2016-->