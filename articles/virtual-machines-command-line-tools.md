<properties
	pageTitle="Uso dell’interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione servizi di Azure"
	description="Imparare a utilizzare gli strumenti da riga di comando per Mac, Linux e Windows per gestire Azure utilizzando la modalità asm dell’interfaccia della riga di comando di Azure."
	services="web-sites, virtual-machines, mobile-services, cloud-services"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="rasquill"/>

# Uso dell’interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione servizi di Azure

In questo argomento viene descritto come utilizzare l’interfaccia della riga di comando di Azure in modalità **asm** per creare, gestire ed eliminare servizi nella riga di comando di computer Mac, Linux e Windows. Questa funzionalità è simile a quella fornita dai cmdlet di Windows PowerShell installati con Azure SDK per .NET, Node.JS e PHP.

> [AZURE.NOTE]Il concetto di utilizzo dei servizi di Azure in modalità **asm** è simile al concetto di utilizzo dei singoli servizi di Azure come Siti Web, Macchine virtuali, Reti virtuali, Archiviazione e così via. Funzionalità più complete con un modello gerarchico raggruppato logicamente di risorse è disponibile nella riga di comando mediante la modalità **arm**. Per passare a tale modalità, vedere [Uso dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md).

Per le istruzioni per l’installazione, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure](xplat-cli-install.md).

I parametri facoltativi sono indicati tra parentesi quadre (ad esempio  [parametro]). Tutti gli altri parametri sono obbligatori.

Oltre ai parametri facoltativi specifici del comando documentati qui, vi sono tre parametri opzionali che possono essere utilizzati per visualizzare output dettagliato come opzioni richiesta e codici di stato. Il parametro -v fornisce output dettagliato, mentre il parametro -vv fornisce un output con un dettaglio ancor maggiore. Utilizzando l'opzione --json verrà visualizzato il risultato in formato json non elaborato.

##<a name="Manage_your_account_information_and_publish_settings"></a>Gestione delle informazioni relative all’account e impostazioni di pubblicazione
Le informazioni relative alla sottoscrizione di Azure vengono usate dallo strumento per connettersi all'account dell'utente. Tali informazioni possono essere ottenute dal portale di Azure in un file di impostazioni di pubblicazione come illustrato di seguito. È possibile importare il file di impostazioni di pubblicazione come impostazione di configurazione locale persistente che lo strumento userà per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

**account download [opzioni]**

Questo comando avvia un browser per scaricare il file con estensione publishsettings dal portale di Azure.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [opzioni] &lt;file>**


Questo comando importa un file di impostazioni di pubblicazione o un certificato che verrà usato dallo strumento per i passaggi successivi.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

> [AZURE.NOTE]Il file di impostazioni di pubblicazione può contenere dettagli (ovvero nome e ID sottoscrizione) di più di una sottoscrizione. Quando si importa il file di impostazioni di pubblicazione, la prima sottoscrizione viene usata come descrizione predefinita. Per utilizzare una sottoscrizione diversa, eseguire il comando riportato di seguito. <code>~$ azure config set subscription &lt;id-altra-sottoscrizione&gt;</code>

**account clear [opzioni]**

Questo comando rimuove le impostazioni di pubblicazione che sono state importate. Utilizzare questo comando se si è terminato il lavoro con lo strumento nel computer in uso e si desidera assicurarsi che nessuno possa utilizzarlo successivamente con quell'account.

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [opzioni]**

Questo comando elenca le sottoscrizioni importate.

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [opzioni] &lt;sottoscrizione&gt;**

Questo comando imposta la sottoscrizione corrente.

###Comandi per la gestione dei gruppi di affinità

**account affinity-group list [opzioni]**

Questo comando elenca i gruppi di affinità di Azure.

È possibile impostare gruppi di affinità quando un gruppo di macchine virtuali si estende su più macchine fisiche. Il gruppo di affinità specifica che le macchine fisiche dovrebbero essere il più vicino possibile una all'altra, per ridurre la latenza di rete.

	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [opzioni] &lt;nome&gt;**

Questo comando crea un nuovo gruppo di affinità.

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [opzioni] &lt;nome&gt;**

Questo comando visualizza i dettagli del gruppo di affinità.

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [opzioni] &lt;nome&gt;**

Questo comando elimina il gruppo di affinità specificato.

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###Comandi per la gestione dell'ambiente dell'account

**account env list [opzioni]**

Questo comando elenca gli ambienti dell'account.

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [opzioni][environment]**

Questo comando visualizza i dettagli relativi all'ambiente dell'account.

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [opzioni][environment]**

Questo comando aggiunge un ambiente all'account.

**account env set [opzioni][environment]**

Questo comando imposta l'ambiente dell'account.

**account env delete [opzioni][environment]**

Questo comando elimina l'ambiente specificato dall'account.

##<a name="Commands_to_manage_your_Azure_virtual_machines"></a>Comandi per la gestione delle macchine virtuali di Azure
Nel diagramma seguente vengono illustrate le modalità di hosting delle macchine virtuali di Azure nell'ambiente della distribuzione di produzione di un servizio cloud di Azure.

