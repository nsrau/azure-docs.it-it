<properties urlDisplayName="Continuous Delivery" pageTitle="Recapito continuo per Servizi cloud con TFS in Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Informazioni sulla configurazione del recapito continuo per le app cloud di Azure. Esempi di codice Code per istruzioni della riga di comando MSBuild e script di PowerShell." metaCanonical="" services="" documentationCenter="" title="Recapito continuo per Servizi cloud in Azure" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Recapito continuo per Servizi cloud in Azure

Nel processo descritto in questo articolo viene illustrato come configurare il recapito
continuo per le app cloud di Azure. Questo processo consente di
creare automaticamente i pacchetti e di implementare il pacchetto in Azure
dopo ogni archiviazione del codice. Il processo di compilazione dei pacchetti descritto in questo
articolo equivale al comando Pacchetto in Visual Studio e i
passaggi della pubblicazione equivalgono al comando Pubblica in Visual Studio.
L'articolo illustra i metodi usati per creare un server di compilazione
con le istruzioni della riga di comando MSBuild e gli script di Windows PowerShell e
descrive anche come configurare facoltativamente le definizioni di Visual Studio Team
Foundation Server - Team Build per poter usare i comandi MSBuild
e gli script di PowerShell. Il processo è personalizzabile per il proprio ambiente
di compilazione e per gli ambienti Azure di destinazione.

Per agevolare tali operazioni è inoltre disponibile Visual Studio Online, una versione di TFS ospitata in Azure. Per altre informazioni, vedere [Recapito continuo in Azure usando Visual Studio Online][Recapito continuo in Azure usando Visual Studio Online].

Prima di iniziare, pubblicare l'applicazione da Visual Studio.
Questo garantisce che tutte le risorse siano disponibili e inizializzate quando si
tenta di automatizzare il processo di pubblicazione.

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Configurare il server di compilazione][Passaggio 1: Configurare il server di compilazione]
-   [Passaggio 2: Compilare un pacchetto usando i comandi MSBuild][Passaggio 2: Compilare un pacchetto usando i comandi MSBuild]
-   [Passaggio 3: Compilare un pacchetto usando TFS Team Build (facoltativo)][Passaggio 3: Compilare un pacchetto usando TFS Team Build (facoltativo)]
-   [Passaggio 4: Pubblicare un pacchetto usando uno Script di PowerShell][Passaggio 4: Pubblicare un pacchetto usando uno Script di PowerShell]
-   [Passaggio 5: Pubblicare un pacchetto usando TFS Team Build (facoltativo)][Passaggio 5: Pubblicare un pacchetto usando TFS Team Build (facoltativo)]

## <a name="step1"> </a><span class="short-header">Configurare il server di compilazione</span>Passaggio 1: Configurare il server di compilazione

Prima di creare un pacchetto di Azure usando MSBuild, è necessario
installare il software e gli strumenti necessari nel server di compilazione.

L'installazione di Visual Studio nel server di compilazione non è necessaria. Per
usare il servizio Team Foundation Build per gestire il server
di compilazione, seguire le istruzioni riportate nella documentazione del [servizio Team Foundation Build][servizio Team Foundation Build]
.

1.  Nel server di compilazione installare [.NET Framework 4][.NET Framework 4] o [.NET Framework 4.5][.NET Framework 4.5], che
    include MSBuild.
2.  Installare gli [strumenti di creazione di Azure][strumenti di creazione di Azure] (cercare
    WindowsAzureAuthoringTools-x86.msi o WindowsAzureAuthoringTools-x64,
    a seconda del processore del server di compilazione).
3.  Installare le [librerie di Azure][librerie di Azure] (cercare WindowsAzureLibsForNet-x86.msi o WindowsAzureLibsForNet-x64. msi).
4.  Copiare il file Microsoft.WebApplication.targets da un'installazione
    di Visual Studio nel server di compilazione. In un computer con installato
    Visual Studio, il file si trova nella directory C:\\Programmi
    (x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0
     per Visual Studio 2013). Copiare
    il file nella stessa directory del server di compilazione.
