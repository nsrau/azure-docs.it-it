<properties urlDisplayName="Autoscaling" pageTitle="Usare il blocco applicazione per la scalabilità automatica (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Learn how to use the Autoscaling Application for Azure. Code samples are written in C# and use the .NET API." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="How to Use the Autoscaling Application Block" authors="rasquill" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/14/2014" ms.author="rasquill" />







# Come usare il blocco applicazione per la scalabilità automatica

In questa guida viene illustrato come eseguire scenari comuni usando il blocco applicazione per la scalabilità automatica di [Microsoft Enterprise Library 5.0 Integration Pack per Azure][]. Negli esempi, scritti in C#, viene usata l'API .NET. Gli scenari presentati includono l'**hosting del blocco**, l'**uso delle regole di vincolo** e l'**uso delle regole reattive**. Per altre informazioni sul blocco applicazione per la scalabilità automatica, vedere la sezione [Passaggi successivi][].

## Sommario

[Informazioni sul blocco applicazione per la scalabilità automatica][]
 [Concetti][]   
 [Raccogliere dati del contatore delle prestazioni dall'applicazione Azure di destinazione][]   
 [Configurare un'applicazione host per il blocco applicazione per la scalabilità automatica][]   
 [Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla][] [ Procedura: Definire il modello del servizio][]   
 [Procedura: Definire le regole di scalabilità automatica][]   
 [Procedura: Configurare il blocco applicazione per la scalabilità automatica][]   
 [Passaggi successivi][]

## <a id="WhatIs"> </a>Informazioni sul blocco applicazione per la scalabilità automatica

Il blocco applicazione per la scalabilità automatica consente di adattare automaticamente un'applicazione Azure in base a regole definite in modo specifico per l'applicazione. È possibile usare queste regole per fare in modo che l'applicazione Azure mantenga la stessa velocità effettiva con il variare del carico di lavoro, controllando al tempo stesso i costi associati all'hosting dell'applicazione in Azure. Oltre ad adattare l'applicazione aumentando o diminuendo il numero di istanze del ruolo nell'applicazione, il blocco consente anche di usare altre operazioni di scalabilità come la limitazione di determinate funzionalità dell'applicazione o l'uso di azioni personalizzate.

È possibile scegliere di ospitare il blocco in un ruolo di Azure o in un'applicazione locale. 
Il blocco applicazione per la scalabilità automatica fa parte di [Microsoft Enterprise Library 5.0 Integration Pack per Azure][].

## <a id="Concepts"> </a>Concetti

Nel diagramma seguente la linea verde rappresenta il numero di istanze in esecuzione del ruolo di Azure in due giorni. Il numero di istanze cambia automaticamente nel tempo in risposta a un insieme di regole di scalabilità automatica.

![diagram of sample autoscaling](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

Il blocco utilizza due tipi di regole per definire il comportamento di scalabilità automatica dell'applicazione.

-   **Regole di vincolo** per configurare il limite massimo e minimo per il numero di istanze, ad esempio tra le quattro e le sei istanze tra le 8.00 e le 10.00 ogni mattina, è possibile usare una **regola di vincolo**. Nel diagramma le linee rossa e blu rappresentano le regole di vincolo. Nel punto **A** del diagramma, ad esempio, il numero minimo delle istanze del ruolo aumenta da due a quattro, per adattarsi in anticipo all'aumento del carico di lavoro dell'applicazione a quest'ora. Nel punto **B** del diagramma, il numero di istanze del ruolo viene limitato a un massimo di cinque, per controllare i costi di esecuzione dell'applicazione.

-   **Regole reattive:** per fare in modo che il numero di istanze del ruolo cambi in risposta a variazioni di domanda impreviste, è possibile usare le **regole reattive**. Nel punto **C** del diagramma, il blocco riduce automaticamente il numero di istanze del ruolo da quattro a tre in seguito a una riduzione del carico di lavoro. Nel punto **D**, il blocco rileva un aumento del carico di lavoro e aumenta automaticamente il numero di istanze del ruolo in esecuzione da tre a quattro.

Le impostazioni di configurazione del blocco sono archiviate nelle due posizioni descritte di seguito.

-   **Archivio regole:** nell'archivio regole viene memorizzata la configurazione aziendale, ovvero un elenco di tutte le regole di scalabilità automatica definite per l'applicazione Azure. Si tratta in genere di un file XML memorizzato in una posizione in cui il blocco applicazione per la scalabilità automatica può leggerlo, ad esempio nell'archiviazione BLOB di Azure o in un file locale.

-   **Archivio informazioni del servizio:** in questo archivio viene memorizzata la configurazione operativa, ovvero il modello del servizio dell'applicazione Azure. Questo modello del servizio include tutte le informazioni sull'applicazione Azure, ad esempio i nomi dei ruoli e i dettagli dell'account di archiviazione, necessarie al blocco per raccogliere i punti dati dall'applicazione Azure di destinazione e per eseguire le operazioni di ridimensionamento.

## <a id="PerfCounter"> </a>Raccogliere dati del contatore delle prestazioni dall'applicazione Azure di destinazione

Nelle regole reattive è possibile usare i dati del contatore delle prestazioni dei ruoli come parte della definizione della regola. Una regola reattiva può ad esempio monitorare l'uso della CPU di un ruolo di Azure per determinare se il blocco deve avviare un'operazione di adattamento. Il blocco legge i dati del contatore delle prestazioni dalla tabella di Diagnostica Azure denominata **WADPerformanceCountersTable** nell'archiviazione di Azure.

Per impostazione predefinita, Azure non scrive i dati del contatore delle prestazioni nella tabella di Diagnostica Azure negli archivi di Azure. Pertanto, è necessario modificare i ruoli da cui raccogliere i dati del contatore delle prestazioni per salvare tali dati. Per informazioni dettagliate su come abilitare i contatori delle prestazioni in un'applicazione, vedere [Uso dei contatori delle prestazioni in Azure][].

## <a id="CreateHost"> </a>Configurare un'applicazione host per il blocco applicazione per la scalabilità automatica

È possibile ospitare il blocco applicazione per la scalabilità automatica in un ruolo di Azure o in un'applicazione locale. Il blocco applicazione per la scalabilità automatica viene in genere ospitato in un'applicazione distinta dall'applicazione di destinazione a cui si desidera applicare la scalabilità automatica. In questa sezione vengono fornite le linee guida per configurare l'applicazione host.

### Scaricare il pacchetto NuGet del blocco applicazione per la scalabilità automatica

Prima di usare il blocco applicazione per la scalabilità automatica in un progetto di Visual Studio, è necessario ottenere i file binari del blocco applicazione per la scalabilità automatica e aggiungere riferimenti a tali file nel progetto. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Web Developer. Il pacchetto NuGet del blocco applicazione per la scalabilità automatica è la soluzione più semplice per ottenere le API del blocco. Per altre informazioni su **NuGet** e su come installare e usare l'estensione **NuGet** di Visual Studio, visitare il sito Web [NuGet][].

Dopo avere installato Gestione pacchetti NuGet, eseguire le operazioni seguenti per installare il pacchetto NuGet per la scalabilità automatica nell'applicazione:

1.  Aprire la finestra della console di **Gestione pacchetti NuGet**. Nel menu **Strumenti** scegliere **Gestione pacchetti libreria**, quindi selezionare **Console di Gestione pacchetti**.

2.  Immettere il comando seguente nella finestra della console di Gestione pacchetti NuGet:

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

L'installazione del pacchetto NuGet aggiorna il progetto con tutti gli assembly e i riferimenti necessari per usare il blocco applicazione per la scalabilità automatica. Il progetto include ora i file di XML Schema per le definizioni delle regole di scalabilità automatica e le informazioni del servizio di scalabilità automatica. Il progetto include inoltre un file readme contenente informazioni importanti sul blocco applicazione per la scalabilità automatica:

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### Impostare .NET Framework 4 come framework di destinazione

Il progetto deve usare .NET Framework 4. Per modificare o verificare il framework di destinazione:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Proprietà**.

2.  Nella scheda **Applicazione** della finestra Proprietà verificare che il framework di destinazione impostato sia **.NET Framework 4**.

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### Aggiungere riferimenti allo spazio dei nomi

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C\# in cui si desidera accedere al blocco applicazione per la scalabilità automatica a livello di codice:

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla

Usare il metodo **IServiceLocator.GetInstance** per creare un'istanza del blocco applicazione per la scalabilità automatica e quindi chiamare il metodo **Autoscaler.Start** per eseguirla****.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>Procedura: Definire il modello del servizio

Il modello del servizio viene in genere archiviato il un file XML contenente una descrizione dell'ambiente Azure con informazioni sulle sottoscrizioni, i servizi ospitati, i ruoli e gli account di archiviazione. Una copia dello schema per questo file XML è disponibile nel file **AutoscalingServiceModel.xsd** del progetto. In Visual Studio, questo schema offre funzionalità Intellisense e di convalida quando si modifica il file XML del modello del servizio.

Creare un nuovo file XML denominato **services.xml** nel progetto.

In Visual Studio verificare che il file del modello del servizio venga copiato nella cartella di output. A tale scopo, effettuare l'operazione seguente:

1.  Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.

2.  Nel riquadro Proprietà impostare il valore di **Copia in directory di output**
    su **Copia sempre**.

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	Nel codice seguente è illustrato un modello di servizio di esempio in un file **services.xml**:

    <?xml version="1.0" encoding="utf-8" ?>
    <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
      <subscriptions>
        <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My">
          <services>
            <service dnsPrefix="[hostedservicednsprefix]" slot="Staging">
              <roles>
                <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/>
              </roles>
            </service>
          </services>
          <storageAccounts>
            <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
            </storageAccount>
          </storageAccounts>
        </subscription>
      </subscriptions>
    </serviceModel>

Sostituire i valori tra parentesi quadre con quelli specifici del proprio ambiente e dell'applicazione di destinazione. Per trovare molti di questi valori, è necessario accedere al [portale di gestione di Azure][].

Accedere al portale di gestione.

-   **[subscriptionname]:** scegliere un nome descrittivo per la sottoscrizione di Azure contenente l'applicazione in cui usare il ridimensionamento automatico.

-   **[subscriptionid]:** ID univoco della sottoscrizione di Azure contenente l'applicazione in cui usare il ridimensionamento automatico.

    1.  Nel portale di gestione di Azure fare clic su **Servizi cloud**.

    2.  Nell'elenco dei servizi cloud fare clic sul servizio che ospita l'applicazione in cui si desidera usare la scalabilità automatica. Nel riquadro Riepilogo rapido a destra verrà visualizzato il valore di **Subscription ID**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]:** prefisso DNS del servizio ospitato in cui si desidera usare il ridimensionamento automatico.

    1.  Nel portale di gestione di Azure fare clic su **Servizi cloud**. 
    2.  Nell'elenco dei servizi cloud individuare il servizio che ospita l'applicazione in cui si desidera usare la scalabilità automatica. Il nome del servizio cloud è il valore di **Prefisso DNS**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]:** nome del ruolo di destinazione delle regole di ridimensionamento automatico.

    1.  Nel portale di gestione di Azure fare clic su **Servizi cloud**.

    2.  Nell'elenco dei servizi cloud fare clic sul servizio che ospita l'applicazione in cui si desidera usare la scalabilità automatica, quindi fare clic su **Istanze**. La colonna **Ruolo* contiene il nome del ruolo di destinazione.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** e **[storageaccountkey]:** nome dell'account di archiviazione di Azure usato per l'applicazione Azure di destinazione.

    1.  Nel portale di gestione di Azure fare clic su **Archiviazione**.

    2.  Nell'elenco di account di archiviazione selezionare l'account in uso. Nella colonna **Nome** verrà visualizzato il **nome**.

    3.  Fare clic sul pulsante **Gestisci chiavi** in fondo alla schermata per ottenere la chiave di accesso primaria.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** **identificazione personale** del certificato di gestione che il blocco userà per proteggere le richieste di ridimensionamento all'applicazione di destinazione.

    1.  Nel portale di gestione di Azure fare clic su **Impostazioni**.

    2.  Nella colonna **Identificazione personale** viene visualizzata l'**identificazione personale**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

Per altre informazioni sul contenuto del file del modello del servizio, vedere l'articolo relativo all'[archiviazione delle informazioni del servizio][].

## <a id="DefineAutoscalingRules"> </a>Procedura: Definire le regole di scalabilità automatica

Le regole di scalabilità automatica che controllano il numero di istanze del ruolo nell'applicazione di destinazione vengono in genere archiviate in un file XML. Una copia dello schema per questo file XML è disponibile nel file **AutoscalingRules.xsd** del progetto. In Visual Studio, questo schema offre funzionalità Intellisense e di convalida quando si modifica il file XML.

Creare un nuovo file XML denominato **rules.xml** nel progetto.

In Visual Studio verificare che il file delle regole venga copiato nella cartella di output. A tale scopo, effettuare l'operazione seguente:

1.  Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.

2.  Nel riquadro Proprietà impostare il valore di **Copia in directory di output** su **Copia sempre**.

Nel codice seguente è illustrato un set di regole di esempio in un file **rules.xml**
:

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

In questo esempio sono incluse tre regole di scalabilità automatica (una **regola di vincolo** e due **regole reattive**) che vengono applicate a un ruolo di destinazione denominato **AutoscalingApplicationRole**, l'alias di un ruolo definito nel **modello del servizio**:

-   La regola di vincolo è sempre attiva e imposta il numero minimo di istanze del ruolo su due e il numero massimo di istanze del ruolo su sei.

-   Entrambe le regole reattive usano un **operando** denominato **WebRoleA_CPU_Avg_5m** che calcola l'uso medio della CPU negli ultimi cinque minuti per un ruolo di Azure denominato **AutoscalingApplicationRole**. Questo ruolo è definito nel **modello del servizio**.

-   La regola reattiva denominata **ScaleUpOnHighUtilization** incrementa il numero di istanze del ruolo di destinazione di un'unità se l'uso medio della CPU negli ultimi cinque minuti è stato maggiore o uguale al 60%.

-   La regola reattiva denominata **ScaleDownOnLowUtilization** riduce il numero di istanze del ruolo di destinazione di un'unità se l'uso medio della CPU negli ultimi cinque minuti è stato minore del 60%.

## <a id="Configure"> </a>Procedura: Configurare il blocco applicazione per la scalabilità automatica

Dopo aver definito il modello del servizio e le regole di scalabilità automatica, è necessario configurare il blocco applicazione per la scalabilità automatica per utilizzarli. Queste informazioni di configurazione operative vengono archiviate nel file di configurazione dell'applicazione.

Per impostazione predefinita, il blocco applicazione per la scalabilità automatica prevede l'archiviazione delle regole e del modello del servizio in BLOB di Azure. In questo esempio, il blocco verrà configurato per caricarli dal file system locale.

### Configurare il blocco applicazione per la scalabilità automatica nell'applicazione host

1.  Fare clic con il pulsante destro del mouse sul file **App.config** in Esplora soluzioni e quindi fare clic su **Edit Configuration File**.

2.  Nel menu **Blocks** fare clic su **Add Autoscaling Settings**:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  Espandere **Autoscaling Settings** e quindi fare clic sui tre puntini (...)     accanto a **Data Points Store Storage Account**, aggiungere il nome in **Account name** e la chiave in **Account key** per l'account di archiviazione di Azure in cui il blocco archivierà i punti dati raccolti (per informazioni su dove trovare questi valori, vedere [Procedura: Definire il modello del servizio][],quindi fare clic su **OK**:  

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  Espandere la sezione **Autoscaling Settings** per visualizzare le sezioni **Rules Store** e **Service Information Store**. Per impostazione predefinita, sono configurate per usare l'archiviazione BLOB di Azure:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  Fare clic sul segno più (+) accanto a **Rules Store**, selezionare **Set Rules Store**, quindi fare clic su **Use Local File Rules Store** e infine su **Yes**.

6.  Nella casella **File Name** digitare **rules.xml**. Questo è il nome del file contenente le regole di scalabilità automatica:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  Fare clic sul segno più (+) accanto a **Service Information Store**,selezionare **Set Service Information Store**, quindi fare clic su **Use Local FileService Information Store** e infine su **Yes**.

8.  Nella casella **File Name** digitare **services.xml**. Questo è il nome del file contenente le regole di scalabilità automatica:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  Nella finestra Enterprise Library Configuration, nel menu **File** fare clic su **Save** per salvare le modifiche apportate alla configurazione. Sempre nella finestra Enterprise Library Configuration, nel menu **File** fare clic su **Exit**.

Per informazioni dettagliate sulle operazioni eseguite dal blocco applicazione per la scalabilità automatica, è necessario acquisire i messaggi scritti nel log dal blocco. Se ad esempio si ospita il blocco in un'applicazione console, è possibile visualizzare i messaggi dei log nella finestra Output di Visual Studio. Nella sezione seguente viene illustrato come configurare questo comportamento.

### Configurare la registrazione nel blocco applicazione per la scalabilità automatica

1.  In Visual Studio fare doppio clic sul file **App.config** in Esplora soluzioni per aprirlo nell'editor. Aggiungere quindi la sezione **system.diagnostics** come mostrato nel seguente esempio:

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  Salvare le modifiche.

È ora possibile eseguire l'applicazione console host del blocco applicazione per la scalabilità automatica e osservare il funzionamento delle regole di scalabilità automatica con l'applicazione Azure di destinazione. Quando si esegue l'applicazione console host, nella finestra Output di Visual Studio dovrebbero essere visualizzati messaggi simili al seguente. Questi messaggi aiutano a comprendere il comportamento del blocco. Indicano ad esempio quali regole vengono applicate al blocco e quali azioni vengono eseguite dal blocco.

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## <a id="NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'uso del blocco applicazione per la scalabilità automatica, visitare i collegamenti seguenti per ulteriori informazioni sull'implementazione di scenari più complessi di scalabilità automatica:

-   [Hosting del blocco applicazione per la scalabilità automatica in un ruolo di lavoro][]
-   [Implementazione del comportamento di limitazione][]
-   [Informazioni sulle classificazioni delle regole e sulla riconciliazione][]
-   [Estensione e modifica del blocco applicazione per la scalabilità automatica][]
-   [Uso dello stabilizzatore dell'ottimizzazione per evitare oscillazioni di alta frequenza e ottimizzare i costi][]
-   [Uso delle notifiche e del ridimensionamento manuale][]
-   [Definizione di gruppi di ridimensionamento][]
-   [Uso dei cmdlet WASABiCmdlets per modificare il blocco tramite Windows PowerShell][]
-   [Guida allo sviluppo per Enterprise Library 5.0 Integration Pack per Azure][]
-   [Riduzione dei costi di hosting di Azure da parte di Sage con la scalabilità automatica][]
-   [Riduzione dei costi di hosting di TechNet e MSDN e impatto sull'ambiente della scalabilità automatica in Azure][]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]:
    http://go.microsoft.com/fwlink/?LinkID=235134
  [Passaggi successivi]: #NextSteps
  [Informazioni sul blocco applicazione per la scalabilità automatica]: #WhatIs
  [Concetti]: #Concepts
  [Raccogliere dati del contatore delle prestazioni dall'applicazione Azure di destinazione]: #PerfCounter
  [Configurare un'applicazione host per il blocco applicazione per la scalabilità automatica]: #CreateHost
  [Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla]: #Instantiate
  [Procedura: Definire il modello del servizio]: #DefineServiceModel
  [Procedura: Definire le regole di scalabilità automatica]: #DefineAutoscalingRules
  [Procedura: Configurare il blocco applicazione per la scalabilità automatica]: #Configure
  [Uso dei contatori di prestazioni in Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [Archiviazione delle informazioni del servizio]: http://msdn.microsoft.com/it-it/library/hh680878(PandP.50).aspx		
  [Hosting del blocco applicazione per la scalabilità automatica in un ruolo di lavoro]: http://msdn.microsoft.com/it-it/library/hh680914(PandP.50).aspx
  [Implementazione del comportamento di limitazione]: http://msdn.microsoft.com/it-it/library/hh680896(PandP.50).aspx
  [Informazioni sulle classificazioni delle regole e sulla riconciliazione]: http://msdn.microsoft.com/it-it/library/hh680923(PandP.50).aspx
  [Estensione e modifica del blocco applicazione per la scalabilità automatica]: http://msdn.microsoft.com/it-it/library/hh680889(PandP.50).aspx
  [Uso dello stabilizzatore dell'ottimizzazione per evitare oscillazioni di alta frequenza e ottimizzare i costi]: http://msdn.microsoft.com/it-it/library/hh680951(PandP.50).aspx
  [Uso delle notifiche e del ridimensionamento manuale]: http://msdn.microsoft.com/it-it/library/hh680885(PandP.50).aspx
  [Definizione di gruppi di ridimensionamento]: http://msdn.microsoft.com/it-it/library/hh680902(PandP.50).aspx
  [Uso dei cmdlet WASABiCmdlets per modificare il blocco tramite Windows PowerShell]: http://msdn.microsoft.com/it-it/library/hh680938(PandP.50).aspx
  [Guida allo sviluppo per Enterprise Library 5.0 Integration Pack per Azure]: http://msdn.microsoft.com/it-it/library/hh680949(PandP.50).aspx
  [Riduzione dei costi di hosting di Azure da parte di Sage con la scalabilità automatica]: http://msdn.microsoft.com/it-it/library/jj838716(PandP.50).aspx
  [Riduzione dei costi di hosting di TechNet e MSDN e impatto sull'ambiente della scalabilità automatica in Azure]: http://msdn.microsoft.com/it-it/library/jj838718(PandP.50).aspx


<!--HONumber=35_1-->
