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
   ms.date="10/10/2016"
   ms.author="osamazia"/>


# <a name="asymmetric-routing-with-multiple-network-paths"></a>Routing asimmetrico con più percorsi di rete

Questo articolo illustra il modo in cui il traffico di rete di inoltro e di ritorno può seguire route diverse quando sono disponibili più percorsi tra l'origine e la destinazione di rete.

Per comprendere il routing asimmetrico è importante comprendere due concetti, ovvero l'effetto della presenza di più percorsi di rete e il modo in cui i dispositivi, ad esempio un firewall, mantengono lo stato. Questi tipi di dispositivi sono detti dispositivi con stato. Una combinazione di questi due fattori crea scenari in cui il traffico di rete viene rilasciato da un dispositivo con stato perché il dispositivo con stato non ha rilevato il traffico originato dal dispositivo stesso.

## <a name="multiple-network-paths"></a>Più percorsi di rete

Quando una rete aziendale ha un solo collegamento a Internet tramite il provider di servizi Internet, tutto il traffico da e verso Internet passa per lo stesso percorso. Spesso le aziende acquistano più circuiti, per avere percorsi ridondanti e migliorare i tempi di attività della rete. In questo caso è possibile che il traffico in uscita dalla rete, verso Internet, passi attraverso un collegamento e il traffico di ritorno passi attraverso un collegamento diverso. Questa approccio viene definito comunemente routing asimmetrico. Nel routing asimmetrico il traffico di rete segue un percorso diverso rispetto al flusso originale.

![Rete con più percorsi](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Anche se si verifica principalmente su Internet, il routing asimmetrico è applicabile anche ad altre combinazioni di percorsi multipli. È ad esempio applicabile a un percorso Internet e un percorso privato per la stessa destinazione e a più percorsi privati per la stessa destinazione.

Ogni router lungo il percorso, dall'origine alla destinazione, calcola il percorso migliore per raggiungere una destinazione. La determinazione del miglior percorso possibile da parte del router si basa su due fattori principali:

-   Il routing tra le reti esterne è basato sul protocollo di routing, ovvero il protocollo BGP (Border Gateway Protocol). Il protocollo BGP riceve annunci dai vicini e li sottopone ad alcune procedure per determinare il percorso migliore per la destinazione specificata, quindi archivia il percorso migliore nella tabella di routing.
-   La lunghezza di una subnet mask associata a una route influenza i percorsi di routing. Se un router riceve più annunci per lo stesso indirizzo IP ma con subnet mask diverse, il router attribuisce la priorità all'annuncio con una subnet mask più lunga perché viene considerata una route più specifica.

## <a name="stateful-devices"></a>Dispositivi con stato

I router esaminano l'intestazione di un pacchetto IP per il routing. Alcuni dispositivi analizzano i pacchetti in modo più approfondito. Questi dispositivi esaminano in genere le intestazioni di tipo Layer4, ovvero TCP (Transmission Control Protocol) o UDP (User Datagram Protocol) oppure addirittura di tipo Layer7 (livello dell'applicazione). Si tratta di dispositivi di sicurezza o di dispositivi di ottimizzazione della larghezza di banda. 

Un esempio comune di dispositivo con stato è rappresentato dal firewall. Quest'ultimo consente o nega il passaggio di un pacchetto attraverso le relative interfacce in base a diversi campi quali protocollo, porta TCP/UDP e intestazioni degli URL. Questo livello di controllo del pacchetto comporta un carico di elaborazione elevato per il dispositivo. Per migliorare le prestazioni, il firewall controlla il primo pacchetto di un flusso. Se il passaggio del pacchetto è consentito, conserva le informazioni sul flusso nella tabella dello stato. Il passaggio di tutti i pacchetti successivi correlati a questo flusso viene consentito in base alla valutazione iniziale. È possibile che un pacchetto incluso in un flusso esistente arrivi al firewall. Se non ha informazioni precedenti sullo stato del pacchetto, il firewall rilascia il pacchetto.

## <a name="asymmetric-routing-with-expressroute"></a>Routing asimmetrico con ExpressRoute

Quando ci si connette a Microsoft tramite Azure ExpressRoute, la rete subisce le modifiche seguenti:

-   Sono presenti più collegamenti a Microsoft. Un collegamento è rappresentato dalla connessione Internet esistente, mentre l'altro avviene tramite ExpressRoute. Parte del traffico diretto a Microsoft può passare per Internet ma tornare tramite ExpressRoute o viceversa.
-   Tramite ExpressRoute si ricevono indirizzi IP più specifici. Per il traffico dalla rete a Microsoft per i servizi offerti tramite ExpressRoute, quindi, i router preferiscono sempre ExpressRoute.