5.  Installare gli [Strumenti di Azure per Visual Studio][librerie di Azure].
    Cercare WindowsAzureTools.VS110.exe per compilare progetti Visual Studio 2012 e WindowsAzureTools.VS120.exe per compilare progetti Visual Studio 2013.

## <a name="step2"> </a><span class="short-header">Compilare un pacchetto usando MSBuild</span>Step 2: Compilare un pacchetto usando i comandi MSBuild

In questa sezione viene descritto come creare un comando MSBuild per compilare un
pacchetto di Azure. Eseguire questo passaggio nel server di compilazione per verificare che
tutto sia configurato correttamente e che il comando MSBuild abbia
l'effetto voluto. È possibile aggiungere questa riga di comando agli script di compilazione
esistenti nel server di compilazione oppure è possibile usare la riga di comando in una
definizione di compilazione di TFS, come descritto nella prossima sezione. Per altre
informazioni sui parametri della riga di comando e su MSBuild, vedere [Riferimenti per la riga di comando di MSBuild][Riferimenti per la riga di comando di MSBuild].

1.  Se Visual Studio è installato nel server di compilazione, fare clic su
    **Start**, scegliere
    **Tutti i programmi**, quindi individuare e fare clic su
    **Prompt dei comandi di Visual Studio** nella cartella
    **Strumenti di Visual Studio**
    .

    Se Visual Studio non è installato nel server di compilazione, aprire un
    prompt dei comandi e verificare che MSBuild.exe sia accessibile nel
    percorso. MSBuild viene installato con .NET Framework nel percorso
    %WINDIR%\\Microsoft.NET\\Framework\\*Versione*. Per
    aggiungere MSBuild.exe alla variabile di ambiente PATH quando .NET
    Framework 4 è stato installato, ad esempio, digitare il comando seguente al prompt dei
    comandi:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  Dal prompt dei comandi, passare alla cartella contenente il file di progetto di Windows
    Azure che si vuole compilare.

3.  Eseguire MSbuild con l'opzione /target:Publish come illustrato nell'esempio
    seguente:

        MSBuild /target:Publish

    Per questa opzione è possibile usare la sintassi abbreviata /t:Publish. Quando è installato
    Azure SDK, l'opzione /t:Publish
    in MSBuild non va confusa con i comandi di pubblicazione
    disponibili in Visual Studio. L'opzione /t:Publish compila unicamente pacchetti
    di Azure, ma non li distribuisce come accade con i comandi di pubblicazione di
    Visual Studio.

    Facoltativamente, è possibile specificare il nome del progetto come parametro
    di MSBuild. Se non specificato, viene usata la directory corrente. Per altre
    informazioni sui parametri della riga di comando di MSBuild, vedere [Riferimenti per la riga
    di comando di MSBuild][Riferimenti per la riga
    di comando di MSBuild].

4.  Individuare l'output. Per impostazione predefinita, questo comando crea una directory in
    relazione alla cartella radice del progetto, ad esempio
    *DirProgetto*\\bin\\*Configurazione*\\app.publish\\. Quando si
    compila un progetto Azure, vengono generati due file, il file
    del pacchetto e il file di configurazione di accompagnamento:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    Per impostazione predefinita, ogni progetto Azure include un
    file di configurazione del servizio (file con estensione .cscfg) per compilazioni locali (debug)
    e un altro per le compilazioni nel cloud (gestione temporanea o produzione), ma
    è possibile aggiungere o rimuovere file di configurazione del servizio in base alle necessità. Quando si
    compila un pacchetto in Visual Studio, verrà chiesto quale
    file di configurazione del servizio includere con il pacchetto.

