<properties
   pageTitle="Esempio di rete perimetrale: Creare una rete perimetrale per proteggere le reti con un firewall, routing definito dall'utente e un gruppo di sicurezza di rete | Microsoft Azure"
   description="Creare una rete perimetrale con un firewall, routing definito dall'utente e un gruppo di sicurezza di rete"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, routing definito dall'utente e un gruppo di sicurezza di rete

[Tornare alla pagina relativa alle procedure consigliate sui limiti di sicurezza][HOME]

Questo esempio illustra come creare una rete perimetrale con un firewall, quattro server Windows, routing definito dall'utente, inoltro IP e gruppi di sicurezza di rete. Illustra in dettaglio anche ogni comando rilevante per favorire una comprensione più approfondita di ogni passaggio. È disponibile anche una sezione sugli scenari di traffico con istruzioni dettagliate sul percorso seguito dal traffico attraverso i livelli di difesa della rete perimetrale. La sezione Riferimenti, infine, include tutto il codice e istruzioni complete per creare l'ambiente per testare e sperimentare vari scenari.

![Rete perimetrale bidirezionale con dispositivo virtuale di rete, gruppo di sicurezza di rete e routing definito dall'utente][1]

## Configurazione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

- Tre servizi cloud, "SecSvc001", "FrontEnd001" e "BackEnd001".
- Una rete virtuale, "CorpNetwork", con tre subnet: "SecNet", "FrontEnd" e "BackEnd"
- Un dispositivo virtuale di rete, in questo esempio un firewall, connesso alla subnet SecNet.
- Un server Windows che rappresenta un server Web applicazioni ("IIS01").
- Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
- Un server Windows che rappresenta un server DNS ("DNS01")

Nella sezione Riferimenti alla fine dell'articolo è disponibile uno script di PowerShell per creare la maggior parte dell'ambiente descritto sopra. La creazione di macchine virtuali e reti virtuali, anche se eseguita dallo script di esempio, non è descritta in dettaglio in questo documento.

Per creare l'ambiente, eseguire queste operazioni:

  1.	Salvare il file XML di configurazione di rete incluso nella sezione Riferimenti, aggiornandolo con i nomi, il percorso e gli indirizzi IP corrispondenti allo scenario specifico.
  2.	Aggiornare le variabili utente incluse nello script in modo che corrispondano all'ambiente in cui lo script verrà eseguito, ad esempio sottoscrizioni, nomi dei servizi e così via.
  3.	Eseguire lo script in PowerShell.

**Nota**: l'area indicata nello script di PowerShell deve corrispondere all'area indicata nel file XML di configurazione di rete.

Dopo l'esecuzione corretta dello script, si potranno eseguire i passaggi successivi allo script seguenti:

1.	Configurare le regole del firewall illustrate nella sezione seguente intitolata Descrizione delle regole del firewall.
2.	Facoltativamente, nella sezione Riferimenti sono disponibili due script per configurare il server Web e il server applicazioni per consentire l'esecuzione dei test con questa configurazione della rete perimetrale.

Dopo l'esecuzione corretta dello script, sarà necessario completare le regole del firewall come descritto nella sezione intitolata Regole del firewall.

## Routing definito dall'utente
Per impostazione predefinita, le route di sistema seguenti sono definite in questo modo:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal corrisponde sempre al prefisso o ai prefissi dell'indirizzo definito della rete virtuale per la rete specifica, ovvero cambierà da rete virtuale a rete virtuale in base alla modalità di definizione di ogni rete virtuale specifica. Le route di sistema rimanenti sono statiche e usano i valori predefiniti illustrati in precedenza.

Per quanto riguarda la priorità, le route vengono elaborate tramite il metodo Longest Prefix Match (LPM), secondo il quale la route più specifica inclusa nella tabella viene applicata a un determinato indirizzo di destinazione.

Di conseguenza, il traffico, ad esempio verso il server DNS01, 10.0.2.4, destinato alla rete locale (10.0.0.0/16) verrà instradato tramite la rete virtuale alla relativa destinazione, cioè la route 10.0.0.0/16. In altre parole, per 10.0.2.4 la route 10.0.0.0/16 è quella più specifica, anche se sarebbero applicabili sia la 10.0.0.0/8 che la 0.0.0.0/0, ma essendo meno specifiche non interessano questo traffico. In questo modo, il traffico per 10.0.2.4 avrà un hop successivo verso al rete virtuale locale e sarà semplicemente inoltrato alla destinazione.

Se il traffico fosse ad esempio destinato a 10.1.1.1, la route 10.0.0.0/16 non sarebbe applicabile, mentre la 10.0.0.0/8 sarebbe la più specifica e in questo caso il traffico verrebbe eliminato, una condizione cosiddetta "black hole", perché l'hop successivo è Null.

Se la destinazione non fosse applicabile ad alcun prefisso Null o ai prefissi VNETLocal, il traffico seguirebbe la route meno specifica, 0.0.0.0/0, e verrebbe instradato su Internet come hop successivo, quindi al di fuori del perimetro Internet di Azure.

Se nella tabella di route sono presenti due prefissi identici, di seguito è riportato l'ordine di precedenza basato sull'attributo "source" delle route:

1.	<blank> = route definita dall'utente aggiunta manualmente alla tabella
2.	"VPNGateway" = route dinamica, o BGP quando viene usata con reti ibride, aggiunta da un protocollo di rete dinamico. Queste route possono cambiare con il tempo, perché il protocollo dinamico riflette automaticamente le modifiche nella rete con peering.
3.	"Default" = route di sistema. La rete virtuale locale e le voci statiche sono quelle visualizzate nella tabella di route precedente.

>[AZURE.NOTE] È ora possibile utilizzare il routing definito dall'utente con ExpressRoute e i gateway VPN per forzare l'instradamento del traffico cross-premise in entrata e in uscita a un'appliance di rete virtuale.

#### Creazione di route locali

In questo esempio sono necessarie due tabelle di routing, una per la subnet front-end e una per la subnet back-end. In ogni tabella vengono caricate le route statiche appropriate per la subnet specifica. Ai fini di questo esempio, ogni tabella include tre route:

1. Traffico della subnet locale senza la definizione di un hop successivo per consentire al traffico della subnet locale di ignorare il firewall.
2. Traffico della rete virtuale con la definizione di un hop successivo come firewall, in modo da eseguire l'override della regola predefinita che consente l'indirizzamento diretto del traffico della rete virtuale locale.
3. Tutto il traffico rimanente (0/0) con un hop successivo definito come firewall.

Dopo la creazione, le tabelle di routing vengono associate alle rispettive subnet. Dopo la creazione e l'associazione alla subnet, la tabella di routing della subnet front-end avrà un aspetto analogo al seguente:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Per questo esempio si usano i comandi seguenti per compilare la tabella di route, aggiungere una route definita dall'utente e quindi associare la tabella di route a una subnet. Si noti che tutte le voci seguenti che iniziano con un segno di dollaro, ad esempio $BESubnet, sono variabili definite dall'utente dallo script disponibile nella sezione Riferimenti di questo documento:

1.	Prima di tutto è necessario creare la tabella di routing di base. Questo frammento di codice mostra la creazione della tabella per la subnet back-end. Nello script viene creata anche una tabella corrispondente per la subnet front-end.

		New-AzureRouteTable -Name $BERouteTableName `
		    -Location $DeploymentLocation `
		    -Label "Route table for $BESubnet subnet"

2.	Una volta creata la tabella di route, si possono aggiungere route definite dall'utente specifiche. In questo frammento di codice, tutto il traffico (0.0.0.0/0) verrà instradato tramite il dispositivo virtuale. Per passare l'indirizzo IP assegnato quando è stato creato il dispositivo virtuale nella parte precedente dello script, viene usata una variabile, $VMIP[0]. Nello script viene anche creata una regola corrispondente nella tabella front-end.

		Get-AzureRouteTable $BERouteTableName | `
		    Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
		    -NextHopType VirtualAppliance `
		    -NextHopIpAddress $VMIP[0]

3. La voce della route precedente sovrascrive la route predefinita "0.0.0.0/0", ma esiste ancora la regola predefinita per 10.0.0.0/16 che consente il routing del traffico della rete virtuale direttamente alla destinazione e non al dispositivo virtuale di rete. Per correggere questo comportamento, è necessario aggiungere la regola seguente.

	    Get-AzureRouteTable $BERouteTableName | `
	        Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]

4. A questo punto occorre fare una scelta. Con le due route precedenti tutto il traffico viene instradato al firewall per la valutazione, anche quello all'interno di una singola subnet. Questo comportamento potrebbe risultare utile, tuttavia per consentire il routing locale del traffico all'interno di una subnet senza coinvolgere il firewall, è possibile aggiungere una terza regola molto specifica. Questa route stabilisce che qualsiasi indirizzo destinato alla subnet locale può essere instradato direttamente alla subnet (NextHopType = VNETLocal).

	    Get-AzureRouteTable $BERouteTableName | `
	        Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
	        -NextHopType VNETLocal

5.	Infine, dopo aver creato la tabella di routing e averla popolata con le route definite dall'utente, è necessario associarla a una subnet. Nello script la tabella di route front-end è associata anche alla subnet front-end. Ecco lo script di associazione per la subnet back-end.

		Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
		   -SubnetName $BESubnet `
		   -RouteTableName $BERouteTableName

## Inoltro IP
L'inoltro IP è una funzionalità complementare del routing definito dall'utente. Si tratta di un'impostazione in un dispositivo virtuale che consente al dispositivo di ricevere traffico non indirizzato in modo specifico ad esso e quindi di inoltrare il traffico alla destinazione finale.

Ad esempio, se il traffico da AppVM01 effettua una richiesta al server DNS01, il routing definito dall'utente indirizzerà tale richiesta al firewall. Se l'inoltro IP è abilitato, il traffico per la destinazione DNS01 (10.0.2.4) verrà accettato dal dispositivo (10.0.0.4) e quindi inoltrato alla destinazione finale (10.0.2.4). Se l'inoltro IP non è abilitato sul firewall, il traffico non verrà accettato dal dispositivo, anche se la tabella di route specifica il firewall come hop successivo.

>[AZURE.IMPORTANT] È essenziale ricordare di abilitare l'inoltro IP insieme al routing definito dall'utente.

La configurazione dell'inoltro IP è costituita da un singolo comando e può essere eseguita al momento della creazione della macchina virtuale. Per il flusso di questo esempio, il frammento di codice si trova verso la fine dello script ed è raggruppato con i comandi relativi al routing definito dall'utente:

1.	Chiamare l'istanza della macchina virtuale corrispondente al dispositivo virtuale, in questo caso il firewall, e abilitare l'inoltro IP. Notare che qualsiasi elemento formattato in rosso che inizia con un segno di dollaro, ad esempio $VMName[0], è una variabile definita dall'utente contenuta nello script disponibile nella sezione Riferimenti di questo documento. Lo zero tra parentesi quadre, [0], rappresenta la prima macchina virtuale dell'array di VM. Per il funzionamento dello script di esempio senza modifiche, la prima macchina virtuale (VM 0) deve essere il firewall:

		Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
		   Set-AzureIPForwarding -Enable

## Gruppi di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui viene poi caricata una singola regola. Questo gruppo viene quindi associato solo alle subnet front-end e back-end, non a SecNet. La regola seguente viene creata in modo dichiarativo:

1.	Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (tutte le subnet) viene negato.

Anche se in questo esempio vengono usati i gruppi di sicurezza di rete, lo scopo principale consiste nel creare un secondo livello di difesa contro errori di configurazione manuale. Si vuole bloccare tutto il traffico in ingresso da Internet verso le subnet front-end o back-end. Il traffico deve solo attraversare la subnet SecNet verso il firewall e quindi, se appropriato, deve raggiungere le subnet front-end o back-end. Con l'abilitazione delle regole del routing definito dall'utente, inoltre, tutto il traffico che raggiunge le subnet front-end o back-end verrà indirizzato verso il firewall, grazie al routing definito dall'utente. Il firewall considera questo traffico come un flusso asimmetrico e rimuove il traffico in uscita. Sono quindi disponibili tre livelli di sicurezza per la protezione delle subnet front-end e back-end: 1) nessun endpoint aperto nei servizi cloud FrontEnd001 e BackEnd001, 2) gruppi di sicurezza di rete che non consentono il traffico da Internet, 3) il firewall che rimuove il traffico asimmetrico.

Un aspetto interessante del gruppo di sicurezza di rete di questo esempio consiste nel fatto che include solo una regola, illustrata di seguito, ovvero non consentire il traffico Internet nell'intera rete virtuale, inclusa la subnet di sicurezza.

	Get-AzureNetworkSecurityGroup -Name $NSGName | `
	    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
	    from the Internet" `
	    -Type Inbound -Priority 100 -Action Deny `
	    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	    -DestinationAddressPrefix VIRTUAL_NETWORK `
	    -DestinationPortRange '*' `
	    -Protocol *

Poiché, tuttavia, il gruppo di sicurezza di rete è associato solo alle subnet front-end e back-end, la regola non viene elaborata sul traffico in ingresso verso la subnet di sicurezza. Anche se la regola del gruppo di sicurezza di rete non consente il traffico Internet verso qualsiasi indirizzo sulla rete virtuale, perché il gruppo di sicurezza di rete non è mai stato associato alla subnet di sicurezza, il traffico raggiungerà quindi la subnet di sicurezza.

	Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
	    -SubnetName $FESubnet -VirtualNetworkName $VNetName
	
	Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
	    -SubnetName $BESubnet -VirtualNetworkName $VNetName

## Regole del firewall
Sul firewall sarà necessario creare regole di inoltro. Poiché il firewall blocca o inoltra tutto il traffico in ingresso, in uscita e tra le reti virtuali, sono necessarie molte regole del firewall. Tutto il traffico in ingresso, inoltre, raggiungerà l'indirizzo IP pubblico del servizio di sicurezza su porte diverse, per l'elaborazione da parte del firewall. Una procedura consigliata consiste nel creare un diagramma dei flussi logici prima di configurare le regole delle subnet e del firewall, per evitare modifiche successive. La figura seguente è una visualizzazione logica delle regole del firewall per questo esempio:
 
![Visualizzazione logica delle regole del firewall][2]

>[AZURE.NOTE] Le porte di gestione dipendono dal dispositivo virtuale di rete usato. In questo esempio si fa riferimento a Barracuda NextGen Firewall, che usa le porte 22, 801 e 807. Per informazioni precise sulle porte usate per la gestione del dispositivo in uso, vedere la documentazione del fornitore del dispositivo.

### Descrizione della regola logica
Nel diagramma logico precedente la subnet di sicurezza non viene mostrata, perché il firewall è l'unica risorsa su tale subnet e questo diagramma illustra le regole del firewall e il modo in cui le regole consentono o negano logicamente i flussi di traffico, non il percorso indirizzato effettivo. Le porte esterne selezionate per il traffico RDP, inoltre, sono le porte incluse negli intervalli più elevati (8014 - 8026) e sono state selezionate per allinearsi almeno in parte con gli ultimi due ottetti dell'indirizzo IP locale per facilitarne la lettura. Ad esempio, l'indirizzo del server locale 10.0.1.4 è associato alla porta esterna 8014. È tuttavia possibile usare qualsiasi porta superiore non in conflitto.

Per questo esempio sono necessari 7 tipi di regole, ovvero:

- Regole esterne (per il traffico in ingresso):
  1.	Regola di gestione del firewall: questa regola di reindirizzamento dell'app consente al traffico di raggiungere le porte di gestione del dispositivo virtuale di rete.
  2.	Regole RDP (per ogni server Windows): queste quattro regole, una per ogni server, consentiranno la gestione dei singoli server tramite RDP. È anche possibile raggruppare queste regole in una sola regola, in base alle capacità del dispositivo di rete virtuale usato.
  3.	Regole del traffico dell'applicazione: sono disponibili due regole del traffico dell'applicazione, la prima per il traffico Web front-end e la seconda per il traffico back-end, ad esempio dal server Web al livello dati. La configurazione di queste regole dipenderà dall'architettura di rete, ovvero dove sono posizionati i server, e dai flussi di traffico, ovvero la direzione dei flussi di traffico e le porte usate.
      - La prima regola consentirà al traffico effettivo dell'applicazione di raggiungere il server applicazioni. Anche se altre regole consentono la sicurezza, la gestione e così via, le regole delle applicazioni consentono a utenti o servizi esterni di accedere alle applicazioni. Per questo esempio è presente un singolo server Web sulla porta 80, quindi una singola regola delle applicazioni per il firewall reindirizzerà il traffico in ingresso all'indirizzo IP esterno, verso l'indirizzo IP interno dei server Web. La sessione di traffico reindirizzato sarà sottoposta al processo NAT verso il server interno.
      - La seconda regola del traffico delle applicazioni è la regola back-end per consentire al server Web di comunicare con il server AppVM01, ma non con AppVM02, tramite qualsiasi porta.
- Regole interne (per il traffico tra reti virtuali)
  4.	Regola in uscita verso Internet: questa regola consentirà al traffico proveniente da qualsiasi rete di passare alle reti selezionate. Questa regola è in genere una regola predefinita già disponibile sul firewall, ma con stato disabilitato. È consigliabile abilitarla per questo esempio.
  5.	Regola DNS: questa regola consente solo al traffico DNS (porta 53) di passare al server DNS. Per questo ambiente la maggior parte del traffico dal front-end e dal back-end è bloccato. Questa regola consente in modo specifico il traffico DNS da qualsiasi subnet locale.
  6.	Regola da subnet a subnet: questa regola consente a qualsiasi server sulla subnet back-end di connettersi a qualsiasi server sulla subnet front-end, ma non viceversa.
- Regola alternativa (per il traffico che non soddisfa alcuna condizione precedente):
  7.	Regola per negare tutto il traffico: deve essere sempre la regola finale, a livello di priorità. Se i flussi di traffico non corrispondono ad alcuna regola precedente, verranno eliminati da questa regola. Questa è una regola predefinita ed è solitamente attivata, quindi non sono in genere necessarie modifiche.

>[AZURE.TIP] Nella seconda regola per il traffico delle applicazioni tutte le porte sono autorizzate per semplificare l'esempio. In uno scenario reale è consigliabile usare la porta e gli intervalli di indirizzi più specifici per ridurre la superficie di attacco di questa regola.

<br />

>[AZURE.IMPORTANT] Dopo la creazione di tutte le regole indicate, è importante esaminare la priorità di ogni regola, per assicurare che il traffico venga consentito o negato in base a quanto previsto. Per questo esempio le regole sono elencate in ordine di priorità. È facile essere bloccati all'esterno del firewall a causa di regole non ordinate correttamente. Assicurarsi come minimo che la gestione del firewall stesso sia sempre la regola con la massima priorità.

### Prerequisiti delle regole
Un prerequisito per la macchina virtuale che esegue il firewall è costituito dagli endpoint pubblici. Perché il firewall possa elaborare il traffico, è necessario che gli endpoint pubblici siano aperti. Ci sono tre tipi di traffico in questo esempio, ovvero 1) traffico di gestione per controllare il firewall e le regole del firewall, 2) traffico RDP per controllare i server Windows e 3) traffico delle applicazioni. Queste sono le tre colonne dei tipi di traffico nella metà superiore della visualizzazione logica delle regole del firewall precedente.

>[AZURE.IMPORTANT] Un concetto importante da ricordare in questo caso è che **tutto** il traffico in ingresso passerà attraverso il firewall. Quindi, perché il desktop remoto possa accedere al server IIS01, anche se si trova nel servizio cloud front-end e nella subnet front-end, sarà necessario attivare il protocollo RDP sulla porta 8014 del firewall e quindi consentire al firewall di instradare la richiesta RDP internamente alla porta RDP del server IIS01. Il pulsante "Connetti" del portale di Azure non funzionerà, perché non esiste un percorso RDP diretto a IIS01 visibile per il portale. Ciò significa che tutte le connessioni da Internet saranno indirizzate alla porta e al servizio di sicurezza, ad esempio secscv001.cloudapp.net:xxxx. Vedere il diagramma precedente per il mapping della porta esterna all'indirizzo IP e alla porta interni.

Un endpoint può essere aperto sia al momento della creazione della VM sia dopo la compilazione, come nello script di esempio e come è illustrato nel frammento di codice seguente. Notare che qualsiasi elemento che inizia con un segno di dollaro, ad esempio $VMName[$i], è una variabile definita dall'utente contenuta nello script disponibile nella sezione Riferimenti di questo documento. L'elemento"$i" tra parentesi graffe, [$i], rappresenta il numero di array di una VM specifica in un array di VM:

	Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
	    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
	    Update-AzureVM

Anche se qui non è chiaramente illustrato a causa dell'uso delle variabili, gli endpoint vengono aperti **solo** nel servizio cloud di sicurezza, in modo da assicurare che tutto il traffico in ingresso, sia instradato, tramite NAT o eliminato, venga gestito dal firewall.

È necessario installare un client di gestione in un PC per controllare il firewall e creare le configurazioni necessarie. Per informazioni sulla gestione del dispositivo, vedere la documentazione del fornitore del firewall o di un altro dispositivo virtuale di rete. Il resto di questa sezione e la sezione successiva, Creazione di regole del firewall, descriveranno la configurazione del firewall stesso tramite il client di gestione dei fornitori, non del portale di Azure o di PowerShell.

Le istruzioni per scaricare il client e connettersi all'applicazione Barracuda usata in questo esempio sono disponibili qui: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Una volta eseguito l'accesso al firewall, ma prima di creare le relative regole, sono disponibili due classi di oggetti, Network e Service, che costituiscono un prerequisito e che possono facilitare la creazione delle regole.

Per questo esempio devono essere definiti tre oggetti di rete denominati, uno per la subnet back-end e uno per la subnet front-end, oltre a un oggetto di rete per l'indirizzo IP del server DNS. Per creare una rete denominata, dal dashboard del client Barracuda NG Admin passare alla scheda di configurazione, nella sezione Operational Configuration fare clic su Ruleset, quindi su "Networks" nel menu Firewall Objects e fare clic su New nel menu Edit Networks. Ora è possibile creare l'oggetto di rete aggiungendo il nome e il prefisso:

![Creare un oggetto di rete di front-end][3]
 
Verrà creata una rete denominata per la subnet front-end e sarà necessario creare un oggetto simile anche per la subnet back-end. Ora è possibile fare riferimento più facilmente alle subnet in base al relativo nome nelle regole del firewall.

Per l'oggetto server DNS:

![Creare un oggetto server DNS][4]

Questo singolo riferimento all'indirizzo IP sarà usato in una regola DNS più avanti in questo documento.

Il secondo oggetto prerequisito è costituito da oggetti servizio, che rappresenteranno le porte di connessione RDP per ogni server. Poiché l'oggetto servizio RDP è associato alla porta RDP predefinita, 3389, si possono creare nuovi servizi per consentire il traffico in ingresso dalle porte esterne (8014-8026). Le nuove porte possono anche essere aggiunte al servizio RDP esistente, ma ai fini di una semplice dimostrazione, si può creare una singola regola per ogni server. Per creare un nuova regola RDP per un server, dal dashboard del client Barracuda NG Admin passare alla scheda di configurazione, nella sezione Operational Configuration fare clic su Ruleset, quindi su "Services" nel menu Firewall Objects, scorrere l'elenco dei servizi e selezionare il servizio "RDP". Fare clic con il pulsante destro del mouse e scegliere Copy, quindi fare clic con il pulsante destro del mouse e scegliere Paste. Ora è disponibile un oggetto servizio RDP-Copy1 che può essere modificato. Fare clic con il pulsante destro del mouse su RDP-Copy1 e scegliere Edit. Verrà visualizzata la finestra Edit Service Object illustrata di seguito:

![Copiare la regola RDP predefinita][5]

I valori possono essere modificati per rappresentare il servizio RDP per un server specifico. Per AppVM01 la regola RDP predefinita precedente dovrà essere modificata per riflettere i nuovi valori per il nome del servizio, la descrizione e la porta RDP esterna usati nel diagramma della figura 8. Si noti che le porte vengono modificate, dalla porta RDP 3389 predefinita alla porta esterna usata per questo server specifico, nel caso di AppVM01 la porta esterna 8025. Il servizio modificato è illustrato di seguito:

![Regole AppVM01][6]
 
Questo processo deve essere ripetuto per creare i servizi RDP per i server rimanenti, AppVM02, DNS01 e IIS01. La creazione di questi servizi renderà più semplice e intuitiva la creazione delle regole illustrata nella sezione successiva.

>[AZURE.NOTE] Un servizio RDP per il firewall non è necessario per due motivi, 1) prima di tutto la macchina virtuale del firewall è un'immagine basata su Linux, quindi per la gestione della VM verrà usato SSH sulla porta 22 invece di RDP e 2) nella prima regola di gestione descritta di seguito la porta 22 e altre due porte di gestione sono consentite ai fini della connettività di gestione.

### Creazione di regole del firewall
In questo esempio si usano tre tipi di regole del firewall ognuna con icone distinte:

Regola Application Redirect: ![Icona di Application Redirect][7]

Regola Destination NAT: ![Icona di Destination NAT][8]

Regola Pass: ![Icona di Pass][9]

Altre informazioni su queste regole sono disponibili sul sito Web Barracuda.

Per creare le regole seguenti, o verificare le regole predefinite esistenti, dal dashboard del client Barracuda NG Admin passare alla scheda di configurazione, quindi nella sezione Operational Configuration fare clic su Ruleset. Una griglia denominata "Main Rules" mostrerà le regole esistenti attive e disattivate in questo firewall. Nell'angolo in alto a destra della griglia è presente un piccolo pulsante verde "+" sul quale occorre fare clic per creare una nuova regola. Si noti che il firewall potrebbe essere "bloccato" per modifiche. Se è visualizzato un pulsante contrassegnato con "Lock" e non si riesce a creare o modificare regole, fare clic sul pulsante per "sbloccare" il set di regole e consentire la modifica. Se si vuole modificare una regola esistente, selezionarla, fare clic con il pulsante destro del mouse e scegliere Edit Rule.

Una volta che le regole sono state create e/o modificate, è necessario effettuarne il push al firewall e quindi attivarle. In caso contrario, le modifiche alle regole non saranno applicate. Il processo di push e attivazione è illustrato dopo le descrizioni delle regole dettagliate.

Le specifiche di ogni regola necessaria per completare questo esempio sono descritte come segue:

- **Regola di gestione del firewall**: questa regola di reindirizzamento dell'app consente al traffico di raggiungere le porte di gestione del dispositivo virtuale di rete, in questo esempio Barracuda NextGen Firewall. Le porte di gestione sono 801, 807 e facoltativamente 22. Le porte esterne e interne sono le stesse, ovvero nessuna conversione di porta. Questa regola, SETUP-MGMT-ACCESS, è una regola predefinita e abilitata per impostazione predefinita in Barracuda NextGen Firewall versione 6.1.

	![Regola Firewall Management][10]

>[AZURE.TIP] Lo spazio degli indirizzi di origine in questa regola è Any. Se gli intervalli di indirizzi IP di gestione sono noti, riducendo l'ambito si ridurrà anche la superficie di attacco alle porte di gestione.

- **Regole RDP**: queste regole Destination NAT consentiranno la gestione di singoli server tramite RDP. Per creare questa regola sono necessari quattro campi critici:
  1.	Source: per consentire il traffico RDP da qualsiasi origine, nel campo Source viene usato il riferimento "Any".
  2.	Service: usare l'oggetto servizio appropriato creato in precedenza, in questo caso "AppVM01 RDP". Le porte esterne reindirizzano il traffico agli indirizzi IP locali dei server e alla porta 3386, ovvero la porta RDP predefinita. Questa regola specifica riguarda l'accesso RDP ad AppVM01.
  3.	Destination: deve essere la *porta locale sul firewall*, "DCHP 1 Local IP" o eth0 se si usano indirizzi IP statici. Il numero ordinale, eth0, eth1 e così via, può essere diverso se il dispositivo di rete ha più interfacce locali. Questa è la porta di invio del firewall, può essere la stessa porta di ricezione. La destinazione di routing effettiva è indicata nel campo Target List.
  4.	Redirection: questa sezione indica il dispositivo virtuale al quale verrà infine indirizzato il traffico. Il reindirizzamento più semplice consiste nell'inserire l'indirizzo IP e la porta, facoltativa, nel campo Target List. Se non si specifica una porta, verrà usata la porta di destinazione nella richiesta in ingresso, ovvero nessuna conversione. Se si designa una porta, questa verrà anche sottoposta al processo NAT insieme all'indirizzo IP.

	![Regola firewall RDP][11]

    Sarà necessario creare un totale di quattro regole RDP:

    | Nome regola | Server | Service | Target List |
    |----------------|---------|-------------|---------------|
    | RDP-to-IIS01 | IIS01 | IIS01 RDP | 10\.0.1.4:3389 |
    | RDP-to-DNS01 | DNS01 | DNS01 RDP | 10\.0.2.4:3389 |
    | RDP-to-AppVM01 | AppVM01 | AppVM01 RDP | 10\.0.2.5:3389 |
    | RDP-to-AppVM02 | AppVM02 | AppVm02 RDP | 10\.0.2.6:3389 |
  
>[AZURE.TIP] Limitando l'ambito dei campi Source e Service, si riduce la superficie di attacco. Sarà necessario usare l'ambito più limitato che consenta la funzionalità.

- **Regole del traffico dell'applicazione**: sono disponibili due regole del traffico dell'applicazione, la prima per il traffico Web front-end e la seconda per il traffico back-end, ad esempio dal server Web al livello dati. Queste regole dipenderanno dall'architettura di rete, ovvero dove sono posizionati i server, e dai flussi di traffico, ovvero la direzione dei flussi di traffico e le porte usate.

	Viene illustrata per prima la regola front-end per il traffico Web:

	![Regola firewall Web][12]
 
	La regola Destination NAT consente al traffico effettivo dell'applicazione di raggiungere il server applicazioni. Anche se altre regole consentono la sicurezza, la gestione e così via, le regole delle applicazioni consentono a utenti o servizi esterni di accedere alle applicazioni. Per questo esempio è presente un singolo server Web sulla porta 80, quindi la singola regola delle applicazioni per il firewall reindirizzerà il traffico in ingresso all'indirizzo IP esterno, verso l'indirizzo IP interno dei server Web.

	**Nota**: nel campo Target List non è assegnata alcuna porta, quindi verrà usata la porta in ingresso 80, o 443 per il servizio selezionato, nel reindirizzamento del server Web. Se il server Web è in ascolto su una porta diversa, ad esempio la porta 8080, il campo Target List potrebbe essere aggiornato in 10.0.1.4:8080 per consentire anche il reindirizzamento della porta.

	La regola del traffico delle applicazioni successiva è la regola back-end per consentire al server Web di comunicare con il server AppVM01, ma non AppVM02, tramite qualsiasi servizio.

	![Regola firewall AppVM01][13]

	La regola Pass consente a qualsiasi server IIS sulla subnet front-end di raggiungere il server AppVM01, con l'indirizzo IP 10.0.2.5, su qualsiasi porta, usando qualsiasi protocollo per accedere ai dati richiesti dall'applicazione Web.

	In questa schermata viene usato "<explicit-dest>" nel campo Destination per indicare 10.0.2.5 come destinazione. Può essere una destinazione esplicita, come illustrato, o un oggetto di rete denominato, come è stato fatto nei prerequisiti del server DNS. La scelta del metodo da usare spetta all'amministratore del firewall. Per aggiungere 10.0.2.5 come destinazione esplicita, fare doppio clic sulla prima riga vuota sotto <explicit-dest> e immettere l'indirizzo nella finestra visualizzata.

    Con questa regola Pass non è necessario usare NAT, perché si tratta di traffico interno, quindi si può impostare Connection Method su "No SNAT".

	**Nota**: la rete di origine in questa regola è qualsiasi risorsa nella subnet front-end. Se ce ne sarà una sola o se sarà disponibile un numero specifico noto di server Web, si potrà creare una risorsa oggetto di rete per indicare in modo più preciso gli indirizzi IP esatti, invece dell'intera subnet front-end.

>[AZURE.TIP] Questa regola usa il servizio "Any" per facilitare la configurazione e l'uso dell'applicazione di esempio, consentendo anche di usare ICMPv4 (ping) in una singola regola. Questa non è comunque una procedura consigliata. Le porte e i protocolli, ovvero "Services", dovranno essere limitati al minimo possibile che consenta all'attività dall'applicazione di ridurre la superficie di attacco su questo limite.

<br />

>[AZURE.TIP] Anche se questa regola illustra l'uso di un riferimento explicit-dest, è opportuno usare un approccio coerente in tutta la configurazione del firewall. È consigliabile usare sempre l'oggetto rete denominato per facilitare la leggibilità e il supporto. Il riferimento explicit-dest viene usato qui solo per illustrare un metodo di riferimento alternativo e non è generalmente consigliabile, specialmente per le configurazioni complesse.

- **Regola in uscita verso Internet**: questa regola Pass consentirà al traffico proveniente da qualsiasi rete di origine di passare alle reti di destinazione selezionate. Questa regola è in genere una regola predefinita già disponibile in Barracuda NextGen Firewall, ma con stato disabilitato. Facendo clic su questa regola, è possibile accedere al comando Activate Rule. La regola illustrata di seguito è stata modificata per aggiungere le due subnet locali create come riferimenti nella sezione dei prerequisiti di questo documento per l'attributo Source di questa regola.

	![Regola firewall in uscita][14]

- **Regola DNS**: questa regola Pass consente solo al traffico DNS (porta 53) di passare al server DNS. Per questo ambiente la maggior parte del traffico dal front-end e dal back-end è bloccato. Questa regola consente in modo specifico il traffico DNS.

	![Regola firewall DNS][15]

	**Nota**: in questa schermata è inclusa l'impostazione Connection Method. Poiché questa regola riguarda il traffico da indirizzo IP interno a indirizzo IP interno, non è richiesto alcun processo NAT e per questa regola Pass l'opzione Connection Method è impostata su "No SNAT".

- **Regola da subnet a subnet**: questa regola Pass è una regola predefinita attivata e modificata per consentire a qualsiasi server sulla subnet back-end di connettersi a qualsiasi server sulla subnet front-end. Questa regola riguarda tutto il traffico interno, quindi è possibile impostare Connection Method su No SNAT.

	![Regola firewall Intra-VNet][16]

	**Nota**: la casella di controllo Bi-Directional non è selezionata, né lo è per la maggior parte delle regole. Questa impostazione è significativa per questa regola, perché la rende unidirezionale. È possibile avviare una connessione dalla subnet back-end alla rete front-end, ma non il contrario. Se la casella di controllo viene selezionata, la regola consente il traffico bidirezionale, che in base al diagramma logico in questo articolo non è auspicabile.

- **Regola per negare tutto il traffico**: deve essere sempre la regola finale, a livello di priorità. Se i flussi di traffico non corrispondono ad alcuna regola precedente, verranno eliminati da questa regola. Questa è una regola predefinita ed è solitamente attivata, quindi non sono in genere necessarie modifiche.

	![Regola firewall Deny][17]

>[AZURE.IMPORTANT] Dopo la creazione di tutte le regole indicate, è importante esaminare la priorità di ogni regola, per assicurare che il traffico venga consentito o negato in base a quanto previsto. Per questo esempio, le regole sono nell'ordine in cui dovrebbero apparire nella griglia principale delle regole di inoltro del client di gestione Barracuda.

## Attivazione delle regole
Dopo aver modificato il set di regole in base alla specifica del diagramma logico, è necessario caricarlo nel firewall e quindi attivarlo.

![Attivazione delle regole firewall][18]
 
Nell'angolo in alto a destra del client di gestione è disponibile un gruppo di pulsanti. Fare clic sul pulsante "Send Changes" per inviare le regole modificate al firewall, quindi fare clic sul pulsante "Activate".
 
Con l'attivazione del set di regole del firewall, la compilazione dell'ambiente di esempio è completata.

## Scenari di traffico
>[AZURE.IMPORTANT] Un concetto importante da ricordare è che **tutto** il traffico in ingresso passerà attraverso il firewall. Quindi, perché il desktop remoto possa accedere al server IIS01, anche se si trova nel servizio cloud front-end e nella subnet front-end, sarà necessario attivare il protocollo RDP sulla porta 8014 del firewall e quindi consentire al firewall di instradare la richiesta RDP internamente alla porta RDP del server IIS01. Il pulsante "Connetti" del portale di Azure non funzionerà, perché non esiste un percorso RDP diretto a IIS01 visibile per il portale. Ciò significa che tutte le connessioni da Internet saranno inoltrate alla porta e al servizio di sicurezza, ad esempio secscv001.cloudapp.net:xxxx.

Per questi scenari devono essere attive le regole del firewall seguenti:

1.	Firewall Management
2.	RDP to IIS01
3.	RDP to DNS01
4.	RDP to AppVM01
5.	RDP to AppVM02
6.	App Traffic to the Web
7.	App Traffic to AppVM01
8.	Outbound to the Internet
9.	Frontend to DNS01
10.	Intra-Subnet Traffic (solo da back-end a front-end)
11.	Deny All

Il set di regole del firewall effettive avrà probabilmente molte altre regole oltre a queste. Le regole di qualsiasi firewall specifico avranno anche diversi numeri di priorità rispetto a quelli elencati di seguito. Questo elenco e i numeri associati vengono usati per fornire la rilevanza solo tra queste undici regole e le relative priorità tra di esse. In altre parole, nel firewall effettivo "RDP to IIS01" potrebbe essere la regola numero 5, ma finché si trova dopo la regola "Firewall Management" e prima della regola "RDP to DNS01", sarà allineata con lo scopo di questo elenco. L'elenco sarà utile anche negli scenari seguenti ai fini della brevità, ad esempio "Regole firewall 9 (DNS)". Sempre per brevità, le quattro regole RDP saranno chiamate collettivamente "regole RDP" quando lo scenario di traffico non è correlato a RDP.

Tenere anche presente che per il traffico Internet in ingresso sulle subnet front-end e back-end sono configurati gruppi di sicurezza di rete.

#### (Consentito) Da Internet a server Web
1.	L'utente Internet richiede una pagina HTTP a SecSvc001.CloudApp.Net (servizio cloud per Internet).
2.	Il servizio cloud passa il traffico attraverso l'endpoint aperto sulla porta 80 all'interfaccia del firewall su 10.0.0.4:80.
3.	Nessun gruppo di sicurezza di rete è assegnato alla subnet di sicurezza, quindi le regole del gruppo di sicurezza di sistema consentono l'inoltro del traffico al firewall.
4.	Il traffico raggiunge l'indirizzo IP interno del firewall (10.0.1.4).
5.	Il firewall inizia l'elaborazione delle regole:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regole firewall da 2 a 5 (regole RDP) non applicabili, passa alla regola successiva.
  3.	Regola firewall 6 (App: Web) applicabile, il traffico è consentito, il firewall lo inoltra tramite NAT a 10.0.1.4 (IIS01).
6.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet) non applicabile, il traffico è stato inoltrato tramite NAT dal firewall, quindi l'indirizzo di origine ora è il firewall che si trova sulla subnet front-end e considerato dal gruppo di sicurezza di rete come traffico "locale" e quindi consentito; passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
7.	IIS01 è in ascolto del traffico Web, riceve la richiesta e ne avvia l'elaborazione.
8.	IIS01 prova ad avviare una sessione FTP per AppVM01 sulla subnet back-end.
9.	Il routing definito dall'utente nella subnet front-end usa il firewall per l'hop successivo.
10.	Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
11.	Il firewall inizia l'elaborazione delle regole:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regole firewall da 2 a 5 (regole RDP) non applicabili, passa alla regola successiva.
  3.	Regola firewall 6 (App: Web) non applicabile, passa alla regola successiva.
  4.	Regola firewall 7 (App: back-end) non applicabile, il traffico è consentito, il firewall inoltra il traffico a 10.0.2.5 (AppVM01).
12.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet), non applicabile, passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
13.	 AppVM01 riceve la richiesta, avvia la sessione e risponde.
14.	Il routing definito dall'utente nella subnet back-end usa il firewall per l'hop successivo.
15.	Non essendoci regole del gruppo di sicurezza di rete in uscita sulla subnet back-end, la risposta è consentita.
16.	Essendo traffico restituito su una sessione stabilita, il firewall restituisce la risposta al server Web (IIS01).
17.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet), non applicabile, passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
18.	Il server IIS riceve la risposta, completa la transazione con AppVM01 e quindi completa la compilazione della risposta HTTP che viene inviata al richiedente.
19.	Non essendoci regole del gruppo di sicurezza di rete in uscita sulla subnet front-end, la risposta è consentita.
20.	La risposta HTTP raggiunge il firewall ed essendo la risposta a una sessione NAT stabilita, viene accettata dal firewall.
21.	Il firewall reindirizza quindi la risposta all'utente Internet.
22.	Non essendoci regole del gruppo di sicurezza di rete in uscita o hop di routing definito dall'utente sulla subnet front-end, la risposta è consentita e l'utente Internet riceve la pagina Web richiesta.

#### (Consentito) Da RDP Internet a back-end
1.	L'amministratore del server su Internet richiede una sessione RDP per AppVM01 tramite SecSvc001.CloudApp.Net:8025, dove 8025 è il numero di porta assegnato dall'utente per la regola del firewall "RDP to AppVM01".
2.	Il servizio cloud passa il traffico attraverso l'endpoint aperto sulla porta 8025 all'interfaccia del firewall su 10.0.0.4:8025.
3.	Nessun gruppo di sicurezza di rete è assegnato alla subnet di sicurezza, quindi le regole del gruppo di sicurezza di sistema consentono l'inoltro del traffico al firewall.
4.	Il firewall inizia l'elaborazione delle regole:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regola firewall 2 (IIS RDP) non applicabile, passa alla regola successiva.
  3.	Regola firewall 3 (DNS01 RDP) non applicabile, passa alla regola successiva.
  4.	Regola firewall 4 (AppVM01 RDP) applicabile, il traffico è consentito, il firewall lo inoltra tramite NAT a 10.0.2.5:3386 (porta RDP su AppVM01).
5.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet) non applicabile, il traffico è stato inoltrato tramite NAT dal firewall, quindi l'indirizzo di origine ora è il firewall che si trova sulle subnet back-end e considerato dal gruppo di sicurezza di rete come traffico "locale" e quindi consentito; passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
6.	AppVM01 è in ascolto del traffico RDP e risponde.
7.	Senza regole del gruppo di sicurezza di rete in uscita sono applicabili le regole predefinite e il traffico restituito è consentito.
8.	Il routing definito dall'utente instrada il traffico in uscita al firewall come hop successivo.
9.	Essendo traffico restituito su una sessione stabilita, il firewall restituisce la risposta all'utente Internet.
10.	La sessione RDP è abilitata.
11.	AppVM01 richiede il nome utente e la password.

#### (Consentito) Ricerca DNS del server Web sul server DNS
1.	Il server Web, IIS01, richiede un feed di dati all'indirizzo www.data.gov, ma deve risolvere l'indirizzo.
2.	La configurazione di rete per la rete virtuale elenca DNS01 (10.0.2.4 sulla subnet back-end) come server DNS primario, IIS01 invia la richiesta DNS a DNS01.
3.	Il routing definito dall'utente instrada il traffico in uscita al firewall come hop successivo.
4.	Non sono presenti regole del gruppo di sicurezza di rete associate alla subnet front-end, il traffico è consentito.
5.	Il firewall inizia l'elaborazione delle regole:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regole firewall da 2 a 5 (regole RDP) non applicabili, passa alla regola successiva.
  3.	Regole firewall 6 e 7 (regole app) non applicabili, passa alla regola successiva.
  4.	Regole firewall 8 (a Internet) non applicabili, passa alla regola successiva.
  5.	Regola firewall 9 (DNS) applicabile, il traffico è consentito, il firewall inoltra il traffico a 10.0.2.4 (DNS01).
6.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet), non applicabile, passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
7.	Il server DNS riceve la richiesta.
8.	Il server DNS non ha l'indirizzo memorizzato nella cache e invia la richiesta a un server DNS radice su Internet.
9.	Il routing definito dall'utente instrada il traffico in uscita al firewall come hop successivo.
10.	Non sono impostate regole del gruppo di sicurezza di rete in uscita sulla subnet back-end, il traffico è consentito.
11.	Il firewall inizia l'elaborazione delle regole:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regole firewall da 2 a 5 (regole RDP) non applicabili, passa alla regola successiva.
  3.	Regole firewall 6 e 7 (regole app) non applicabili, passa alla regola successiva.
  4.	 Regola firewall 8 (a Internet) applicabile, il traffico è consentito, la sessione viene inoltrata tramite SNAT al server DNS radice su Internet.
12.	Il server DNS Internet risponde perché la sessione è stata avviata dal firewall; la risposta viene accettata dal firewall.
13.	Essendo una sessione stabilita, il firewall inoltra la risposta al server che l'ha avviata, DNS01.
14.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet), non applicabile, passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
15.	Il server DNS riceve la risposta e la memorizza nella cache, quindi restituisce a IIS01 la risposta alla richiesta iniziale.
16.	Il routing definito dall'utente nella subnet back-end usa il firewall per l'hop successivo.
17.	Non esistono regole del gruppo di sicurezza di rete in uscita sulla subnet back-end, il traffico è consentito.
18.	Essendo una sessione stabilita sul firewall, la risposta viene restituita dal firewall al server IIS.
19.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2.	La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
20.	IIS01 riceve la risposta da DNS01.

#### (Consentito) Da server back-end a server front-end
1.	Un amministratore connesso ad AppVM02 tramite RDP richiede un file direttamente al server IIS01 con Esplora file di Windows.
2.	Il routing definito dall'utente nella subnet back-end usa il firewall per l'hop successivo.
3.	Non essendoci regole del gruppo di sicurezza di rete in uscita sulla subnet back-end, la risposta è consentita.
4.	Il firewall inizia l'elaborazione delle regole:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regole firewall da 2 a 5 (regole RDP) non applicabili, passa alla regola successiva.
  3.	Regole firewall 6 e 7 (regole app) non applicabili, passa alla regola successiva.
  4.	Regole firewall 8 (a Internet) non applicabili, passa alla regola successiva.
  5.	Regola firewall 9 (DNS) non applicabile, passa alla regola successiva.
  6.	Regola firewall 10 (tra subnet) non applicabile, il traffico è consentito, il firewall passa il traffico a 10.0.1.4 (IIS01).
5.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet), non applicabile, passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
6.	Presupponendo un'autenticazione e un'autorizzazione correte, IIS01 accetta la richiesta e risponde.
7.	Il routing definito dall'utente nella subnet front-end usa il firewall per l'hop successivo.
8.	Non essendoci regole del gruppo di sicurezza di rete in uscita sulla subnet front-end, la risposta è consentita.
9.	Essendo una sessione esistente sul firewall, la risposta è consentita e il firewall restituisce la risposta ad AppVM02.
10.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (blocco Internet), non applicabile, passa alla regola successiva.
  2.	Regole gruppo di sicurezza di rete predefinite, consentono il traffico da subnet a subnet, il traffico è consentito, l'elaborazione della regola del gruppo di sicurezza di rete viene arrestata.
11.	AppVM02 riceve la risposta.

#### (Negato) Traffico Internet diretto al server Web
1.	L'utente Internet prova ad accedere al server Web, IIS01, tramite il servizio FrontEnd001.CloudApp.Net.
2.	Non essendoci endpoint aperti per il traffico HTTP, questo non passa attraverso il servizio cloud e non raggiunge il server.
3.	In caso di apertura degli endpoint per qualunque motivo, il gruppo di sicurezza di rete (blocco Internet) sulla subnet front-end bloccherà questo traffico.
4.	Infine, il routing definito dall'utente nella subnet front-end invia tutto il traffico in uscita da IIS01 al firewall come hop successivo. Il firewall lo considera come traffico asimmetrico ed elimina la risposta in uscita. Ci sono quindi tre livelli di difesa indipendenti tra Internet e IIS01 tramite il servizio cloud che impedisce l'accesso non autorizzato/non appropriato.

#### (Negato) Da Internet al server back-end
1.	L'utente Internet prova ad accedere a un file su AppVM01 tramite il servizio BackEnd001.CloudApp.Net.
2.	Non essendoci endpoint aperti per la condivisione file, il traffico non passa attraverso il servizio cloud e non raggiunge il server.
3.	In caso di apertura degli endpoint per qualunque motivo, il gruppo di sicurezza di rete (blocco Internet) bloccherà questo traffico.
4.	Infine, il routing definito dall'utente invia tutto il traffico in uscita da AppVM01 al firewall come hop successivo. Il firewall lo considera come traffico asimmetrico ed elimina la risposta in uscita. Ci sono quindi tre livelli di difesa indipendenti tra Internet e AppVM01 tramite il servizio cloud che impedisce l'accesso non autorizzato/non appropriato.

#### (Negato) Da server front-end a server back-end
1.	Si supponga che IIS01 sia stato compromesso ed esegua codice dannoso tentando di analizzare i server sulla subnet back-end.
2.	Il routing definito dall'utente nella subnet front-end invia tutto il traffico in uscita da IIS01 al firewall come hop successivo. Questo comportamento potrebbe essere modificato dalla macchina virtuale compromessa.
3.	Il firewall elabora il traffico. Se la richiesta era destinata ad AppVM01 o al server DNS per ricerche DNS, il traffico potrebbe essere consentito dal firewall, a causa delle regole firewall 7 e 9. Tutto il resto del traffico verrebbe bloccato dalla regola firewall 11 (Deny All).
4.	Se sul firewall è stato abilitato il rilevamento delle minacce avanzato, che non viene descritto in questo documento e quindi occorre vedere la documentazione del fornitore per informazioni sulle funzionalità di rilevamento avanzato delle minacce per il dispositivo di rete specifico, anche il traffico consentito dalle regole di inoltro di base illustrate in questo documento potrebbe non essere consentito qualora contenga firme note o modelli che contrassegnano una regola riguardante minacce avanzate.

#### (Negato) Ricerca DNS Internet sul server DNS
1.	L'utente Internet prova a cercare un record DNS interno su DNS01 tramite il servizio BackEnd001.CloudApp.Net.
2.	Non essendoci endpoint aperti per il traffico DNS, questo non passa attraverso il servizio cloud e non raggiunge il server.
3.	In caso di apertura degli endpoint per qualunque motivo, la regola del gruppo di sicurezza di rete (blocco Internet) sulla subnet front-end bloccherà questo traffico.
4.	Infine, il routing definito dall'utente nella subnet back-end invia tutto il traffico in uscita da DNS01 al firewall come hop successivo. Il firewall lo considera come traffico asimmetrico ed elimina la risposta in uscita. Ci sono quindi tre livelli di difesa indipendenti tra Internet e DNS01 tramite il servizio cloud che impedisce l'accesso non autorizzato/non appropriato.

#### (Negato) Accesso da Internet a SQL tramite il firewall
1.	L'utente Internet richiede dati SQL a SecSvc001.CloudApp.Net (servizio cloud per Internet).
2.	Non essendoci endpoint aperti per SQL, il traffico non passa attraverso il servizio cloud e non raggiunge il server.
3.	In caso di apertura degli endpoint SQL per qualunque motivo, il firewall avvierà l'elaborazione della regola:
  1.	Regola firewall 1 (FW Mgmt) non applicabile, passa alla regola successiva.
  2.	Regole firewall da 2 a 5 (regole RDP) non applicabili, passa alla regola successiva.
  3.	Regole firewall 6 e 7 (regole applicazione) non applicabili, passa alla regola successiva.
  4.	Regole firewall 8 (a Internet) non applicabili, passa alla regola successiva.
  5.	Regola firewall 9 (DNS) non applicabile, passa alla regola successiva.
  6.	Regola firewall 10 (tra subnet) non applicabile, passa alla regola successiva.
  7.	Regole firewall 11 (Deny All) applicabile, il traffico viene bloccato, l'elaborazione della regola si arresta.


## Riferimenti
### Script principale e configurazione di rete
Salvare lo script completo in un file di script PowerShell. Salvare la configurazione di rete in un file denominato "NetworkConf2.xml". Modificare le variabili definite dall'utente secondo le esigenze. Eseguire lo script, quindi seguire le istruzioni per la configurazione delle regole del firewall.

#### Script completo
In base alle variabili definite dall'utente, lo script consente di:

1.	Connettersi a una sottoscrizione di Azure.
2.	Creare un nuovo account di archiviazione.
3.	Creare una nuova rete virtuale e tre subnet, come definito nel file di configurazione di rete.
4.	Compilare cinque macchine virtuali, 1 firewall e 4 VM Windows Server.
5.	Configurare il routing definito dall'utente, incluse le operazioni seguenti:
  1.	Creazione di due nuove tabelle di route.
  2.	Aggiunta di route alle tabelle.
  3.	Associazione di tabelle alle subnet appropriate.
6.	Abilitare l'inoltro IP sul dispositivo virtuale di rete.
7.	Configurare il gruppo di sicurezza di rete, incluse le operazioni seguenti:
  1.	Creazione di un gruppo di sicurezza di rete.
  2.	Aggiunta di una regola.
  3.	Associazione del gruppo di sicurezza di rete alle subnet appropriate.

Questo script di PowerShell deve essere eseguito localmente in un server o un PC connesso a Internet.

>[AZURE.IMPORTANT] Quando si esegue lo script, in PowerShell potrebbero venire visualizzati avvisi o altri messaggi informativi. Solo i messaggi di errore formattati in rosso possono indicare un problema.

	<# 
	 .SYNOPSIS
	  Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Three new cloud services
	   - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
	   - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
	   - One server on the FrontEnd Subnet
	   - Three Servers on the BackEnd Subnet
	   - IP Forwading from the FireWall out to the internet
	   - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
	
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Everyone's security requirements are different and can be addressed in a myriad of ways.
	  Please be sure that any sensitive data or applications are behind the appropriate
	  layer(s) of protection. This script serves as an example of some of the techniques
	  that can be used, but should not be used for all scenarios. You are responsible to
	  assess your security needs and the appropriate protections needed, and then effectively
	  implement those protections.
	
	  Security Service (SecNet subnet 10.0.0.0/24)
	   myFirewall - 10.0.0.4
	 
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   IIS01      - 10.0.1.4
	 
	  BackEnd Service (BackEnd subnet 10.0.2.0/24)
	   DNS01      - 10.0.2.4
	   AppVM01    - 10.0.2.5
	   AppVM02    - 10.0.2.6
	
	#>
	
	# Fixed Variables
	    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
	    $VMName = @()
	    $ServiceName = @()
	    $VMFamily = @()
	    $img = @()
	    $size = @()
	    $SubnetName = @()
	    $VMIP = @()
	
	# User Defined Global Variables
	  # These should be changes to reflect your subscription and services
	  # Invalid options will fail in the validation section
	
	  # Subscription Access Details
	    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	
	  # VM Account, Location, and Storage Details
	    $LocalAdmin = "theAdmin"
	    $DeploymentLocation = "Central US"
	    $StorageAccountName = "vmstore02"
	
	  # Service Details
	    $SecureService = "SecSvc001"
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $VNetPrefix = "10.0.0.0/16"
	    $SecNet = "SecNet"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	    $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	
	  # UDR Details
	    $FERouteTableName = "FrontEndSubnetRouteTable"
	    $BERouteTableName = "BackEndSubnetRouteTable"
	
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure UDR and IP forwarding is setup
	    # properly this script requires VM 0 be the NVA.
	
	    # VM 0 - The Network Virtual Appliance (NVA)
	      $VMName += "myFirewall"
	      $ServiceName += $SecureService
	      $VMFamily += "Firewall"
	      $img += $FWImg
	      $size += "Small"
	      $SubnetName += $SecNet
	      $VMIP += "10.0.0.4"
	
	    # VM 1 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.4"
	
	    # VM 2 - The First Appliaction Server
	      $VMName += "AppVM01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.5"
	
	    # VM 3 - The Second Appliaction Server
	      $VMName += "AppVM02"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.6"
	
	    # VM 4 - The DNS Server
	      $VMName += "DNS01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.4"
	
	# ----------------------------- #
	# No User Defined Varibles or   #
	# Configuration past this point #
	# ----------------------------- #
	
	  # Get your Azure accounts
	    Add-AzureAccount
	    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
	    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
	
	  # Create Storage Account
	    If (Test-AzureName -Storage -Name $StorageAccountName) { 
	        Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
	        Return}
	    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
	          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
	
	  # Update Subscription Pointer to New Storage Account
	    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
	    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
	
	# Validation
	$FatalError = $false
	
	If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
	     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
	     $FatalError = $true}
	
	If (Test-AzureName -Service -Name $SecureService) { 
	    Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $BackEndService) { 
	    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
	
	If (-Not (Test-Path $NetworkConfigFile)) { 
	    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The network config file was found" -ForegroundColor Green
	        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
	            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
	            $FatalError = $true}
	        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
	
	If ($FatalError) {
	    Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
	    Return}
	Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
	
	# Create VNET
	    Write-Host "Creating VNET" -ForegroundColor Cyan 
	    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
	
	# Create Services
	    Write-Host "Creating Services" -ForegroundColor Cyan
	    New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
	
	# Build VMs
	    $i=0
	    $VMName | Foreach {
	        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
	        If ($VMFamily[$i] -eq "Firewall") 
	            { 
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            # Set up all the EndPoints we'll need once we're up and running
	            # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
	            #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
	            #       rule, so all of these endpoint have the same public and local port and the firewall
	            #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
	            Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
	            }
	        Else
	            {
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	                Remove-AzureEndpoint -Name "RemoteDesktop" | `
	                Remove-AzureEndpoint -Name "PowerShell" | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            }
	        $i++
	    }
	
	# Configure UDR and IP Forwarding
	    Write-Host "Configuring UDR" -ForegroundColor Cyan
	
	  # Create the Route Tables
	    Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
	    New-AzureRouteTable -Name $BERouteTableName `
	        -Location $DeploymentLocation `
	        -Label "Route table for $BESubnet subnet"
	    New-AzureRouteTable -Name $FERouteTableName `
	        -Location $DeploymentLocation `
	        -Label "Route table for $FESubnet subnet"
	
	  # Add Routes to Route Tables
	    Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $BERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
	        -NextHopType VNETLocal
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
	        -NextHopType VirtualAppliance `
	        -NextHopIpAddress $VMIP[0]
	    Get-AzureRouteTable $FERouteTableName `
	        |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
	        -NextHopType VNETLocal
	
	  # Assoicate the Route Tables with the Subnets
	    Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
	    Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
	        -SubnetName $BESubnet `
	        -RouteTableName $BERouteTableName
	    Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
	        -SubnetName $FESubnet `
	        -RouteTableName $FERouteTableName
	
	 # Enable IP Forwarding on the Virtual Appliance
	    Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
	        |Set-AzureIPForwarding -Enable
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rule
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	  # Assign the NSG to two Subnets
	    # The NSG is *not* bound to the Security Subnet. The result
	    # is that internet traffic flows only to the Security subnet
	    # since the NSG bound to the Frontend and Backback subnets
	    # will Deny internet traffic to those subnets.
	    Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
	    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
	    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
	
	# Optional Post-script Manual Configuration
	  # Configure Firewall
	  # Install Test Web App (Run Post-Build Script on the IIS Server)
	  # Install Backend resource (Run Post-Build Script on the AppVM01)
	  Write-Host
	  Write-Host "Build Complete!" -ForegroundColor Green
	  Write-Host
	  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
	  Write-Host " - Configure Firewall" -ForegroundColor Gray
	  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
	  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
	  Write-Host
	