![Diagramma tecnico di Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** consente di creare l'unità nell'archiviazione BLOB (E:/ nel diagramma); **attach** consente di collegare un disco già creato, ma non collegato, a una macchina virtuale.

**vm create [opzioni] &lt;nome-dns> &lt;immagine> &lt;nomeUtente>[password]**

Questo comando crea una nuova macchina virtuale di Azure. Per impostazione predefinita, ogni macchina virtuale viene creata in un proprio servizio cloud. È tuttavia possibile specificare l'aggiunta di una macchina a un servizio cloud esistente usando l'opzione -c, come indicato di seguito.

Il comando vm create, come il portale di Azure, consente di creare macchine virtuali solo nell'ambiente della distribuzione di produzione. Non è disponibile un'opzione per la creazione di una macchina virtuale nell'ambiente della distribuzione di gestione temporanea di un servizio cloud. Se la sottoscrizione non ha un account di archiviazione di Azure, il comando ne crea uno.

È possibile specificare un percorso tramite il parametro --location o un gruppo di affinità tramite il parametro --affinity-group. Se non viene utilizzato nessuno di questi due parametri, verrà richiesto di fornire un percorso scegliendolo da un elenco di percorsi validi.

La lunghezza della password fornita deve essere compresa tra 8 e 123 caratteri e soddisfare i requisiti di complessità della password del sistema operativo utilizzato per la macchina virtuale.

Se per gestire una macchina virtuale Linux distribuita si prevede la necessità di utilizzare SSH (che è in genere il metodo più diffuso) è necessario abilitare SSH mediante l'opzione -e al momento della creazione della macchina virtuale stessa. Non è possibile abilitare SSH dopo che una macchina virtuale è stata creata.

Per le macchine virtuali Windows, è possibile abilitare RDP in un secondo momento mediante l'aggiunta della porta 3389 come endpoint.

Per questo comando sono supportati i seguenti parametri facoltativi:

**-c, --connect** Crea la macchina virtuale all'interno di una distribuzione già creata in un servizio di hosting. Se -vmname non viene usato con questa opzione, il nome della nuova macchina virtuale sarà generato automaticamente.<br /> **-n, --vm-name** Specificare il nome della macchina virtuale. Per impostazione predefinita, questo parametro prende il nome del servizio di hosting. Se -vmname non è specificato, il nome della nuova macchina virtuale viene generato come &lt;nome-servizio>&lt;id>, dove &lt;id> è il numero delle macchine virtuali esistenti nel servizio più 1. Ad esempio, se si utilizza questo comando per aggiungere una nuova macchina virtuale al servizio di hosting MyService che dispone di una sola macchina virtuale, la nuova macchina virtuale viene denominata MyService2.<br /> **-u, --blob-url** Specificare l’URL di archiviazione del BLOB in cui creare il disco di sistema della macchina virtuale. <br /> **-z, --vm-size** Specificare la dimensione della macchina virtuale. I valori validi sono: "ExtraSmall", "Small", "Medium", "Large", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2", "Standard_G3", "Standard_G4", "Standard_G55". Il valore predefinito è "Small". <br /> **-r** Aggiunge la connettività RDP  a un macchina virtuale Windows. <br /> **-e, --ssh** Aggiunge la connettività SSH a una macchina virtuale Windows. <br /> **-t, --ssh-cert** Specifica il certificato SSH. <br /> **-s** La sottoscrizione <br /> **-o, --community** L’immagine specificata è un’immagine della community<br /> **-w** Il nome della rete virtuale<br/> **-l, --location** specifica la posizione (ad esempio "North Central US"). <br /> **-a, --affinity-group** specifica il gruppo di affinità.<br /> **-w, --virtual-network-name** Specificare la rete virtuale in cui aggiungere la nuova macchina virtuale. Le reti virtuali possono essere configurate e gestite dal portale di Azure.<br /> **-b, --subnet-names** Specifica i nomi delle subnet da assegnare alla macchina virtuale.

Nell'esempio seguente MSFT__Win2K8R2SP1-120514-1520-141205-01-it-it-30GB è un'immagine fornita dalla piattaforma. Per ulteriori informazioni sulle immagini del sistema operativo, vedere il comando vm image list.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************
	info:   vm create command OK

**vm create-from &lt;nome-dns> &lt;file-ruolo>**

Questo comando crea una nuova macchina virtuale di Azure da un file di ruolo JSON.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [opzioni]**

Questo comando elenca le macchine virtuali di Azure. L'opzione --json specifica che i risultati vengono restituiti in formato JSON non elaborato.

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [opzioni]**

Questo comando elenca i percorsi di tutti gli account Azure disponibili.

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name
	data:   ---------------------  ------------
	data:   Azure Preview  West US
	info:   account location list command OK

**vm show [opzioni] &lt;nome>**

Questo comando visualizza i dettagli relativi a una macchina virtuale di Azure. L'opzione --json specifica che i risultati vengono restituiti in formato JSON non elaborato.

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   }
	info:   vm show command OK

**vm delete [opzioni] &lt;nome>**

Questo comando elimina una macchina virtuale di Azure. Per impostazione predefinita, il comando non elimina l'archivio BLOB di Azure da cui vengono creati il disco del sistema operativo e il disco dati. Per eliminare l'archiviazione BLOB insieme alla macchina virtuale su cui si basa, specificare l'opzione -b.

	~$ azure vm delete my-vm
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [opzioni] &lt;nome>**

Questo comando avvia una macchina virtuale di Azure.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [opzioni] &lt;nome>**

Questo comando riavvia una macchina virtuale di Azure.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [opzioni] &lt;nome>**

Questo comando arresta una macchina virtuale di Azure. È possibile utilizzare l'opzione -p per specificare che la risorsa di calcolo non deve essere rilasciata in fase di arresto.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;nome-vm> &lt;nome-immagine-destinazione>**

Questo comando acquisisce un'immagine di una macchina virtuale di Azure.

È possibile acquisire un'immagine di macchina virtuale solo se lo stato della macchina virtuale è **Arrestato**. Arrestare la macchina virtuale prima di continuare.

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [opzioni] &lt;nome-vm> &lt;percorso-file>**

Questo comando esporta un'immagine di una macchina virtuale di Azure in un file.

	~$ azure vm export "myvm" "C:"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_endpoints"></a>Comandi per la gestione degli endpoint delle macchine virtuali di Azure
Nel diagramma seguente viene illustrata l'architettura di una tipica distribuzione di più istanze di una macchina virtuale. Si noti che, in questo esempio, la porta 3389 è aperta su ogni macchina virtuale (per l'accesso RDP) e che è anche presente un indirizzo IP interno (ad esempio 168.55.11.1) su ogni macchina virtuale. Questo viene utilizzato dal servizio di bilanciamento del carico per l'indirizzamento del traffico alla macchina virtuale. L'indirizzo IP interno può essere utilizzato anche per la comunicazione tra macchine virtuali.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Le richieste esterne alle macchine virtuali passano attraverso un servizio di bilanciamento del carico. Per questo motivo, in distribuzioni con più macchine virtuali, non è possibile specificare richieste indirizzate a una macchina virtuale specifica. Per le distribuzioni con più macchine virtuali, è necessario configurare il mapping delle porte tra le macchine virtuali (porta-vm) e il servizio di bilanciamento del carico (porta-bc).

**vm endpoint create &lt;nome-vm> &lt;porta-lb> [porta-vm]**

Questo comando crea un nuovo endpoint per una macchina virtuale. È inoltre possibile utilizzare le opzioni -u oppure --enable-direct-server-return per specificare se abilitare Direct Server Return sull'endpoint, che per impostazione predefinita è disabilitato.

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [opzioni] &lt;nome-vm> &lt;porta-lb>[:&lt;porta-vm>[:&lt;protocollo>[:&lt;enable-direct-server-return>[:&lt;nome-set-lb>[:&lt;protocollo-probe>[:&lt;porta-probe>[:&lt;percorso-probe>[:&lt;nome-lb-interno>]]]]]]]] {1-*}**

Creare più endpoint per macchine virtuali.

**vm endpoint delete [opzioni] &lt;nome-vm> &lt;nome-endpoint>**

Questo comando elimina un endpoint di una macchina virtuale.

	~$ azure vm endpoint delete my-vm http
	azure vm endpoint delete my-vm http
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list &lt;nome-vm>**

Questo comando elenca tutti gli endpoint della macchina virtuale. L'opzione --json specifica che i risultati vengono restituiti in formato JSON non elaborato.

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [opzioni] &lt;nome-vm> &lt;nome-endpoint>**

Questo comando aggiorna un endpoint VM ai nuovi valori usando le opzioni seguenti.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [opzioni] &lt;nome-vm>**

Questo comando visualizza i dettagli dell'endpoint in una macchina virtuale.

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_images"></a>Comandi per la gestione delle immagini delle macchine virtuali di Azure