5.  Specificare il file di configurazione del servizio. Quando si compila un pacchetto
    usando MSBuild, il file di configurazione del servizio locale viene incluso per
    impostazione predefinita. Per includere un file di configurazione del servizio diverso, impostare la
    proprietà TargetProfile del comando MSBuild, come nell'esempio
    seguente:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Specificare il percorso per l'output. Impostare il percorso usando l'opzione
    /p:PublishDir=*Directory*\\, compreso il separatore a barra rovesciata finale,
    come nell'esempio seguente:

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    Una volta creata e testata una riga di comando
    di MSBuild appropriata per compilare i progetti e combinarli in un pacchetto di Azure,
    è possibile aggiungere tale riga di comando agli script di compilazione. Se il server di compilazione
    usa script personalizzati, questo processo dipende dalle
    specifiche del processo personalizzato di compilazione. Se si usa TFS come
    ambiente di compilazione, è possibile seguire le istruzioni nel prossimo
    passaggio per aggiungere la compilazione del pacchetto di Azure al proprio processo di compilazione.

## <a name="step3"> </a><span class="short-header">Compilare un pacchetto usando TFS </span>Passaggio 3: Compilare un pacchetto usando TFS Team Build (facoltativo)

Se Team Foundation Server (TFS) è configurato come controller di compilazione
e il server di compilazione è impostato come computer di compilazione TFS, è possibile configurare
una compilazione automatica per il pacchetto di Azure. Per informazioni su
come configurare e usare Team Foundation Server come sistema di compilazione, vedere
[Informazioni sul sistema di compilazione di Team Foundation][Informazioni sul sistema di compilazione di Team Foundation]. In particolare, per la
procedura seguente si presuppone che sia stato configurato il server di compilazione
come descritto in [Configurazione di un computer di compilazione][Configurazione di un computer di compilazione].

Per configurare TFS per la compilazione di pacchetti di Azure, eseguire la procedura
seguente:

1.  In Visual Studio, nel computer di sviluppo, dal menu Visualizza
    scegliere **Team Explorer** oppure premere CTRL+\\, CTRL+M. Nella
    finestra di Team Explorer espandere il nodo **Compilazioni**, fare clic con il pulsante destro del mouse su **Tutte
    le definizioni di compilazione**, quindi fare clic su **Nuova definizione di compilazione**:

    ![][0]

2.  Fare clic sulla scheda **Processo**. Nella scheda Processo scegliere il modello predefinito,
    quindi in Elementi da compilare scegliere il progetto
    ed espandere la sezione **Avanzate** nella griglia.

3.  Scegliere **Argomenti MSBuild** e impostare gli argomenti della riga di comando MSBuild
    appropriati come descritto al precedente passaggio 2. Immettere ad esempio
    /t:Publish /p:PublishDir=\\\\myserver\\drops\\ **per compilare un**pacchetto e copiare i relativi file nel percorso
    \\\\myserver\\drops\\:

    ![][1]

    **Nota:** la copia dei file in una condivisione pubblica semplifica la
    distribuzione manuale dei pacchetti dal computer di sviluppo.

4.  Fare clic sulla scheda **Trigger** e specificare le condizioni desiderate relativamente
    al momento in cui si vuole che il pacchetto venga creato. Specificare
    **Integrazione continuata** per compilare il pacchetto ogni volta che ha luogo un'archiviazione del controllo
    del codice sorgente.

5.  Scegliere la scheda **Impostazioni predefinite compilazione** e in Controller di compilazione verificare
    il nome del server di compilazione. Scegliere anche l'opzione **Copia output di compilazione
    nella seguente cartella di ricezione** e specificare la destinazione finale
    desiderata.

6.  Verificare l'esito del passaggio di compilazione archiviando una modifica al
    progetto o inserire in coda una nuova compilazione. Per inserire in coda una nuova compilazione, in
    Team Explorer fare clic con il pulsante destro del mouse su **Tutte le definizioni di compilazione**, quindi
    scegliere **Accoda nuova compilazione**.

## <a name="step4"> </a><span class="short-header">Pubblicare usando Powershell</span>Passaggio 4: Pubblicare un pacchetto usando uno script di PowerShell

In questa sezione viene descritto come creare uno script di Windows PowerShell che
pubblicherà l'output del pacchetto dell'app cloud in Azure con
parametri facoltativi. Questo script può essere chiamato dopo l'istruzione di compilazione
nell'automazione della compilazione personalizzata. È possibile chiamare lo script anche da attività del flusso di lavoro del modello di processo
in Visual Studio TFS Team Build.

