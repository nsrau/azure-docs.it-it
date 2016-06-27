<properties
   pageTitle="Esempio di rete perimetrale: Creare una rete perimetrale per proteggere le applicazioni con un firewall e gruppi di sicurezza di rete | Microsoft Azure"
   description="Creare una rete perimetrale con un firewall e gruppi di sicurezza di rete"
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

# Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e gruppi di sicurezza di rete

[Tornare alla pagina relativa alle procedure consigliate sui limiti di sicurezza][HOME]

Questo esempio illustra come creare una rete perimetrale con un firewall, quattro server Windows e gruppi di sicurezza di rete. Illustra in dettaglio anche ogni comando rilevante per favorire una comprensione più approfondita di ogni passaggio. È disponibile anche una sezione sugli scenari di traffico con istruzioni dettagliate sul percorso seguito dal traffico attraverso i livelli di difesa della rete perimetrale. La sezione Riferimenti, infine, include tutto il codice e istruzioni complete per creare l'ambiente per testare e sperimentare vari scenari.

![Rete perimetrale in ingresso con dispositivo virtuale di rete e gruppo di sicurezza di rete][1]

## Descrizione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

- Due servizi cloud, "FrontEnd001" e "BackEnd001"
- Una rete virtuale, "CorpNetwork", con due subnet: "FrontEnd" e "BackEnd"
- Un singolo gruppo di sicurezza di rete applicato a entrambe le subnet.
- Un dispositivo virtuale di rete, in questo esempio Barracuda NextGen Firewall, connesso alla subnet FrontEnd
- Un server Windows che rappresenta un server Web applicazioni ("IIS01").
- Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
- Un server Windows che rappresenta un server DNS ("DNS01").

>[AZURE.NOTE] Anche se in questo esempio si usa Barracuda NextGen Firewall, possono essere usati molti altri dispositivi virtuali di rete.

Nella sezione Riferimenti alla fine dell'articolo è disponibile uno script di PowerShell per creare la maggior parte dell'ambiente descritto sopra. La creazione di macchine virtuali e reti virtuali, anche se eseguita dallo script di esempio, non è descritta in dettaglio in questo documento.
 
Per creare l'ambiente, eseguire queste operazioni:

  1.	Salvare il file XML di configurazione di rete incluso nella sezione Riferimenti, aggiornandolo con i nomi, il percorso e gli indirizzi IP corrispondenti allo scenario specifico.
  2.	Aggiornare le variabili utente incluse nello script in modo che corrispondano all'ambiente in cui lo script verrà eseguito, ad esempio sottoscrizioni, nomi dei servizi e così via.
  3.	Eseguire lo script in PowerShell.

**Nota**: l'area indicata nello script di PowerShell deve corrispondere all'area indicata nel file XML di configurazione di rete.

Dopo l'esecuzione corretta dello script, si potranno eseguire i passaggi successivi allo script seguenti:

1.	Configurare le regole del firewall illustrate nella sezione seguente intitolata Regole del firewall.
2.	Facoltativamente, nella sezione Riferimenti sono disponibili due script per configurare il server Web e il server applicazioni per consentire l'esecuzione dei test con questa configurazione della rete perimetrale.

La sezione successiva descrive la maggior parte delle istruzioni dello script relativamente ai gruppi di sicurezza di rete.

## Gruppi di sicurezza di rete
Per questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono poi caricate sei regole.

>[AZURE.TIP] In genere, è consigliabile creare prima di tutto le regole specifiche di tipo "Consenti" e infine le regole di tipo "Nega" più generiche. La priorità assegnata determina quali regole vengono valutate per prime. Quando si rileva che al traffico è applicabile una determinata regola, non vengono valutate altre regole. Le regole del gruppo di sicurezza di rete possono essere applicate nella direzione in ingresso o in uscita, dal punto di vista della subnet.

A livello dichiarativo, per il traffico in ingresso vengono create le righe seguenti:

1.	Il traffico DNS interno (porta 53) è consentito.
2.	Il traffico RDP (porta 3389) da Internet a qualsiasi VM è consentito.
3.	Il traffico HTTP (porta 80) da Internet al dispositivo virtuale di rete (firewall) è consentito.
4.	Tutto il traffico (tutte le porte) da IIS01 ad AppVM1 è consentito.
5.	Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) viene bloccato.
6.	Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene bloccato.