Le immagini di macchine virtuali sono acquisizioni di macchine virtuali già configurate che possono essere replicate in base alle esigenze.

**vm image list [opzioni]**

Con questo comando è possibile ottenere un elenco di immagini di macchine virtuali. Esistono tre tipi di immagini: immagini create da Microsoft, con prefisso "MSFT", immagini create da terze parti, che hanno in genere come prefisso il nome del fornitore e immagini create dall'utente. Per creare un'immagine, è possibile acquisire una macchina virtuale esistente o creare un'immagine da un file VHD personalizzato caricato nell'archiviazione BLOB. Per ulteriori informazioni sull'utilizzo di un file VHD personalizzato, vedere il comando vm image create. L'opzione --json specifica che i risultati vengono restituiti in formato JSON non elaborato.

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-it-it-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.it-it.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.it-it.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-it-it-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-it-it-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-it-it-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK

**vm image show [opzioni] &lt;nome>**

Questo comando visualizza i dettagli di un'immagine di macchina virtuale.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK

**vm image delete [opzioni] &lt;nome>**

Questo comando elimina un'immagine di una macchina virtuale.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image
	info:   vm image delete command OK

**vm image create &lt;nome> [percorso-origine]**

Questo comando crea un'immagine di una macchina virtuale. I file VHD personalizzati vengono caricati nell'archiviazione BLOB, che è l'origina da cui viene creata l'immagine della macchina virtuale. L'immagine potrà quindi essere utilizzata per creare una macchina virtuale. I parametri relativi all'ubicazione e al sistema operativo sono obbligatori.

Alcuni sistemi impongono limitazioni sui descrittori di file per processo. Se questo limite viene superato, dallo strumento viene visualizzato un errore di limite di descrittori di file. È possibile eseguire nuovamente il comando usando il parametro -p &lt;numero> per ridurre il numero massimo di caricamenti paralleli. Il numero massimo di caricamenti paralleli predefinito è 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_data_disks"></a>Comandi per la gestione dei dischi dati delle macchine virtuali di Azure

I dischi dati sono file con estensione vhd nell'archiviazione BLOB che possono essere usati da una macchina virtuale. Per ulteriori informazioni sulle modalità di distribuzione dei dischi dati nell'archiviazione BLOB, vedere il diagramma tecnico di Azure riportato in precedenza in questo articolo.

I comandi per collegare dischi dati (azure vm disk attach and azure vm disk attach-new) consentono di assegnare un numero di unità logica (LUN, Logical Unit Number) al disco dati collegato, come previsto dal protocollo SCSI. Al primo disco dati collegato a una macchina virtuale viene assegnato il numero LUN 0, a quello successivo LUN 1 e così via.

Quando si scollega un disco dati con il comando azure vm disk detach, usare il parametro  &lt;lun&gt; per indicare il disco da scollegare.

> [AZURE>NOTA] Si noti che i dischi dati devono essere sempre scollegati in ordine inverso, iniziando dall'unità con il numero LUN più alto assegnato. Il livello SCSI di Linux non supporta lo scollegamento di un'unità mentre è ancora collegata un'unità con un numero LUN superiore. L'unità con LUN 0, ad esempio, non può essere scollegata se l'unità LUN 1 è ancora collegata.

**vm disk show [opzioni] &lt;nome>**

Questo comando visualizza i dettagli relativi a un disco di Azure.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-it-it-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [opzioni][vm-name]**

Questo comando elenca i dischi di Azure o i dischi collegati a una macchina virtuale specificata. Se viene eseguito con un nome di macchina virtuale come parametro, restituisce tutti i dischi collegati alla macchina virtuale. L'unità LUN 1viene creata insieme alla macchina virtuale ed eventuali altri dischi elencati vengono collegati separatamente.

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK

L'esecuzione di questo comando senza un nome di macchina virtuale come parametro restituisce tutti i dischi.

	~$ azure vm disk list
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [opzioni] &lt;nome>**

Questo comando elimina un disco di Azure da un archivio personale. Il disco deve essere disconnesso dalla macchina virtuale prima dell'eliminazione.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052
	info:   vm disk delete command OK

**vm disk create &lt;nome> [percorso-origine]**

Questo comando carica e registra un disco di Azure. È necessario specificare --blob-url, --location o --affinity-group. Se si utilizza questo comando con  [percorso-origine], viene caricato il file con estensione vhd specificato e viene creata una nuova immagine. È quindi possibile collegare questa immagine a una macchina virtuale utilizzando il comando vm disk attach.

Alcuni sistemi impongono limitazioni sui descrittori di file per processo. Se questo limite viene superato, dallo strumento viene visualizzato un errore di limite di descrittori di file. È possibile eseguire nuovamente il comando usando il parametro -p &lt;numero> per ridurre il numero massimo di caricamenti paralleli. Il numero massimo di caricamenti paralleli predefinito è 96.

	~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [opzioni] &lt;percorso-origine> &lt;url-blob> &lt;chiave-account-archiviazione>**

Questo comando carica un disco di macchina virtuale.

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach &lt;nome-vm> &lt;nome-immagine-disco>**

Questo comando collega un disco esistente nell'archiviazione BLOB a una macchina virtuale distribuita in un servizio cloud.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new &lt;nome-vm> &lt;dimensione-in-gb> [url-blob]**

Questo comando collega un disco dati a una macchina virtuale di Azure. In questo esempio, 20 è la dimensione in gigabyte del nuovo disco da collegare. È possibile utilizzare facoltativamente un URL di BLOB come ultimo argomento, per specificare in modo esplicito il BLOB di destinazione da creare. Se non si specifica un URL di BLOB, verrà generato un oggetto BLOB automaticamente.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach &lt;nome-vm> &lt;lun>**

Questo comando consente di scollegare un disco dati collegato a una macchina virtuale di Azure. &lt;lun> identifica il disco da scollegare. Per ottenere un elenco di dischi associati a un disco prima che questo venga scollegato, usare il comando vm disk-list &lt;nome-vm>.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

##<a name="Commands_to_manage_your_Azure_cloud_services"></a>Comandi per la gestione dei servizi cloud di Azure

I servizi cloud di Azure sono applicazioni e servizi ospitati in ruoli Web e ruoli di lavoro. I comandi seguenti possono essere utilizzati per gestire i servizi cloud di Azure.

**service create [opzioni] &lt;nomeServizio>**

Questo comando crea un nuovo servizio cloud.

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [opzioni] &lt;nomeServizio>**

Questo comando visualizza i dettagli di un servizio cloud di Azure.

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [opzioni]**

Questo comando elenca i servizi cloud di Azure.

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [opzioni] &lt;nome>**

Questo comando elimina un servizio cloud di Azure.

	~$ azure service delete myservice
	info:   Executing command service delete myservice
	info:   cloud-service delete command OK

Per forzare l'eliminazione, usare il parametro  `-q`.


##<a name="Commands_to_manage_your_Azure_certificates"></a>Comandi per la gestione dei certificati di Azure