1.  Installare i [cmdlet di Azure PowerShell][cmdlet di Azure PowerShell] (v0.6.1 o versione successiva).
    Durante la fase di installazione dei cmdlet, scegliere l'installazione come snap-in. Si noti
    che questa versione ufficialmente supportata sostituisce la versione precedente offerta
    tramite CodePlex, anche se le versioni precedenti sono state numerate 2.x.x.

2.  Avviare Azure PowerShell usando il menu Start. Con l'avvio in questa modalità,
    verranno caricati i cmdlet di Azure PowerShell.

3.  Al prompt di PowerShell, verificare che i cmdlet di PowerShell siano stati caricati
    digitando il comando parziale Get-Azure, quindi premendo TAB per il completamento
    istruzioni.

    Verranno visualizzati vari comandi di Azure PowerShell.

4.  Verificare che sia possibile connettersi alla propria sottoscrizione di Azure
    importando le proprie informazioni di sottoscrizione dal file con estensione publishsettings.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    Digitare quindi il comando seguente:

    Get-AzureSubscription

    Verranno visualizzate le informazioni relative alla sottoscrizione. Verificarne la correttezza.

5.  Salvare il modello di script fornito alla [fine di questo articolo][fine di questo articolo] nella
    cartella degli script, ad esempio
    c:\\script\\WindowsAzure\\**PublishCloudService.ps1**.

6.  Rivedere la sezione dello script relativa ai parametri. Aggiungere o modificare i
    valori predefiniti. Questi valori possono sempre essere sostituiti mediante il passaggio
    di parametri espliciti.

7.  Assicurarsi che siano presenti account validi per i servizi cloud e di archiviazione creati
    nella sottoscrizione a cui lo script di pubblicazione possa fare riferimento. L'account
    di archiviazione (archiviazione BLOB) verrà usato per caricare e
    archiviare temporaneamente il pacchetto di distribuzione e il file di configurazione durante la
    creazione della distribuzione.

    -   Per creare un nuovo servizio cloud, è possibile chiamare questo script o usare
        il portale di gestione di Azure. Il nome del servizio cloud
        verrà usato come prefisso di un nome di dominio completo e
        deve quindi essere univoco.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Per creare un nuovo account di archiviazione, è possibile chiamare questo script o usare
        il portale di gestione di Azure. Il nome dell'account di archiviazione
        verrà usato come prefisso di un nome di dominio completo e
        deve quindi essere univoco. È possibile provare a usare lo stesso nome del
        servizio cloud.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  Chiamare lo script direttamente da Azure PowerShell o collegarlo
    all'automazione delle compilazioni dell'host in modo che venga eseguito dopo la compilazione
    del pacchetto.

    **AVVISO:** se vengono rilevate distribuzioni esistenti,
    lo script le elimina o le sostituisce per impostazione predefinita. Questo è necessario per
    consentire il recapito continuo da automazioni in cui non è possibile
    visualizzare richieste utente.

    **Scenario di esempio 1:** distribuzione continua di un servizio nell'ambiente
    di gestione temporanea:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Questo è in genere seguito da verifica con esecuzione dei test e da uno swapping
    di indirizzi VIP. Tale swapping può essere eseguito tramite il portale di gestione
    di Azure o usando il cmdlet Move-Deployment.

    **Scenario di esempio 2:** distribuzione continua di un servizio di test dedicato
    nell'ambiente di produzione

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Desktop remoto:**

    Se Desktop remoto è abilitato nel progetto Azure, sarà
    necessario eseguire passaggi aggiuntivi una tantum per assicurarsi che il corretto
    certificato di servizio cloud venga caricato in tutti i servizi cloud
    che questo script ha come destinazione.

    Individuare i valori di identificazione personale del certificato previsti dai ruoli in uso. I
    valori di identificazione personale sono visibili nella sezione Certificati del
    file di configurazione cloud (ovvero ServiceConfiguration.Cloud.cscfg). Sono
    anche visibili nella finestra di dialogo Configurazione Desktop remoto in Visual
    Studio quando si accede alle opzioni e si visualizza il certificato selezionato.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Caricare i certificati di Desktop remoto come passaggio di installazione una tantum con
    lo script di cmdlet seguente:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)lt;THUMBPRINT>)

    Ad esempio:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    In alternativa, è possibile esportare il file PFX di certificato con la chiave privata
    e caricare i certificati per ogni servizio cloud di destinazione con il
    portale di gestione di Azure. Per altre informazioni, leggere l'articolo
    seguente:
    [http://msdn.microsoft.com/it-it/library/windowsazure/gg443832.aspx][http://msdn.microsoft.com/it-it/library/windowsazure/gg443832.aspx].

    **Aggiornamento distribuzione ed eliminazione distribuzione-\> Nuova distribuzione**

    Per impostazione predefinita, questo script esegue un aggiornamento della distribuzione
    ($enableDeploymentUpgrade = 1) quando non viene passato alcun parametro o quando il
    valore 1 viene passato in modo esplicito. Per singole istanze, questo ha il
    vantaggio di richiedere meno tempo rispetto a una distribuzione completa. Per istanze
    che richiedono un'elevata disponibilità, c'è anche il vantaggio
    del mantenimento di alcune istanze in esecuzione mentre altre vengono aggiornate (enumerazione
    del dominio di aggiornamento). L'indirizzo VIP, inoltre, non verrà eliminato.

    L'aggiornamento della distribuzione può essere disabilitato nello script
    ($enableDeploymentUpgrade = 0) oppure passando
    - enableDeploymentUpgrade 0 come parametro, il che modificherà il
    comportamento dello script in modo che questo elimini innanzitutto qualsiasi distribuzione esistente e quindi
    ne crei una nuova.

    **Avviso:** se vengono rilevate distribuzioni esistenti,
    lo script le elimina o le sostituisce per impostazione predefinita. Questo è necessario per
    consentire il recapito continuo da automazioni in cui non è possibile visualizzare
    richieste utente/operatore.

## <a name="step5"> </a><span class="short-header">Pubblicare usando TFS </span>Passaggio 5: Pubblicare un pacchetto usando TFS Team Build (facoltativo)

In questo passaggio verrà collegato TFS Team Build con lo script creato nel passaggio 4,
che gestisce la pubblicazione della compilazione del pacchetto in Azure. Questo
comporta la modifica del modello di processo usato dalla definizione di
compilazione in modo che venga eseguita un'attività Publish al termine del flusso di lavoro. L'attività
Publish eseguirà il comando PowerShell passando i parametri dalla
compilazione. L'output delle destinazioni di MSBuild e dello script di pubblicazione verrà
inviato tramite pipe all'output standard della compilazione.

1.  Modificare la definizione di compilazione responsabile della distribuzione continua.

2.  Selezionare la scheda **Processo**.

3.  Creare un nuovo modello di processo facendo clic sul pulsante Nuovo
    nell'area di selezione del modello di processo della scheda. Verrà caricata la
    finestra di dialogo Nuovo modello di processo di compilazione. In questa finestra di dialogo verificare che l'opzione Copia
    un file XAML esistente sia selezionata, quindi passare al modello di processo
    da copiare, se si vuole cambiare la base proposta dall'impostazione predefinita. Assegnare
    un nome al nuovo modello, ad esempio DefaultTemplateAzure.

4.  Aprire il modello di processo selezionato per la modifica. È possibile aprirlo
    direttamente in Progettazione flussi di lavoro o nell'editor XML per
    lavorare con XAML.

5.  Aggiungere l'elenco seguente di argomenti nuovi come voci, una per riga, nella
    scheda degli argomenti della progettazione flussi di lavoro. Tutti gli argomenti devono
    avere direction=In e type=String. Questi verranno usati per inviare
    i parametri dalla definizione di compilazione al flusso di lavoro, dove poi
    verranno usati per chiamare lo script di pubblicazione.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][2]

    Il codice XAML corrispondente sarà analogo al seguente:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Aggiungere una nuova sequenza alla fine di Esegui su agente:

    1.  Iniziare con l'aggiunta di un'attività Statement If per verificare la presenza di un file
        di script valido. Impostare la condizione sul valore seguente:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Nella condizione Then dell'istruzione If aggiungere una nuova attività
        Sequence. Impostare il nome visualizzato su 'Start publish'

    3.  Con l'opzione di avvio della sequenza di pubblicazione ancora selezionata, aggiungere
        l'elenco seguente di nuove variabili come voci ognuna su una nuova riga nella
        scheda delle variabili della progettazione flussi di lavoro. Tutte le variabili devono
        avere Variable type =String e Scope=Start publish. Questi
        verranno usati per inviare i parametri dalla definizione di compilazione al
        flusso di lavoro, dove poi verranno usati per chiamare lo script di pubblicazione.

        -   SubscriptionDataFilePath, di tipo String

        -   PublishScriptFilePath, di tipo String

            ![][3]

    4.  Aggiungere un'attività ConvertWorkspaceItem all'inizio della nuova
        sequenza. Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Questa
        attività converte il percorso dello script di pubblicazione dai percorsi
        server TFS (se applicabile) in un percorso standard di disco locale.

    5.  Aggiungere un'altra attività ConvertWorkspaceItem alla fine della nuova
        sequenza. Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Aggiungere un'attività InvokeProcess alla fine della nuova sequenza.
        Questa attività chiama PowerShell.exe con gli argomenti passati
        dalla definizione di compilazione.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Execute publish script'

        3.  FileName = "PowerShell"

        4.  OutputEncoding=
            'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  Nella casella di testo della sezione Gestisci output standard di
        InvokeProcess impostare il valore della casella di testo su 'data'. Questa è una
        variabile in cui archiviare i dati dell'output standard.

    8.  Aggiungere un'attività WriteBuildMessage appena sotto la sezione di output
        standard. Impostare Importance =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        e Message='data'. Questo garantisce che l'output standard dello
        script verrà scritto nell'output di compilazione.

    9.  Nella casella di testo della sezione Gestisci standard error di
        InvokeProcess impostare il valore della casella di testo su 'data'. Questa è una
        variabile in cui archiviare i dati di standard error.

    10. Aggiungere un'attività WriteBuildError appena sotto la sezione di output
        standard. Impostare Message='data'. Questo garantisce che gli standard
        error dello script verranno scritti nell'output dell'errore di compilazione.

    Il risultato finale delle attività del flusso di lavoro di pubblicazione sarà simile
    al seguente nella finestra di progettazione:

    ![][4]

    Il risultato finale delle attività del flusso di lavoro di pubblicazione sarà simile
    al seguente nel codice XAML:

            </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If.Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence.Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap:WorkflowViewStateService.ViewState>
                      <scg:Dictionary x:TypeArguments="x:String, x:Object">
                        <x:Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa:InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa:InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa:InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa:InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa:CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  Salvare il flusso di lavoro DefaultTemplateAzure ed eseguire l'archiviazione di questo file.

