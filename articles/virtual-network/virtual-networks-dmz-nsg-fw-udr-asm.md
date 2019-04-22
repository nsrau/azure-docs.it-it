---
title: Esempio di rete perimetrale-Proteggi le reti con rete perimetrale costituito da un firewall, UDR e Nsg | Microsoft Docs
description: Creare una rete perimetrale (detta anche DMZ) con un firewall, routing definito dall'utente (UDR) e gruppi di sicurezza di rete (Nsg).
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426529"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, UDR e Nsg

[Tornare alla pagina relativa alle procedure consigliate sui limiti di sicurezza][HOME]

In questo esempio, si crea una rete perimetrale (noto anche come una rete Perimetrale, DMZ e subnet schermata). L'esempio implementa un firewall, quattro server Windows, routing definito dall'utente (UDR), l'inoltro IP e gruppi di sicurezza di rete (Nsg). Questo articolo illustra ognuno dei comandi rilevanti per fornire una conoscenza più approfondita di ogni passaggio. La sezione sugli scenari di traffico viene illustrato anche in modo approfondito come il traffico avviene attraverso i livelli di difesa della rete perimetrale. Infine, la sezione riferimenti contiene tutto il codice e le istruzioni per creare l'ambiente in modo da poter testare e sperimentare vari scenari.