Con queste regole associate a ogni subnet, se una richiesta HTTP proviene da Internet verso il server Web, le regole 3 (consenti) e 5 (nega) saranno applicabili, ma poiché la regola 3 ha una priorità maggiore, verrà applicata solo tale regola e la regola 5 non verrà presa in considerazione. La richiesta HTTP verrà quindi consentita sul firewall. Se lo stesso traffico prova a raggiungere il server DNS01, la regola 5 (nega) sarà la prima applicabile e il traffico non sarà autorizzato a passare al server. La regola 6 (nega) impedisce alla subnet front-end di comunicare con la subnet back-end (ad eccezione del traffico consentito nelle regole 1 e 4), proteggendo la rete back-end nel caso in cui un utente malintenzionato comprometta l'applicazione Web sul front-end. L'utente malintenzionato avrà infatti accesso limitato alla rete "protetta" back-end, ovvero solo alle risorse esposte nel server AppVM01.

Esiste una regola in uscita predefinita che consente il traffico in uscita verso Internet. Per questo esempio si consente il traffico in uscita e non si modificano le regole in uscita. Per bloccare il traffico in entrambe le direzioni, è richiesto il routing definito dall'utente. Questo aspetto viene esaminato in un esempio diverso, disponibile nel [documento sui principali limiti della sicurezza][HOME].

Le regole per i gruppi di sicurezza di rete illustrate sopra sono molto simili a quelle descritte nell'[Esempio 1: Creare una semplice rete perimetrale con gruppi di sicurezza di rete][Example1]. Vedere anche la descrizione relativa ai gruppi di sicurezza di rete in quel documento per un'analisi dettagliata della regola sui gruppi di sicurezza di rete e i relativi attributi.

## Regole del firewall
È necessario installare un client di gestione in un PC per controllare il firewall e creare le configurazioni necessarie. Per informazioni sulla gestione del dispositivo, vedere la documentazione del fornitore del firewall o di un altro dispositivo virtuale di rete. Il resto di questa sezione descrive la configurazione del firewall stesso tramite il client di gestione dei fornitori, non del portale di Azure o di PowerShell.

Le istruzioni per scaricare il client e connettersi all'applicazione Barracuda usata in questo esempio sono disponibili qui: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Sul firewall sarà necessario creare regole di inoltro. Poiché questo esempio indirizza solo il traffico Internet in ingresso verso il firewall e quindi verso il server Web, sarà necessaria solo una regola del processo NAT di inoltro. Nel dispositivo Barracuda NextGen Firewall usato in questo esempio, per passare il traffico viene usata una regola Destination NAT ("Dst NAT").

Per creare la regola seguente, o verificare le regole predefinite esistenti, dal dashboard del client Barracuda NG Admin passare alla scheda di configurazione, quindi nella sezione Operational Configuration fare clic su Ruleset. Una griglia denominata "Main Rules" mostrerà le regole esistenti attive e disattivate nel firewall. Nell'angolo in alto a destra della griglia è presente un piccolo pulsante verde "+" sul quale occorre fare clic per creare una nuova regola. Notare che il firewall potrebbe essere "bloccato" per modifiche. Se è visualizzato un pulsante contrassegnato con "Lock" e non si riescono a creare o modificare regole, fare clic sul pulsante per "sbloccare" il set di regole e consentire la modifica. Se si vuole modificare una regola esistente, selezionarla, fare clic con il pulsante destro del mouse e scegliere Edit Rule.

Creare una nuova regola e specificare un nome, ad esempio "WebTraffic".

L'icona della regola Destination NAT è simile alla seguente: ![Icona di Destination NAT][2]

La regola stessa dovrebbe essere simile alla schermata seguente:

![Regola del firewall][3]

In questo caso, qualsiasi indirizzo di origine in ingresso che accede al firewall nel tentativo di raggiungere HTTP (porta 80 o 443 per HTTPS) verrà trasmesso all'interfaccia "DHCP1 Local IP" del firewall e reindirizzato al server Web con l'indirizzo IP 10.0.1.5. Poiché il traffico arriva sulla porta 80 e viene inoltrato al server Web sulla porta 80, non sono necessarie modifiche della porta. Il campo Target List poteva tuttavia essere 10.0.1.5:8080 se il server Web fosse stato in ascolto sulla porta 8080, convertendo così la porta in ingresso 80 sul firewall nella porta in ingresso 8080 sul server Web.