#### File di configurazione di rete
Salvare questo file XML con il percorso aggiornato e aggiungere il collegamento a questo file nella variabile $NetworkConfigFile dello script precedente.

	<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
	  <VirtualNetworkConfiguration>
	    <Dns>
	      <DnsServers>
	        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
	        <DnsServer name="Level3" IPAddress="209.244.0.3" />
	      </DnsServers>
	    </Dns>
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
	        <AddressSpace>
	          <AddressPrefix>10.0.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="SecNet">
	            <AddressPrefix>10.0.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="FrontEnd">
	            <AddressPrefix>10.0.1.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="BackEnd">
	            <AddressPrefix>10.0.2.0/24</AddressPrefix>
	          </Subnet>
	        </Subnets>
	        <DnsServersRef>
	          <DnsServerRef name="DNS01" />
	          <DnsServerRef name="Level3" />
	        </DnsServersRef>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>
	  </VirtualNetworkConfiguration>
	</NetworkConfiguration>

#### Script di applicazione di esempio
Se si vuole installare un'applicazione di esempio per questo e altri esempi di rete perimetrale, tramite il collegamento seguente ne viene fornita una: [Script di applicazione di esempio][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Rete perimetrale bidirezionale con dispositivo virtuale di rete, gruppo di sicurezza di rete e routing definito dall'utente"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Visualizzazione logica delle regole del firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Creare un oggetto di rete di front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Creare un oggetto server DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copiare la regola RDP predefinita"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regole AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icona di Application Redirect"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icona di Destination NAT"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icona di Pass"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regola Firewall Management"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regola firewall RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regola firewall Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regola firewall AppVM01"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regola firewall in uscita"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regola firewall DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regola firewall Intra-VNet"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regola firewall Deny"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Attivazione delle regole firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

<!---HONumber=AcomDC_0803_2016-->