![Rete perimetrale bidirezionale con Appliance virtuale di rete, sicurezza di rete e routing definito dall'utente][1]

## <a name="environment-setup"></a>Configurazione dell'ambiente

Questo esempio Usa una sottoscrizione che contiene i componenti seguenti:

* Tre servizi cloud: SecSvc001 FrontEnd001 e BackEnd001
* Una rete virtuale (CorpNetwork) con tre subnet: SecNet, front-end e back-end
* Appliance virtuale di rete: un firewall connesso alla subnet SecNet.
* Un server Windows che rappresenta un server web applicazioni: IIS01
* Due server Windows che rappresentano server back-end dell'applicazione: AppVM01, AppVM02
* Un server Windows che rappresenta un server DNS: DNS01

Il [fa riferimento a sezione](#references) contiene uno script di PowerShell che consente di creare la maggior parte dell'ambiente descritto di seguito. Questo articolo in caso contrario, non offre istruzioni dettagliate per la creazione di macchine virtuali (VM) e le reti virtuali.

Per creare l'ambiente, eseguire queste operazioni:

1. Salvare il file XML di configurazione rete inclusi nel [sezione di riferimento](#references). È necessario aggiornarlo con i nomi di percorso e indirizzi IP corrispondenti allo scenario specifico.
1. Aggiornare le variabili utente incluse lo script seguente in base all'ambiente specifico (ad esempio, le sottoscrizioni, nomi di servizio e così via).
1. Eseguire lo script di PowerShell.

> [!NOTE]
> L'area specificata nello script di PowerShell deve corrispondere all'area specificata nel file XML di configurazione di rete.

Dopo la corretta esecuzione dello script, procedere come segue:

1. Configurare le regole del firewall. Vedere le [regole del firewall](#firewall-rules) sezione.
1. Facoltativamente, è possibile usare i due script nella sezione Riferimenti per configurare un'applicazione web nel server web e server dell'app per consentire la verifica di questa configurazione della rete Perimetrale.

## <a name="user-defined-routing"></a>Routing definito dall'utente

Per impostazione predefinita, le route di sistema seguenti sono definite in questo modo:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal è sempre i prefissi di indirizzi definiti per la rete virtuale specifica. Ad esempio, cambierà da rete virtuale a rete virtuale a seconda del modo in cui viene definito ogni specifica rete virtuale. Le route di sistema rimanenti sono statiche e predefinite come illustrato.

Per quanto riguarda la priorità, le route vengono elaborate tramite il metodo di corrispondenza di prefisso più lunga (LPM). Pertanto, la route più specifica della tabella si applica a un indirizzo di destinazione specificato.

Pertanto, il traffico destinato a un server, ad esempio DNS01 (10.0.2.4) in locale (10.0.0.0/16) di rete viene indirizzato attraverso la rete virtuale a causa della route 10.0.0.0/16.  Per 10.0.2.4 la route 10.0.0.0/16 è la route più specifica. Questa regola si applica anche se il 10.0.0.0/8 e 0.0.0.0/0 potrebbe anche essere applicabile. Sono meno specifici, tuttavia, in modo che non interessano questo traffico. Il traffico a 10.0.2.4 ha la rete virtuale locale come hop successivo in modo che venga indirizzato alla destinazione.

Ad esempio, la route 10.0.0.0/16 non si applica al traffico destinato a 10.1.1.1. La route di sistema 10.0.0.0/8 è la più specifica in modo che il traffico viene eliminato o "black holed" perché l'hop successivo è Null.

Se la destinazione non è applicabile a qualsiasi prefisso Null o i prefissi VNETLocal, il traffico segue la route meno specifica (0.0.0.0/0). Si viene instradato su internet come hop successivo e all'esterno di perimetro internet di Azure.

Se sono presenti due prefissi identici nella tabella di route, l'ordine di preferenza è basato sull'attributo di origine dell'itinerario:

1. VirtualAppliance: Una Route definita dall'utente aggiunti manualmente alla tabella.
1. VPNGateway: Aggiunta di una Route dinamico (BGP se usato con reti ibride) da un protocollo di rete dinamici. Queste route possono cambiare nel tempo come il protocollo dinamico riflette automaticamente le modifiche in rete con peering.
1. Predefinito: Le route di sistema, la rete virtuale locale e le voci statiche come illustrato nella tabella di route precedente.

> [!NOTE]
> È ora possibile usare routing definito dall'utente (UDR) con i gateway VPN ed ExpressRoute per forzare l'instradamento a un'appliance virtuale di rete (NVA) del traffico in ingresso e in uscita cross-premise.

### <a name="create-local-routes"></a>Creare route locali

Questo esempio usa due tabelle di routing, rispettivamente per le subnet front-end e back-end. In ogni tabella vengono caricate le route statiche appropriate per la subnet specifica. Ai fini di questo esempio, ogni tabella include tre route:

1. Traffico della subnet locale con alcun hop successivo definito. Questa route consente il traffico della subnet locale di ignorare il firewall.
2. Traffico della rete virtuale con un hop successivo definito come firewall. Questa route sostituisce la regola predefinita che consente di traffico della rete virtuale locale per l'indirizzamento diretto.
3. Tutto il traffico rimanente (0/0) con un hop successivo definito come firewall.

Dopo aver create le tabelle di routing, sono associate alle rispettive subnet. La tabella di routing subnet front-end dovrebbe essere simile:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

In questo esempio Usa i comandi seguenti per creare la tabella di route, aggiungere una route definita dall'utente e quindi associare la tabella di route a una subnet. Gli elementi che iniziano con `$`, ad esempio `$BESubnet`, sono variabili definite dall'utente dallo script nella sezione di riferimento.

1. Innanzitutto, creare la tabella di routing di base. Il frammento di codice seguente crea la tabella per la subnet back-end. Lo script seguente crea anche una tabella corrispondente per la subnet front-end.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Dopo aver creato la tabella di route, è possibile aggiungere route definite dall'utente specifiche. Il frammento di codice seguente specifica che tutto il traffico (0.0.0.0/0) viene instradato attraverso l'appliance virtuale. Una variabile `$VMIP[0]` viene usato per passare l'indirizzo IP assegnato quando l'appliance virtuale è stata creata in precedenza nello script. Lo script seguente crea anche una regola corrispondente nella tabella front-end.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. La voce della route precedente esegue l'override di route predefinita "0.0.0.0/0", ma la regola predefinita di 10.0.0.0/16 ancora consente il traffico all'interno della rete virtuale per instradare direttamente alla destinazione e non all'appliance virtuale di rete. Per correggere questo comportamento, è necessario aggiungere la regola seguente:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. A questo punto, è necessario effettuare una scelta. Le due regole precedenti indirizzare tutto il traffico verso il firewall per la valutazione, incluso il traffico all'interno di una singola subnet. È possibile che questo comportamento. In caso contrario, tuttavia, è possibile consentire il traffico all'interno di una subnet di routing locale senza coinvolgere il firewall. Aggiungere un terzo, regola specifica che consente di indirizzare direttamente qualsiasi indirizzo destinato alla subnet locale (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Infine, dopo la tabella di routing viene creata e popolata con le route definite dall'utente, è necessario associare la tabella a una subnet. Il frammento di codice seguente associa la tabella per la subnet back-end. Inoltre, lo script seguente associa la tabella di route front-end alla subnet front-end.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Inoltro IP

L'inoltro IP è una funzionalità complementare delle route definite dall'utente. Questa impostazione in un'appliance virtuale consente di ricevere traffico non indirizzato all'appliance e quindi di inoltrare il traffico alla destinazione finale.

Ad esempio, se il traffico da AppVM01 effettua una richiesta al server DNS01, routing definito dall'utente instrada il traffico al firewall. L'inoltro IP è abilitato, il traffico con la destinazione DNS01 (10.0.2.4) viene accettato dall'appliance firewall (10.0.0.4) e quindi inoltrato alla destinazione finale (10.0.2.4). Senza l'inoltro IP abilitato sul firewall, il traffico non viene accettato dall'appliance anche se la tabella di route il firewall come hop successivo.

> [!IMPORTANT]
> Ricordare di abilitare l'inoltro IP in combinazione con routing definito dall'utente.

L'inoltro IP può essere abilitata con un singolo comando in fase di creazione della macchina virtuale. Si chiama l'istanza di macchina virtuale che è l'appliance virtuale firewall e attiva l'inoltro IP. Tenere presente che gli elementi in rosso che iniziano con `$`, ad esempio `$VMName[0]`, sono variabili definite dall'utente dallo script disponibile nella sezione riferimenti di questo documento. Lo zero tra parentesi quadre, `[0]`, rappresenta la prima macchina virtuale nella matrice di macchine virtuali. Per lo script di esempio funzionare senza alcuna modifica, la prima macchina virtuale (VM 0) deve essere il firewall. In uno script completo, il frammento di codice rilevante è raggruppato con i comandi di route definita dall'utente verso la fine.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

In questo esempio, creare un gruppo di sicurezza di rete (NSG) e caricarla con una singola regola. Nell'esempio viene quindi associato il gruppo di sicurezza solo alle subnet front-end e back-end (non a SecNet). La regola che si caricano in sicurezza di rete è come segue:

* Tutto il traffico (tutte le porte) da internet all'intera rete virtuale (tutte le subnet) è stato negato

Anche se gli Nsg vengono usati in questo esempio, il loro scopo principale consiste in un secondo livello di difesa contro errori di configurazione manuale. Si vuole bloccare tutto il traffico in ingresso da internet verso le subnet front-end o back-end. Il traffico deve solo attraversare la subnet SecNet verso il firewall, dopo il quale il traffico solo appropriato deve ottenere indirizzato alla subnet front-end o back-end. Inoltre, le regole di routing definito dall'utente instradano tutto il traffico che raggiunge le subnet front-end o back-end al firewall. Il firewall lo considera come un flusso asimmetrico ed elimina il traffico in uscita.

Tre livelli di sicurezza per proteggono la subnet front-end e back-end:

1. Nessun endpoint aperto nei servizi cloud FrontEnd001 e BackEnd001
1. Gli Nsg rifiutano il traffico da internet
1. Il firewall rilascia traffico asimmetrico

Uno spunto interessante riguardo il NSG in questo esempio consiste nel fatto che include una sola regola, illustrata di seguito. Questa regola Nega il traffico internet nell'intera rete virtuale, inclusa la subnet di sicurezza.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Perché il NSG è associato solo alle subnet front-end e back-end, non viene applicata la regola per il traffico in ingresso alla subnet di sicurezza. Di conseguenza, il traffico raggiungerà la subnet di sicurezza.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Regole del firewall

È necessario creare le regole di inoltro sul firewall. Poiché il firewall blocca o inoltra tutto il traffico in ingresso e in uscita all'interno-virtual-network, è necessario molte regole del firewall. Inoltre, il firewall deve elaborare tutto il traffico in ingresso verso l'indirizzo IP pubblico del servizio di sicurezza (su porte diverse). Per evitare variazioni in un secondo momento, seguire le procedure consigliate per la creazione di diagrammi flussi logici prima di configurare le subnet e le regole del firewall. La figura seguente è una visualizzazione logica delle regole del firewall per questo esempio:

![Visualizzazione logica delle regole del firewall][2]

> [!NOTE]
> Le porte di gestione variano a seconda di appliance di rete virtuale. Questo esempio mostra Barracuda NextGen Firewall che usa le porte 22, 801 e 807. Consultare la documentazione del fornitore di appliance per trovare precise sulle porte per gestire il dispositivo.

### <a name="logical-rule-description"></a>Descrizione della regola logica

Il diagramma logico raffigurato sopra non mostra la subnet di sicurezza perché il firewall è l'unica risorsa su tale subnet. Questo diagramma illustra le regole del firewall, la modalità consentono o negano logicamente i flussi di traffico, ma non l'effettivo indirizzati percorso. Inoltre, porte esterne selezionate per il traffico remote desktop protocol (RDP) sono porte con intervallo superiore (8014-8026), scelte per facilitarne la lettura per la compatibilità con gli ultimi due ottetti degli indirizzi IP locali. Ad esempio, l'indirizzo del server locale 10.0.1.4 è associato alla porta esterna 8014. Tuttavia, è possibile utilizzare qualsiasi porta superiore non in conflitto.

I seguenti tipi di regole sono necessarie per questo esempio:

* Regole esterne per il traffico in ingresso:
  1. Regola di firewall di gestione: consente il traffico di raggiungere le porte di gestione dell'appliance virtuale di rete.
  2. Regole RDP per ogni server di windows: consente la gestione di singoli server tramite RDP.  A seconda delle funzionalità dell'appliance virtuale di rete, potrebbe essere in grado di aggregare le regole in uno.
  3. Le regole del traffico dell'applicazione: uno per il traffico web front-end e uno per il traffico di back-end (ad esempio, server web al livello dati). La configurazione di queste regole dipende architettura di rete e il traffico di flussi.

     * La prima regola consente il traffico effettivo dell'applicazione di raggiungere il server applicazioni. Diversamente dalle regole per la sicurezza, gestione e così via, le regole delle applicazioni consentono di utenti o servizi esterni accedere alle applicazioni. In questo esempio ha un singolo server web sulla porta 80, che consente a una singola applicazione regola firewall reindirizzare il traffico destinato a un indirizzo IP esterno invece instradare all'indirizzo IP interno del server web. La sessione di traffico reindirizzato viene modificato il mapping da NAT al server interno.
     * La seconda regola del traffico dell'applicazione è la regola back-end per consentire al server web usare qualsiasi porta per instradare il traffico al server AppVM01, ma non per il server AppVM02.

* Regole interne per il traffico all'interno-virtual-network:
  1. Regola internet in uscita verso: consente il traffico da qualsiasi rete di passare alle reti selezionate. Questa regola è in genere un valore predefinito sul firewall, ma in uno stato disabilitato. Attivare questa regola per questo esempio.
  2. Regola DNS: ammette solo il traffico DNS (porta 53) da passare al server DNS. Per questo ambiente la maggior parte del traffico dal front-end e dal back-end è bloccato. Questa regola consente in modo specifico il traffico DNS da qualsiasi subnet locale.
  3. Regola da subnet a subnet: consente a qualsiasi server sulla subnet back-end di connettersi a qualsiasi server sulla subnet front-end, ma non viceversa.

* Regola alternativa per il traffico che non soddisfa i criteri precedenti:
  1. Regola per negare tutto il traffico: sempre la regola finale in termini di priorità. Se il flusso del traffico non corrispondono ad alcuna regola precedente, questa regola blocca. È una regola predefinita. Poiché in genere è attivato, non sono necessarie modifiche.

> [!TIP]
> Nella seconda regola del traffico delle applicazioni, per semplificare questo esempio è consentita qualsiasi porta. In uno scenario reale, è necessario utilizzare porta specifica e gli intervalli di indirizzi per ridurre la superficie di attacco di questa regola.


> [!IMPORTANT]
> Dopo aver creato le regole, è importante esaminare la priorità di ogni regola, per assicurarsi che il traffico è consentito o negato in base alle esigenze. Per questo esempio le regole sono elencate in ordine di priorità. È facile rimanere bloccati fuori dal firewall se le regole sono non ordinate correttamente. Assicurarsi di impostare la regola firewall di gestione come la priorità più alta assoluta.

### <a name="rule-prerequisites"></a>Prerequisiti delle regole

Gli endpoint pubblici sono necessari per la macchina virtuale che esegue il firewall. Questi endpoint pubblici devono essere aperti in modo che il firewall possa elaborare il traffico. Esistono tre tipi di traffico in questo esempio:

1. Traffico di gestione per controllare il firewall e regole del firewall
1. Traffico RDP per controllare i server windows
1. Traffico delle applicazioni

I tipi di traffico vengono visualizzati nella parte superiore del firewall regole diagramma logico precedente.

> [!IMPORTANT]
> Tenere presente che *tutti* arriva il traffico attraverso il firewall. Desktop remoto al server IIS01, è necessario connettersi al firewall sulla porta 8014 e quindi consentire al firewall di instradare la richiesta RDP internamente alla porta RDP a IIS01. Il portale di Azure **Connect** pulsante non funzionerà perché non è un percorso RDP diretto a IIS01 quanto possibile visualizzare il portale. Tutte le connessioni da internet sono per il servizio di sicurezza e una porta (ad esempio secscv001.cloudapp.net:xxxx). Il diagramma precedente per il mapping della porta esterna e indirizzo IP interno e la porta di riferimento.

È possibile aprire un endpoint al momento della creazione della VM o dopo la compilazione. Lo script di esempio e il seguente frammento di codice aprire un endpoint dopo aver creata la macchina virtuale.

Tenere presente che gli elementi che iniziano con `$`, ad esempio `$VMName[$i]`, sono variabili definite dall'utente dallo script nella sezione di riferimento. Il `[$i]` rappresenta il numero di array di una VM specifica in una matrice di macchine virtuali.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Anche se non è chiaramente indicato qui a causa di variabili, è necessario aprire solo gli endpoint del servizio cloud di protezione. Questa precauzione consente di garantire che il firewall gestisce tutto il traffico in ingresso, se è indirizzato, modificare il mapping da NAT o eliminato.

Installare un client di gestione in un computer per gestire il firewall e creare le configurazioni necessarie. Per informazioni dettagliate su come gestire il firewall o altre Appliance virtuale di rete, vedere la documentazione del fornitore. Il resto di questa sezione, nonché **la creazione di regole Firewall** sezione descrivono la configurazione del firewall. Usare il client di gestione del fornitore, non il portale di Azure o PowerShell.

Passare a [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) per scaricare il client di gestione e informazioni su come connettersi al firewall Barracuda.

Dopo che è connessi a firewall, definire gli oggetti di rete e il servizio prima di creare le regole del firewall. Queste due classi di oggetti dei prerequisiti è possono rendere più semplice creare le regole.

In questo esempio definisce tre oggetti di rete denominata per ogni:

* Subnet front-end
* Subnet back-end
* Indirizzo IP del server DNS

Per creare una rete denominata dal dashboard del client Barracuda NG Admin:

1. Andare alla **scheda configurazione**.
1. Selezionare **Ruleset** nel **Operational Configuration** sezione
1. Selezionare **reti** sotto il **Firewall Objects** menu.
1. Selezionare **New** nel **Edit Networks** menu.
1. Creare l'oggetto di rete aggiungendo il nome e il prefisso:

   ![Creare un oggetto di rete di front-ed][3]

La procedura precedente crea una rete denominata per la subnet front-end. Creare un oggetto simile per la subnet back-end anche. Ora è possibile fare riferimento più facilmente alle subnet in base al relativo nome nelle regole del firewall.

Per l'oggetto server DNS:

![Creare un oggetto server DNS][4]

Questo singolo riferimento di indirizzo IP viene usato in una regola DNS più avanti nel documento.

L'altra classe oggetto include gli oggetti di servizi, che rappresentano le porte di connessione RDP per ogni server. L'oggetto servizio RDP esistente è associato alla porta RDP predefinita, 3389. Di conseguenza, è possibile creare nuovi servizi per consentire il traffico verso le porte esterne (8014-8026). È anche possibile aggiungere le nuove porte per il servizio RDP esistente. Tuttavia, per facilitare la dimostrazione, è possibile apportare una singola regola per ogni server. Per creare una nuova regola RDP per un server dal dashboard del client Barracuda NG Admin:

1. Andare alla **scheda configurazione**.
1. Selezionare **Ruleset** nel **Operational Configuration** sezione.
1. Selezionare **Services** sotto il **Firewall Objects** menu.
1. Scorrere verso il basso l'elenco dei servizi e selezionare **RDP**.
1. Pulsante destro del mouse e scegliere Copia, quindi fare doppio clic su e scegliere Incolla.
1. È ora un oggetto servizio RDP-Copy1 che può essere modificato. Fare doppio clic su **RDP-Copy1** e selezionare **modificare**.
1. Il **Edit Service Object** finestra viene visualizzata come illustrato di seguito:

   ![Copiare la regola RDP predefinita][5]

1. Modificare i valori per rappresentare il servizio RDP per un server specifico. Per AppVM01, la regola RDP predefinita deve essere modificata per riflettere un nuovo servizio **Name**, **descrizione**e la porta RDP esterna usati nel diagramma della figura 8. Tenere presente che le porte vengono modificate da quello predefinito RDP 3389 alla porta esterna per questo server specifico. Ad esempio, la porta esterna per AppVM01 è 8025. La regola del servizio modificato è illustrata di seguito:

   ![Regola AppVM01][6]

Ripetere questo processo per creare i servizi RDP per i server rimanenti: AppVM02, DNS01 e IIS01. Questi servizi verificare le regole nella sezione successiva più ovvie e più semplice da creare.

> [!NOTE]
> Un servizio RDP per il firewall non è necessaria perché il firewall della macchina virtuale è un'immagine basata su Linux, pertanto viene usato SSH sulla porta 22 per la gestione delle macchine Virtuali invece di RDP. Inoltre, la porta 22 e due altre porte sono consentite per la connettività di gestione. Vedere le **regola Firewall gestione** nella sezione successiva.

### <a name="firewall-rules-creation"></a>Creazione di regole firewall

Esistono tre tipi di regole del firewall usate in questo esempio, tutti con icone distinte:

La regola di reindirizzamento dell'applicazione: ![Icona di Application Redirect][7]

La regola NAT di destinazione: ![Icona di Destination NAT][8]

Regola pass: ![Icona di Pass][9]

Altre informazioni su queste regole sono reperibile presso il sito Web Barracuda.

Per creare le regole seguenti o verificare le regole predefinite esistenti:

1. Dal dashboard del client Barracuda NG Admin, passare al **configurazione** scheda.
1. Nel **Operational Configuration** sezione, selezionare **Ruleset**.
1. Il **Main Rules** griglia Mostra esistenti attive e disattivate le regole in questo firewall. Selezionare il colore verde **+** nell'angolo superiore destro per creare una nuova regola. Se il firewall è bloccato per le modifiche, viene visualizzato un pulsante contrassegnato **blocco** e non è possibile creare o modificare le regole. Selezionare il **blocco** per sbloccare il set di regole e consentire la modifica. Fare doppio clic su una regola che si desidera modificare e selezionare **Modifica regola**.

Dopo aver creato o modificare le regole, eseguirne il push al firewall e attivarli. In caso contrario, le modifiche alle regole non avranno effetto. Il processo di push e attivazione è descritto nella [attivazione della regola](#rule-activation).

Di seguito sono le specifiche di ogni regola necessaria per completare questo esempio:

* **Regola del firewall gestione**: Questa regola di reindirizzamento dell'app consente il traffico di raggiungere le porte di gestione dell'appliance di rete virtuale, in questo esempio Barracuda NextGen Firewall. Le porte di gestione sono 801, 807 e facoltativamente 22. Le porte interne ed esterne sono gli stessi, nessuna conversione di porta. Questa regola viene chiamata il programma di installazione-MGMT-ACCESS. È una regola predefinita e abilitata per impostazione predefinita in Barracuda NextGen Firewall versione 6.1.
  
    ![Regola di gestione del firewall][10]

  > [!TIP]
  > Lo spazio degli indirizzi di origine in questa regola viene **qualsiasi**. Se gli intervalli di indirizzi IP di gestione sono noti, riducendo l'ambito riduce anche la superficie di attacco per le porte di gestione.

* **Regole RDP**:  Queste regole NAT di destinazione consentono la gestione di singoli server tramite RDP. I campi critici per queste regole sono:
  * Origine. Per consentire il protocollo RDP da qualsiasi posizione, usare il riferimento **qualsiasi** nel campo di origine.
  * Service. Usare l'oggetto servizio RDP creato in precedenza: **AppVM01 RDP**. Le porte esterne reindirizzano a indirizzo IP locale del server e alla porta RDP predefinita 3386. Questa regola specifica riguarda l'accesso RDP ad AppVM01.
  * Destinazione. Usare la porta nel firewall locale: **DCHP 1 Local IP** oppure **eth0** se si usano indirizzi IP statici. Il numero ordinale (eth0, eth1 e così via) potrebbero essere diverso se il dispositivo di rete ha più interfacce locali. Il firewall Usa questa porta per l'invio e potrebbe essere la stessa porta di ricezione. La destinazione di routing effettiva è il **elenco di destinazione** campo.
  * Reindirizzamento. Configurare per indicare a quale verrà infine il traffico all'appliance virtuale. Il reindirizzamento più semplice consiste nell'inserire l'indirizzo IP nel campo Target List. È inoltre possibile specificare la porta e la porta sia l'indirizzo IP in modo che segua NAT. Se non si specifica una porta, l'appliance virtuale Usa la porta di destinazione nella richiesta in ingresso.

    ![Regola firewall RDP][11]

    Creare quattro regole RDP:

    | Nome regola | Server | Service | Target List |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > Limitare l'ambito dei campi di origine e di servizio consente di ridurre la superficie di attacco. Usare l'ambito più limitato che consente di funzionalità.

* **Le regole del traffico dell'applicazione**: Esistono due regole del traffico dell'applicazione. Uno è per il traffico web front-end. L'altra riguarda il traffico di back-end, ad esempio server web al livello dati. Queste regole dipendono sull'architettura di rete e flussi di traffico.
  
  * La regola front-end per il traffico web:
  
    ![Regola firewall Web][12]
  
    Questa regola Destination NAT consente il traffico effettivo dell'applicazione di raggiungere il server applicazioni. Diversamente dalle regole di sicurezza, gestione e così via, le regole delle applicazioni consentono di utenti o servizi esterni accedere alle applicazioni. In questo esempio ha un singolo server web sulla porta 80, che consente a una singola applicazione regola firewall reindirizzare il traffico destinato a un indirizzo IP esterno invece instradare all'indirizzo IP interno del server web. La sessione di traffico reindirizzato viene modificato il mapping da NAT al server interno.

    > [!NOTE]
    > Nessuna porta assegnata nel **elenco di destinazione** campo. Pertanto, nel reindirizzamento del server web viene utilizzata la porta in ingresso (80 o 443 per il servizio selezionato). Se il server web è in ascolto su una porta diversa, ad esempio 8080, è possibile aggiornare il campo Target List in 10.0.1.4: 8080 per consentire anche il reindirizzamento della porta.
  
  * La regola back-end consente al server web di comunicare con il server AppVM01, ma non con AppVM02, tramite **qualsiasi** servizio:
  
    ![Regola firewall AppVM01][13]
  
    Questa regola pass consente a un server IIS sulla subnet front-end di raggiungere AppVM01 (10.0.2.5) su qualsiasi porta con qualunque protocollo e in modo che i dati sono accessibili dall'applicazione web.
  
    In questo screenshot `<explicit-dest>` viene utilizzata per il **destinazione** campo per indicare 10.0.2.5 come destinazione. È possibile specificare l'indirizzo IP in modo esplicito, come illustrato nello screenshot. È anche possibile usare un oggetto di rete denominato, ad esempio nei prerequisiti per il server DNS. L'amministratore di firewall può scegliere il metodo da usare. Per aggiungere 10.0.2.5 come destinazione esplicita, fare doppio clic sulla prima riga vuota sotto `<explicit-dest>` e immettere l'indirizzo nella finestra di dialogo visualizzata.
  
    Con questa regola pass, nessun NAT è necessaria perché sta gestendo il traffico interno. È possibile impostare il **Connection Method** a `No SNAT`.
  
    > [!NOTE]
    > La rete di origine in questa regola è qualsiasi risorsa nella subnet front-end se è presente una sola. Se l'architettura specifica un numero noto di server web, è possibile creare una risorsa oggetto di rete per essere più specifici per gli indirizzi IP esatti, anziché dell'intera subnet front-end.

    > [!TIP]
    > Questa regola utilizza il servizio **qualsiasi** per rendere più semplice configurare e usare l'applicazione di esempio. Consente a ICMPv4 (ping) in una singola regola. Tuttavia, per ridurre l'attacco superficie oltre questo limite, è consigliabile limitare i servizi di porte e protocolli al minimo possibile che consentono il funzionamento dell'applicazione.

    > [!TIP]
    > Anche se questa regola di esempio utilizza `<explicit-dest>` riferimento, è consigliabile usare un approccio coerente in tutta la configurazione del firewall. Si consiglia di usare un oggetto di rete denominato per semplificare la leggibilità e al supporto. Il `<explicit-dest>` qui è solo per illustrare un metodo alternativo di riferimento. È in genere non consigliabile, in particolare per configurazioni complesse.

* **Regola in uscita a internet**: Questa regola pass consente il traffico proveniente da qualsiasi rete di origine per passare a reti di destinazione selezionate. Barracuda NextGen firewall ha in genere questa regola di "on" per impostazione predefinita, ma in uno stato disabilitato. Fare clic su questa regola di accesso di **Activate Rule** comando. Modificare la regola illustrata nella schermata per aggiungere gli oggetti di rete per la subnet back-end e front-end per l'attributo di origine di questa regola. Questi oggetti di rete creata nella sezione dei prerequisiti di questo articolo.
  
    ![Regola firewall in uscita][14]

* **Regola DNS**: Questa regola pass consente solo il traffico DNS (porta 53) da passare al server DNS. Per questo ambiente, la maggior parte delle traffico dal front-end al back-end è bloccato in modo che questa regola consente in modo specifico il traffico DNS.
  
    ![Regola firewall DNS][15]
  
    > [!NOTE]
    > Il **Connection Method** è impostata su `No SNAT` perché questa regola è per indirizzo IP interno per il traffico di indirizzo IP interno ed è previsto alcun reindirizzamento tramite NAT non è obbligatorio.

* **Regola da subnet a subnet**: Questa regola pass predefinita è attivata e modificata per consentire a qualsiasi server sulla subnet back-end di connettersi a qualsiasi server sulla subnet front-end. Questa regola coves solo il traffico interno in modo che il **Connection Method** può essere impostato su `No SNAT`.

    ![Regola firewall Intra-VNet][16]
  
    > [!NOTE]
    > Il **Bi-directional** casella di controllo non è selezionata qui in modo che questa regola si applica solo in una direzione. Una connessione può essere avviata dalla subnet back-end per la rete di front-end, ma non viceversa. Se la casella di controllo selezionata, questa regola consente il traffico bidirezionale, che è stato indicato come indesiderati nel nostro diagramma logico.

* **Regola per negare tutto il traffico**: Questa regola deve essere sempre la regola finale in termini di priorità. Se il flusso del traffico non corrisponde a uno qualsiasi di queste regole, questa regola ne determina che si desidera eliminare. La regola viene comunemente attivata per impostazione predefinita in modo che non sono necessarie modifiche.
  
    ![Regola firewall Deny][17]

> [!IMPORTANT]
> Dopo che tutte le regole precedenti vengono create, esaminare la priorità di ogni regola, per assicurarsi che il traffico è consentito o negato in base alle esigenze. Per questo esempio, le regole sono elencate nell'ordine in che cui dovrebbero apparire nella griglia principale del client Barracuda gestione di regole di inoltro.

## <a name="rule-activation"></a>Attivazione delle regole

Dopo aver modificato il set di regole per soddisfare le specifiche del diagramma per la logica, è necessario caricare il set di regole del firewall e attivarlo.

![Attivazione delle regole firewall][18]

Cerca in alto a destra della finestra del client di gestione e selezionare **Send Changes** per caricare le regole modificate al firewall. Selezionare **Attiva**.

Quando si attiva il set di regole firewall, in questo ambiente di esempio è stato completato.

## <a name="traffic-scenarios"></a>Scenari di traffico

> [!IMPORTANT]
> Tenere presente che *tutti* arriva il traffico attraverso il firewall. Desktop remoto al server IIS01, è necessario connettersi al firewall sulla porta 8014 e quindi consentire al firewall di instradare la richiesta RDP internamente alla porta RDP a IIS01. Il portale di Azure **Connect** pulsante non funzionerà perché non è un percorso RDP diretto a IIS01 quanto possibile visualizzare il portale. Tutte le connessioni da internet sono per il servizio di sicurezza e una porta (ad esempio secscv001.cloudapp.net:xxxx). Il diagramma precedente per il mapping della porta esterna e indirizzo IP interno e la porta di riferimento.

Per questi scenari devono essere attive le regole del firewall seguenti:

1. Firewall Management (FW Mgmt)
2. RDP to IIS01
3. RDP to DNS01
4. RDP to AppVM01
5. RDP to AppVM02
6. App Traffic to the Web
7. App Traffic to AppVM01
8. In uscita a internet
9. Front-end a DNS01.
10. Intra-Subnet Traffic (solo da back-end a front-end)
11. Deny All

Il set di regole firewall effettivo richiederà probabilmente più regole rispetto a quelle in questo esempio. È probabile che abbiano anche i numeri di priorità diversa. È consigliabile consultare questo elenco e i numeri associati per la relativa priorità tra loro. Ad esempio, la regola "RDP to IIS01" potrebbe essere la regola numero 5 nel firewall effettivo, ma, purché riportato di seguito "Firewall Management" e sopra il "RDP to DNS01" della regola, il set viene allineato con l'intenzione di questo elenco. Questo elenco consente inoltre di semplificare le istruzioni per gli scenari seguenti. Ad esempio, "regola Firewall 9 (DNS)." Tenere presente che le quattro regole RDP sono collettivamente denominate "regole RDP" quando lo scenario di traffico è correlato a RDP.

Tenere presente inoltre che i gruppi di sicurezza di rete (Nsg) siano in atto per il traffico internet in ingresso nella subnet front-end e back-end.

### <a name="allowed-internet-to-web-server"></a>(Consentito) Internet al server web

1. Un utente internet richiede una pagina HTTP a secsvc001.cloudapp.NET (servizio cloud con connessione internet).
1. Il servizio cloud passa il traffico attraverso un endpoint aperto sulla porta 80 all'interfaccia di firewall su 10.0.0.4: 80.
1. Nessun gruppo di sicurezza di rete viene assegnato a subnet di sicurezza in modo che le regole di sicurezza di rete del sistema consentano il traffico al firewall.
1. Il traffico raggiunge un indirizzo IP interno del firewall (10.0.1.4).
1. Il firewall esegue l'elaborazione delle regole:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Non si applicano le regole del firewall 2 a 5 (regole RDP). Passa alla regola successiva.
   1. Regola firewall 6 (App: Applicazione Web). Il traffico è consentito. Firewall reindirizza il traffico tramite NAT a 10.0.1.4 (IIS01).
1. La subnet front-end esegue l'elaborazione delle regole in ingresso:
   1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Il firewall reindirizzato il traffico tramite NAT in modo che l'indirizzo di origine ora è il firewall. Poiché il firewall si trova nella subnet di sicurezza e viene visualizzato come il NSG della subnet front-end del traffico locale, il traffico è consentito. Passa alla regola successiva.
   1. Regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet in modo che il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. Iis01 è in ascolto per il traffico web. Riceve la richiesta e inizia l'elaborazione della richiesta.
1. Iis01 prova ad avviare una sessione FTP per AppVM01 sulla subnet back-end.
1. La route UDR nella subnet front-end Usa il firewall per l'hop successivo.
1. Non sono presenti regole in uscita sulla subnet front-end in modo che il traffico è consentito.
1. Il firewall inizia l'elaborazione delle regole:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Non si applicano le regole del firewall 2 a 5 (regole RDP). Passa alla regola successiva.
   1. Regola firewall 6 (App: Applicazioni Web) non è valida. Passa alla regola successiva.
   1. Regola firewall 7 (App: back-end) è applicabile. Il traffico è consentito. Il firewall inoltra il traffico a 10.0.2.5 (AppVM01).
1. La subnet back-end esegue l'elaborazione delle regole in ingresso:
    1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Passa alla regola successiva.
    1. Regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet. Il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. AppVM01 riceve la richiesta, avvia la sessione e risponde.
1. La route UDR nella subnet back-end Usa il firewall per l'hop successivo.
1. Esistono regole NSG in uscita sulla subnet back-end in modo che la risposta è consentita.
1. Poiché traffico restituito su una sessione stabilita, il firewall restituisce la risposta al server web (IIS01).
1. Subnet front-end esegue l'elaborazione delle regole in ingresso:
    1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Passa alla regola successiva.
    1. Le regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet in modo che il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. Il server IIS riceve la risposta e completa la transazione con AppVM01. Il server completa la compilazione della risposta HTTP e lo invia al richiedente.
1. Esistono regole NSG in uscita sulla subnet front-end in modo che la risposta è consentita.
1. La risposta HTTP raggiunge il firewall. Poiché si tratta di una risposta a una sessione NAT stabilita, il firewall lo accetta.
1. Il firewall reindirizza la risposta all'utente internet.
1. Non esistono regole di sicurezza di rete in uscita o hop delle route definite dall'utente nella subnet front-end in modo che la risposta è consentita. L'utente internet riceve la pagina web richiesta.

### <a name="allowed-internet-rdp-to-back-end"></a>(Consentito) RDP Internet a back-end

1. Un amministratore del server su internet richiede una sessione RDP ad AppVM01 tramite SecSvc001.CloudApp.Net:8025. 8025 è il numero di porta assegnata dall'utente per la regola del firewall 4 (AppVM01 RDP).
1. Il servizio cloud passa il traffico attraverso l'endpoint aperto sulla porta 8025 all'interfaccia del firewall su 10.0.0.4: 8025.
1. Nessun gruppo di sicurezza di rete viene assegnato a subnet di sicurezza in modo che le regole NSG sistema consentano il traffico al firewall.
1. Il firewall esegue l'elaborazione delle regole:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Regola del firewall 2 (IIS RDP) non è applicabile. Passa alla regola successiva.
   1. Regola del firewall 3 (DNS01 RDP) non è applicabile. Passa alla regola successiva.
   1. Regola del firewall 4 (AppVM01 RDP) è applicabile in modo che il traffico è consentito. Il firewall in modo che segua lo tramite NAT a 10.0.2.5: 3386 (porta RDP su AppVM01).
1. La subnet back-end esegue l'elaborazione delle regole in ingresso:
   1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Il firewall reindirizzato il traffico tramite NAT in modo che l'indirizzo di origine ora è il firewall che fa parte della subnet di sicurezza. È rilevato dal NSG della subnet back-end come traffico locale e si è consentito. Passa alla regola successiva.
   1. Regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet in modo che il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. AppVM01 è in ascolto per il traffico RDP e risponde.
1. Esistono regole NSG in uscita in modo che si applicano le regole predefinite. È consentito il traffico di ritorno.
1. Route definite dall'utente instrada il traffico in uscita al firewall come hop successivo.
1. Poiché traffico restituito su una sessione stabilita, il firewall restituisce la risposta all'utente internet.
1. Una sessione RDP è abilitata.
1. AppVM01 richiede nome utente password.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Consentito) Ricerca DNS nel server Web sul server DNS

1. Server Web IIS01 richiede un fornitura dei dati in http\:\/\/www.data.gov, ma deve risolvere l'indirizzo.
1. La configurazione di rete per gli elenchi di rete virtuale DNS01 (10.0.2.4 sulla subnet back-end) come server DNS primario. Iis01 invia la richiesta DNS a DNS01.
1. Route definite dall'utente instrada il traffico in uscita al firewall come hop successivo.
1. Non essendoci regole del gruppo sicurezza di rete sono associate alle subnet front-end. Il traffico è consentito.
1. L'elaborazione della regola eseguita dal firewall:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Regola del firewall 2 a 5 (regole RDP) non sono applicabili. Passa alla regola successiva.
   1. Non si applicano le regole firewall 6 e 7 (regole App). Passa alla regola successiva.
   1. Non è applicabile la regola firewall 8 (a internet). Passa alla regola successiva.
   1. Applicare la regola firewall 9 (DNS). Il traffico è consentito. Firewall inoltra il traffico a 10.0.2.4 (DNS01).
1. La subnet back-end esegue l'elaborazione delle regole in ingresso:
   1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Passa alla regola successiva.
   1. Regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet. Il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. Il server DNS riceve la richiesta.
1. Il server DNS non ha l'indirizzo memorizzato nella cache e richiede un server DNS radice su internet.
1. Route definite dall'utente instrada il traffico in uscita al firewall come hop successivo.
1. Sono disponibili regole di sicurezza di rete in uscita sulla subnet back-end per il traffico non è consentito.
1. L'elaborazione della regola eseguita dal firewall:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Regola del firewall 2 a 5 (regole RDP) non sono applicabili. Passa alla regola successiva.
   1. Non si applicano le regole firewall 6 e 7 (regole App). Passa alla regola successiva.
   1. Applicare la regola firewall 8 (a internet). Il traffico è consentito. La sessione viene reindirizzata tramite SNAT al server DNS radice su internet.
1. Il DNS internet risponde server. In questa sessione è stata avviata dal firewall in modo che la risposta viene accettata dal firewall.
1. In questa sessione è già presente in modo che il firewall inoltra la risposta al server di origine, DNS01.
1. La subnet back-end esegue l'elaborazione delle regole in ingresso:
    1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Passa alla regola successiva.
    1. Regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet per il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. Il server DNS riceve e memorizza nella cache la risposta e quindi risponda alla richiesta iniziale a IIS01.
1. La route UDR nella subnet back-end Usa il firewall per l'hop successivo.
1. Non essendoci regole del gruppo sicurezza di rete presenti sulla subnet back-end in modo che il traffico è consentito.
1. In questa sessione è già presente nel firewall in modo che il firewall in modo che segua la risposta al server IIS.
1. La subnet front-end esegue l'elaborazione delle regole in ingresso:
    1. Non sono presenti regole NSG per il traffico in ingresso dalla subnet back-end alla subnet front-end in modo che nessuna delle regole NSG si applicano.
    1. La regola di sistema predefinita consente il traffico tra subnet. Il traffico è consentito.
1. Iis01 riceve la risposta da DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Consentito) Server di back-end al server front-end

1. Un amministratore connesso ad AppVM02 tramite RDP richiede un file direttamente dal server IIS01 tramite Esplora file.
1. La route UDR nella subnet back-end Usa il firewall per l'hop successivo.
1. Esistono regole NSG in uscita sulla subnet back-end in modo che la risposta è consentita.
1. Il firewall esegue l'elaborazione delle regole:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Regola del firewall 2 a 5 (regole RDP) non sono applicabili. Passa alla regola successiva.
   1. Non si applicano le regole firewall 6 e 7 (regole App). Passa alla regola successiva.
   1. Non è applicabile la regola firewall 8 (a internet). Passa alla regola successiva.
   1. Regola firewall 9 (DNS) non è applicabile. Passa alla regola successiva.
   1. Applicare la regola firewall 10 (tra Subnet). Il traffico è consentito. Il firewall passa il traffico a 10.0.1.4 (IIS01).
1. La subnet front-end inizia l'elaborazione delle regole in ingresso:
   1. Regola di sicurezza di rete 1 (blocco internet) non applicabile, passa alla regola successiva.
   1. Le regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet in modo che il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. Supponendo che l'autorizzazione e l'autenticazione appropriata, IIS01 accetta la richiesta e risponde.
1. La route UDR nella subnet front-end Usa il firewall per l'hop successivo.
1. Esistono regole NSG in uscita sulla subnet front-end in modo che la risposta è consentita.
1. Questa sessione esiste già nel firewall in modo che la risposta è consentita. Il firewall restituisce la risposta ad AppVM02.
1. La subnet back-end esegue l'elaborazione delle regole in ingresso:
    1. Non è applicabile la regola di sicurezza di rete 1 (blocco internet). Passa alla regola successiva.
    2. Le regole di sicurezza di rete predefinite consentono il traffico da subnet a subnet in modo che il traffico è consentito. Arrestare l'elaborazione delle regole di sicurezza di rete.
1. AppVM02 riceve la risposta.

### <a name="denied-internet-direct-to-web-server"></a>(Negato) Internet diretto al server web

1. Un utente internet prova ad accedere al server web IIS01 tramite il servizio FrontEnd001.CloudApp.Net.
1. Non esistono Nessun endpoint aperto per il traffico HTTP in modo che il traffico non passa attraverso il servizio cloud. Il traffico non raggiunga il server.
1. Se gli endpoint sono aperti per qualche motivo, la sicurezza di rete (blocco internet) sulla subnet front-end consente di bloccare il traffico.
1. Infine, la route UDR subnet front-end invia tutto il traffico in uscita da IIS01 al firewall come hop successivo. Il firewall lo considera come traffico asimmetrico ed elimina la risposta in uscita.

>Di conseguenza, esistono almeno tre livelli di difesa tra internet e IIS01 indipendenti. Il servizio cloud impedisce l'accesso non autorizzato o non appropriato.

### <a name="denied-internet-to-back-end-server"></a>(Negato) Internet al server back-end

1. Un utente internet prova ad accedere a un file in AppVM01 tramite il servizio BackEnd001.CloudApp.Net.
2. Non esistono Nessun endpoint aperto per la condivisione di file in modo che questa richiesta non soddisfa il servizio cloud. Il traffico non raggiunga il server.
3. Se gli endpoint sono aperti per qualche motivo, la sicurezza di rete (blocco internet) consente di bloccare il traffico.
4. Infine, il routing invia tutto il traffico in uscita da AppVM01 al firewall come hop successivo. Il firewall lo considera come traffico asimmetrico ed elimina la risposta in uscita.

> Di conseguenza, esistono almeno tre livelli di difesa tra internet e AppVM01 indipendenti. Il servizio cloud impedisce l'accesso non autorizzato o non appropriato.

### <a name="denied-front-end-server-to-back-end-server"></a>(Negato) Server front-end al server back-end

1. Iis01 è compromesso ed esegua codice dannoso tentando di analizzare il server sulla subnet back-end.
1. La route UDR subnet front-end invia tutto il traffico in uscita da IIS01 al firewall come hop successivo. Macchina virtuale compromessa non è possibile modificare il routing.
1. Il firewall elabora il traffico. Se la richiesta ad AppVM01 o al server DNS per le ricerche DNS, il firewall potrebbe essere potenzialmente in grado di consentire il traffico a causa delle regole Firewall 7 e 9. Tutto il traffico è bloccato dalla regola Firewall 11 (Deny All).
1. Se si abilita il rilevamento delle minacce avanzato sul firewall, il traffico che contiene firme note o modelli che contrassegnano una regola avanzata dalle minacce potrebbe non essere consentito. Questa misura può funzionare anche se il traffico è consentito secondo le regole di inoltro di base illustrati in questo articolo. Rilevamento avanzato delle minacce non viene trattato in questo documento. Vedere la documentazione del fornitore per l'appliance di rete specifiche funzionalità di minacce avanzate.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negato) Ricerca DNS Internet sul server DNS

1. Un utente internet prova a cercare un record DNS interno su DNS01 tramite il servizio BackEnd001.CloudApp.Net.
1. Poiché non esistono Nessun endpoint aperto per il traffico DNS, questo traffico non passa attraverso il servizio cloud. Questa non raggiungere il server.
1. Se gli endpoint sono aperti per qualche motivo, la regola di sicurezza di rete (blocco internet) sulla subnet front-end consente di bloccare il traffico.
1. Infine, la subnet back-end routing definito dall'utente invia il traffico in uscita da DNS01 al firewall come hop successivo. Il firewall considera questo traffico asimmetrico ed elimina la risposta in uscita.

> Di conseguenza, esistono almeno tre livelli di difesa tra internet e DNS01 indipendenti. Il servizio cloud impedisce l'accesso non autorizzato o non appropriato.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negato) Internet per l'accesso a SQL tramite firewall

1. Un utente internet richiede dati SQL dal servizio cloud con connessione internet SecSvc001.CloudApp.Net.
1. Non sono presenti endpoint aperti per SQL in modo che il traffico non passa al servizio cloud. Non raggiunge il firewall.
1. Se gli endpoint SQL sono aperti per qualche motivo, il firewall esegue l'elaborazione delle regole:
   1. Regola del firewall 1 (FW Mgmt) non è applicabile. Passa alla regola successiva.
   1. Non si applicano le regole del firewall 2 a 5 (regole RDP). Passa alla regola successiva.
   1. Non si applicano le regole firewall 6 e 7 (regole applicazione). Passa alla regola successiva.
   1. Non è applicabile la regola firewall 8 (a internet). Passa alla regola successiva.
   1. Regola firewall 9 (DNS) non è applicabile. Passa alla regola successiva.
   1. Regola firewall 10 (tra Subnet) non è applicabile. Passa alla regola successiva.
   1. Applicare la regola firewall 11 (Deny All). Il traffico è bloccato. Arrestare l'elaborazione della regola.

## <a name="references"></a>Riferimenti

In questa sezione contiene gli elementi seguenti:

* Script completo. Salvarlo in un file di script di PowerShell.
* Configurazione di rete. Salvarlo in un file denominato NetworkConf2.xml.

Modificare le variabili definite dall'utente nei file in base alle esigenze. Eseguire lo script e quindi seguire le istruzioni di installazione della regola Firewall elencate in precedenza in questo articolo.

### <a name="full-script"></a>Script completo

Dopo aver impostato le variabili definite dall'utente, eseguire questo script per:

1. Connettersi a una sottoscrizione di Azure
1. Creare un nuovo account di archiviazione.
1. Creare una nuova rete virtuale e tre subnet, come definito nel file di configurazione di rete
1. Compilare cinque macchine virtuali: un firewall e quattro macchine virtuali Windows Server
1. Configurare route definite dall'utente:
   1. Creare due nuove tabelle di route
   1. Aggiunta di route alle tabelle.
   1. Associazione di tabelle alle subnet appropriate.
1. Abilitare l'inoltro IP sul dispositivo virtuale di rete.
1. Configurare NSG:
   1. Creare un gruppo di sicurezza
   1. Aggiungere una regola
   1. Associare il NSG a subnet appropriate.

Eseguire questo PowerShell script in locale su un internet connesso PC o server.

> [!IMPORTANT]
> Quando si esegue questo script, avvisi o altri messaggi informativi potrebbe essere visualizzato in PowerShell. Solo i messaggi di errore rossa sono motivo di preoccupazione.

```powershell
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
       - IP Forwarding from the FireWall out to the internet
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

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
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
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
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
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
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

      # Associate the Route Tables with the Subnets
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
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>File di configurazione di rete

Salvare questo file XML con il percorso aggiornato. Modifica il `$NetworkConfigFile` variabili nello script completo precedente il collegamento al file di configurazione di rete salvata.

```xml
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
```

## <a name="next-steps"></a>Passaggi successivi

È possibile installare un'applicazione di esempio per semplificare questo esempio di rete perimetrale.

> [!div class="nextstepaction"]
> [Script di applicazione di esempio](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Rete perimetrale bidirezionale con appliance virtuale di rete, gruppo di sicurezza di rete e routing definito dall'utente"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Visualizzazione logica delle regole del firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Creare un oggetto di rete front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Creare un oggetto server DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copiare la regola RDP predefinita"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regola AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icona di reindirizzamento dell'applicazione"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icona di Destination NAT"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icona di passaggio"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regola di gestione del firewall"
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