È necessario definire un valore in Connection Method. Come regola di destinazione da Internet la più appropriata è "Dynamic SNAT".

Anche se è stata creata una sola regola, è importante impostarne la priorità correttamente. Se nella griglia di tutte le regole sul firewall questa nuova regola si trova in basso, sotto la regola "BLOCKALL", non verrà mai applicata. Assicurarsi che la regola appena creata per il traffico Web si trovi sopra la regola BLOCKALL.

Una volta che la regola è stata creata, è necessario effettuarne il push al firewall e quindi attivarla. In caso contrario, la modifica della regola non sarà applicata. Il processo di push e attivazione è descritto nella sezione successiva.

## Attivazione delle regole
Dopo aver modificato il set di regole per aggiungervi questa regola, è necessario caricarlo nel firewall e attivarlo.

![Attivazione delle regole firewall][4]

Nell'angolo in alto a destra del client di gestione è disponibile un gruppo di pulsanti. Fare clic sul pulsante "Send Changes" per inviare le regole modificate al firewall, quindi fare clic sul pulsante "Activate".

Con l'attivazione del set di regole del firewall, la compilazione dell'ambiente di esempio è completata. Facoltativamente, è possibile eseguire gli script successivi alla compilazione disponibili nella sezione Riferimenti per aggiungere un'applicazione a questo ambiente e testare gli scenari di traffico seguenti.

>[AZURE.IMPORTANT] È importante comprendere che non si raggiungerà il server Web direttamente. Quando un browser richiede una pagina HTTP da FrontEnd001.CloudApp.Net, l'endpoint HTTP (porta 80) passa questo traffico al firewall, non al server Web. Il firewall inoltra quindi la richiesta tramite NAT al server Web, in base alla regola creata sopra.

## Scenari di traffico

#### (Consentito) Da Web a server Web tramite il firewall
1.	L'utente Internet richiede una pagina HTTP a FrontEnd001.CloudApp.Net (servizio cloud per Internet).
2.	Il servizio cloud passa il traffico attraverso l'endpoint aperto sulla porta 80 all'interfaccia locale del firewall su 10.0.1.4:80.
3.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2.	Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3.	Regola gruppo di sicurezza di rete 3 (da Internet a firewall) applicabile, il traffico è consentito, l'elaborazione della regola si arresta.
4.	Il traffico raggiunge l'indirizzo IP interno del firewall (10.0.1.4).
5.	La regola di inoltro al firewall riconosce il traffico per la porta 80 e lo reindirizza al server Web IIS01.
6.	IIS01 è in ascolto del traffico Web, riceve la richiesta e ne avvia l'elaborazione.
7.	IIS01 chiede informazioni a SQL Server in AppVM01.
8.	Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
9.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2.	Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3.	Regola gruppo di sicurezza di rete 3 (da Internet a firewall), non applicabile, passa alla regola successiva.
  4.	Regola gruppo di sicurezza di rete 4 (da IIS01 ad AppVM01) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
10.	AppVM01 riceve la query SQL e risponde.
11.	Non essendoci regole in uscita sulla subnet back-end, la risposta è consentita.
12.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2.	La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
13.	Il server IIS riceve la risposta SQL, completa la risposta HTTP e la invia al richiedente.
14.	Essendo una sessione NAT dal firewall, la destinazione della risposta (inizialmente) è il firewall.
15.	Il firewall riceve la risposta dal server Web e la restituisce all'utente Internet.
16.	Non essendoci regole in uscita sulla subnet front-end, la risposta è consentita e l'utente Internet riceve la pagina Web richiesta.

#### (Consentito) Da RDP a back-end
1.	L'amministratore del server su Internet richiede una sessione RDP ad AppVM01 su BackEnd001.CloudApp.Net:xxxxx, dove xxxxx è il numero di porta assegnato casualmente per RDP a AppVM01. Si può trovare la porta assegnata sul portale di Azure o tramite PowerShell.
2.	Poiché il firewall è in ascolto solo dell'indirizzo FrontEnd001.CloudApp.Net, non è interessato da questo flusso di traffico.
3.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2.	Regola gruppo di sicurezza di rete 2 (RDP) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
4.	Senza regole in uscita, sono applicabili le regole predefinite e il traffico restituito è consentito.
5.	La sessione RDP è abilitata.
6.	AppVM01 richiede il nome utente e la password.