I certificati di servizio di Azure sono certificati SSL collegati all'account Azure. Per altre informazioni sui certificati di Azure, vedere [Gestione certificati](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**service cert list [opzioni]**

Questo comando elenca i certificati di Azure.

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services
	+ Fetching certificates
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
	info:   service cert list command OK

**service cert create &lt;prefisso-dns> &lt;file> [password]**

Questo comando carica un certificato. Per certificati non protetti da password è possibile lasciare vuoto il prompt della password.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password:
	+ Creating certificate
	info:   service cert create command OK

**service cert delete [opzioni] &lt;identificazione-digitale>**

Questo comando elimina un certificato.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate
	info:   nghinazz : cert deleted
	info:   service cert delete command OK


##<a name="Commands_to_manage_your_web_sites"></a>Comandi per la gestione delle app Web

Una app Web di Azure è una configurazione Web accessibile tramite URI. Le app Web sono ospitate in macchine virtuali, ma non è necessario che l'utente si occupi delle attività di creazione e distribuzione della macchina virtuale stessa. Tali attività vengono gestite da Azure in modo automatico.

**site list [opzioni]**

Questo comando consente di elencare le app Web.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [opzioni][name]**

Questo comando consente di impostare le opzioni di configurazione per l’app Web [nome]

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [opzioni]**

Questo comando genera uno script di distribuzione personalizzata

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [opzioni][name]**

Questo comando consente di creare una nuova app Web e una directory locale.

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

> [AZURE.NOTE]Il nome del sito deve essere univoco. Non è possibile creare un sito con lo stesso nome DNS di un sito esistente.

**site browse [opzioni][name]**

Questo comando consente di aprire l’app Web in un browser.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [opzioni][name]**

Questo comando mostra i dettagli relativi a un’app Web.

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [opzioni][name]**

Questo comando consente di eliminare un’app Web.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [opzioni][name]**

Questo comando scambia gli slot di due app Web.

Il comando supporta l'opzione aggiuntiva seguente:

**-q or **--quiet**: non chiedere alcuna conferma. Usare questa opzione negli script automatici.


**site start [opzioni][name]**

Questo comando consente di avviare un’app Web.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [opzioni][name]**

Questo comando consente di arrestare un’app Web.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

**site restart [opzioni][name]

Questo comando consente di arrestare e quindi avviare un’app Web specificata.

Il comando supporta l'opzione aggiuntiva seguente:

**--slot** &lt;slot>: il nome dello slot da riavviare.


**site location list [opzioni]**

Questo comando consente di elencare le posizioni delle app Web

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###Comandi per la gestione delle impostazioni delle applicazioni dell’app Web

**site appsetting list [opzioni][name]**

Questo comando elenca le impostazioni di app aggiunte all’app Web.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [opzioni] &lt;coppiachiavevalore> [nome]**

Questo comando aggiunge un’impostazione di app Web all’app Web come coppia chiave-valore.

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [opzioni] &lt;chiave> [nome]**

Questo comando elimina l'impostazione di app specificata dall’app Web.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [opzioni] &lt;chiave> [nome]**

Questo comando visualizza i dettagli dell'impostazione di app specificata

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###Comandi per la gestione dei certificati dell’app Web

**site cert list [opzioni][name]**

Questo comando visualizza un elenco dei certificati dell’app Web.

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [opzioni] &lt;percorso-certificato> [nome]**

**site cert delete [opzioni] &lt;identificazione digitale> [nome]**

**site cert show [opzioni] &lt;identificazione digitale> [nome]**

Questo comando visualizza i dettagli del certificato.

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###Comandi per la gestione delle stringhe di connessione dell’app Web

**site connectionstring list [opzioni][name]**

**site connectionstring add [opzioni] &lt;nomeconnessione> &lt;valore> &lt;tipo> [nome]**

**site connectionstring delete [opzioni] &lt;nomeconnessione> [nome]**

**site connectionstring show [opzioni] &lt;nomeconnessione> [nome]**

###Comandi per la gestione dei documenti predefiniti dell’app Web

**site defaultdocument list [opzioni][name]**

**site defaultdocument add [opzioni] &lt;documento> [nome]**

**site defaultdocument delete [opzioni] &lt;documento> [nome]**

###Comandi per la gestione delle distribuzioni dell’app Web

**site deployment list [opzioni][name]**

**site deployment show [opzioni] &lt;idCommit> [nome]**

**site deployment redeploy [opzioni] &lt;idCommit> [nome]**

**site deployment github [opzioni][name]**

**site deployment user set [opzioni][username] [pass]**

###Comandi per la gestione dei domini dell’app Web

**site domain list [opzioni][name]**

**site domain add [opzioni] &lt;dn> [nome]**

**site domain delete [opzioni] &lt;dn> [nome]**

###Comandi per la gestione dei mapping del gestore dell’app Web

**site handler list [opzioni][name]**

**site handler add [opzioni] &lt;estensione> &lt;processore> [nome]**

**site handler delete [opzioni] &lt;estensione> [nome]**

###Comandi per la gestione dei processi Web

**site job list [opzioni][name]**

Questo comando elenca tutti i processi Web eseguiti in un’app Web.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-type** &lt;tipo-processo>: facoltativo. Tipo di processo Web. Il valore valido è "triggered" o "continuous". Per impostazione predefinita vengono restituiti i processi Web di qualsiasi tipo.
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

**site job show [opzioni] &lt;nomeProcesso> &lt;tipoProcesso> [nome]**

Questo comando visualizza i dettagli di un processo Web specifico.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo>: obbligatorio. Nome del processo Web.
+ **--job-type** &lt;tipo-processo>: obbligatorio. Tipo di processo Web. Il valore valido è "triggered" o "continuous".
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

**site job delete [opzioni] &lt;nomeProcesso> &lt;tipoProcesso> [nome]**

Questo comando carica il processo Web specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo> obbligatorio. Nome del processo Web.
+ **--job-type** &lt;tipo-processo> obbligatorio. Tipo di processo Web. Il valore valido è "triggered" o "continuous".
+ **-q** o **--quiet**: non chiedere conferma. Utilizzare questa opzione negli script automatici.
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

**site job upload [opzioni] &lt;nomeProcesso> &lt;tipoProcesso> <jobFile> [nome]**

Questo comando carica il processo Web specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo>: obbligatorio. Nome del processo Web.
+ **--job-type** &lt;tipo-processo>: obbligatorio. Tipo di processo Web. Il valore valido è "triggered" o "continuous".
+ **--job-file** &lt;file-processo>: obbligatorio. File del processo.
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

**site job start [opzioni] &lt;nomeProcesso> &lt;tipoProcesso> [nome]**

Questo comando avvia il processo Web specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo>: obbligatorio. Nome del processo Web.
+ **--job-type** &lt;tipo-processo>: obbligatorio. Tipo di processo Web. Il valore valido è "triggered" o "continuous".
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

**site job stop [opzioni] &lt;nomeProcesso> &lt;tipoProcesso> [nome]**

Questo comando arresta il processo Web specificato. È possibile arrestare solo i processi eseguiti in modo continuo.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo>: obbligatorio. Nome del processo Web.
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

###Comandi per la gestione della cronologia dei processi Web 

**site job history list [opzioni][jobName] [nome]**

Questo comando visualizza una cronologia delle esecuzioni del processo Web specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo>: obbligatorio. Nome del processo Web.
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

**site job history show [opzioni][jobName] [idEsecuzione][name]**

Questo comando ottiene i dettagli dell'esecuzione del processo Web specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--job-name** &lt;nome-processo>: obbligatorio. Nome del processo Web.
+ **--run-id** &lt;id-esecuzione>: facoltativo. ID dell'esecuzione. Se non è specificato viene visualizzata l'ultima esecuzione.
+ **--slot** &lt;slot>: il nome dello slot da riavviare.

###Comandi per la gestione della diagnostica dell’app Web

**site log download [opzioni][name]**

Questo comando scarica un file ZIP contenente la diagnostica dell’app Web.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [opzioni][name]**

Questo comando connette il terminale in uso al servizio di streaming dei log.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [opzioni][name]**

Questo comando configura le opzioni di diagnostica per l’app Web.

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###Comandi per la gestione dei repository del sito Web

**site repository branch [opzioni] &lt;ramo> [nome]**

**site repository delete [opzioni][name]**

**site repository sync [opzioni][name]**

###Comandi per la gestione della scalabilità dell’app Web

**site scale mode [opzioni] &lt;modalità> [nome]**

**site scale instances [opzioni] &lt;istanze> [nome]**


##<a name="Commands_to_manage_mobile_services"></a>Comandi per la gestione di Servizi mobili di Azure

Servizi mobili di Azure è costituito da un insieme di servizi Azure che abilitano le funzionalità di back-end delle applicazioni. I comandi relativi a Servizi mobili sono suddivisi nelle categorie seguenti:

