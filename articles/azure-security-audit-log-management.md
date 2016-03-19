<properties
   pageTitle="Gestione della protezione e del log di controllo di Microsoft Azure | Microsoft Azure"
   description="L’articolo fornisce informazioni introduttive per la generazione, la raccolta e l’analisi dei log di protezione da servizi ospitati in Azure. È destinato ai professionisti IT e ad analisti della protezione che si occupano di gestione delle risorse di informazioni su base giornaliera, inclusi i responsabili dell'attività di protezione e conformità dell'organizzazione."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="nayak-mahesh"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Gestione della protezione e del log di controllo di Microsoft Azure

Azure consente ai clienti di eseguire la generazione di eventi di protezione e la raccolta dall'infrastruttura di Azure come Servizio (IaaS) e dalla piattaforma come ruoli di servizio (PaaS) in un archivio centrale nelle loro sottoscrizioni. I clienti possono utilizzare [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) per aggregare e analizzare gli eventi raccolti. Inoltre, questi eventi raccolti possono essere esportati in sistemi (SIEM) di gestione di eventi e informazioni di protezione locali per il monitoraggio continuo.

Il ciclo di vita della registrazione di protezione di Azure, dell’analisi e del monitoraggio include:

- **Generazione**: applicazioni dello strumento e l'infrastruttura per la generazione di eventi
- **Raccolta**: configurare Azure per raccogliere i vari registri di protezione in un account di archiviazione
- **Analisi**: utilizzare strumenti Azure come HDInsight e sistemi SIEM locali per analizzare i registri e generare informazioni di protezione
- **Monitoraggio e reporting**: Azure offre sistemi di monitoraggio e analisi centralizzati che forniscono visibilità continua e tempestività negli avvisi

Questo articolo si concentra sulle e fasi di generazione e raccolta del ciclo di vita.

## Generazione di log
Gli eventi di protezione vengono generati nel registro eventi di Windows per i canali **Sistema**, **Protezione**, e **Applicazione** nelle macchine virtuali. Per garantire che gli eventi vengano registrati senza perdita di dati potenziali, è importante configurare correttamente le dimensioni del log eventi. Basare la dimensione del log eventi in base al numero di eventi che generano le impostazioni dei criteri di controllo e i criteri di raccolta eventi. Per ulteriori informazioni, vedere [Pianificazione per la gestione e il monitoraggio del controllo di sicurezza](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4).

>[AZURE.NOTE] Quando si utilizza l'inoltro eventi di Windows (WCF) o diagnostica Azure (illustrato nella sezione [raccolta log](#log-collection)) per scaricare i log da servizi Cloud o dalle macchine virtuali, prendere in considerazione il potenziale impatto di malfunzionamenti del sistema. Ad esempio, se l'ambiente di WCF si arresta per qualche tempo, è necessario assicurarsi che le dimensioni del log siano sufficientemente grandi per l'account per un periodo di tempo più lungo o essere preparati per possibili perdite di dati di log.

Per le applicazioni di servizi Cloud che vengono distribuiti in Azure e in macchine virtuali create dal [Mercato macchine virtuali di Azure](https://azure.microsoft.com/marketplace/virtual-machines/#microsoft), un set di eventi di protezione del sistema operativo è abilitato per impostazione predefinita. I clienti possono aggiungere, rimuovere o modificare gli eventi da controllare personalizzando i criteri di controllo del sistema operativo. Per ulteriori informazioni, vedere [Riferimento alle impostazioni di criteri di protezione](http://technet.microsoft.com/library/jj852210.aspx).

Per generare log aggiuntivi dal sistema operativo (ad esempio modifiche ai criteri di controllo) e componenti di Windows (ad esempio IIS), è possibile utilizzare i metodi seguenti:

- Criteri di gruppo per implementare le impostazioni dei criteri per le macchine virtuali in Azure che appartengono al dominio
- Configurazione dello stato desiderato (DSC) per inserire e gestire le impostazioni dei criteri. Per ulteriori informazioni, vedere [Azure PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)
- Codice di avvio del ruolo di distribuzione del servizio per implementare le impostazioni per i servizi Cloud (scenario PaaS)

Configurare le attività di avvio del ruolo di Azure consente al codice di eseguire prima l'avvio di un ruolo. È possibile definire un'attività di avvio per un ruolo aggiungendo l’elemento **Avvio** alla definizione del ruolo nel file di definizione del servizio, come illustrato nell'esempio seguente. Per altre informazioni, vedere [Eseguire attività di avvio in Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).

Il file di attività che deve essere eseguito come attività di avvio (EnableLogOnAudit.cmd nell'esempio seguente) deve essere incluso nel pacchetto di compilazione. Se si utilizza Visual Studio, aggiungere il file al progetto di cloud, fare clic con il tasto destro sul nome del file, fare clic su **Proprietà**, poi impostare **Copia in Directory di output** su **Copia sempre**.

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Contenuto di EnableLogOnAudit.cmd:

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

[Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx) utilizzato nell'esempio precedente è uno strumento da riga di comando incluso nel sistema operativo Windows Server che consente di gestire le impostazioni dei criteri di controllo.

Oltre alla generazione di log di eventi di Windows, vari componenti del sistema operativo Windows possono essere configurati per generare i log che sono importanti per il monitoraggio e l’analisi della protezione. Ad esempio, i log di Internet Information Services (IIS) e i log http.err sono generati automaticamente per i ruoli web, e possono essere configurati per la raccolta. Questi log forniscono informazioni utili che possono essere utilizzate per identificare gli accessi non autorizzati o gli attacchi contro il ruolo web. Per ulteriori informazioni, vedere [Configura la registrazione in IIS](http://technet.microsoft.com/library/hh831775.aspx) e [Registrazione Avanzata per IIS - Registrazioni personalizzate](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging).

Per modificare la registrazione IIS in un ruolo web, i clienti possono aggiungere un'attività di avvio al file di definizione del servizio ruolo web. L’esempio seguente abilita la registrazione HTTP per un sito Web denominato Contoso, e specifica che IIS deve registrare tutte le richieste per il sito Web di Contoso.

L'attività che aggiorna la configurazione di IIS deve essere inclusa nel file di definizione del servizio del ruolo web. Le modifiche seguenti al file di definizione del servizio eseguono un'attività di avvio che configura la registrazione eseguendo uno script chiamato ConfigureIISLogging.cmd IIS.

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Contenuto di ConfigureIISLogging:cmd

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>Raccolta di log
La raccolta di eventi e registri di protezione da servizi Cloud o macchine virtuali in Azure avviene tramite due metodi principali:

- Diagnostica di Azure, raccoglie gli eventi nell'account di archiviazione Azure del cliente
- L’inoltro dell’evento di Windows (WCF), una tecnologia nei computer che eseguono Windows

Nella tabella seguente sono incluse alcune differenze fondamentali tra queste due tecnologie. In base alle proprie esigenze e a queste differenze principali, il metodo appropriato deve essere scelto per implementare una raccolta di log.

| Diagnostica Azure | Inoltro di Eventi di Windows |
|-----|-----|
|Supporta macchine virtuali di Azure e servizi Cloud di Azure | Supporta solamente le Macchine virtuali di Azure dotate di dominio |
|Supporta un'ampia gamma di formati di log, ad esempio Registri eventi di Windows, [Event Tracing per Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) log tracce (ETW) e IIS. Per ulteriori informazioni, vedere [Origini dati supportati Diagnostica Azure](#diagnostics) |Supporta solo i log eventi di Windows |
|Inserisce i dati raccolti in archiviazione di Azure |Sposta i dati raccolti ai server dell'agente di raccolta centrale |

##	Raccolta dei dati degli eventi di protezione con l'inoltro di eventi di Windows
Per macchine virtuali appartenenti a un dominio di Azure, è possibile configurare WCF utilizzando le impostazioni di criteri di gruppo nello stesso modo utilizzato per computer appartenenti a un dominio locale. Per altre informazioni, vedere [Cloud ibrida](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx).

In questo modo, un'organizzazione potrebbe acquistare una sottoscrizione IaaS, collegarla alla rete aziendale tramite [ExpressRoute](https://azure.microsoft.com/services/expressroute/) o VPN da sito a sito e poi aggiungere le macchine virtuali disponibili in Azure al dominio aziendale. Successivamente, è possibile configurare WCF da computer appartenenti a un dominio.

L’inoltro di eventi è suddiviso in due parti: l'origine e l'agente di raccolta. L'origine è il computer in cui vengono generati i log di protezione. L'agente di raccolta è il server centralizzato che raccoglie e consolida i log di eventi. Gli amministratori IT possono iscriversi agli eventi in modo da ricevere e archiviare gli eventi inoltrati da computer remoti (origine dell’evento). Per ulteriori informazioni, vedere [Configurare i computer per l'inoltro e la raccolta di eventi](http://technet.microsoft.com/library/cc748890.aspx).

Gli eventi di Windows raccolti possono essere inviati a strumenti di analisi in locale, ad esempio un SIEM, per un'ulteriore analisi.

## Raccolta di dati di protezione con diagnostica Azure
Diagnostica Azure consente di raccogliere i dati di diagnostica da un ruolo di lavoro di servizio cloud o da un ruolo Web, o da macchine virtuali in esecuzione in Azure. È un'estensione di agente guest predefinito che deve essere abilitato e configurato per la raccolta dati. La sottoscrizione di un cliente può includere l’inserimento di dati nell’archiviazione di Azure.

I dati sono crittografati in transito (utilizzando HTTPS). Gli esempi forniti in questo documento utilizzano la Diagnostica di Azure 1.2. Si consiglia di eseguire l'aggiornamento alla versione 1.2 o successiva per la raccolta di dati di protezione. Per ulteriori informazioni, vedere [Raccolta dei dati di registrazione utilizzando la diagnostica di Azure](http://msdn.microsoft.com/library/gg433048.aspx).

Il diagramma seguente illustra un flusso di dati di alto livello per la raccolta di dati di protezione che utilizza la Diagnostica di Azure e ulteriori analisi e monitoraggio.

![][1]

La Diagnostica di Azure sposta i log da applicazioni di servizi Cloud cliente e [macchine virtuali di Azure](virtual-machines-about.md) ad Archiviazione di Azure. Sulla base di un formato di log, alcuni dati vengono archiviati in tabelle di Azure e alcuni in BLOB. I dati raccolti in [Archiviazione di Azure](storage-introduction.md) possono essere scaricati su sistemi SIEM locali tramite la libreria client di Archiviazione di Azure per il monitoraggio e l’analisi.

Inoltre, HDInsight può essere utilizzato per un'ulteriore analisi dei dati nel cloud. Di seguito sono riportati alcuni esempi di raccolta dati di protezione utilizzati dalla Diagnostica di Azure.

### Raccolta di dati di protezione da macchine virtuali di Azure utilizzando Diagnostica di Azure

Negli esempi seguenti si utilizza la Diagnostica di Azure 1.2 e i cmdlet Azure PowerShell per abilitare la raccolta di dati di protezione da macchine virtuali. I dati vengono raccolti da macchine virtuali a intervalli pianificati (che sono configurabili) e inseriti in Archiviazione di Azure all'interno della sottoscrizione del cliente. In questa sezione verranno illustrati due scenari di raccolta di log tramite Diagnostica Azure:

1. Impostare una nuova istanza di una pipeline di raccolta log di protezione in una macchina virtuale.
2. Aggiornare una pipeline di raccolta di log di protezione esistente con una nuova configurazione su una macchina virtuale.

#### Impostare una nuova istanza di una pipeline di raccolta log di protezione in una macchina virtuale.
In questo esempio si imposta una nuova istanza di una pipeline di raccolta del log di protezione che utilizza Diagnostica Azure, e si rilevano degli eventi di errore di accesso (eventi ID 4624 e 4625) dalle macchine virtuali. È possibile implementare i passaggi seguenti dall'ambiente di sviluppo oppure è possibile utilizzare una sessione di Desktop remoto tramite Remote Desktop Protocol (RDP) per il nodo nel cloud.

##### Passaggio 1: Installare l’SDK Azure PowerShell
L’SDK Azure PowerShell offre i cmdlet per configurare Diagnostica Azure nelle macchine virtuali di Azure. I cmdlet necessari sono disponibili nella versione di Azure PowerShell 0.8.7 o versione successiva. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

##### Passaggio 2: Creazione del file di configurazione
Preparare il file di configurazione sulla base degli eventi da raccogliere. Di seguito è riportato un esempio di un file di configurazione di Diagnostica Azure per raccogliere gli eventi di Windows dal canale **Protezione**, con i filtri aggiunti per raccogliere solo eventi di accesso ad esito positivo e negativo. Per altre informazioni, vedere [Schema di configurazione di Diagnostica Azure 1.2](http://msdn.microsoft.com/library/azure/dn782207.aspx).

L'account di archiviazione può essere specificato nel file di configurazione, o può essere specificato come parametro quando si eseguono i cmdlet di Azure PowerShell per configurare Diagnostica Azure.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

Quando si salva il contenuto precedente come file XML, impostare la codifica **UTF-8**. Se si utilizza il blocco note, si noterà l'opzione di codifica disponibile nella finestra di dialogo "Salva con nome". Nella tabella seguente sono elencati alcuni attributi principali da tenere presente nel file di configurazione.

| Attributo | Descrizione |
|----- |-----|
| overallQuotaInMB | La quantità massima di spazio su disco locale che può essere utilizzato da Diagnostica Azure (il valore è configurabile). |
| scheduledTransferPeriod | L'intervallo tra i trasferimenti pianificati per l'Archiviazione di Azure, arrotondato per eccesso fino al minuto più vicino. |
| Nome | In WindowsEventLog, questo attributo è la query XPath che descrive gli eventi di Windows da raccogliere. È possibile filtrare la raccolta dei dati mediante l'aggiunta di un filtro come ID evento, nome del Provider o canale. |

Tutti i dati del log eventi di Windows vengono spostati in una tabella denominata **WADWindowsEventLogsTable**. Attualmente, Diagnostica Azure non supporta la ridenominazione della tabella.

##### <a name="step3"></a> Passaggio 3: Convalidare i file di configurazione XML
Utilizzare la procedura seguente per convalidare che non sono presenti errori nel file di configurazione XML e che è compatibile con lo schema di Diagnostica Azure:

1. Per scaricare il file di schema, eseguire il comando seguente e poi salvare il file.

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. Dopo aver scaricato il file di schema, è possibile convalidare il file di configurazione XML rispetto allo schema. Per convalidare il file utilizzando Visual Studio:
  - Aprire il file XML in Visual Studio.
  - Premere F4 per aprire **Proprietà**
  - Fare clic su **Schema**, fare clic su **Aggiungi**, selezionare il file di schema scaricato (WadConfigSchema.XSD) e poi fare clic su **OK**

3.	Nel menù **Visualizzazione**, fare clic su **Elenco errori** per vedere se sono presenti errori di convalida.

##### <a name="step4"></a> Passaggio 4: Configurare Diagnostica Azure
 Per abilitare Diagnostica Azure e avviare la raccolta dei dati, procedere come segue:

 1.	Per aprire Azure PowerShell, digitare **Add-AzureAccount**, quindi premere INVIO.
 2.	Accedere con l'account Azure.
 3.	Eseguire lo script di PowerShell seguente: Assicurarsi di aggiornare il storage\_name, chiave, config\_path, service\_name e vm\_name.

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### Passaggio 5: Generare eventi
A scopo dimostrativo, si creano alcuni eventi di accesso e si verifica che i dati vengano trasmessi al servizio di Archiviazione Azure. Come illustrato in precedenza nel passaggio 2, il file XML è configurato per raccogliere eventi ID 4624 (accesso riuscito) e 4625 ID (errore di accesso) dal canale **Protezione**.

 Per generare questi eventi:

1.	Aprire una sessione RDP alla macchina virtuale.
2.	Immettere le credenziali non corrette per generare alcuni eventi di accesso non riuscito (4625 ID).
3.	Dopo alcuni tentativi di accesso non riuscito, immettere le credenziali corrette per generare un evento di accesso riuscito (ID 4624).

##### Passaggio 6: Visualizzare i dati
Circa cinque minuti dopo aver completato i passaggi precedenti, i dati devono avviare l’indirizzamento verso l'account di archiviazione cliente in base alla configurazione nel file XML. Sono disponibili molti strumenti per visualizzare i dati dal servizio di Archiviazione Azure. Per altre informazioni, vedere:

- [Esplorazione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Azure Storage Explorer 6 Preview 3 (agosto 2014)](http://azurestorageexplorer.codeplex.com/)

Per visualizzare i dati:

1.	In Visual Studio (2013, 2012 e 2010 con SP1), scegliere **Visualizza**, quindi fare clic su **Esplora server**.
2.	Passare all'account di archiviazione
3.	Fare clic su **Tabelle** e fare doppio clic sulle tabelle appropriate per visualizzare i registri di protezione raccolti dalle macchine virtuali. ![][2]

4.	Fare clic con il destro sulla tabella denominata WADWindowsEventLogsTable destro, poi fare clic su **Dati della visualizzazione** per aprire la visualizzazione di tabella, come illustrato di seguito:

![][3]

Nella tabella di archiviazione precedente, **PartitionKey**, **RowKey**, e **Timestamp** sono proprietà di sistema.

- **PartitionKey** è un timestamp in secondi, ed è un identificatore univoco per la partizione all'interno della tabella.
- **RowKey** è un identificatore univoco per un’entità all'interno di una partizione.

Insieme **PartitionKey** e **RowKey** identificano in modo univoco tutte le entità di una tabella.

- Timestamp è un valore data/ora che viene mantenuto nel server per rilevare quando un'entità è stata modificata.

>[AZURE.NOTE] Le dimensioni massime delle righe in una tabella di Archiviazione di Azure sono limitate a 1 MB. Un account di archiviazione può contenere fino a 200 TB di dati di BLOB, code e tabelle se l'account è stato creato dopo giugno 2012. In questo modo, le dimensioni della tabella possono aumentare fino a 200 TB se BLOB e le code non prendono nessuno spazio di archiviazione. Gli account creati prima di giugno 2012 hanno un limite di 100 TB.

Esplora memoria consente inoltre di modificare i dati di tabella. Fare doppio clic su una determinata riga nella visualizzazione della tabella per aprire la finestra di modifica entità, come illustrato di seguito:

![][4]

#### Aggiornamento di una pipeline di raccolta di log protezione esistente con una nuova configurazione in una macchina virtuale
In questa sezione, si aggiorna una pipeline di raccolta del log di protezione esistente di Diagnostica Azure in una macchina virtuale, e si rilevano gli errori nel log eventi applicazioni di Windows.

##### Passaggio 1: Aggiornare i file di configurazione per includere gli eventi di interesse
Il file di Diagnostica Azure creato nel precedente esempio deve essere aggiornato per includere i tipi di errore di log eventi delle applicazioni di Windows.

>[AZURE.NOTE] Le impostazioni di configurazione di Diagnostica Azure esistenti devono essere unite con il nuovo file di configurazione. Le impostazioni definite nel nuovo file sovrascriveranno le configurazioni esistenti.

Per recuperare l'impostazione di configurazione esistente, è possibile utilizzare il cmdlet **Get AzureVMDiagnosticsExtension**. Di seguito è uno script di PowerShell di Azure di esempio per recuperare la configurazione esistente:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
Aggiornare la configurazione di Diagnostica Azure per raccogliere errori nel log eventi delle applicazioni di Windows gli eventi critici come segue:

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Convalidare il file di configurazione utilizzando gli stessi passaggi come illustrato in precedenza in [Passaggio 3: convalidare file XML di configurazione](#step3).

##### Passaggio 2: Aggiornare Diagnostica Azure per utilizzare il nuovo file di configurazione
Utilizzare i cmdlet **Set AzureVMDiagnosticsExtension** e **Update-AzureVM** per aggiornare la configurazione, come illustrato in precedenza in [Passaggio 4: configurare Diagnostica Azure](#step4).

##### Passaggio 3: Verificare le impostazioni di configurazione
Eseguire il comando seguente per verificare che le impostazioni di configurazione siano state aggiornate:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### Passaggio 4: Generare eventi
Ad esempio, eseguire il comando seguente per generare un log eventi dell'applicazione del tipo **Errore**:

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

Aprire il Visualizzatore eventi per verificare che l'evento venga creato.

![][6]

##### Passaggio 5: Visualizzare i dati
Aprire Esplora Server in Visual Studio per visualizzare i dati del log. Verrà visualizzato un **ID evento 100** creato in **ContosoDesktop** come illustrato di seguito:

![][7]

## Raccolta di dati di protezione da servizi cloud di Azure utilizzando Diagnostica Azure

Diagnostica Azure verrà ora utilizzata per esplorare gli stessi due scenari di raccolta log da servizi Cloud di Azure come nella sezione precedente di macchine virtuali (IaaS):

1.	Impostare una nuova istanza della pipeline del log di protezione in un servizio cloud.
2.	Aggiornare una pipeline di raccolta di log protezione esistente con una nuova configurazione in un servizio cloud.

La procedura dettagliata in questa sezione comprende:

1.	Creare un servizio cloud:
2.	Configurare il servizio cloud per la raccolta di log di protezione utilizzando Diagnostica Azure.
3.	Illustrare la generazione e la raccolta degli eventi di protezione nel servizio Cloud:

    - Aggiungere un amministratore a un gruppo locale con un'elevazione dei privilegi
    - Creazione del nuovo processo
4.	Aggiornare una pipeline di raccolta di log protezione esistente in un servizio cloud.

    - Attivare il controllo degli eventi di firewall host (ad esempio degli eventi di protezione di rete) mediante Auditpol
    - Configurare firewall controllo dati da raccogliere e visualizzare gli eventi raccolti nell'account di archiviazione cliente
5.	Mostrare la distribuzione dell’evento di protezione di Windows e il picco di rilevamento di eventi.
6.	Configurare la raccolta di log di IIS e verificare i dati.

Tutti gli eventi e i log vengono raccolti in un account di archiviazione del cliente in Azure. Gli eventi possono essere visualizzati e possono essere esportati dal cliente a sistemi SIEM locali. Inoltre possono essere aggregati e analizzati tramite HDInsight.

### Impostare una nuova istanza di una pipeline di raccolta di log in un servizio cloud
In questo esempio si imposta una nuova istanza di una pipeline di raccolta del log di protezione che utilizza diagnostica Azure, e viene rilevata l'aggiunta dell'utente a un gruppo locale e nuovi eventi di creazione di processo in un'istanza del servizio cloud.

#### Passaggio 1: Creare un servizio cloud (ruolo web) e distribuire

1.	Nel computer di sviluppo avviare Visual Studio 2013.
2.	Creare un nuovo progetto di servizio cloud (questo esempio utilizza ContosoWebRole).
3.	Selezionare il ruolo web **ASP.NET**.
4.	Selezionare il progetto **MVC**.
5.	In Esplora soluzioni, fare clic su **Ruoli**, poi fare doppio clic sul ruolo web (WebRole1) per aprire la finestra **Proprietà**.
6.	Nella scheda **Configurazione**, deselezionare la casella di controllo **Abilita diagnostica** per disabilitare la versione di Diagnostica Azure che viene fornita con Visual Studio 2013. ![][8]

7.	Compilare la soluzione per verificare che non ci siano errori.
8.	Aprire il file WebRole1/Controllers/HomeController.cs.
9.	Aggiungere il metodo seguente per abilitare l'applicazione di esempio per il log di codice di stato HTTP 500 come un evento di registro di IIS di esempio (questa verrà utilizzata nell'esempio di IIS in un secondo momento):

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 Fare clic con il pulsante destro del mouse sul progetto servizio cloud, e selezionare **Pubblica**.

#### Passaggio 2: Creazione del file di configurazione
Si prepara ora il file di configurazione di Diagnostica Azure per aggiungere gli eventi che consentono di rilevare le situazioni seguenti:

- Nuova aggiunta dell'utente a un gruppo locale
- Creazione del nuovo processo

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### Passaggio 3: Convalidare i file di configurazione XML
Convalidare il file di configurazione utilizzando gli stessi passaggi come illustrato in precedenza in [Passaggio 3: convalidare file XML di configurazione](#step3).
#### Passaggio 4: Configurare Diagnostica Azure
Eseguire il seguente script di Azure PowerShell per abilitare la Diagnostica Azure (assicurarsi di aggiornare il storage\_name, chiave, config\_path e service\_name).

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Per verificare che il servizio disponga della configurazione di diagnostica più recente, eseguire il comando di Azure PowerShell seguente:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Passaggio 5: Generare eventi
Per generare eventi:

1.	Per avviare una sessione Desktop remoto per l'istanza del servizio cloud, in Visual Studio, aprire Esplora Server, fare clic con il tasto destro sull'istanza del ruolo, e fare clic su Connetti tramite Desktop remoto.
2.	Aprire un prompt dei comandi con privilegi elevati ed eseguire i comandi seguenti per creare un account amministratore locale nella macchina virtuale:


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	Aprire il Visualizzatore eventi, aprire il canale **Protezione** e notare che è stato creato un evento 4732, come illustrato di seguito:

![][9]

#### Passaggio 6: Visualizzare i dati
Attendere circa cinque minuti per consentire all'agente Diagnostica Azure di indirizzare gli eventi alla tabella di archiviazione.

![][10]

Per convalidare l'evento di creazione del processo, aprire un Blocco note. Come illustrato di seguito, è stato registrato un evento di creazione del processo nel canale di sicurezza.

![][11]

È ora possibile visualizzare lo stesso evento nell'account di archiviazione, come illustrato di seguito:

![][12]

### Aggiornare una pipeline di raccolta di log di protezione esistente con una nuova configurazione in un servizio cloud.
In questa sezione, si aggiorna una pipeline di raccolta del log di protezione esistente di Diagnostica Azure, e si rilevano gli eventi di modifica di Windows Firewall in un’istanza del servizio cloud.

Per rilevare le modifiche al firewall, si aggiornerà la configurazione esistente per includere gli eventi di modifica di firewall.

#### Passaggio 1: Ottenere la configurazione esistente
>[AZURE.NOTE] Le nuove impostazioni di configurazione sovrascriveranno la configurazione esistente. Dunque, è importante che le impostazioni di configurazione di Diagnostica Azure esistenti vengano unite con il nuovo file di configurazione.

Per recuperare l'impostazione di configurazione esistente, è possibile utilizzare il cmdlet **Get AzureVMDiagnosticsExtension**:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Passaggio 2: Aggiornare la configurazione XML per includere gli eventi di firewall

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Convalidare il contenuto XML utilizzando lo stesso processo di convalida descritto precedentemente nel [Passaggio 3: convalidare file XML di configurazione](#step3).

#### Passaggio 3: Aggiornare Diagnostica Azure per utilizzare la nuova configurazione

Eseguire lo script di Azure PowerShell seguente per aggiornare Diagnostica Azure per utilizzare la nuova configurazione (assicurarsi di aggiornare il storage\_name, chiave, config\_path e service\_name con le informazioni sul servizio cloud).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Per verificare che il servizio disponga della configurazione di diagnostica più recente, eseguire il comando di Azure PowerShell seguente:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Passaggio 4: Abilitare gli eventi di firewall

1.	Aprire una sessione Desktop remoto per l'istanza del servizio cloud.
2.	Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente:

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### Passaggio 5: Generare eventi

1.	Aprire Windows Firewall, e fare clic su **Regole connessioni in entrata**.
2.	Fare clic su **Aggiungi una nuova regola**, e poi fare clic su **Porta**.
3.	Nel campo **Porte locali**, digitare **5000**, e poi fare clic su **Avanti** tre volte.
4.	Nel campo **Nome**, digitare **Test5000** e fare clic su **Fine**.
5.	Aprire il Visualizzatore eventi, aprire il canale **Protezione**, e notare che è stato creato un evento 4946, come illustrato di seguito:

![][13]

#### Passaggio 6: Visualizzare i dati
Attendere circa cinque minuti per consentire all'agente Diagnostica Azure di indirizzare i dati degli eventi alla tabella di archiviazione.

![][14]

### Distribuzione dell’evento di protezione e picco di rilevamento.
Dopo che gli eventi sono nell'account di archiviazione del cliente, le applicazioni possono utilizzare le librerie client di archiviazione per accedere ed eseguire l'aggregazione di eventi. Per il codice di esempio per accedere ai dati di tabella, vedere [Procedura: recuperare i dati della tabella](storage-dotnet-how-to-use-tables.md).

Di seguito è riportato un esempio di aggregazione di eventi: È possibile poi analizzare i picchi in una distribuzione di eventi per attività anomale.

![][15]

### Raccolta di log di IIS ed elaborazione tramite HDInsight
In questa sezione, si raccolgono i log di IIS dall'istanza del ruolo web e si spostano l log di Azure BLOB in un account di archiviazione del cliente.

#### Passaggio 1: Aggiornare i file di configurazione per includere la raccolta del log di IIS

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Nella configurazione di Diagnostica Azure precedente, **containerName** è il nome del contenitore BLOB in cui i log verranno spostati nell'account di archiviazione del cliente.

Convalidare il file di configurazione utilizzando gli stessi passaggi come illustrato in precedenza in [Passaggio 3: convalidare file XML di configurazione](#step3).


#### Passaggio 2: Aggiornare Diagnostica Azure per utilizzare una nuova configurazione
Eseguire lo script di Azure PowerShell seguente per aggiornare Diagnostica Azure per utilizzare la nuova configurazione (assicurarsi di aggiornare il storage\_name, chiave, config\_path e service\_name con le informazioni sul servizio cloud).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Per verificare che il servizio disponga della configurazione di diagnostica più recente, eseguire il comando di Azure PowerShell seguente:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Passaggio 3: Generare log di IIS

1.	Aprire un web browser e passare al ruolo web servizio cloud (ad esempio, http://contosowebrole.cloudapp.net/).
2.	Spostarsi sulle pagine **Chi siamo** e **Contatto** per creare alcuni log eventi.
3.	Passare a una pagina che genera un codice di stato 500 (ad esempio, http://contosowebrole.cloudapp.net/Home/StatusCode500). Verrà visualizzato un errore analogo a quello riportato di seguito. Tenere presente che è stato aggiunto un codice per **StatusCode500** nel passaggio 1 della sezione denominata Configurazione di una nuova istanza della pipeline di raccolta di log in un cloud ServiceName. ![][16]
4.	Aprire una sessione Desktop remoto per l'istanza del servizio cloud.
5.	Aprire Gestione IIS.
6.	La registrazione di IIS è abilitata per impostazione predefinita ed è impostata per generare ogni ora file che contengono tutti i campi in formato W3C. Fare clic su **Sfoglia**, e vi sarà almeno un file di log, come illustrato di seguito: ![][17]

7.	Attendere circa cinque minuti per far sì che l'agente Diagnostica Azure inserisca il file di log per il contenitore BLOB. Per convalidare i dati, aprire **Esplora Server** > **archiviazione** > **Account di archiviazione** > **BLOB**. Come illustrato di seguito, il BLOB **iislogs** è creato: ![][18]

8.	Fare clic con il tasto destro del mouse e scegliere **Visualizza contenitore BLOB** per visualizzare il file di log IIS memorizzato nel BLOB: ![][19]
9.	Dopo gli eventi IIS nell'account di archiviazione del cliente, le applicazioni che sfruttano l’analisi HDInsight possono essere utilizzate per eseguire l'aggregazione di eventi. Il seguente grafico a linee è un esempio di un'attività di aggregazione di eventi contenente il codice di stato HTTP 500: ![][20]

## Consigli sulla raccolta di log di sicurezza
Quando si raccolgono i registri di protezione, è consigliabile:

- Raccogliere i propri eventi di log di controllo specifici dell'applicazione di servizio.
- Configurare solo i dati necessari per il monitoraggio e l’analisi. Acquisire una quantità eccessiva di dati può rendere difficile la risoluzione dei problemi e può influire sui costi del servizio o di archiviazione.
- Unire le impostazioni di configurazione di Diagnostica Azure esistenti con le modifiche apportate. Il nuovo file di configurazione sovrascriverà le impostazioni di configurazione esistenti.
- Scegliere l’intervallo **Periodo di trasferimento pianificato** in modo appropriato. Tempi di trasferimento più brevi aumenteranno la rilevanza dei dati, ma ciò può aumentare i costi di archiviazione il sovraccarico di elaborazione.

>[AZURE.NOTE] L’altra variabile che influirà in modo significativo sulla quantità di dati raccolti è il livello di registrazione. Di seguito è riportato un esempio di come filtrare i log dal livello di registrazione:

    System!*[System[(Level =2)]]

Il livello di registrazione è cumulativo. Se il filtro è impostato su **Avviso**, allora verranno raccolti anche gli eventi **Errore** e **Critico**.

- Copiare e cancellare periodicamente i dati di diagnostica da Archiviazione di Azure se non più necessari.

>[AZURE.NOTE] Per ulteriori informazioni sui dati di diagnostica, vedere [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx). I contenitori e le tabelle che archiviano dati di diagnostica sono esattamente come altri contenitori e tabelle, è possibile eliminare entità e BLOB da essi nello stesso modo che si utilizzerebbe per altri dati. È possibile eliminare i dati di diagnostica a livello di codice attraverso una delle librerie client di archiviazione o in modo visivo tramite un [client di archiviazione explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

- È consigliabile archiviare i dati di servizio e dati del log di protezione in account di archiviazione distinti. Questo isolamento garantisce che il salvataggio dei dati del log di protezione non influisca sulle prestazioni di archiviazione per dati del servizio di produzione.
- Scegliere la durata di conservazione dei log in base ai criteri di conformità dell'organizzazione e all'analisi dei dati, e al monitoraggio dei requisiti.

## Esportazione dei log di protezione in un altro sistema
È possibile scaricare i dati BLOB tramite la libreria Client di Archiviazione di Azure, e poi esportare i risultati al sistema locale per l'elaborazione. Per esempi di codice per gestire i dati BLOB, vedere [Come utilizzare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md).

Analogamente, è possibile scaricare i dati di protezione archiviati in tabelle di Azure tramite la libreria Client di Archiviazione Azure. Per ulteriori informazioni sull'accesso ai dati archiviati in tabelle, vedere [Come utilizzare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md).

## Report di Azure Active Directory
Azure Active Directory (Azure AD) include un set di sicurezza, di utilizzo e di report del log di controllo che forniscono visibilità nell'integrità e nella protezione del tenant di Azure AD. Ad esempio, Azure AD ha la capacità di analizzare automaticamente l’attività dell'utente e l’accesso anomalo nell’area, e poi li rende disponibili tramite i report visibili al cliente.

Questi report sono disponibili tramite il [Portale di gestione di Azure](https://manage.windowsazure.com/) in **Active Directory** > **Directory**. Alcuni di questi report sono gratuiti, mentre altre vengono forniti come parte di un'edizione di Azure AD Premium. Per altre informazioni sui report di Azure AD, vedere [Visualizzare i report di accesso e utilizzo](http://msdn.microsoft.com/library/azure/dn283934.aspx).

## Log delle operazioni di Azure
I log per le operazioni correlate alle risorse della sottoscrizione di Azure sono disponibili anche tramite la funzionalità **Log delle operazioni** nel portale di gestione.

Per visualizzare il **Log delle operazioni**, aprire il [Portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Servizi di Gestione**, e poi sulla scheda **Log delle operazioni**.

## <a name="diagnostics"></a> Origini dei dati supportati da Diagnostica Azure

| Origine dati | Descrizione |
|----- | ----- |
| Log di IIS | Informazioni sui siti Web di IIS. |
| Log dell'infrastruttura diagnostica di Azure | Informazioni su Diagnostica Azure |
| Log delle richieste non riuscite di IIS | Informazioni sulle richieste non riuscite a un sito o a un'applicazione di IIS. |
| Registri eventi di Windows | Informazioni inviate al sistema di registrazione eventi di Windows. |
| Contatori delle prestazioni | Contatori delle prestazioni personalizzate e del sistema operativo. |
| Dump di arresto anomalo del sistema | Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione. |
| Log degli errori personalizzati | Log creati dall'applicazione o dal servizio. |
| EventSource .NET | Eventi generati dal codice utilizzando .NET classe EventSource |
| ETW basato su manifesto | Traccia di eventi per eventi Windows generati da qualsiasi processo |

## Risorse aggiuntive
Le risorse seguenti forniscono informazioni generali su Microsoft Azure e i servizi Microsoft correlati:

- [Centro protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/)

    Informazioni sull'incorporamento della protezione e della privacy nello sviluppo di Azure e su come Azure soddisfi un ampio set di standard di conformità agli standard internazionali e alle specifiche di settore

- [Home page di Microsoft Azure](http://www.microsoft.com/windowsazure/)

    Informazioni generali e collegamenti relativi a Microsoft Azure

- [Centro di documentazione di Microsoft Azure](http://msdn.microsoft.com/windowsazure/default.aspx)

    Linee guida per i servizi di Azure e gli script di automazione

- [Microsoft Security Response Center (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    MSRC lavora con partner e ricercatori dediti alla protezione in tutto il mondo per impedire problemi di protezione e anticipare la protezione dei prodotti Microsoft

- [Email di Microsoft Security Response Center](mailto:secure@microsoft.com)

    Inviare email per segnalare le vulnerabilità della protezione Microsoft, tra cui Microsoft Azure

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=AcomDC_0128_2016-->