Per comprendere l'effetto di queste due modifiche sulla rete, è possibile esaminare alcuni scenari. Si supponga ad esempio di avere un solo circuito per Internet e di utilizzare tutti i servizi Microsoft tramite Internet. Il traffico dalla rete a Microsoft e viceversa attraversa lo stesso collegamento Internet e passa per il firewall. Il firewall registra il flusso non appena rileva il primo pacchetto e ai pacchetti di ritorno viene consentito il passaggio perché il flusso è già presente nella tabella dello stato.

![Routing asimmetrico con ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


A questo punto si abilita ExpressRoute e si utilizzano i servizi offerti da Microsoft tramite ExpressRoute. Tutti gli altri servizi Microsoft vengono utilizzati tramite Internet. Si distribuisce un firewall di confine separato connesso a ExpressRoute. Tramite ExpressRoute, Microsoft annuncia alla rete prefissi più specifici per servizi specifici. Per tali prefissi l'infrastruttura di routing sceglie ExpressRoute come percorso preferito. Se gli indirizzi IP pubblici non vengono annunciati a Microsoft tramite ExpressRoute, Microsoft comunica con gli indirizzi IP pubblici tramite Internet. Il traffico inoltrato dalla rete a Microsoft usa ExpressRoute e il traffico di ritorno da Microsoft usa Internet. Quando il firewall di confine rileva un pacchetto di risposta per un flusso non presente nella tabella dello stato, elimina il traffico di ritorno.

Se si sceglie di usare lo stesso pool NAT (Network Address Translation) sia per ExpressRoute che per Internet, si verificano problemi simili con i client negli indirizzi IP privati della rete. La richiesta di servizi come Windows Update passa per Internet, poiché gli indirizzi IP per questi servizi non vengono annunciati tramite ExpressRoute. Tuttavia, il traffico di ritorno passa per ExpressRoute. Se Microsoft riceve un indirizzo IP con la stessa subnet mask da Internet e da ExpressRoute, preferisce ExpressRoute a Internet. Se un firewall o un altro dispositivo con stato al confine della rete e connesso a ExpressRoute non ha informazioni precedenti sul flusso, elimina i pacchetti che appartengono a tale flusso.

## <a name="asymmetric-routing-solutions"></a>Soluzioni per il routing asimmetrico

Sono disponibili due opzioni principali per risolvere il problema del routing asimmetrico, ovvero il routing e l'uso di Source NAT.

### <a name="routing"></a>Routing

Assicurarsi che gli indirizzi IP pubblici vengano annunciati ai collegamenti WAN (Wide Area Network) appropriati. Se ad esempio si vuole usare Internet per il traffico di autenticazione ed ExpressRoute per il traffico di posta elettronica, è necessario non annunciare gli indirizzi IP pubblici di Active Directory Federation Services (AD FS) tramite ExpressRoute. È analogamente necessario non esporre alcun server AD FS locale per gli indirizzi IP ricevuti dal router tramite ExpressRoute. Le route ricevute tramite ExpressRoute sono più specifiche. Questo fa di ExpressRoute il percorso preferito per il traffico di autenticazione diretto a Microsoft, dando luogo al routing asimmetrico.

Per usare ExpressRoute per l'autenticazione, è necessario assicurarsi che gli indirizzi IP pubblici AD FS vengano annunciati tramite ExpressRoute senza NAT. In questo modo il traffico proveniente da Microsoft e diretto a un server AD FS locale passa attraverso ExpressRoute. Il traffico di ritorno dal cliente a Microsoft usa ExpressRoute perché questa è la route predefinita su Internet.

### <a name="source-based-nat"></a>Source NAT

È possibile risolvere i problemi del routing asimmetrico anche tramite SNAT. Si supponga ad esempio che l'indirizzo IP pubblico di un server SMTP (Simple Mail Transfer Protocol) locale non sia stato annunciato tramite ExpressRoute perché si intende usare Internet per questo tipo di comunicazioni. Una richiesta originata da Microsoft e diretta al server SMTP locale attraversa Internet. Tramite SNAT la richiesta in ingresso viene inviata a un indirizzo IP interno. Il traffico inverso dal server SMTP viene indirizzato al firewall di confine, usato per NAT, invece che a ExpressRoute. Il traffico di ritorno viene restituito tramite Internet.


![Configurazione della rete con Source NAT](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Rilevamento del routing asimmetrico

Il modo migliore per assicurarsi che il traffico attraversi il percorso previsto è il comando traceroute. Se il traffico dal server SMTP locale a Microsoft deve passare per Internet, eseguire il comando traceroute dal server SMTP a Office 365. Il risultato permette di verificare che il traffico in uscita dalla rete sia effettivamente diretto verso Internet e non verso ExpressRoute.



<!--HONumber=Oct16_HO2-->