+ [Comandi per la gestione delle istanze di Servizi mobili](#Mobile_Services)
+ [Comandi per la gestione della configurazione di Servizi mobili](#Mobile_Configuration)
+ [Comandi per la gestione delle tabelle di Servizi mobili](#Mobile_Tables)
+ [Comandi per la gestione degli script di Servizi mobili](#Mobile_Scripts)
+ [Comandi per la gestione dei processi pianificati](#Mobile_Jobs)
+ [Comandi per la scalabilità di un servizio mobile](#Mobile_Scale)

Le opzioni seguenti si applicano alla maggior parte dei comandi di Servizi mobili:

+ **-h** o **--help**: visualizzare le informazioni sull’uso dell’output.
+ **-s `<id>`** o **--subscription `<id>`**: usare una sottoscrizione specifica, specificata come `<id>`.
+ **-v** or **--verbose**: scrivere l’output dettagliato.
+ **--json**: scrivere l’output JSON.

###<a name="Mobile_Services"></a>Comandi per la gestione delle istanze di Servizi mobili

**mobile locations [opzioni]**

Questo comando elenca le ubicazioni geografiche supportate da Servizi mobili.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US
	info:    North Europe

**mobile create [opzioni][servicename] [nomeUtenteAmministratoreSql][sqlAdminPassword]**

Questo comando crea un servizio mobile insieme a un database SQL e a un server SQL Server.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-r `<sqlServer>`** o **--sqlServer `<sqlServer>`**: usare un server di database SQL esistente, specificato come `<sqlServer>`.
+ **-d `<sqlDb>`** o **--sqlDb `<sqlDb>`**: usare il database SQL esistente, specificato come `<sqlDb>`.
+ **-l `<location>`** or **--location `<location>`**: creare il servizio in una posizione specifica, specificata come `<location>`. Eseguire il comando azure mobile locations per ottenere le ubicazioni disponibili.
+ **--sqlLocation `<location>`**: creare il server SQL in una specifica `<location>`; per impostazione predefinita la posizione del servizio mobile.

**mobile delete [opzioni][servicename]**

Questo comando elimina un servizio mobile insieme al relativo database SQL e server SQL Server.

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-d** o **--deleteData**: eliminare tutti i dati dal servizio mobile dal database.
+ **-a** o **--deleteAll**: eliminare server e database SQL.
+ **-q** o **--quiet**: non chiedere conferma. Utilizzare questa opzione negli script automatici.

**mobile list [opzioni]**

Questo comando elenca i servizi mobili.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [opzioni][servicename]**

Questo comando visualizza i dettagli relativi a un servizio mobile.

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK

**mobile restart [opzioni][servicename]**

Questo comando riavvia un'istanza di un servizio mobile.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [opzioni][servicename]**

Questo comando restituisce i log del servizio mobile, escludendo tutti i tipi di log ad eccezione di `error`.

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-r `<query>`** o **--query `<query>`**: eseguire la query di log specificata.
+ **-t `<type>`** o **--type `<type>`**: filtrare i log restituiti per `<type>` di voce, che può essere `information`, `warning` o `error`.
+ **-k `<skip>`** o **--skip `<skip>`**: ignora il numero di righe specificato da `<skip>`.
+ **-p `<top>`** o **--top `<top>`**: restituisce un numero specifico di righe, specificato da `<top>`.

> [AZURE.NOTE]Il parametro **--query** ha la precedenza sui parametri **--type**, **--skip** e **--top**.

**mobile recover [opzioni][unhealthyservicename] [nomeserviziointegro]**

Questo comando ripristina un servizio mobile non integro spostandolo in un servizio mobile integro in un'area geografica differente.

Il comando supporta l'opzione aggiuntiva seguente:

**-q** o **--quiet**: elimina la richiesta di conferma di ripristino.

**mobile key regenerate [opzioni][servicename] [tipo]**

Questo comando rigenera la chiave applicazione del servizio mobile.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

I tipi di chiave sono `master` e `application`.

> [AZURE.NOTE]Quando si rigenerano le chiavi, i client che usano la vecchia chiave potrebbero non essere più in grado di accedere al servizio mobile. Quando si rigenera la chiave applicazione, è necessario aggiornare l'app con il nuovo valore della chiave.

**mobile key set [opzioni][servicename] [tipo][value]**

Questo comando imposta un valore specifico per la chiave del servizio mobile.


###<a name="Mobile_Configuration"></a>Comandi per la gestione della configurazione di Servizi mobili

**mobile config list [opzioni][servicename]**

Questo comando elenca le opzioni di configurazione per un servizio mobile.

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config get [opzioni][servicename] [chiave]**

Questo comando ottiene un'opzione di configurazione specifica per un servizio mobile, in questo caso lo schema dinamico.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [opzioni][servicename] [chiave][value]**

Questo comando imposta un'opzione di configurazione specifica per un servizio mobile, in questo caso lo schema dinamico.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


###<a name="Mobile_Tables"></a>Comandi per la gestione delle tabelle di Servizi mobili

**mobile table list [opzioni][servicename]**

Questo comando elenca tutte le tabelle nel servizio mobile desiderato.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [opzioni][servicename] [nometabella]**

Questo comando restituisce i dettagli relativi a una tabella specifica.

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [opzioni][servicename] [nometabella]**

Questo comando crea una tabella.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

Il comando supporta l'opzione aggiuntiva seguente:

+ **-p `&lt;permissions>`** o **--permissions `&lt;permissions>`**: elenco delimitato da virgole di coppie `<operation>`=`<permission>`, dove `<operation>` è `insert`, `read`, `update` o `delete` e `&lt;permissions>` è `public`, `application` (predefinito), `user` o `admin`.

**mobile data read [opzioni][servicename] [nometabella][query]**

Questo comando legge i dati da una tabella.

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-k `<skip>`** o **--skip `<skip>`**: ignora il numero di righe specificato da `<skip>`.
+ **-t `<top>`** o **--top `<top>`**: restituisce uno specifico numero di righe specificato da `<top>`.
+ **-l** o **--list**: restituisce i dati in formato elenco.

**mobile table update [opzioni][servicename] [nometabella]**

Questo comando modifica le autorizzazioni di eliminazione per una tabella concedendole solo agli amministratori.

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-p `&lt;permissions>`** o **--permissions `&lt;permissions>`**: elenco delimitato da virgole di coppie `<operation>`=`<permission>`, dove `<operation>` è `insert`, `read`, `update` o `delete` e `&lt;permissions>` è `public`, `application` (predefinito), `user` o `admin`.
+ **--deleteColumn `<columns>`**: elenco delimitato da virgole di colonne da eliminare, come `<columns>`.
+ **-q** or **--quiet**: elimina le colonne senza chiedere conferma.
+ **--addIndex `<columns>`**: elenco delimitato da virgole di colonne da includere nell’indice.
+ **--deleteIndex `<columns>`**: elenco delimito da colonne da escludere dall’indice.

**mobile table delete [opzioni][servicename] [nometabella]**

Questo comando elimina una tabella.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

Specificare il parametro -q per eliminare la tabella senza conferma. Ciò consente di evitare il blocco degli script di automazione.

**mobile data truncate [opzioni][servicename] [nometabella]**

Questo comando rimuove tutte le righe di dati dalla tabella.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


###<a name="Mobile_Scripts"></a>Comandi per la gestione degli script

I comandi in questa sezione vengono usati per gestire gli script del server appartenenti a un servizio mobile. Per ulteriori informazioni, vedere [Utilizzo degli script del server in Servizi mobili](mobile-services-how-to-use-server-scripts.md).

**mobile script list [opzioni][servicename]**

Questo comando elenca gli script registrati, compresi gli script tabella e quelli relativi all'utilità di pianificazione.

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [opzioni][servicename] [nomescript]**

Questo comando scarica lo script insert dalla tabella TodoItem in un file denominato  `todoitem.insert.js` nella sottocartella `table`.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-p `<path>`** o **--path `<path>`**: il percorso del file in cui salvare lo script, in cui la directory di lavoro corrente è quella predefinita.
+ **-f `<file>`** o **--file `<file>`**: il nome del file in cui salvare lo script.
+ **-o** o **--override**: sovrascrivere un file esistente.
+ **-c** o **--console**: scrivere lo script nella console anziché in un file.

**mobile script upload [opzioni][servicename] [nomescript]**

Questo comando carica un nuovo script denominato `todoitem.insert.js` dalla sottocartella `table`.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

Il nome del file deve essere composto dai nomi della tabella e dell'operazione e deve trovarsi nella sottocartella table relativa al percorso in cui viene eseguito il comando. È anche possibile usare il parametro **-f `<file>`** o **--file `<file>`**   per specificare un nome file diverso e un percorso di file che contiene lo script da registrare.


**mobile script delete [opzioni][servicename] [nomescript]**

Questo comando rimuove lo script insert esistente dalla tabella TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

###<a name="Mobile_Jobs"></a>Comandi per la gestione dei processi pianificati

I comandi in questa sezione vengono usati per gestire i processi pianificati appartenenti a un servizio mobile. Per ulteriori informazioni, vedere [Pianificare i processi](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**mobile job list [opzioni][servicename]**

Questo comando elenca i processi pianificati.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [opzioni][servicename] [nomeprocesso]**

Questo comando crea un nuovo processo denominato `getUpdates` pianificato per essere eseguito ogni ora.

	~$azure mobile job create -i 1 -u hour todolist getUpdates
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-i `<number>`** o **--interval `<number>`**: l’intervallo del processo, come intero. Il valore predefinito è `15`.
+ **-u `<unit>`** o **--intervalUnit `<unit>`**: l’unità per _interval_, che può essere uno dei valori seguenti:
	+ **minute** (predefinito)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (processi su richiesta)
+ **-t `<time>`** **--startTime `<time>`** l'ora di inizio della prima esecuzione dello script, in formato ISO. Il valore predefinito è `now`.

> [AZURE.NOTE]I nuovi processi vengono creati con stato disabilitato perché uno script deve ancora essere caricato. Utilizzare il comando **mobile script upload** per caricare uno script e il comando **mobile job update** per abilitare il processo.

**mobile job update [opzioni][servicename] [nomeprocesso]**

Il comando seguente abilita il processo `getUpdates` disabilitato.

	~$azure mobile job update -a enabled todolist getUpdates
	info:    Executing command mobile job update
	info:    mobile job update command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-i `<number>`** o **--interval `<number>`**: l’intervallo del processo, come intero. Il valore predefinito è `15`.
+ **-u `<unit>`** o **--intervalUnit `<unit>`**: l’unità per _interval_, che può essere uno dei valori seguenti:
	+ **minute** (predefinito)
	+ **hour**
	+ **day**
	+ **month**
	+ **none** (processi su richiesta)
+ **-t `<time>`** **--startTime `<time>`** l'ora di inizio della prima esecuzione dello script, in formato ISO. Il valore predefinito è `now`.
+ **-a `<status>`** o **--status `<status>`**: lo stato del processo, che può essere `enabled` o `disabled`.

**mobile job delete [opzioni][servicename] [nomeprocesso]**

Questo comando rimuove il processo pianificato getUpdates dal server TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

> [AZURE.NOTE]L'eliminazione di un processo comporta anche l'eliminazione dello script caricato.

###<a name="Mobile_Scale"></a>Comandi per la scalabilità di un servizio mobile

I comandi in questa sezione vengono usati per ridimensionare un servizio mobile. Per ulteriori informazioni, vedere [Ridimensionamento di un servizio mobile](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**mobile scale show [opzioni][servicename]**

Questo comando visualizza informazioni di scalabilità, compresa l'attuale modalità di calcolo e il numero di istanze.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [opzioni][servicename]**

Questo comando modifica la scala del servizio mobile da modalità gratuita a modalità Premium.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-c `<mode>`** o **--computeMode `<mode>`**: la modalità di calcolo deve essere `Free` o `Reserved`.
+ **-i `<count>`** o **--numberOfInstances `<count>`**: il numero di istanze utilizzate durante l’esecuzione in modalità riservata.

> [AZURE.NOTE]Se si imposta la modalità di calcolo su `Reserved`, tutti i servizi mobili disponibili nella stessa area verranno eseguiti nella modalità premium.


###Comandi per abilitare le funzionalità di anteprima per il servizio mobile

**mobile preview list [opzioni][servicename]**

Questo comando visualizza le funzionalità di anteprima disponibili sul servizio specificato e indica se sono abilitate.

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [opzioni][servicename] [nomefunzionalità]**

Questo comando abilita la funzionalità di anteprima specificata per un servizio mobile. Una volta abilitate, le funzionalità di anteprima non possono essere disabilitate per un servizio mobile.

###Comandi per la gestione delle API del servizio mobile

**mobile api list [opzioni][servicename]**

Questo comando visualizza l'elenco di API personalizzate create per il servizio mobile.

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [opzioni][servicename] [nomeapi]**

Questo comando crea un'API personalizzata per il servizio mobile.

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

Il comando supporta l'opzione aggiuntiva seguente:

**-p** o **--permissions** &lt;autorizzazioni>: un elenco delimitato da virgole di coppie &lt;metodo>=&lt;autorizzazione>.

**mobile api update [opzioni][servicename] [nomeapi]**

Questo comando aggiorna l'API personalizzata specificata del servizio mobile.

Il comando supporta l'opzione aggiuntiva seguente:

Il comando supporta le opzioni aggiuntive seguenti:

+ **-p** o **--permissions** &lt;autorizzazioni>: un elenco delimitato da virgole di coppie &lt;metodo>=&lt;autorizzazione>.
+ **-f** o **--force**: sovrascrive qualsiasi cambiamento personalizzato del file dei metadati delle autorizzazioni.

**mobile api delete [opzioni][servicename] [nomeapi]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

Questo comando elimina l'API personalizzata specificata del servizio mobile.

###Comandi per la gestione delle impostazioni delle applicazioni mobili

**mobile appsetting list [opzioni][servicename]**

Questo comando visualizza le impostazioni delle applicazioni mobili per il servizio specificato.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [opzioni][servicename] [nome][value]**

Questo comando aggiunge un'impostazione di applicazione personalizzata per il servizio mobile.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [opzioni][servicename] [nome]**

Questo comando visualizza l'impostazione di applicazione specificata per il servizio mobile.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [opzioni][servicename] [nome]**

Questo comando visualizza l'impostazione di applicazione specificata per il servizio mobile.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

##<a name="Manage_tool_local_settings"></a>Gestione delle impostazioni locali dello strumento

Le impostazioni locali sono costituite dall'ID sottoscrizione e dal nome dell'account di archiviazione predefinito.

**config list [opzioni]**

Questo comando visualizza le impostazioni di configurazione.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [opzioni] &lt;nome&gt;,&lt;valore&gt;**

Questo comando modifica un'impostazione di configurazione.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

##<a name ="Commands_to_manage_service_bus"></a>Comandi per la gestione del bus di servizio

Usare i comandi seguenti per gestire l'account del bus di servizio.

**sb namespace check [opzioni] &lt;nome>**

Questo comando verifica se uno spazio dei nomi del bus di servizio è valido e disponibile.

**sb namespace create &lt;nome> &lt;posizione>**

Questo comando crea un nuovo spazio dei nomi del bus di servizio.

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete &lt;nome>**

Questo comando rimuove uno spazio dei nomi.

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

Questo comando elenca tutti gli spazi dei nomi creati per l'account.

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

Questo comando visualizza l'elenco di tutte le ubicazioni degli spazi dei nomi disponibili.

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show &lt;nome>**

Questo comando visualizza i dettagli relativi a uno spazio dei nomi specifico.

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify &lt;nome>**

Questo comando verifica se lo spazio dei nomi è disponibile.

##<a name="Commands_to_manage_your_Storage_objects"></a>Comandi per la gestione degli oggetti di archiviazione

###Comandi per la gestione degli account di archiviazione

**storage account list [opzioni]**

Questo comando visualizza gli account di archiviazione definiti nella sottoscrizione.

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [opzioni] <name>**

Questo comando visualizza le informazioni relative all'account di archiviazione specificato, inclusi l'URI e le proprietà dell'account.

**storage account create [opzioni] <name>**

Questo comando crea un account di archiviazione in base alle opzioni fornite.

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-e** o **--label** &lt;etichetta>: l’etichetta per l’account di archiviazione.
+ **-d** o **--description** &lt;descrizione>: la descrizione dell’account di archiviazione.
+ **-l** o **--location** &lt;nome>: l’area geografica in cui creare l’account di archiviazione.
+ **-a** o **--affinity-group** &lt;nome>: il gruppo di affinità a cui associare l’account di archiviazione.
+ **--geoReplication**: indica se la replica geografica è abilitata.
+ **--disable-geoReplication**: indica se la replica geografica è disabilitata.

**storage account set [opzioni] <name>**

Questo comando aggiorna l'account di archiviazione specificato.

	~$ azure storage account set mybasestorage --geoReplication
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

Il comando supporta le opzioni aggiuntive seguenti:

+ **-e** o **--label** &lt;etichetta>: l’etichetta per l’account di archiviazione.
+ **-d** o **--description** &lt;descrizione>: la descrizione dell’account di archiviazione.
+ **-l** o **--location** &lt;nome>: l’area geografica in cui creare l’account di archiviazione.
+ **--geoReplication**: indica se la replica geografica è abilitata.
+ **--disable-geoReplication**: indica se la replica geografica è disabilitata.

**storage account delete [opzioni] <name>**

Questo comando elimina l'account di archiviazione specificato.

Il comando supporta l'opzione aggiuntiva seguente:

**-q** o **--quiet**: non chiedere conferma. Utilizzare questa opzione negli script automatici.

###Comandi per la gestione delle chiavi dell'account di archiviazione

**storage account keys list [opzioni] <name>**

Questo comando elenca le chiavi di accesso primaria e secondaria per l'account di archiviazione specificato.

**storage account keys renew [opzioni] <name>**

###Comandi per la gestione del contenitore di archiviazione

**storage container list [opzioni][prefix]**

Questo comando visualizza l'elenco dei contenitori di archiviazione per un account di archiviazione specificato. L'account di archiviazione è specificato dalla stringa di connessione o dal nome e dalla chiave dell'account.

Il comando supporta le opzioni aggiuntive seguenti:

+ **-p** o **-prefix** &lt;prefisso>: il prefisso del nome del contenitore di archiviazione.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage container show [opzioni][container]** **storage container create [opzioni][container]**

Questo comando crea un contenitore di archiviazione per l'account di archiviazione specificato. L'account di archiviazione è specificato dalla stringa di connessione o dal nome e dalla chiave dell'account.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-p** o **-prefix** &lt;prefisso>: il prefisso del nome del contenitore di archiviazione.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione
+ **-k** o **--account-key** &lt;accountKey>: la chiave dell’account di archiviazione
+ **-c** or **--connection-string** &lt;connectionString>: la stringa di connessione dell’archiviazione
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage container delete [opzioni][container]**

Questo comando elimina il contenitore di archiviazione specificato. L'account di archiviazione è specificato dalla stringa di connessione o dal nome e dalla chiave dell'account.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-p** o **-prefix** &lt;prefisso>: il prefisso del nome del contenitore di archiviazione.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage container set [opzioni][container]**

Questo comando imposta l'elenco di controllo di accesso per il contenitore di archiviazione. L'account di archiviazione è specificato dalla stringa di connessione o dal nome e dalla chiave dell'account.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-p** o **-prefix** &lt;prefisso>: il prefisso del nome del contenitore di archiviazione.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

###Comandi per la gestione del BLOB di archiviazione

**storage blob list [opzioni][container] [prefisso]**

Questo comando restituisce l'elenco dei BLOB di archiviazione nel contenitore di archiviazione specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-p** o **-prefix** &lt;prefisso>: il prefisso del nome del contenitore di archiviazione.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage blob show [opzioni][container] [blob]**

Questo comando visualizza i dettagli del BLOB di archiviazione specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-p** o **-prefix** &lt;prefisso>: il prefisso del nome del contenitore di archiviazione.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage blob delete [opzioni][container] [blob]**

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-b** o **--blob** &lt;blobName>: il nome del BLOB da eliminare.
+ **-q** o **--quiet**: rimuove il BLOB di archiviazione specificato senza conferma.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage blob upload [opzioni][file] [contenitore][blob]**

Questo comando carica il file specificato nel BLOB di archiviazione specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-b** o **--blob** &lt;nomeBLOB>: il nome del BLOB di archiviazione da caricare.
+ **-t** o **--blobtype** &lt;tipoBLOB>: il tipo di BLOB di archiviazione: pagina o blocco.
+ **-p** o **--properties** &lt;proprietà>: le proprietà del BLOB di archiviazione per il file caricato Le proprietà sono costituite da coppie chiave=valore e sono separate da punto e virgola (;). Le proprietà disponibili sono contentType, contentEncoding, contentLanguage e cacheControl.
+ **-m** o **--metadata** &lt;metadati>: i metadati del BLOB di archiviazione per il file caricato. I metadati sono costituiti da coppie chiave=valore e sono separati da punto e virgola (;).
+ **--concurrenttaskcount** &lt;conteggioattività simultanee>: il numero massimo di richieste di caricamento simultanee.
+ **-q** o **--quiet**: sovrascrive il BLOB di archiviazione specificato senza conferma.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

**storage blob download [opzioni][container] [blob][destination]**

Questo comando scarica il BLOB di archiviazione specificato.

Il comando supporta le opzioni aggiuntive seguenti:

+ **--container** &lt;contenitore>: il nome del contenitore di archiviazione da creare.
+ **-b** o **--blob** &lt;nomeBLOB>: il nome del BLOB di archiviazione.
+ **-d** o **--destination** [destinazione]: il file di destinazione o il percorso di directory.
+ **-m** o **--checkmd5**: check md5sum per il file scaricato.
+ **--concurrenttaskcount** &lt;conteggioattivitàsimultanee> il numero massimo di richieste di caricamento simultanee
+ **-q** o **--quiet**: sovrascrive il file di destinazione senza conferma.
+ **-a** o **--account-name** &lt;nomeAccount>: il nome dell’account di archiviazione.
+ **-k** o **--account-key** &lt;chiaveAccount>: la chiave dell’account di archiviazione.
+ **-c** o **--connection-string** &lt;stringaConnessione>: la stringa di connessione della risorsa di archiviazione.
+ **--debug**: esegue il comando di archiviazione in modalità debug.

##<a name ="Commands_to_manage_sql"></a>Comandi per la gestione dei database SQL

Usare i comandi seguenti per gestire i database SQL di Azure.

###Comandi per la gestione dei server SQL Server

Usare i comandi seguenti per gestire i server SQL Server

**sql server create &lt;LoginAmministratore> &lt;PasswordAmministratore> &lt;posizione>**

Questo comando crea un nuovo server di database.

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show &lt;nome>**

Questo comando visualizza i dettagli del server.

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

Questo comando ottiene l'elenco di server.

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete &lt;nome>**

Questo comando elimina un server.

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###Comandi per la gestione dei database SQL

Usare i comandi seguenti per gestire i database SQL.

**sql db create [opzioni] &lt;nomeServer> &lt;noemDatabase> &lt;passwordAmministratore>**

Questo comando crea una nuova istanza di database.

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [opzioni] &lt;nomeServer> &lt;nomeDatabase> &lt;passwordAmministratore>**

Questo comando visualizza i dettagli del database.

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [opzioni] &lt;nomeServer> &lt;passwordAmministratore>**

Questo comando elenca i database.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [opzioni] &lt;nomeServer> &lt;nomeDatabase> &lt;passwordAmministratore>**

Questo comando elimina un database.

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###Comandi per la gestione delle regole del firewall di SQL Server

Usare i comandi seguenti per gestire le regole del firewall di SQL Server

**sql firewallrule create [opzioni] &lt;nomeServer> &lt;nomeRegola> &lt;indirizzoIPiniziale> &lt;indirizzoIPfinale>**

Questo comando crea nuova regola del firewall per un server SQL Server.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [opzioni] &lt;nomeServer> &lt;nomeRegola>**

Questo comando visualizza i dettagli di una regola del firewall.

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [opzioni] &lt;nomeServer>**

Questo comando elenca le regole del firewall.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [opzioni] &lt;nomeServer> &lt;nomeRegola>**

Questo comando elimina una regola del firewall.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

##<a name ="Commands_to_manage_vnet"></a>Comandi per la gestione delle reti virtuali

Usare i comandi seguenti per gestire le reti virtuali.

**network vnet create [opzioni] &lt;posizione>**

Questo comando crea una nuova rete virtuale.

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show &lt;nome>**

Questo comando visualizza i dettagli di una rete virtuale.

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**vnet list**

Questo comando elenca tutte le reti virtuali esistenti.

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete &lt;nome>**

Questo comando elimina la rete virtuale specificata.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [percorso-file]**

Per eseguire operazioni di configurazione avanzate della rete, è possibile esportare la configurazione di rete in locale. Si noti che la configurazione di rete esportata include le impostazioni del server DNS, della rete virtuale, del sito della rete locale e altre impostazioni.

**network import [percorso-file]**

Questo comando importa una configurazione di rete locale.

**network dnsserver register [opzioni] &lt;IPdns>**

Questo comando registra il server DNS che si prevede di usare per la risoluzione dei nomi nella configurazione di rete.

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

Questo comando elenca tutti i server DNS registrati nella configurazione di rete.

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [opzioni] &lt;IPdns>**

Questo comando rimuove una voce di server DNS dalla configurazione di rete.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!---HONumber=58-->