#### (Consentito) Ricerca DNS del server Web sul server DNS
1.	Il server Web, IIS01, richiede un feed di dati all'indirizzo www.data.gov, ma deve risolvere l'indirizzo.
2.	La configurazione di rete per la rete virtuale elenca DNS01 (10.0.2.4 nella subnet back-end) come server DNS primario, IIS01 invia la richiesta DNS a DNS01.
3.	Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
4.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	 Regola gruppo di sicurezza di rete 1 (DNS) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
5.	Il server DNS riceve la richiesta.
6.	Il server DNS non ha l'indirizzo memorizzato nella cache e invia la richiesta a un server DNS radice su Internet.
7.	Non sono impostate regole in uscita sulla subnet back-end, il traffico è consentito.
8.	Il server DNS Internet risponde perché la sessione è stata avviata internamente, la risposta è consentita.
9.	Il server DNS memorizza la risposta nella cache e restituisce a IIS01 la risposta alla richiesta iniziale.
10.	Non sono impostate regole in uscita sulla subnet back-end, il traffico è consentito.
11.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2.	La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
12.	IIS01 riceve la risposta da DNS01.

#### (Consentito) Il server Web richiede l'accesso a un file su AppVM01
1.	IIS01 richiede un file su AppVM01.
2.	Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
3.	La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2.	Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3.	Regola gruppo di sicurezza di rete 3 (da Internet a firewall), non applicabile, passa alla regola successiva.
  4.	Regola gruppo di sicurezza di rete 4 (da IIS01 ad AppVM01) applicabile, il traffico è consentito, l'elaborazione della regola si arresta.
