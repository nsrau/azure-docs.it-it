<properties linkid="manage-linux-other-resources-command-line-tools" urlDisplayName="Command-Line Tools" pageTitle="Azure Command-Line Tools for Mac and Linux" metaKeywords="Azure command-line, Azure tools Mac, Azure tools Linux" description="Learn about using the command-line tool for Mac and Linux in Azure." metaCanonical="" services="web-sites,virtual-machines,mobile-services,cloud-services" documentationCenter="" title="" authors="larryfr" solutions="" manager="" editor="" />

Strumenti da riga di comando di Azure per Mac e Linux
=====================================================

Questo strumento fornisce la funzionalità per la creazione, la distribuzione e la gestione di macchine virtuali, siti Web e Servizi mobili di Azure da desktop Mac e Linux. Questa funzionalità è simile a quella fornita dai cmdlet di Windows PowerShell installati con Azure SDK per .NET, Node.JS e PHP.

Per installare lo strumento su un Mac, scaricare ed eseguire il [programma di installazione di Azure SDK](http://go.microsoft.com/fwlink/?LinkId=252249).

Per installare lo strumento su Linux, installare l'ultima versione di Node.JS, quindi utilizzare NPM per procedere:

    npm install azure-cli -g

I parametri facoltativi sono indicati tra parentesi quadre (ad esempio [parametro]). Tutti gli altri parametri sono obbligatori.

Oltre ai parametri facoltativi specifici del comando documentati qui, vi sono tre parametri opzionali che possono essere utilizzati per visualizzare output dettagliato come opzioni richiesta e codici di stato. Il parametro -v fornisce output dettagliato, mentre il parametro -vv fornisce un output con un dettaglio ancor maggiore. Utilizzando l'opzione --json verrà visualizzato il risultato in formato json non elaborato.

**Sommario:**

-   [Gestione delle informazioni relative all'account e le impostazioni di pubblicazione](#Manage_your_account_information_and_publish_settings)
-   [Comandi per la gestione delle macchine virtuali di Azure](#Commands_to_manage_your_Azure_virtual_machines)
-   [Comandi per la gestione degli endpoint delle macchine virtuali di Azure](#Commands_to_manage_your_Azure_virtual_machine_endpoints)
-   [Comandi per la gestione delle immagini delle macchine virtuali di Azure](#Commands_to_manage_your_Azure_virtual_machine_images)
-   [Comandi per la gestione dei dischi dati delle immagini delle macchine virtuali di Azure](#Commands_to_manage_your_Azure_virtual_machine_data_disks)
-   [Comandi per la gestione dei servizi cloud di Azure](#Commands_to_manage_your_Azure_cloud_services)
-   [Comandi per la gestione dei certificati di Azure](#Commands_to_manage_your_Azure_certificates)
-   [Comandi per la gestione dei siti Web](#Commands_to_manage_your_web_sites)
-   [Comandi per la gestione di Servizi mobili di Azure](#Commands_to_manage_mobile_services)
-   [Gestione delle impostazioni locali dello strumento](#Manage_tool_local_settings)
-   [Comandi per la gestione del bus di servizio](#Commands_to_manage_service_bus)
-   [Comandi per la gestione dei database SQL](#Commands_to_manage_sql)
-   [Comandi per la gestione delle reti virtuali](#Commands_to_manage_vnet)

Gestione delle informazioni relative all'account e le impostazioni di pubblicazione
-----------------------------------------------------------------------------------

Le informazioni relative alla sottoscrizione di Azure vengono utilizzate dallo strumento per connettersi all'account dell'utente. Tali informazioni possono essere ottenute dal portale di Azure in un file di impostazioni di pubblicazione come illustrato di seguito. Il file di impostazioni di pubblicazione può essere quindi importato come impostazione di configurazione locale persistente che verrà utilizzata per le operazioni successive. Sarà necessario importare le impostazioni di pubblicazione una sola volta.

**account download [opzioni]**

Questo comando avvia un browser per scaricare il file con estensione .publishsettings dal portale di Azure.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**account import [opzioni] &lt;file\>**

Questo comando importa un file di impostazioni di pubblicazione o un certificato che verrà utilizzato dallo strumento per i passaggi successivi.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

**Nota**

Il file di impostazioni di pubblicazione può contenere dettagli (ovvero nome e ID sottoscrizione) di più di una sottoscrizione. Quando si importa il file di impostazioni di pubblicazione, la prima sottoscrizione viene utilizzata come descrizione predefinita. Per utilizzare una sottoscrizione diversa, eseguire il comando seguente.

`~$ azure config set subscription <other-subscription-id>`

**account clear [opzioni]**

Questo comando consente di rimuovere le impostazioni di pubblicazione che sono state importate. Utilizzare questo comando se si è terminato il lavoro con lo strumento nel computer in uso e si desidera assicurarsi che nessuno possa utilizzarlo successivamente con quell'account.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**account list [opzioni]**

Elenca le sottoscrizioni importate

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
           -----  -------
           data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [opzioni] &lt;sottoscrizione\>**

Imposta la sottoscrizione attuale

### Comandi per la gestione dei gruppi di affinità

**account affinity-group list [opzioni]**

Questo comando consente di elencare i gruppi di affinità di Azure.

È possibile impostare gruppi di affinità quando un gruppo di macchine virtuali si estende su più macchine fisiche. Il gruppo di affinità specifica che le macchine fisiche dovrebbero essere il più vicino possibile una all'altra, per ridurre la latenza di rete.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**account affinity-group create [opzioni] &lt;nome\>**

Questo comando consente di creare un nuovo gruppo di affinità

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**account affinity-group show [opzioni] &lt;nome\>**

Questo comando consente di visualizzare i dettagli del gruppo di affinità

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

**account affinity-group delete [opzioni] &lt;nome\>**

Questo comando consente di eliminare il gruppo di affinità specificato

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec
     [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

### Comandi per la gestione dell'ambiente dell'account

**account env list [opzioni]**

Elenco degli ambienti dell'account

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Nome
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**account env show [opzioni] [ambiente]**

Visualizza i dettagli relativi all'ambiente dell'account

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/
    LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/
    LinkId=2544
    info:    account env show command OK

**account env add [opzioni] [ambiente]**

Questo comando consente di aggiungere un ambiente all'account

**account env set [opzioni] [ambiente]**

Questo comando consente di impostare l'ambiente dell'account

**account env delete [opzioni] [ambiente]**

Questo comando consente di eliminare l'ambiente specificato dall'account

Comandi per la gestione delle macchine virtuali di Azure
--------------------------------------------------------

Nel diagramma seguente vengono illustrate le modalità di hosting delle macchine virtuali di Azure nell'ambiente della distribuzione di produzione di un servizio cloud di Azure.

![Diagramma tecnico di Azure](./media/command-line-tools/architecturediagram.jpg)

**create-new** consente di creare l'unità nell'archiviazione BLOB (E:/ nel diagramma); **attach** consente di collegare un disco già creato, ma non collegato, a una macchina virtuale.

**vm create [opzioni] &lt;nome\_dns\> &lt;immagine\> &lt;nome\_utente\> [password]**

Questo comando consente di creare una nuova macchina virtuale di Azure. Per impostazione predefinita, ogni macchina virtuale viene creata in un proprio servizio cloud. È tuttavia possibile specificare l'aggiunta di una macchina a un servizio cloud esistente utilizzando l'opzione -c, come indicato qui di seguito.

Si noti che il comando vm create, come accade nel portale di Azure, consente di creare macchine virtuali solo nell'ambiente della distribuzione di protezione. Non è attualmente disponibile alcuna opzione per la creazione di una macchina virtuale nell'ambiente della distribuzione di gestione temporanea di un servizio cloud. Si noti che con questo comando viene creato un account di archiviazione di Azure se non ne esiste già uno per la sottoscrizione.

È possibile specificare un percorso tramite il parametro --location o un gruppo di affinità tramite il parametro --affinity-group. Se non viene utilizzato nessuno di questi due parametri, verrà richiesto di fornire un percorso scegliendolo da un elenco di percorsi validi.

La lunghezza della password fornita deve essere compresa tra 8 e 123 caratteri e soddisfare i requisiti di complessità della password del sistema operativo utilizzato per la macchina virtuale.

Se per gestire una macchina virtuale Linux distribuita si prevede la necessità di utilizzare SSH (che è in genere il metodo più diffuso) è necessario abilitare SSH mediante l'opzione -e al momento della creazione della macchina virtuale stessa. Non è possibile abilitare SSH dopo che una macchina virtuale è stata creata.

Per le macchine virtuali Windows, è possibile abilitare RDP in un secondo momento mediante l'aggiunta della porta 3389 come endpoint.

Per questo comando sono supportati i seguenti parametri facoltativi:

**-c, --connect** Crea la macchina virtuale all'interno di una distribuzione già creata in un servizio di hosting. Se il parametro -vm-name non viene utilizzato con questa opzione, il nome della nuova macchina virtuale verrà generato automaticamente. <br />
 **-n, --vm-name** Specifica il nome della macchina virtuale. Per impostazione predefinita, questo parametro prende il nome del servizio di hosting. Se -vmname non viene specificato, il nome per la nuova macchina virtuale viene generato come &lt;nome\_servizio\> &lt;id\>, dove &lt;id\> è il numero di macchine virtuali esistenti nel servizio più 1. Se ad esempio si utilizza questo comando per aggiungere una nuova macchina virtuale a un servizio di hosting denominato MyService con una macchina virtuale già esistente, la nuova macchina virtuale viene denominata MyService2. <br />
**-u, --blob-url** Specifica l'archiviazione BLOB da cui creare il disco di sistema della macchina virtuale. <br />
 **-z, --vm-size** Specifica le dimensioni della macchina virtuale. I valori validi sono "extrasmall", "small", "medium", "large", "extralarge". Il valore predefinito è "small". <br />
 **-r** Aggiunge connettività RDP a una macchina virtuale Windows. <br />
 **-e, --ssh** Aggiunge connettività SSH a una macchina virtuale Windows. <br />
 **-t, --ssh-cert** Specifica il certificato SSH. <br />
 **-s** La sottoscrizione in uso <br />
 **-o, --community** L'immagine specificata è un'immagine della community <br />
 **-w** Nome della rete virtuale <br />
 **-l, --location** Specifica l'ubicazione (ad esempio "Stati Uniti centro-settentrionali"). <br />
 **-a, --affinity-group** Specifica il gruppo di affinità. <br />
 **-w, --virtual-network-name** Specifica la rete virtuale in cui aggiungere la nuova macchina virtuale. Le reti virtuali possono essere configurate e gestite dal portale di Azure. <br />
 **-b, --subnet-names** Specifica il nome subnet da assegnare alla macchina virtuale.

Nell'esempio seguente MSFT\_\_Win2K8R2SP1-120514-1520-141205-01-en-us-30GB è un'immagine fornita dalla piattaforma. Per ulteriori informazioni sulle immagini del sistema operativo, vedere il comando vm image list.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "Western US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************                                     
    info:   vm create command OK

**vm create-from &lt;nome\_dns\> &lt;file\_ruolo\>**

Questo comando consente di creare una nuova macchina virtuale di Azure da un file di ruolo JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**vm list [opzioni]**

Questo comando consente di elencare le macchine virtuali di Azure. L'opzione -json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status                  
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**vm location list [opzioni]**

Questo comando consente di elencare i percorsi di tutti gli account Azure disponibili.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name                                    
    data:   ---------------------  ------------
    data:   Azure Preview  West US     
    info:   account location list command OK

**vm show [opzioni] &lt;nome\>**

Questo comando consente di visualizzare i dettagli relativi a una macchina virtuale di Azure. L'opzione -json specifica che i risultati vengono restituiti in formato JSON non elaborato.

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

**vm delete [opzioni] &lt;nome\>**

Questo comando consente di eliminare una macchina virtuale di Azure. Per impostazione predefinita, questo comando non elimina l'archivio BLOB di Azure da cui vengono creati il disco del sistema operativo e il disco dati. Per eliminare l'archiviazione BLOB insieme alla macchina virtuale su cui si basa, specificare l'opzione -b.

    ~$ azure vm delete my-vm 
    info:   Executing command vm delete
    info:   vm delete command OK

**vm start [opzioni] &lt;nome\>**

Questo comando consente di avviare una macchina virtuale di Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**vm restart [opzioni] &lt;nome\>**

Questo comando consente di riavviare una macchina virtuale di Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**vm shutdown [opzioni] &lt;nome\>**

Questo comando consente di arrestare una macchina virtuale di Azure. È possibile utilizzare l'opzione -p per specificare che la risorsa di calcolo non deve essere rilasciata in fase di arresto.

`~$ azure vm shutdown my-vm info:   Executing command vm shutdown info:   vm shutdown command OK`

**vm capture &lt;nome-vm\> &lt;nome-immagine-dest\>**

Questo comando consente di acquisire un'immagine di una macchina virtuale di Azure.

L'acquisizione di un'immagine della macchina virtuale è possibile unicamente quandola macchina virtuale è arrestata.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**vm export [opzioni] &lt;nome\_vm\> &lt;percorso-file\>**

Questo comando consente di esportare un'immagine di una macchina virtuale di Azure in un file.

    ~$ azure vm export "myvm" "C:~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK
    quot;
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

Comandi per la gestione degli endpoint delle macchine virtuali di Azure
-----------------------------------------------------------------------

Nel diagramma seguente viene illustrata l'architettura di una tipica distribuzione di più istanze di una macchina virtuale. Si noti che, in questo esempio, la porta 3389 è aperta su ogni macchina virtuale (per l'accesso RDP) e che è anche presente un indirizzo IP interno (ad esempio 168.55.11.1) su ogni macchina virtuale. Questo viene utilizzato dal servizio di bilanciamento del carico per l'indirizzamento del traffico alla macchina virtuale. L'indirizzo IP interno può essere utilizzato anche per la comunicazione tra macchine virtuali.

![azurenetworkdiagram](./media/command-line-tools/networkdiagram.jpg)

Le richieste esterne alle macchine virtuali passano attraverso un servizio di bilanciamento del carico. Per questo motivo, in distribuzioni con più macchine virtuali, non è possibile specificare richieste indirizzate a una macchina virtuale specifica. Per le distribuzioni con più macchine virtuali, è necessario configurare il mapping delle porte tra le macchine virtuali (porta-vm) e il servizio di bilanciamento del carico (porta-bc).

**vm endpoint create &lt;nome\_vm\> &lt;porta\_bc\> [porta\_vm]**

Questo comando consente di creare un nuovo endpoint per una macchina virtuale. È inoltre possibile utilizzare le opzioni -u oppure --enable-direct-server-return per specificare se abilitare Direct Server Return sull'endpoint, che per impostazione predefinita è disabilitato.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm endpoint create-multiple [options] &lt;vm-name> &lt;lb-port>[:&lt;vm-port>[:&lt;protocol>[:&lt;lb-set-name>[:&lt;prob-protocol>:&lt;lb-prob-port>[:&lt;prob-path>]]]]] ]{1-*}**

Creare più endpoint per macchine virtuali. È inoltre possibile utilizzare le opzioni -u oppure --enable-direct-server-return per specificare se abilitare Direct Server Return sull'endpoint, che per impostazione predefinita è disabilitato.

**vm endpoint delete &lt;nome\_vm\> &lt;porta\_bc\>**

Questo comando consente di eliminare un endpoint di una macchina virtuale.

    ~$ azure vm endpoint delete my-vm 8888
    azure vm endpoint delete my-vm 8888
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**vm endpoint list &lt;nome\_vm\>**

Questo comando consente di elencare tutti gli endpoint della macchina virtuale. L'opzione -json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port                                        
    data:   ----  -------------  ----------
    data:   ssh   22             22

**vm endpoint update [opzioni] &lt;nome\_vm\> &lt;nome\_endpoint\>**

Questo comando consente di aggiornare un endpoint VM ai nuovi valori utilizzando le opzioni seguenti.

    -n, --endpoint-name <nome>          nuovo nome dell'endpoint
    -t, --lb-port <porta>                nuova porta del servizio di bilanciamento di carico
    -t, --vm-port <porta>                nuova porta locale
    -o, --endpoint-protocol <protocollo> nuovo protocollo del livello di trasporto per la porta (tcp o udp) 

**vm endpoint show [opzioni] &lt;nome\_vm\>**

Questo comando consente di visualizzare i dettagli dell'endpoint in una VM

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

Comandi per la gestione delle immagini delle macchine virtuali di Azure
-----------------------------------------------------------------------

Le immagini di macchine virtuali sono acquisizioni di macchine virtuali già configurate che possono essere replicate in base alle esigenze.

**vm image list [opzioni]**

Con questo comando è possibile ottenere un elenco di immagini di macchine virtuali. Vi sono tre tipi di immagini: immagini create da Microsoft, con prefisso "MSFT", immagini create da terze parti, che hanno in genere come prefisso il nome del fornitore, e le immagini create dall'utente. Per creare un'immagine, è possibile acquisire una macchina virtuale esistente o creare un'immagine da un file VHD personalizzato caricato nell'archiviazione BLOB. Per ulteriori informazioni sull'utilizzo di un file VHD personalizzato, vedere il comando vm image create. L'opzione -json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK   

**vm image show [opzioni] &lt;nome\>**

Questo comando consente di visualizzare i dettagli di un immagine di macchina virtuale.

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

**vm image delete [opzioni] &lt;nome\>**

Questo comando consente di eliminare un'immagine di una macchina virtuale.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image                                         
    info:   vm image delete command OK

**vm image create &lt;nome\> [percorso\_origine]**

Questo comando consente di creare un'immagine di una macchina virtuale. I file VHD personalizzati vengono caricati nell'archiviazione BLOB, che è l'origina da cui viene creata l'immagine della macchina virtuale. L'immagine potrà quindi essere utilizzata per creare una macchina virtuale. I parametri relativi all'ubicazione e al sistema operativo sono obbligatori.

Alcuni sistemi impongono limitazioni sui descrittori di file per processo. Se questo limite viene superato, dallo strumento viene visualizzato un errore di limite di descrittori di file. È possibile eseguire nuovamente il comando utilizzando il parametro -p &lt;numero\> per ridurre il numero massimo di caricamenti paralleli. Il numero massimo di caricamenti paralleli predefinito è 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

Comandi per la gestione dei dischi dati delle immagini delle macchine virtuali di Azure
---------------------------------------------------------------------------------------

I dischi dati sono file con estensione vhd nell'archiviazione BLOB che possono essere utilizzati da una macchina virtuale. Per ulteriori informazioni sulle modalità di distribuzione dei dischi dati nell'archiviazione BLOB, vedere il diagramma tecnico di Azure riportato in precedenza in questo articolo.

I comandi per collegare dischi dati (azure vm disk attach and azure vm disk attach-new) consentono di assegnare un numero di unità logica (LUN, Logical Unit Number) al disco dati collegato, come previsto dal protocollo SCSI. Al primo disco dati collegato a una macchina virtuale viene assegnato il numero LUN 0, a quello successivo LUN 1 e così via.

Quando si scollega un disco dati con il comando azure vm disk detach, utilizzare il parametro &lt;lun\> per indicare il disco da scollegare.

**Nota**

Si noti che i dischi dati devono essere sempre scollegati in ordine inverso, iniziando dall'unità con il numero LUN più alto assegnato. Il livello SCSI di Linux non supporta lo scollegamento di un'unità mentre è ancora collegata un'unità con un numero LUN superiore. L'unità con LUN 0, ad esempio, non può essere scollegata se l'unità LUN 1 è ancora collegata.

**vm disk show [opzioni] &lt;nome\>**

Questo comando consente di visualizzare i dettagli relativi a un disco di Azure.

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
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**vm disk list [opzioni] [nome-vm]**

Questo comando consente di elencare i dischi di Azure o i dischi collegati a una macchina virtuale specificata. Se viene eseguito con un nome di macchina virtuale come parametro, restituisce tutti i dischi collegati alla macchina virtuale. L'unità LUN 1viene creata insieme alla macchina virtuale ed eventuali altri dischi elencati vengono collegati separatamente.

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

**vm disk delete [opzioni] &lt;nome\>**

Questo comando consente di eliminare un disco di Azure da un archivio personale. Il disco deve essere disconnesso dalla macchina virtuale prima dell'eliminazione.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052                  
    info:   vm disk delete command OK

**vm disk create &lt;nome\> [percorso-origine]**

Questo comando consente di caricare e registrare un disco di Azure. È necessario specificare --blob-url, --location o --affinity-group. Se si utilizza questo comando con [percorso-origine], viene caricato il file con estensione vhd specificato e viene creata una nuova immagine. È quindi possibile collegare questa immagine a una macchina virtuale utilizzando il comando vm disk attach.

Alcuni sistemi impongono limitazioni sui descrittori di file per processo. Se questo limite viene superato, dallo strumento viene visualizzato un errore di limite di descrittori di file. È possibile eseguire nuovamente il comando utilizzando il parametro -p &lt;numero\> per ridurre il numero massimo di caricamenti paralleli. Il numero massimo di caricamenti paralleli predefinito è 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "Western US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB                                                       
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s 
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**vm disk upload [opzioni] &lt;percorso\_origine\> &lt;url-blob\> &lt;chiave\_account\_archiviazione\>**

Questo comando consente di caricare un disco di macchina virtuale

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload                                                      
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**vm disk attach &lt;nome\_vm\> &lt;nome\_immagine\_disco\>**

Questo comando consente di collegare un disco esistente nell'archiviazione BLOB a una macchina virtuale distribuita in un servizio cloud.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**vm disk attach-new &lt;nome\_vm\> &lt;dimensioni\_in\_gb\> [url\_blob]**

Questo comando consente di collegare un disco di dati a una macchina virtuale di Azure. In questo esempio, 20 è la dimensione in gigabyte del nuovo disco da collegare. È possibile utilizzare facoltativamente un URL di BLOB come ultimo argomento, per specificare in modo esplicito il BLOB di destinazione da creare. Se non si specifica un URL di BLOB, verrà generato un oggetto BLOB automaticamente.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**vm disk detach &lt;nome\_vm\> &lt;lun\>**

Questo comando consente di scollegare un disco dati collegato a una macchina virtuale di Azure. &lt;lun\> identifica il disco da scollegare. Per ottenere un elenco di dischi associati a un disco prima che questo venga scollegato, utilizzare il comando vm disk-list &lt;nome\_vm\>.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

Comandi per la gestione dei servizi cloud di Azure
--------------------------------------------------

I servizi cloud di Azure sono applicazioni e servizi ospitati in ruoli Web e ruoli di lavoro. I comandi seguenti possono essere utilizzati per gestire i servizi cloud di Azure.

**service create [opzioni] &lt;nome\_servizio\>**

Questo comando consente di creare un nuovo servizio cloud

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

**service show [opzioni] &lt;nome\_servizio\>**

Questo comando consente di visualizzare i dettagli di un servizio cloud di Azure

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

Questo comando consente di elencare i servizi cloud di Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status                                                    
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**service delete [opzioni] &lt;nome\>**

Questo comando consente di eliminare un servizio cloud di Azure.

    ~$ azure cloud-service delete myservice
    info:   Executing command cloud-service delete myservice 
    info:   cloud-service delete command OK

Comandi per la gestione dei certificati di Azure
------------------------------------------------

I certificati di Azure sono certificati SSL collegati all'account Azure.

**service cert list [opzioni]**

Questo comando consente di elencare i certificati di Azure.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services                                                      
    + Fetching certificates                                                        
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1     
    info:   service cert list command OK

**service cert create &lt;prefisso\_dns\> &lt;file\> [password]**

Questo comando consente di caricare un certificato. Per certificati non protetti da password è possibile lasciare vuoto il prompt della password.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password: 
    + Creating certificate                                                         
    info:   service cert create command OK

**service cert delete [opzioni] &lt;identificazione\_personale\>**

Questo comando consente di eliminare un certificato.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate                                                         
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

Comandi per la gestione dei siti Web
------------------------------------

Un sito Web di Azure è una configurazione Web accessibile tramite URI. I siti Web sono ospitati in macchine virtuali ma non è necessario che l'utente si occupi delle attività di creazione e distribuzione della macchina virtuale. Tali attività vengono gestite da Azure in modo automatico.

**site list [opzioni]**

Questo comando consente di elencare i siti Web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names                                        
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net     
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net     
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**site set [opzioni] [nome]**

Questo comando consente di impostare le opzioni di configurazione per il sito Web [nome]

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

**site create [opzioni] [nome]**

Questo comando consente di creare un nuovo sito Web e una directory locale.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

**Nota**

Il nome del sito deve essere univoco. Non è possibile creare un sito con lo stesso nome DNS di un sito esistente.

**site portal [opzioni] [nome]**

Questo comando consente di aprire il portale in un browser per la gestione dei siti Web.

    ~$ azure site portal mysite
    info:   Executing command site portal
    info:   Launching browser to https://windows.azure.net/#Workspaces/WebsiteExtension/Website/mysite/dashboard
    info:   site portal command OK

**site browse [opzioni] [nome]**

Questo comando consente di aprire il sito Web in un browser.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**site show [opzioni] [nome]**

Questo comando mostra i dettagli relativi a un sito Web.

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

**site delete [opzioni] [nome]**

Questo comando consente di eliminare un sito Web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

**site start [opzioni] [nome]**

Questo comando consente di avviare un sito Web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**site stop [opzioni] [nome]**

Questo comando consente di arrestare un sito Web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**site location list [opzioni]**

Questo comando consente di elencare le ubicazioni dei siti Web

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

### Comandi per gestire le impostazioni delle applicazioni del sito Web

**site appsetting list [opzioni] [nome]**

Questo comando consente di elencare le impostazioni app aggiunte al sito Web

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test value
    info:    site appsetting list command OK

**site appsetting add [opzioni] &lt;coppiaChiaveValore\> [nome]**

Questo comando consente di aggiungere un'impostazione app per il sito Web come coppia chiave-valore

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting delete [opzioni] &lt;chiave\> [nome]**

Questo comando cancella l'impostazione app specificata dal sito Web

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test
     [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting show [opzioni] &lt;chiave\> [nome]**

Questo comando consente di visualizzare i dettagli dell'impostazione app specificata

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

### Comandi per la gestione dei certificati del sito Web

**site cert list [opzioni] [nome]**

Questo comando consente di visualizzare un elenco dei certificati del sito Web

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**site cert add [opzioni] &lt;percorso\_certificato\> [nome]**

**site cert delete [opzioni] &lt;identificazione\_personale\> [nome]**

**site cert show [opzioni] &lt;identificazione\_personale\> [nome]**

Questo comando consente di visualizzare i dettagli del certificato

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

### Comandi per la gestione delle stringhe di connessione del sito Web

**site connectionstring list [opzioni] [nome]**

**site connectionstring add [opzioni] &lt;nome\_connessione\> &lt;valore\> &lt;tipo\> [nome]**

**site connectionstring delete [opzioni] &lt;nome\_connessione\> [nome]**

**site connectionstring show [opzioni] &lt;nome\_connessione\> [nome]**

### Comandi per la gestione dei documenti predefiniti del sito Web

**site defaultdocument list [opzioni] [nome]**

**site defaultdocument add [opzioni] &lt;documento\> [nome]**

**site defaultdocument delete [opzioni] &lt;documento\> [nome]**

### Comandi per la gestione delle distribuzioni del sito Web

**site deployment list [opzioni] [nome]**

**site deployment show [opzioni] &lt;id\_commit\> [nome]**

**site deployment redeploy [opzioni] &lt;id\_commit\> [nome]**

**site deployment github [opzioni] [nome]**

**site deployment user set [opzioni] [nome\_utente] [password]**

### Comandi per la gestione dei domini del sito Web

**site domain list [opzioni] [nome]**

**site domain add [opzioni] &lt;dn\> [nome]**

**site domain delete [opzioni] &lt;dn\> [nome]**

### Comandi per la gestione dei mapping del gestore del sito Web

**site handler list [opzioni] [nome]**

**site handler add [opzioni] &lt;estensione\> &lt;processore\> [nome]**

**site handler delete [opzioni] &lt;estensione\> [nome]**

### Comandi per la gestione della diagnostica del sito Web

**site log download [opzioni] [nome]**

Consente di scaricare un file ZIP contenente la diagnostica del sito Web

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**site log tail [opzioni] [nome]**

Questo comando consente di connettere il terminale in uso al servizio di streaming dei log

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [opzioni] [noame]**

Questo comando consente di configurare le opzioni di diagnostica

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

### Comandi per la gestione degli archivi del sito Web

**site repository branch [opzioni] &lt;ramo\> [nome]**

**site repository delete [opzioni] [nome]**

**site repository sync [opzioni] [nome]**

### Comandi per la gestione del ridimensionamento del sito Web

**site scale mode [opzioni] &lt;modalità\> [nome]**

**site scale instances [opzioni] &lt;istanze\> [nome]**

Comandi per la gestione di Servizi mobili di Azure
--------------------------------------------------

Servizi mobili di Azure è costituito da un insieme di servizi Azure che abilitano le funzionalità di back-end delle applicazioni. I comandi relativi a Servizi mobili sono suddivisi nelle categorie seguenti:

-   [Comandi per la gestione delle istanze di Servizi mobili](#Mobile_Services)
-   [Comandi per la gestione della configurazione di Servizi mobili](#Mobile_Configuration)
-   [Comandi per la gestione delle tabelle di Servizi mobili](#Mobile_Tables)
-   [Comandi per la gestione degli script di Servizi mobili](#Mobile_Scripts)
-   [Comandi per la gestione dei processi pianificati](#Mobile_Jobs)
-   [Comandi per il ridimensionamento di un servizio mobile](#Mobile_Scale)

Le opzioni seguenti si applicano alla maggior parte dei comandi di Servizi mobili:

-   **-h** o **--help**: Consente di visualizzare le informazioni sull'utilizzo dell'output.
-   **-s `<id>`** o **--subscription `<id>`**: Utilizza una sottoscrizione specifica, specificata come `<id>`.
-   **-v** o **--verbose**: Visualizza output dettagliato.
-   **--json**: Visualizza output JSON.

### Comandi per la gestione delle istanze di servizi mobili

**mobile locations [opzioni]**

Questo comando consente di elencare le posizioni geografiche supportate da Servizi mobili.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US        
    info:    North Europe

**mobile create [opzioni] [nome\_servizio] [nomeutenteAmminSql] [passwordAmminSql]**

Questo comando consente di creare un servizio mobile insieme a un database SQL e a un server SQL Server.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Il comando supporta le opzioni aggiuntive seguenti:

-   **-r `<sqlServer>`** o **--sqlServer `<sqlServer>`**: utilizza un server di database SQL esistente, specificato come `<sqlServer>`.
-   **-d `<sqlDb>`** o **--sqlDb `<sqlDb>`**: utilizza un database SQL esistente, specificato come `<sqlDb>`.
-   **-l `<ubicazione>`** o **--location `<ubicazione>`**: crea il servizio in un'ubicazione specifica, specificata come `<ubicazione>`. Eseguire il comando azure mobile locations per ottenere le ubicazioni disponibili.
-   **--sqlLocation `<ubicazione>`**: crea il server SQL in un'`<ubicazione>` specifica. L'ubicazione predefinita è quella del servizio mobile.

**mobile delete [opzioni] [nome\_servizio]**

Questo comando consente di eliminare un servizio mobile insieme al relativo database SQL e server SQL Server.

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

-   **-d** o **--deleteData**: elimina tutti i dati relativi al servizio mobile dal database.
-   **-a** o **--deleteAll**: Elimina il Database SQL e il server SQL Server.
-   **-q o \*\*--quiet**: Non chiede alcuna conferma. Utilizzare questa opzione negli script automatici.

**mobile list [opzioni]**

Questo comando consente di elencare i servizi mobili.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**mobile show [opzioni] [nome\_servizio]**

Questo comando consente di visualizzare i dettagli relativi a un servizio mobile.

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

**mobile restart [opzioni] [nome\_servizio]**

Questo comando consente di riavviare un'istanza di un servizio mobile.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**mobile log [opzioni] [nome\_servizio]**

Questo comando restituisce i log del servizio mobile, escludendo dal filtro tutti i tipi di voci ad eccezione di `error`.

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

-   **-r `<query>`** o **--query `<query>`**: esegue la query nel log specificato.
-   **-t `<tipo>`** o **--type `<tipo>`**: filtra i log restituiti dalla voce `<tipo>`, che può essere `information`, `warning` o `error`.
-   **-k `<ignora>`** o **--skip `<ignora>`**: ignora il numero di righe specificato in `<ignora>`.
-   **-p `<righe>`** o **--top `<righe>`**: restituisce un numero specifico di righe, specificato in `<righe>`.

**Nota**

Il parametro \*\*--query\*\* ha la precedenza sui parametri \*\*--type\*\*, \*\*--skip\*\* e \*\*--top\*\*.

**mobile key regenerate [opzioni] [nome\_servizio] [tipo]**

Questo comando consente di rigenerare la chiave applicazione del servizio mobile.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

I tipi di chiave sono `master` e `application`.

**Nota**

Quando si rigenerano le chiavi, i client che utilizzano la vecchia chiave potrebbero non essere più in grado di accedere al servizio mobile. Quando si rigenera la chiave applicazione, è necessario aggiornare l'app con il nuovo valore della chiave.

### Comandi per la gestione della configurazione di Servizi mobili

**mobile config list [opzioni] [nome\_servizio]**

Questo comando consente di elencare le opzioni di configurazione per un servizio mobile.

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

**mobile config get [opzioni] [nome\_servizio] [chiave]**

Questo comando consente di ottenere un'opzione di configurazione specifica per un servizio mobile, in questo caso lo schema dinamico.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**mobile config set [opzioni] [nome\_servizio] [chiave] [valore]**

Questo comando consente di impostare un'opzione di configurazione specifica per un servizio mobile, in questo caso lo schema dinamico.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK

### Comandi per la gestione delle tabelle di Servizi mobili

**mobile table list [opzioni] [nome\_servizio]**

Questo comando consente di elencare tutte le tabelle nel servizio mobile desiderato.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**mobile table show [opzioni] [nome\_servizio] [nome\_tabella]**

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

**mobile table create [opzioni] [nome\_servizio] [nome\_tabella]**

Questo comando consente di creare una tabella.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Il comando supporta l'opzione aggiuntiva seguente:

-   **-p `<autorizzazioni>`** o **--permissions `<autorizzazioni>`**: elenco delimitato da virgole di coppie `<operazione>`=`<autorizzazione>`, dove `<operazione>` è `insert`, `read`, `update` o `delete` e `<autorizzazioni>` è `public`, `application` (predefinito), `user` o `admin`.

**mobile data read [opzioni] [nome\_servizio] [nome\_tabella] [query]**

Questo comando consente di leggere i dati da una tabella.

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

-   **-k `<ignora>`** o **--skip `<ignora>`**: ignora il numero di righe specificato in `<ignora>`.
-   **-t `<righe>`** o **--top `<righe>`**: restituisce un numero specifico di righe, specificato in `<righe>`.
-   **-l** o **--list**: restituisce i dati in formato elenco.

**mobile table update [opzioni] [nome\_servizio] [nome\_tabella]**

Questo comando consente di modificare le autorizzazioni di eliminazione per una tabella concedendole solo agli amministratori.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Il comando supporta le opzioni aggiuntive seguenti:

-   **-p `<autorizzazioni>`** o **--permissions `<autorizzazioni>`**: elenco delimitato da virgole di coppie `<operazione>`=`<autorizzazione>`, dove `<operazione>` è `insert`, `read`, `update` o `delete` e `<autorizzazioni>` è `public`, `application` (predefinito), `user` o `admin`.
-   **--deleteColumn `<colonne>`**: elenco delimitato da virgole delle colonne da eliminare, come da `<colonne>`.
-   **-q** o **--quiet**: elimina le colonne senza chiedere conferma.
-   **--addIndex `<colonne>`**: elenco delimitato da virgole di colonne da includere nell'indice.
-   **--deleteIndex `<colonne>`**: elenco delimitato da virgole di colonne da escludere dall'indice.

**mobile table delete [opzioni] [nome\_servizio] [nome\_tabella]**

Questo comando consente di eliminare una tabella.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Specificare il parametro -q per eliminare la tabella senza conferma. Ciò consente di evitare il blocco degli script di automazione.

**mobile data truncate [opzioni] [nome\_servizio] [nome\_tabella]**

Questo comando consente di rimuovere tutte le righe di dati dalla tabella.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table
     (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK

### Comandi per la gestione degli script

Comandi in questa sezione vengono utilizzati per gestire gli script del server appartenenti a un servizio mobile. Per ulteriori informazioni, vedere [Utilizzo degli script del server in Servizi mobili](http://www.windowsazure.com/en-us/develop/mobile/how-to-guides/work-with-server-scripts/).

**mobile script list [opzioni] [nome\_servizio]**

Questo comando consente di elencare gli script registrati, compresi gli script tabella e quelli relativi all'utilità di pianificazione.

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

**mobile script upload [opzioni] [nome\_servizio] [nome\_script]**

Il comando seguente consente di caricare uno script denominato `todoitem.insert.js` dalla sottocartella `table`.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

Il nome del file deve essere composto dai nomi della tabella e dell'operazione e deve trovarsi nella sottocartella table relativa al percorso in cui viene eseguito il comando. È inoltre possibile utilizzare l'opzione **-f `<file>`** o **--file `<file>`** per specificare un nome file diverso e un percorso al file che contiene lo script da registrare.

**mobile script download [opzioni] [nome\_servizio] [nome\_script]**

Questo comando consente di scaricare lo script insert dalla tabella TodoItem in un file denominato `todoitem.insert.js` nella sottocartella `table`.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Il comando supporta le opzioni aggiuntive seguenti:

-   **-p `<percorso>`** o **--path `<percorso>`**: il percorso del file in cui salvare lo script, dove la directory di lavoro corrente è il percorso predefinito.
-   **-f `<file>`** o **--file `<file>`**: il nome del file in cui salvare lo script.
-   **-o** o **--override**: sovrascrive un file esistente.
-   **-c** o **--console**: scrive lo script sulla console invece che un file.

**mobile script delete [opzioni] [nome\_servizio] [nome\_script]**

Questo comando consente di rimuovere lo script insert esistente dalla tabella TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### Comandi per la gestione dei processi pianificati

I comandi in questa sezione vengono utilizzati per gestire i processi pianificati appartenenti a un servizio mobile. Per ulteriori informazioni, vedere [Pianificare i processi](http://msdn.microsoft.com/en-us/library/windowsazure/jj860528.aspx).

**mobile job list [opzioni] [nome\_servizio]**

Questo comando consente di elencare i processi pianificati.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**mobile job create [opzioni] [nome\_servizio] [nome\_processo]**

Questo comando consente di creare un nuovo processo denominato `getUpdates` programmato per essere eseguito ogni ora.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates 
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Il comando supporta le opzioni aggiuntive seguenti:

-   **-i `<numero>`** o **--interval `<numero>`**: l'intervallo del processo, espresso come valore integer. Il valore predefinito è `15`.
-   **-u `<unità>`** o **--intervalUnit `<unità>`**: l'unità per l'*intervallo*, che può essere uno dei valori seguenti:
    -   **minute** (predefinito)
    -   **hour**
    -   **day**
    -   **month**
    -   **none** (processi su richiesta)
-   **-t `<orario>`** **--startTime `<orario>`** L'orario di inizio della prima esecuzione dello script in formato ISO. Il valore predefinito è `now`.

**Nota**

I nuovi processi vengono creati con stato disabilitato perché uno script deve ancora essere caricato. Utilizzare il comando **mobile script upload** per caricare uno script e il comando **mobile job update** per abilitare il processo.

**mobile job update [opzioni] [nome\_servizio] [nome\_processo]**

Il comando seguente consente di abilitare il processo disabilitato `getUpdates`.

    ~$azure mobile job update -a enabled todolist getUpdates 
    info:    Executing command mobile job update
    info:    mobile job update command OK

Il comando supporta le opzioni aggiuntive seguenti:

-   **-i `<numero>`** o **--interval `<numero>`**: l'intervallo del processo, espresso come valore integer. Il valore predefinito è `15`.
-   **-u `<unità>`** o **--intervalUnit `<unità>`**: l'unità per l'*intervallo*, che può essere uno dei valori seguenti:
    -   **minute** (predefinito)
    -   **hour**
    -   **day**
    -   **month**
    -   **none** (processi su richiesta)
-   **-t `<orario>`** **--startTime `<orario>`** L'orario di inizio della prima esecuzione dello script in formato ISO. Il valore predefinito è `now`.
-   **-a `<stato>`** o **--status `<stato>`**: lo stato del processo, che può essere `enabled` o `disabled`.

**mobile job delete [opzioni] [nome\_servizio] [nome\_processo]**

Questo comando consente di rimuovere il processo pianificato getUpdates dal server TodoList.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

**Nota**

L'eliminazione di un processo comporta anche l'eliminazione dello script caricato.

### Comandi per il ridimensionamento di un servizio mobile

I comandi in questa sezione vengono utilizzati per ridimensionare un servizio mobile. Per ulteriori informazioni, vedere [Ridimensionamento di un servizio mobile](http://msdn.microsoft.com/en-us/library/windowsazure/jj193178.aspx).

**mobile scale show [opzioni] [nome\_servizio]**

Questo comando consente di visualizzare informazioni di scalabilità, compresa l'attuale modalità di calcolo e il numero di istanze.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**mobile scale change [opzioni] [nome\_servizio]**

Questo comando consente di modificare la scala del servizio mobile da modalità gratuita a modalità Premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Il comando supporta le opzioni aggiuntive seguenti:

-   **-c `<modalità>`** o **--computeMode `<modalità>`**: la modalità di calcolo deve essere `Free` o `Reserved`.
-   **-i `<conteggio>` o \*\*--numberOfInstances `<conteggio>`**: il numero di istanze utilizzato quando in esecuzione in modalità riservata.

**Nota**

Se si imposta la modalità di calcolo su "Reserved", tutti i servizi mobili disponibili nella stessa area verranno eseguiti nella modalità Premium.

Gestione delle impostazioni locali dello strumento
--------------------------------------------------

Le impostazioni locali sono costituite dall'ID sottoscrizione e dal nome dell'account di archiviazione predefinito.

**config list [opzioni]**

Questo comando consente di visualizzare le impostazioni di configurazione.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value                               
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**config set [opzioni] &lt;nome\>,&lt;valore\>**

Questo comando consente di modificare un'impostazione di configurazione.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

Comandi per la gestione del bus di servizio
-------------------------------------------

Utilizzare i comandi di seguito per gestire l'account del bus di servizio

**sb namespace create &lt;nome\> &lt;ubicazione\>**

Consente di creare un nuovo spazio dei nomi del bus di servizio

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

**sb namespace show &lt;nome\>**

Consente di visualizzare i dettagli relativi a uno spazio dei nomi specifico

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

**sb namespace list**

Consente di elencare tutti gli spazi dei nomi creati per l'account

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK

**sb namespace delete &lt;nome\>**

Consente di rimuovere uno spazio dei nomi

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test
     [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**sb namespace location list**

Consente di visualizzare un elenco di tutte le ubicazioni degli spazi dei nomi disponibili

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

**sb namespace verify &lt;nome\>**

Consente di verificare se lo spazio dei nomi è disponibile

Comandi per la gestione dei database SQL
----------------------------------------

Utilizzare i comandi di seguito per gestire i database SQL di Azure

### Comandi per la gestione dei server SQL Server

Utilizzare i comandi seguenti per gestire i server SQL Server

**sql server create &lt;ID\_accesso\_amministratore\> &lt;password\_amministratore\> &lt;ubicazione\>**

Consente di creare un nuovo server database

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**sql server show &lt;nome\>**

Consente di visualizzare i dettagli del server

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**sql server list**

Consente di ottenere l'elenco dei server

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**sql server delete &lt;nome\>**

Consente di eliminare un server

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts
     [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

### Comandi per la gestione dei database SQL

Utilizzare i comandi di seguito per gestire i database SQL

**sql db create [opzioni] &lt;nome\_server\> &lt;nome\_database\> &lt;password\_amministratore\>**

Consente di creare una nuova istanza di database

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**sql db show [opzioni] &lt;nome\_server\> &lt;nome\_database\> &lt;password\_amministratore\>**

Consente di visualizzare i dettagli del database

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

**sql db list [opzioni] &lt;nome\_server\> &lt;password\_amministratore\>**

Consente di elencare i database

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**sql db delete [opzioni] &lt;nome\_server\> &lt;nome\_database\> &lt;password\_amministratore\>**

Consente di eliminare un database

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb
     [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

### Comandi per la gestione delle regole del firewall di SQL Server

Utilizzare i comandi seguenti per gestire le regole del firewall di SQL Server

**sql firewallrule create [opzioni] &lt;nome\_server\> &lt;nome\_regola\> &lt;indirizzo\_IP\_inizio\> &lt;indirizzo\_IP\_fine\>**

Consente di creare una nuova regola del firewall per un server SQL Server

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**sql firewallrule show [opzioni] &lt;nome\_server\> &lt;nome\_regola\>**

Consente di visualizzare i dettagli di una regola del firewall.

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

**sql firewallrule list [opzioni] &lt;nome\_server\>**

Consente di elencare le regole del firewall

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**sql firewallrule delete [opzioni] &lt;nome\_server\> &lt;nome\_regola\>**

Questo comando consente di eliminare una regola del firewall

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed
     [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

Comandi per la gestione delle reti virtuali
-------------------------------------------

Utilizzare i comandi seguenti per gestire le reti virtuali

**network vnet create [opzioni] &lt;percorso\>**

Consente di creare una nuova rete virtuale

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

**network vnet show &lt;nome\>**

Consente di visualizzare i dettagli di una rete virtuale

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

Consente di elencare tutte le reti virtuali esistenti

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

**network vnet show &lt;nome\>**

Consente di visualizzare i dettagli relativi alla rete virtuale specificata

    ~$ azure network vnet show opentechvn1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "opentechvn1"
    data:    Id "cab41cb0-396a-413b-83a1-302f0f1c867d"
    data:    AffinityGroup "AG-CLI-456f89eaa7fae2b3"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.100.23.255/27"
    data:    Subnets 0 Name "frontend"
    data:    Subnets 0 AddressPrefix "10.100.23.224/29"
    info:    network vnet show command OK

**network vnet delete &lt;nome\>**

Consente di eliminare la rete virtuale specificata

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 
      (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**network export [percorso\_file]**

Per eseguire operazioni di configurazione avanzate della rete, è possibile esportare la configurazione di rete in locale. Si noti che la configurazione di rete esportata include le impostazioni del server DNS, della rete virtuale, del sito della rete locale e altre impostazioni.

**network import [percorso\_file]**

Consente di importare una configurazione di rete locale.

**network dnsserver register [opzioni] &lt;IP\_DNS\>**

Consente di registrare un server DNS che si intende utilizzare per la risoluzione dei nomi nella configurazione di rete

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**network dnsserver list**

Consente di elencare tutti i server DNS registrati nella configurazione di rete

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**network dnsserver unregister [opzioni] &lt;IP\_DNS\>**

Consente di rimuovere una voce di server DNS dalla configurazione di rete

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s 
     (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK
