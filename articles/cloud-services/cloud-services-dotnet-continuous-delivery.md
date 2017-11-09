---
title: Recapito continuo per Servizi cloud con TFS in Azure | Documentazione Microsoft
description: Informazioni sulla configurazione del recapito continuo per le app cloud di Azure. Esempi di codice Code per istruzioni della riga di comando MSBuild e script di PowerShell.
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 0979722b9ec715e91825c7aba74657451df6e83f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Recapito continuo per Servizi cloud in Azure
Nel processo descritto in questo articolo viene illustrato come configurare il recapito continuo per le app cloud di Azure. Questa procedura crea automaticamente e distribuisce pacchetti in Azure dopo ogni archiviazione di codice. Il processo di compilazione del pacchetto descritto in questo articolo è equivalente al comando **Pacchetto** di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando **Pubblica** di Visual Studio.
L'articolo riguarda i metodi da usare per creare un server di compilazione con istruzioni da riga di comando di MSBuild e script di Windows PowerShell. Viene inoltre illustrato come configurare, facoltativamente, definizioni di Visual Studio Team Foundation Server - Team Build per usare i comandi di MSBuild e gli script di PowerShell. Il processo è personalizzabile per il proprio ambiente di compilazione e per gli ambienti Azure di destinazione.

Per agevolare tali operazioni è inoltre disponibile Visual Studio Team Services, una versione di TFS ospitata in Azure. 

Prima di iniziare, pubblicare l'applicazione da Visual Studio.
Questo garantisce che tutte le risorse siano disponibili e inizializzate quando si tenta di automatizzare il processo di pubblicazione.

## <a name="1-configure-the-build-server"></a>1: Configurare il server di compilazione
Prima di creare un pacchetto di Azure usando MSBuild, è necessario installare il software e gli strumenti necessari nel server di compilazione.

L'installazione di Visual Studio nel server di compilazione non è necessaria. Per usare il servizio Team Foundation Build per gestire il server di compilazione, seguire le istruzioni riportate nella documentazione del [servizio Team Foundation Build][Team Foundation Build Service].

1. Nel server di compilazione installare [.NET Framework 4.5.2][.NET Framework 4.5.2], che include MSBuild.
2. Installare la versione più recente [Strumenti di creazione di Azure per .NET](https://azure.microsoft.com/develop/net/).
3. Installare le [Librerie di Azure per .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Copiare il file Microsoft.WebApplication.targets da un'installazione di Visual Studio al server di compilazione.

   In un computer in cui è installato Visual Studio, questo file si trova nella directory \\Program Files (x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Copiare il file nella stessa cartella del server di compilazione.
5. Installare gli [strumenti di Azure per Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: Compilare un pacchetto usando i comandi MSBuild
Questa sezione descrive come creare un comando MSBuild per compilare un pacchetto di Azure. Eseguire questo passaggio nel server di compilazione per verificare che tutto sia configurato correttamente e che il comando MSBuild abbia l'effetto desiderato. È possibile aggiungere questa riga di comando agli script di compilazione esistenti nel server di compilazione oppure è possibile usare la riga di comando in una definizione di compilazione di TFS, come descritto nella sezione successiva. Per altre informazioni sui parametri della riga di comando e su MSBuild, vedere [Riferimenti alla riga di comando di MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Se Visual Studio è installato nel server di compilazione, trovare e scegliere **Prompt dei comandi di Visual Studio** nella cartella **Strumenti di Visual Studio** in Windows.

   Se Visual Studio non è installato nel server di compilazione, aprire un prompt dei comandi e verificare che MSBuild.exe sia accessibile nel percorso. MSBuild viene installato con .NET Framework nel percorso %WINDIR%\\Microsoft.NET\\Framework\\*Versione*. Per aggiungere MSBuild.exe alla variabile di ambiente PATH quando .NET Framework 4 è stato installato, ad esempio, digitare il comando seguente al prompt dei comandi:

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. Dal prompt dei comandi, passare alla cartella contenente il file del progetto di Azure che si desidera compilare.
3. Eseguire MSbuild con l'opzione /target:Publish come illustrato nell'esempio seguente:

       MSBuild /target:Publish

   Per questa opzione è possibile usare la sintassi abbreviata /t:Publish. Quando è installato Azure SDK, l'opzione /t:Publish in MSBuild non va confusa con i comandi di pubblicazione disponibili in Visual Studio. L'opzione /t:Publish compila unicamente pacchetti di Azure ma non li distribuisce come accade con i comandi di pubblicazione di Visual Studio.

   Facoltativamente, è possibile specificare il nome del progetto come parametro di MSBuild. Se non specificato, viene usata la directory corrente. Per altre informazioni sulle opzioni della riga di comando di MSBuild, vedere [Riferimenti alla riga di comando di MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Individuare l'output. Per impostazione predefinita, questo comando crea una directory in relazione alla cartella radice del progetto, ad esempio *ProjectDir*\\bin\\*Configuration*\\app.publish\\. Quando si compila un progetto Azure, vengono generati due file, il file del pacchetto e il file di configurazione di accompagnamento:

   * Project.cspkg
   * ServiceConfiguration.*TargetProfile*.cscfg

   Per impostazione predefinita, ogni progetto Azure include un file di configurazione del servizio (file con estensione cscfg) per compilazioni locali (debug) e un altro per le compilazioni nel cloud (gestione temporanea o produzione), ma è possibile aggiungere o rimuovere file di configurazione del servizio in base alle necessità. Quando si compila un pacchetto all'interno di Visual Studio, verrà chiesto quale file di configurazione del servizio includere insieme al pacchetto.
5. Specificare il file di configurazione del servizio. Quando si compila un pacchetto usando MSBuild, il file di configurazione del servizio locale viene incluso per impostazione predefinita. Per includere un file di configurazione del servizio diverso, impostare la proprietà TargetProfile del comando MSBuild, come nell'esempio seguente:

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Specificare il percorso per l'output. Impostare il percorso usando l'opzione /p:PublishDir=*Directory*\\, compreso il separatore barra rovesciata finale, come nell'esempio seguente:

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   Una volta creata e testata una riga di comando di MSBuild appropriata per compilare i progetti e combinarli in un pacchetto di Azure, è possibile aggiungere tale riga di comando agli script di compilazione. Se il server di compilazione usa script personalizzati, questo processo dipende dalle specifiche del processo personalizzato di compilazione. Se si usa TFS come ambiente di compilazione, è possibile seguire le istruzioni nel prossimo passaggio per aggiungere la compilazione del pacchetto di Azure al proprio processo di compilazione.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: Compilare un pacchetto usando TFS Team Build
Se si dispone di Team Foundation Server (TFS) configurato come controller di compilazione, e il server di compilazione è impostato come computer di compilazione TFS, è possibile configurare facoltativamente una compilazione automatica per il pacchetto Azure. Per informazioni su come configurare e usare Team Foundation Server come sistema di compilazione, vedere [Aumentare il numero di istanze del sistema di compilazione][Scale out your build system]. In particolare, nella procedura seguente si presuppone che il server di compilazione sia stato configurato come descritto in [Distribuire e configurare un server di compilazione][Deploy and configure a build server] e che siano stati creati un progetto team e un progetto servizio cloud nel progetto team.

Per configurare TFS per la compilazione di pacchetti Azure, eseguire la procedura seguente:

1. In Visual Studio, nel computer di sviluppo, scegliere **Team Explorer** dal menu Visualizza oppure premere Ctrl+\\, Ctrl+M. Nella finestra di Team Explorer espandere il nodo **Compilazioni** o scegliere la pagina **Compilazioni** e fare clic su **Nuova definizione di compilazione**.

   ![Opzione Nuova definizione di compilazione][0]
2. Selezionare la scheda **Trigger** e specificare le condizioni desiderate relativamente al momento in cui si desidera che il pacchetto venga creato. Specificare **Integrazione continuata** per compilare il pacchetto ogni volta che ha luogo un'archiviazione del controllo del codice sorgente.
3. Scegliere la scheda **Impostazioni di origine** e verificare che la cartella del progetto sia elencata nella colonna **Cartella del controllo del codice sorgente** e che lo stato sia **Attivo**.
4. Scegliere la scheda **Impostazioni predefinite compilazione** e, in Controller di compilazione, verificare il nome del server di compilazione.  Scegliere anche l'opzione **Copia output di compilazione nella seguente cartella di ricezione** e specificare la destinazione finale desiderata.
5. Selezionare la scheda **Processo** . Nella scheda Processo scegliere il modello predefinito in **Compilazione**, scegliere il progetto se non è già selezionato ed espandere la sezione **Avanzate** nella sezione **Compilazione** della griglia.
6. Scegliere **Argomenti MSBuild**e impostare gli argomenti della riga di comando MSBuild appropriati come descritto al precedente punto 2. Immettere ad esempio **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** per compilare un pacchetto e copiare i relativi file nel percorso \\\\myserver\\drops\\:

   ![Argomenti MSBuild][2]

   > [!NOTE]
   > la copia dei file in una condivisione pubblica semplifica la distribuzione manuale dei pacchetti dal computer di sviluppo.
7. Verificare l'esito del passaggio di compilazione archiviando una modifica al progetto o inserire in coda una nuova compilazione. Per inserire in coda una nuova compilazione, in Team Explorer fare clic con il pulsante destro del mouse su **Tutte le definizioni di compilazione**, quindi scegliere **Accoda nuova compilazione**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: Pubblicare un pacchetto usando uno script PowerShell
In questa sezione viene descritto come creare uno script di Windows PowerShell che pubblicherà l'output del pacchetto dell'app per cloud in Azure usando parametri facoltativi. Questo script può essere chiamato dopo il passaggio di compilazione nell'automazione della compilazione personalizzata. È possibile chiamare lo script anche da attività del flusso di lavoro del modello di processo in Visual Studio TFS Team Build.

1. Installare i [cmdlet di Azure PowerShell][Azure PowerShell cmdlets] (0.6.1 o versione successiva).
   Durante la fase di installazione dei cmdlet, scegliere l'installazione come snap-in. Si noti che questa versione ufficialmente supportata sostituisce la versione precedente offerta tramite CodePlex, anche se le versioni precedenti sono state numerate 2.x.x.
2. Avviare Azure PowerShell usando il menu o la pagina Start. Con l'avvio in questa modalità, verranno caricati i cmdlet di Azure PowerShell.
3. Al prompt di PowerShell, verificare che i cmdlet di PowerShell sono caricati immettendo il comando parziale `Get-Azure` , quindi premendo il tasto TAB per il completamento.

   Premendo il tasto TAB ripetutamente, verranno visualizzati diversi comandi di Azure PowerShell.
4. Verificare che sia possibile connettersi alla propria sottoscrizione Azure importando le proprie informazioni di sottoscrizione dal file con estensione .publishsettings come segue:

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Quindi immettere il comando

   `Get-AzureSubscription`

   Vengono visualizzate le informazioni relative alla sottoscrizione. Verificarne la correttezza.
5. Salvare il modello di script fornito alla fine di questo articolo nella cartella degli script, ad esempio c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.
6. Rivedere la sezione dello script relativa ai parametri. Aggiungere o modificare i valori predefiniti. Questi valori possono sempre essere sostituiti mediante il passaggio di parametri espliciti.
7. Assicurarsi che siano presenti account validi per i servizi cloud e di archiviazione creati nella sottoscrizione a cui lo script sia in grado di fare riferimento. L'account di archiviazione (archiviazione BLOB) verrà usato per caricare e archiviare temporaneamente il pacchetto di distribuzione e il file di configurazione durante la creazione della distribuzione.

   * Per creare un nuovo servizio cloud, è possibile chiamare questo script o usare il [portale di Azure](https://portal.azure.com). Il nome del servizio cloud verrà usato come prefisso di un nome di dominio completo e deve pertanto essere univoco.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Per creare un nuovo account di archiviazione, è possibile chiamare questo script o usare il [portale di Azure](https://portal.azure.com). Il nome dell'account di archiviazione verrà usato come prefisso di un nome di dominio completo e deve pertanto essere univoco. È possibile provare a usare lo stesso nome come del servizio cloud.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. Chiamare lo script direttamente da Azure PowerShell o collegarlo all'automazione delle compilazioni dell'host in uso in modo che venga eseguito dopo la compilazione del pacchetto.

   > [!IMPORTANT]
   > se vengono rilevate distribuzioni esistenti, lo script le elimina o le sostituisce per impostazione predefinita. Questo è necessario per consentire il recapito continuo da automazioni in cui non è possibile visualizzare richieste utente.
   >
   >

   **Scenario di esempio 1** : distribuzione continua di un servizio nell'ambiente di gestione temporanea:

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Questo è in genere seguito da verifica tramite esecuzione di test e da uno scambio di indirizzi VIP. Tale scambio di indirizzi VIP può essere eseguito nel [portale di Azure](https://portal.azure.com) o con il cmdlet Move-Deployment.

   **Scenario di esempio 2** : distribuzione continua di un servizio di test dedicato nell'ambiente di produzione

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Desktop remoto:**

   Se Desktop remoto è abilitato nel progetto Azure, sarà necessario eseguire passaggi aggiuntivi una tantum per assicurarsi che il corretto certificato di servizio cloud venga caricato in tutti i servizi cloud che questo script ha come destinazione.

   Individuare i valori di identificazione personale del certificato previsti dai ruoli in uso. I valori di identificazione personale sono visibili nella sezione Certificati del file config cloud (ovvero ServiceConfiguration.Cloud.cscfg). Sono inoltre visibili nella finestra di dialogo di configurazione di Desktop remoto in Visual Studio quando si accede alle opzioni e si visualizza il certificato selezionato.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   Caricare i certificati di Desktop remoto come passaggio di installazione una tantum usando lo script di cmdlet seguente:

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   ad esempio:

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   In alternativa è possibile esportare il file di certificato PFX con la chiave privata e caricare i certificati in ogni servizio cloud di destinazione tramite il [portale di Azure](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Aggiorna distribuzione ed Elimina distribuzione -** Nuova distribuzione\>

   Per impostazione predefinita, questo script esegue un aggiornamento della distribuzione ($enableDeploymentUpgrade = 1) quando non viene passato alcun parametro o quando 1 viene passato in modo esplicito. Per singole istanze, questo ha il vantaggio di richiedere meno tempo rispetto a una distribuzione completa. Per istanze che richiedono un'elevata disponibilità, vi è inoltre il vantaggio del mantenimento di alcune istanze in esecuzione mentre altre vengono aggiornate (enumerazione del dominio di aggiornamento). L'indirizzo VIP, inoltre, non verrà eliminato.

   L'aggiornamento della distribuzione può essere disabilitato nello script ($enableDeploymentUpgrade = 0) oppure passando *- enableDeploymentUpgrade 0* come parametro, il che modifica il comportamento dello script in modo che questo elimini innanzitutto qualsiasi distribuzione esistente e quindi ne crei una nuova.

   > [!IMPORTANT]
   > se vengono rilevate distribuzioni esistenti, lo script le elimina o le sostituisce per impostazione predefinita. Questo è necessario per consentire il recapito continuo da automazioni in cui non è possibile visualizzare richieste utente/operatore.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: Pubblicare un pacchetto usando TFS Team Build
In questo passaggio facoltativo si collega TFS Team Build con lo script creato nel passaggio 4, che gestisce la pubblicazione della compilazione del pacchetto in Azure. Questo comporta la modifica del modello di processo usato dalla definizione di compilazione in modo che venga eseguita un'attività di pubblicazione al termine del flusso di lavoro. L'attività di pubblicazione eseguirà il comando PowerShell passando i parametri dalla compilazione. L'output delle destinazioni di MSBuild e dello script di pubblicazione verrà inviato all'output standard della compilazione.

1. Modificare la definizione di compilazione responsabile della distribuzione continua.
2. Selezionare la scheda **Processo** .
3. Seguire [queste istruzioni](http://msdn.microsoft.com/library/dd647551.aspx) per aggiungere un progetto di attività per il modello di processo di compilazione, scaricare il modello predefinito, aggiungerlo al progetto e archiviarlo. Assegnare un nuovo nome al modello di processo di compilazione, ad esempio AzureBuildProcessTemplate.
4. Tornare alla scheda **Processo** e usare **Mostra dettagli** per visualizzare un elenco dei modelli di processo di compilazione disponibili. Scegliere il pulsante **Nuovo** e passare al progetto appena aggiunto e archiviato. Individuare il modello appena creato e scegliere **OK**.
5. Aprire il modello di processo selezionato per la modifica. È possibile aprirlo direttamente in Progettazione flussi di lavoro o nell'editor XML per lavorare con XAML.
6. Aggiungere l'elenco seguente di argomenti nuovi come voci, una per riga, nella scheda degli argomenti della progettazione flussi di lavoro. Tutti gli argomenti devono presentare direction=In e type=String. Questi verranno usati per inviare i parametri dalla definizione di compilazione al flusso di lavoro, dove poi verranno usati per chiamare lo script di pubblicazione.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Elenco di argomenti][3]

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
7. Aggiungere una nuova sequenza alla fine di Esegui su agente:

   1. Iniziare con l'aggiunta di un'attività di istruzione If per verificare la presenza di un file di script valido. Impostare la condizione sul valore seguente:

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. Nella condizione Then dell'istruzione If aggiungere una nuova attività di sequenza. Impostare il nome visualizzato su 'Start publish'
   3. Con l'opzione di avvio della sequenza di pubblicazione ancora selezionata, aggiungere l'elenco seguente di nuove variabili come voci ognuna su una nuova riga nella scheda delle variabili della progettazione flussi di lavoro. Tutte le variabili presentare Variable type =String e Scope=Start publish. Questi verranno usati per inviare i parametri dalla definizione di compilazione al flusso di lavoro, dove poi verranno usati per chiamare lo script di pubblicazione.

      * SubscriptionDataFilePath, di tipo String
      * PublishScriptFilePath, di tipo String

        ![Nuove variabili][4]
   4. Se si usa TFS 2012 o versione precedente, aggiungere un'attività ConvertWorkspaceItem all'inizio della nuova sequenza. Se si usa TFS 2013 o versione successiva, aggiungere un'attività GetLocalPath all'inizio della nuova sequenza. Per ConvertWorkspaceItem impostare le proprietà come segue: Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation', Result='PublishScriptFilePath', Workspace='Workspace'. Per un'attività GetLocalPath impostare la proprietà IncomingPath su 'PublishScriptLocation' e Result su 'PublishScriptFilePath'. Questa attività converte il percorso dello script di pubblicazione dai percorsi server TFS (se applicabile) in un percorso standard di disco locale.
   5. Se si usa TFS 2012 o versione precedente, aggiungere un'altra attività ConvertWorkspaceItem alla fine della nuova sequenza. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Se si usa TFS 2013 o versione successiva, aggiungere un'altra attività GetLocalPath. IncomingPath='SubscriptionDataFileLocation' e Result='SubscriptionDataFilePath.'
   6. Aggiungere un'attività InvokeProcess alla fine della nuova sequenza.
      Questa attività chiama PowerShell.exe con gli argomenti passati dalla definizione di compilazione.

      + Arguments = String.Format(" -File ""{0}"" -serviceName {1}  -storageAccountName {2} -packageLocation ""{3}""  -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""  -selectedSubscription {6} -environment ""{7}""",  PublishScriptFilePath, ServiceName, StorageAccountName,  PackageLocation, CloudConfigLocation,  SubscriptionDataFilePath, SubscriptionName, Environment)
      + DisplayName = Execute publish script
      + FileName = "PowerShell" (includere le virgolette)
      + OutputEncoding=  System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. Nella casella di testo della sezione **Gestisci output standard** di InvokeProcess impostare il valore della casella di testo su 'data'. Questa è una variabile in cui archiviare i dati dell'output standard.
   8. Aggiungere un'attività WriteBuildMessage appena sotto la sezione **Gestisci output standard** . Impostare Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' e Message='data'. Questo garantisce che l'output standard dello script verrà scritto nell'output di compilazione.
   9. Nella casella di testo della sezione **Gestisci output errore** di InvokeProcess impostare il valore della casella di testo su 'data'. Questa è una variabile in cui archiviare i dati dell'output errore.
   10. Aggiungere un'attività WriteBuildError appena sotto la sezione **Gestisci output errore** . Impostare Message='data'. Questo garantisce che l'output di errore dello script verrà scritto nell'output di errore della compilazione.
   11. Correggere eventuali errori, indicati da punti esclamativi blu. Passare il puntatore sui punti esclamativi per ottenere un suggerimento relativo all'errore. Salvare il flusso di lavoro per cancellare gli errori.

   Il risultato finale delle attività del flusso di lavoro di pubblicazione sarà simile al seguente nella finestra di progettazione:

   ![Attività flusso di lavoro][5]

   Il risultato finale delle attività del flusso di lavoro di pubblicazione sarà simile al seguente nel codice XAML:

       <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
           <If.Then>
             <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
               <Sequence.Variables>
                 <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                 <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
               </Sequence.Variables>
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
               <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                 <mtbwa:InvokeProcess.ErrorDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.ErrorDataReceived>
                 <mtbwa:InvokeProcess.OutputDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.OutputDataReceived>
               </mtbwa:InvokeProcess>
             </Sequence>
           </If.Then>
         </If>
       </Sequence>
8. Salvare il flusso di lavoro del modello di processo di compilazione ed eseguire l'archiviazione di questo file.
9. Modificare la definizione di compilazione (chiuderla se è già aperta) e fare clic sul pulsante **Nuovo** se il nuovo modello non è ancora visibile nell'elenco di modelli di processo.
10. Impostare i valori delle proprietà dei parametri nella sezione Varie come indicato di seguito:

    1. CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Questo valore deriva da: ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg' *Questo valore deriva da: ($PublishDir)($ProjectName).cspkg*
    3. PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'
    4. ServiceName = 'mycloudservicename' *Qui usare il nome del servizio cloud appropriato*
    5. Environment = 'Staging'
    6. StorageAccountName = 'mystorageaccountname' *Qui usare il nome dell'account di archiviazione appropriato*
    7. SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'
    8. SubscriptionName = 'default'

    ![Valori delle proprietà dei parametri][6]
11. Salvare le modifiche nella definizione di compilazione.
12. Accodare una compilazione per eseguire sia la compilazione del pacchetto che la pubblicazione. Se è impostato un trigger per la distribuzione continua, questo comportamento verrà eseguito a ogni archiviazione.

### <a name="publishcloudserviceps1-script-template"></a>Modello di script PublishCloudService.ps1
```
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
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

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
```

## <a name="next-steps"></a>Passaggi successivi
Per abilitare il debug remoto durante l'uso della distribuzione continua, vedere [Abilitare il debug remoto con la distribuzione continua per la pubblicazione in Azure](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