8.  Selezionare il modello di processo DefaultTemplateAzure nella definizione
    di compilazione. Fare clic su Aggiorna oppure selezionare il pulsante Nuovo se il file non è ancora
     visualizzato nell'elenco dei modelli di processo.

9.  Impostare i valori delle proprietà dei parametri nella sezione Varie come indicato di seguito:

    1.  CloudConfigLocation =
        'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
        *Questo valore deriva da:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation =
        'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *Questo valore deriva da: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation =
        'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *Usare il nome del servizio cloud the appropriato*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *Usare il nome dell'account di archiviazione appropriato*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][5]

10. Salvare le modifiche nella definizione di compilazione.

11. Accodare una compilazione per eseguire sia la compilazione del pacchetto che la pubblicazione. S
     è impostato un trigger per la distribuzione continua, questo comportamento
    verrà eseguito a ogni archiviazione.

### <a name="script"> </a>Modello di script PublishCloudService.ps1

    Param(  $serviceName = "",
            $storageAccountName = "",
            $packageLocation = "",
            $cloudConfigLocation = "",
            $environment = "Staging",
            $deploymentLabel = "ContinuousDeploy to $servicename",
            $timeStampFormat = "g",
            $alwaysDeleteExistingDeployments = 1,
            $enableDeploymentUpgrade = 1,
            $selectedsubscription = "default",
            $subscriptionDataFile = ""
         )
          

    function Publish()
    {
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
        if ($a[0] -ne $null)
        {
            Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
        }
        #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
        if ($deployment.Name -ne $null)
        {
            switch ($alwaysDeleteExistingDeployments)
            {
                1 
                {
                    switch ($enableDeploymentUpgrade)
                    {
                        1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                            UpgradeDeployment
                        }
                        0  #Delete then create new deployment
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                            DeleteDeployment
                            CreateNewDeployment
                            
                        }
                    } # switch ($enableDeploymentUpgrade)
                }
                0
                {
                    Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                    exit
                }
            } #switch ($alwaysDeleteExistingDeployments)
        } else {
                CreateNewDeployment
        }
    }

    function CreateNewDeployment()
    {
        write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

        $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
            
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid

        write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
        
        StartInstances
    }

    function UpgradeDeployment()
    {
        write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

        # perform Update-Deployment
        $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
        
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid
        
        write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
    }

    function DeleteDeployment()
    {

        write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

        #WARNING - always deletes with force
        $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

        write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
        
    }

    function StartInstances()
    {
        write-progress -id 4 -activity "Starting Instances" -status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $runstatus = $deployment.Status

        if ($runstatus -ne 'Running') 
        {
            $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
        }
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $oldStatusStr = @("") * $deployment.RoleInstanceList.Count
        
        while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
        {
            $i = 1
            foreach ($roleInstance in $deployment.RoleInstanceList)
            {
                $instanceName = $roleInstance.InstanceName
                $instanceStatus = $roleInstance.InstanceStatus

                if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
                {
                    $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
                }

                write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
                $i = $i + 1
            }

            sleep -Seconds 1

            $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        }

        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            $i = $i + 1
        }
        
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $opstat = $deployment.Status 
        
        write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
    }

    function AllInstancesRunning($roleInstanceList)
    {
        foreach ($roleInstance in $roleInstanceList)
        {
            if ($roleInstance.InstanceStatus -ne "ReadyRole")
            {
                return $false
            }
        }
        
        return $true
    }

    #configure powershell with Azure 1.7 modules
    Import-Module Azure

    #configure powershell with publishsettings for your subscription
    $pubsettings = $subscriptionDataFile
    Import-AzurePublishSettingsFile $pubsettings
    Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

    #set remaining environment variables for Azure cmdlets
    $subscription = Get-AzureSubscription $selectedsubscription
    $subscriptionname = $subscription.subscriptionname
    $subscriptionid = $subscription.subscriptionid
    $slot = $environment

    #main driver - publish & write progress to activity log
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
    Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

    Publish

    $deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
    $deploymentUrl = $deployment.Url

    Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."

## Passaggi successivi

Per abilitare il debug remoto con la distribuzione continua, vedere [queste istruzioni][queste istruzioni].

  [Recapito continuo in Azure usando Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Passaggio 1: Configurare il server di compilazione]: #step1
  [Passaggio 2: Compilare un pacchetto usando i comandi MSBuild]: #step2
  [Passaggio 3: Compilare un pacchetto usando TFS Team Build (facoltativo)]: #step3
  [Passaggio 4: Pubblicare un pacchetto usando uno Script di PowerShell]: #step4
  [Passaggio 5: Pubblicare un pacchetto usando TFS Team Build (facoltativo)]: #step5
  [servizio Team Foundation Build]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [strumenti di creazione di Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [librerie di Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Riferimenti per la riga di comando di MSBuild]: http://msdn.microsoft.com/it-it/library/ms164311(v=VS.90).aspx
  [Informazioni sul sistema di compilazione di Team Foundation]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Configurazione di un computer di compilazione]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [1]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [cmdlet di Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [fine di questo articolo]: #script
  [2]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
  [queste istruzioni]: http://go.microsoft.com/fwlink/p/?LinkID=402354