4.	AppVM01 riceve la richiesta e risponde con il file (presupponendo che l'accesso sia autorizzato).
5.	Non essendoci regole in uscita sulla subnet back-end, la risposta è consentita.
6.	La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2.	La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
7.	Il server IIS riceve il file.

#### (Negato) Traffico Web diretto al server Web
Poiché il server Web, IIS01, e il firewall si trovano nello stesso servizio cloud, condividono lo stesso indirizzo IP pubblico. Tutto il traffico HTTP verrà quindi indirizzato al firewall. Anche se la richiesta viene servita correttamente, non può passare direttamente al server Web. Come progettato, passa prima attraverso il firewall. Per il flusso di traffico, vedere il primo scenario in questa sezione.

#### (Negato) Traffico Web al server back-end
1.	L'utente Internet prova ad accedere a un file su AppVM01 tramite il servizio BackEnd001.CloudApp.Net.
2.	Non essendoci endpoint aperti per la condivisione file, il traffico non passa attraverso il servizio cloud e non raggiunge il server.
3.	In caso di apertura degli endpoint per qualunque motivo, la regola del gruppo di sicurezza di rete 5 (da Internet a rete virtuale) bloccherà questo traffico.

#### (Negato) Ricerca DNS Web sul server DNS
1.	L'utente Internet prova a cercare un record DNS interno su DNS01 tramite il servizio BackEnd001.CloudApp.Net.
2.	Non essendoci endpoint aperti per DNS, il traffico non passa attraverso il servizio cloud e non raggiunge il server.
3.	In caso di apertura degli endpoint per qualunque motivo, la regola del gruppo di sicurezza di rete 5 (da Internet a rete virtuale) bloccherà questo traffico. Si noti che la regola 1 (DNS) non è applicabile per due motivi, prima di tutto l'indirizzo di origine è Internet e questa regola si applica solo quando l'origine è la rete virtuale locale, poi questa è una regola di tipo Consenti e quindi non bloccherà mai il traffico.

#### (Negato) Accesso dal Web a SQL tramite il firewall
1.	L'utente Internet richiede dati SQL a FrontEnd001.CloudApp.Net (servizio cloud per Internet).
2.	Non essendoci endpoint aperti per SQL, il traffico non passa attraverso il servizio cloud e non raggiunge il firewall.
3.	Se gli endpoint sono aperti per qualunque motivo, la subnet front-end inizia l'elaborazione delle regole in ingresso:
  1.	Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2.	Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3.	Regola gruppo di sicurezza di rete 2 (da Internet a firewall) applicabile, il traffico è consentito, l'elaborazione della regola si arresta.
4.	Il traffico raggiunge l'indirizzo IP interno del firewall (10.0.1.4).
5.	Il firewall non ha regole di inoltro per SQL ed elimina il traffico.

## Conclusioni
Questo è un modo relativamente semplice per proteggere l'applicazione con un firewall e isolare la subnet back-end dal traffico in ingresso.

Altri esempi e una panoramica dei limiti di sicurezza della rete sono disponibili [qui][HOME].

## Riferimenti
### Script principale e configurazione di rete
Salvare lo script completo in un file di script PowerShell. Salvare la configurazione di rete in un file denominato "NetworkConf2.xml". Modificare le variabili definite dall'utente secondo le esigenze. Eseguire lo script, quindi seguire le istruzioni per la configurazione delle regole del firewall.

#### Script completo
In base alle variabili definite dall'utente, lo script consente di:

1.	Connettersi a una sottoscrizione di Azure.
2.	Creare un nuovo account di archiviazione
3.	Creare una nuova rete virtuale e due subnet, come definito nel file di configurazione di rete.
4.	Creare 4 macchine virtuali di Windows Server.
5.	Configurare il gruppo di sicurezza di rete eseguendo queste operazioni:
  -	Creazione di un gruppo di sicurezza di rete
  -	Inserimento delle regole.
  -	Associazione del gruppo di sicurezza di rete alle subnet appropriate

Questo script di PowerShell deve essere eseguito localmente in un server o un PC connesso a Internet.

>[AZURE.IMPORTANT] Quando si esegue lo script, in PowerShell potrebbero venire visualizzati avvisi o altri messaggi informativi. Solo i messaggi di errore formattati in rosso possono indicare un problema.


	<# 
	 .SYNOPSIS
	  Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Two new cloud services
	   - Two Subnets (FrontEnd and BackEnd subnets)
	   - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
	   - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
	   - Three Servers on the BackEnd Subnet
	   - Network Security Groups to allow/deny traffic patterns as declared
	  
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Security requirements are different for each use case and can be addressed in a
	  myriad of ways. Please be sure that any sensitive data or applications are behind
	  the appropriate layer(s) of protection. This script serves as an example of some
	  of the techniques that can be used, but should not be used for all scenarios. You
	  are responsible to assess your security needs and the appropriate protections
	  needed, and then effectively implement those protections.
	
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   myFirewall - 10.0.1.4
	   IIS01      - 10.0.1.5
	 
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
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	    $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure proper NSG Rule creation later in this script:
	    #       - The Web Server must be VM 1
	    #       - The AppVM1 Server must be VM 2
	    #       - The DNS server must be VM 4
	    #
	    #       Otherwise the NSG rules in the last section of this
	    #       script will need to be changed to match the modified
	    #       VM array numbers ($i) so the NSG Rule IP addresses
	    #       are aligned to the associated VM IP addresses.
	
	    # VM 0 - The Network Virtual Appliance (NVA)
	      $VMName += "myFirewall"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Firewall"
	      $img += $FWImg
	      $size += "Small"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.4"
	
	    # VM 1 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.5"
	
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
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
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
	            # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
	            #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
	            #       forwarding rule, so the HTTP endpoint here will have the same public and local
	            #       port and the firewall will do the NATing and redirection as declared in the
	            #       firewall rule.
	            Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
	            # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
	            }
	        Else
	            {
	            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	                Remove-AzureEndpoint -Name "PowerShell" | `
	                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
	            }
	        $i++
	    }
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	    
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rules
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
	        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
	        -SourceAddressPrefix Internet -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
	        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
	        -Protocol *
	    
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
	        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
	        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
	        -Protocol *
	
	    # Assign the NSG to the Subnets
	        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Rete perimetrale in ingresso con gruppo di sicurezza di rete"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icona di Destination NAT"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regola del firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Attivazione delle regole firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

<!---HONumber=AcomDC_0615_2016-->