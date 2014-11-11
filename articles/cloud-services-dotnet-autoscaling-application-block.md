<properties linkid="dev-net-how-to-autoscaling" urlDisplayName="Autoscaling" pageTitle="Use the autoscaling application block (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Learn how to use the Autoscaling Application for Azure. Code samples are written in C# and use the .NET API." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="How to Use the Autoscaling Application Block" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Come usare il blocco applicazione per la scalabilità automatica

In questa guida viene illustrato come eseguire scenari comuni usando il blocco applicazione per la scalabilità automatica di [Microsoft Enterprise Library 5.0 Integration Pack per Azure][Microsoft Enterprise Library 5.0 Integration Pack per Azure]. Negli esempi, scritti in C#, viene utilizzata l'API .NET. Gli scenari presentati includono l'**hosting del blocco**, l'**uso delle regole di vincolo** e l **'uso delle regole reattive**. Per altre informazioni sul blocco applicazione per la scalabilità automatica, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

[Informazioni sul blocco applicazione per la scalabilità automatica][Informazioni sul blocco applicazione per la scalabilità automatica]
 [Concetti][Concetti]
 [Raccolta dei dati del contatore delle prestazioni dall'applicazione Azure di destinazione][Raccolta dei dati del contatore delle prestazioni dall'applicazione Azure di destinazione]
 [Configurazione di un'applicazione host per il blocco applicazione per la scalabilità automatica][Configurazione di un'applicazione host per il blocco applicazione per la scalabilità automatica]
 [Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla][Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla] [Procedura: Definire il modello del servizio][Procedura: Definire il modello del servizio]
 [Procedura: Definire le regole di scalabilità automatica][Procedura: Definire le regole di scalabilità automatica]
 [Procedura: Configurare il blocco applicazione per la scalabilità automatica][Procedura: Configurare il blocco applicazione per la scalabilità automatica]
 [Passaggi successivi][Passaggi successivi]

## <span id="WhatIs"></span> </a>Informazioni sul blocco applicazione per la scalabilità automatica

Il blocco applicazione per la scalabilità automatica consente di adattare automaticamente un'applicazione Microsoft base a regole definite in modo specifico per l'applicazione. È possibile usare queste regole per fare in modo che l'applicazione di Azure mantenga la stessa velocità effettiva con il variare del carico di lavoro, controllando al tempo stesso i costi associati all'hosting dell'applicazione in Azure. Oltre ad adattare l'applicazione aumentando o diminuendo il numero di istanze del ruolo nell'applicazione, il blocco consente anche di usare altre operazioni di scalabilità come la limitazione di determinate funzionalità dell'applicazione o l'uso di azioni personalizzate.

È possibile scegliere di ospitare il blocco in un ruolo di Azure o in un'applicazione locale.

Il blocco applicazione per la scalabilità automatica fa parte di [Microsoft Enterprise Library 5.0 Integration Pack per Azure][Microsoft Enterprise Library 5.0 Integration Pack per Azure].

## <span id="Concepts"></span> </a>Concetti

Nel diagramma seguente la linea verde rappresenta il numero di istanze in esecuzione del ruolo di Azure in due giorni. Il numero di istanze cambia automaticamente nel tempo in risposta a un insieme di regole di scalabilità automatica.

![diagramma di esempio di scalabilità automatica][diagramma di esempio di scalabilità automatica]

Il blocco usa due tipi di regole per definire il comportamento di scalabilità automatica dell'applicazione.

-   **Regole di vincolo:** per configurare il limite massimo e minimo per il numero di istanze, ad esempio tra le quattro e le sei istanze tra le 8.00 e le 10.00 ogni mattina, è possibile usare una **regola di vincolo**. Nel diagramma le linee rossa e blu rappresentano le regole di vincolo. Nel punto **A** del diagramma, ad esempio, il numero minimo delle istanze del ruolo aumenta da due a quattro, per adattarsi in anticipo all'aumento del carico di lavoro dell'applicazione a quest'ora. Nel punto **B** del diagramma, il numero di istanze del ruolo viene limitato a un massimo di cinque, per controllare i costi di esecuzione dell'applicazione.

-   **Regole reattive:** per fare in modo che il numero di istanze del ruolo cambi in risposta a variazioni di domanda impreviste, è possibile usare le **regole reattive**. Nel punto **C** del diagramma, il blocco riduce automaticamente il numero di istanze del ruolo da quattro a tre in seguito a una riduzione del carico di lavoro. Nel punto **D**, il blocco rileva un aumento del carico di lavoro e aumenta automaticamente il numero di istanze del ruolo in esecuzione da tre a quattro.

Le impostazioni di configurazione del blocco sono archiviate nelle due posizioni descritte di seguito.

-   **Archivio regole:** nell'archivio regole viene memorizzata la configurazione aziendale, ovvero un elenco di tutte le regole di scalabilità automatica definite per l'applicazione Azure. Si tratta in genere di un file XML memorizzato in una posizione in cui il blocco applicazione per la scalabilità automatica può leggerlo, ad esempio nell'archivio BLOB di Azure o in un file locale.

-   **Archivio informazioni del servizio:** in questo archivio viene memorizzata la configurazione operativa, ovvero il modello del servizio dell'applicazione Azure. Questo modello del servizio include tutte le informazioni sull'applicazione Azure, ad esempio i nomi dei ruoli e i dettagli dell'account di archiviazione, necessarie al blocco per raccogliere i punti dati dall'applicazione Azure di destinazione e per eseguire le operazioni di adattamento.

## <span id="PerfCounter"></span> </a>Raccolta dei dati del contatore delle prestazioni dall'applicazione Azure di destinazione

Nelle regole reattive è possibile usare i dati del contatore delle prestazioni dei ruoli come parte della definizione della regola. Una regola reattiva può ad esempio monitorare l'uso della CPU di un ruolo di Azure per determinare se il blocco deve avviare un'operazione di adattamento. Il blocco legge i dati del contatore delle prestazioni dalla tabella di Diagnostica Azure denominata **WADPerformanceCountersTable** negli archivi di Azure.

Per impostazione predefinita, Azure non scrive i dati del contatore delle prestazioni nella tabella di Diagnostica Azure negli archivi di Azure. Pertanto, è necessario modificare i ruoli da cui raccogliere i dati del contatore delle prestazioni per salvare tali dati. Per informazioni dettagliate su come abilitare i contatori delle prestazioni in un'applicazione, vedere [Utilizzo dei contatori delle prestazioni in Azure][Utilizzo dei contatori delle prestazioni in Azure].

## <span id="CreateHost"></span> </a>Configurazione di un'applicazione host per il blocco applicazione per la scalabilità automatica

È possibile ospitare il blocco applicazione per la scalabilità automatica in un ruolo di Azure o in un'applicazione locale. Il blocco applicazione per la scalabilità automatica viene in genere ospitato in un'applicazione distinta dall'applicazione di destinazione a cui si desidera applicare la scalabilità automatica. In questa sezione vengono fornite le linee guida per configurare l'applicazione host.

### Scaricare il pacchetto NuGet del blocco applicazione per la scalabilità automatica

Prima di usare il blocco applicazione per la scalabilità automatica in un progetto di Visual Studio, è necessario ottenere i file binari del blocco applicazione per la scalabilità automatica e aggiungere riferimenti a tali file nel progetto. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Web Developer. Il pacchetto NuGet del blocco applicazione per la scalabilità automatica è la soluzione più semplice per ottenere le API del blocco. Per altre informazioni su **NuGet** e su come installare e usare l'estensione **NuGet** di Visual Studio, visitare il sito Web [NuGet][NuGet].

Dopo avere installato Gestione pacchetti NuGet, eseguire le operazioni seguenti per installare il pacchetto NuGet per la scalabilità automatica nell'applicazione:

1.  Aprire la finestra **NuGet Package Manager Console**. Nel menu **Tools** scegliere **Library Package Manager**, quindi selezionare **Package Manager Console**.

2.  Immettere il comando seguente nella finestra della console di Gestione pacchetti NuGet:

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

L'installazione del pacchetto NuGet aggiorna il progetto con tutti gli assembly e i riferimenti necessari per usare il blocco applicazione per la scalabilità automatica. Il progetto include ora i file di XML Schema per le definizioni delle regole di scalabilità automatica e le informazioni del servizio di scalabilità automatica. Il progetto include anche un file readme contenente informazioni importanti sul blocco applicazione per la scalabilità automatica:

![file configurati dal pacchetto NuGet per la scalabilità automatica][file configurati dal pacchetto NuGet per la scalabilità automatica]

### Impostare .NET Framework 4 come framework di destinazione

Il progetto deve usare .NET Framework 4 come destinazione. Per modificare o controllare il framework di destinazione:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Proprietà**.

2.  Nella scheda **Applicazione** della finestra Proprietà verificare che il framework di destinazione impostato sia **.NET Framework 4**.

    ![immagine][immagine]

### Aggiungere riferimenti allo spazio dei nomi

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si desidera accedere al blocco applicazione per la scalabilità automatica a livello di codice:

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <span id="Instantiate"></span> </a>Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla

Usare il metodo **IServiceLocator.GetInstanc** e per creare un'istanza del blocco applicazione per la scalabilità automatica e quindi chiamare il metodo **Autoscaler.Start** per la **scalabilità automatica**.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <span id="DefineServiceModel"></span> </a>Procedura: Definire il modello del servizio

Il modello del servizio viene in genere archiviato il un file XML contenente una descrizione dell'ambiente Microsoft Azure con informazioni sulle sottoscrizioni, i servizi ospitati, i ruoli e gli account di archiviazione. Una copia dello schema per questo file XML è disponibile nel file **AutoscalingServiceModel.xsd** del progetto. In Visual Studio, questo schema offre funzionalità Intellisense e di convalida quando si modifica il file XML del modello del servizio.

Creare un nuovo file XML denominato **services.xml** nel progetto.

In Visual Studio verificare che il file del modello del servizio venga copiato nella cartella di output. A tale scopo, effettuare l'operazione seguente:

1.  Fare clic con il pulsante destro del mouse sul file e selezionare **Proprietà**.

2.  Nel riquadro Proprietà impostare il valore di **Copia in directory di output** su **Copia sempre**.

    ![Impostare il valore di Copia in directory di output][Impostare il valore di Copia in directory di output]

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


Sostituire i valori tra parentesi quadre con quelli specifici del proprio ambiente e dell'applicazione di destinazione. Per trovare molti di questi valori, è necessario accedere al [portale di gestione di Azure][portale di gestione di Azure].

Accedere al portale di gestione.

-   **[nomesottoscrizione]:** scegliere un nome descrittivo per la sottoscrizione di Azure contenente l'applicazione in cui usare la scalabilità automatica.

-   **[idsottoscrizione]:** ID univoco della sottoscrizione di Azure contenente l'applicazione in cui usare
    la scalabilità automatica.

    1.  Nel portale di gestione di Azure fare clic su **Servizi cloud**.

    2.  Nell'elenco dei servizi cloud fare clic sul servizio che ospita l'applicazione in cui si desidera usare la scalabilità automatica. Nel riquadro Riepilogo rapido a destra verrà visualizzato il valore di **Subscription ID**.

        ![immagine][1]

    -   **[prefissodnsservizioospitato]:** prefisso DNS del servizio ospitato in cui si desidera usare la scalabilità automatica.

    1.  Nel portale di gestione di Azure fare clic su **Servizi cloud**.

    2.  Nell'elenco dei servizi cloud individuare il servizio che ospita l'applicazione in cui si desidera usare la scalabilità automatica. Il nome del servizio cloud è il valore di **DNS Prefix**.

        ![immagine][2]

    -   **[nomeruolodestinazione]:** nome del ruolo di destinazione delle regole di scalabilità automatica.

    1.  Nel portale di gestione di Azure fare clic su **Servizi cloud**.

    2.  Nell'elenco dei servizi cloud fare clic sul servizio che ospita l'applicazione in cui si desidera usare la scalabilità automatica, quindi fare clic su **Istanze**. La colonna \*\*Ruolo\* contiene il nome del ruolo di destinazione.

        ![immagine][3]

    -   **[nomeaccountarchiviazione]** e **[chiaveaccountarchiviazione]:** nome dell'account di archiviazione di Azure utilizzato per l'applicazione Azure di destinazione.

    1.  Nel portale di gestione di Azure fare clic su **Storage**.

    2.  Nell'elenco di account di archiviazione selezionare l'account in uso. Nella colonna **Name** verrà visualizzato il nome.

    3.  Fare clic sul pulsante **Gestisci chiavi** in fondo alla schermata per ottenere la chiave di accesso primaria.

        ![immagine][4]

    -   **[identificazionepersonalecertificatogestione]:** **identificazione personale** del certificato di gestione che il blocco utilizzerà per proteggere le richieste di adattamento all'applicazione di destinazione.

    1.  Nel portale di gestione di Azure fare clic su **Settings**.

    2.  Nella colonna **Thumbprint** viene visualizzata l'identificazione personale **.**

        ![immagine][5]

Per altre informazioni sul contenuto del file del modello del servizio, vedere [Archiviazione delle informazioni del servizio][Archiviazione delle informazioni del servizio].

## <span id="DefineAutoscalingRules"></span> </a>Procedura: Definire le regole di scalabilità automatica

Le regole di scalabilità automatica che controllano il numero di istanze del ruolo nell'applicazione di destinazione vengono in genere archiviate in un file XML. Una copia dello schema per questo file XML è disponibile nel file toscalingRules.xs**del progetto.**  In Visual Studio, questo schema offre funzionalità Intellisense e di convalida quando si modifica il file XML.

Creare un nuovo file XML denominato **rules.xml** nel progetto.

In Visual Studio verificare che il file delle regole venga copiato nella cartella di output. A tale scopo, effettuare l'operazione seguente:

1.  Fare clic con il pulsante destro del mouse sul file e selezionare **Proprietà**.

2.  Nel riquadro Proprietà impostare il valore di **Copia in directory di output** su **Copia sempre**.

Nel codice seguente è illustrato un set di regole di esempio in un file **rules.xml**:

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

In questo esempio sono incluse tre regole di scalabilità automatica (una **regola di vincolo** e due **regole reattive**) che vengono applicate a un ruolo di destinazione denominato **AutoscalingApplicationRole, l'alias di un ruolo** definito nel modello del servizio**:**

-   La regola di vincolo è sempre attiva e imposta il numero minimo di istanze del ruolo su due e il numero massimo di istanze del ruolo su sei.

-   Entrambe le regole reattive utilizzano un **operando** denominato **WebRoleA\_CPU\_Avg\_5m** che calcola l'uso medio della CPU negli ultimi cinque minuti per un ruolo di Azure denominato AutoscalingApplicationRole. Questo ruolo è definito nel **modello del servizio**.

-   La regola reattiva denominata **ScaleUpOnHighUtilization** incrementa il numero di istanze del ruolo di destinazione di un'unità se l'uso medio della CPU negli ultimi cinque minuti è stato maggiore o uguale al 60%.

-   La regola reattiva denominata **ScaleDownOnLowUtilization** riduce il numero di istanze del ruolo di destinazione di un'unità se l'uso medio della CPU negli ultimi cinque minuti è stato minore del 60%.

## <span id="Configure"></span> </a>Procedura: Configurare il blocco applicazione per la scalabilità automatica

Dopo aver definito il modello del servizio e le regole di scalabilità automatica, è necessario configurare il blocco applicazione per la scalabilità automatica per utilizzarli. Queste informazioni di configurazione operative vengono archiviate nel file di configurazione dell'applicazione.

Per impostazione predefinita, il blocco applicazione per la scalabilità automatica prevede l'archiviazione delle regole e del modello del servizio in BLOB di Azure. In questo esempio, il blocco verrà configurato per caricarli dal file system locale.

### Configurare il blocco applicazione per la scalabilità automatica nell'applicazione host

1.  Fare clic con il pulsante destro del mouse sul file **App.config** in Esplora soluzioni e quindi fare clic su **Edit Configuration File**.

2.  Nel menu **Blocks** fare clic su **Add Autoscaling Settings**:
    ![image][image]

3.  Espandere **Autoscaling Settings** e quindi fare clic sui tre puntini (...) accanto a **Data Points Store Storage Account**, aggiungere il nome in **Account name** e la chiave in **Account key** per l'account di archiviazione di Azure in cui il blocco archivierà i punti dati raccolti (per informazioni su dove trovare questi valori, vedere [Procedura: Definire il modello del servizio][Procedura: Definire il modello del servizio], quindi fare clic su **OK**:

    ![immagine][6]

4.  Espandere la sezione **Autoscaling Settings** per visualizzare le sezioni **Rules Store** e **Service Information Store**. Per impostazione predefinita, sono configurate per usare l'archivio BLOB di Azure:
    ![image][7]

5.  Fare clic sul segno più (+) accanto a **Rules Store**, selezionare **Set Rules Store**, quindi fare clic su **Use Local File Rules Store** e infine su **Yes**.

6.  Nella casella **File Name** digitare **rules.xml**. Questo è il nome del file contenente le regole di scalabilità automatica:
    ![image][8]

7.  Fare clic sul segno più (+) accanto a **Service Information Store**, selezionare **Set Service Information Store**, quindi fare clic su **Use Local File Service Information Store** e infine su **Yes**.

8.  Nella casella **File Name** digitare **services.xml**. Questo è il nome del file contenente le regole di scalabilità automatica:
    ![image][9]

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

È ora possibile eseguire l'applicazione console host del blocco applicazione per la scalabilità automatica e osservare il funzionamento delle regole di scalabilità automatica con l'applicazione Azure di destinazione. Quando si esegue l'applicazione console host, nella finestra Output di Vsual Studio dovrebbero essere visualizzati messaggi simili al seguente. Questi messaggi aiutano a comprendere il comportamento del blocco. Indicano ad esempio quali regole vengono applicate al blocco e quali azioni vengono eseguite dal blocco.

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

## <span id="NextSteps"></span> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'utilizzo del blocco applicazione per la scalabilità automatica, visitare i collegamenti seguenti per altre informazioni sull'implementazione di scenari più complessi di scalabilità automatica:

-   [Hosting del blocco applicazione per la scalabilità automatica in un ruolo di lavoro][Hosting del blocco applicazione per la scalabilità automatica in un ruolo di lavoro]
-   [Implementazione del comportamento di limitazione][Implementazione del comportamento di limitazione]
-   [Informazioni sulle classificazioni delle regole e sulla riconciliazione][Informazioni sulle classificazioni delle regole e sulla riconciliazione]
-   [Estensione e modifica del blocco applicazione per la scalabilità automatica][Estensione e modifica del blocco applicazione per la scalabilità automatica]
-   [Utilizzo dello stabilizzatore dell'ottimizzazione per evitare oscillazioni di alta frequenza e ottimizzare i costi][Utilizzo dello stabilizzatore dell'ottimizzazione per evitare oscillazioni di alta frequenza e ottimizzare i costi]
-   [Utilizzo delle notifiche e della scalabilità manuale][Utilizzo delle notifiche e della scalabilità manuale]
-   [Definizione di gruppi di scala][Definizione di gruppi di scala]
-   [Utilizzo dei cmdlet WASABiCmdlets per modificare il blocco tramite Windows PowerShell][Utilizzo dei cmdlet WASABiCmdlets per modificare il blocco tramite Windows PowerShell]
-   [Guida allo sviluppo per Enterprise Library 5.0 Integration Pack per Azure][Guida allo sviluppo per Enterprise Library 5.0 Integration Pack per Azure]
-   [Riduzione dei costi di hosting di Azure da parte di Sage con la scalabilità automatica][Riduzione dei costi di hosting di Azure da parte di Sage con la scalabilità automatica]
-   [Riduzione dei costi di hosting di TechNet e MSDN e impatto sull'ambiente della scalabilità automatica in Azure][Riduzione dei costi di hosting di TechNet e MSDN e impatto sull'ambiente della scalabilità automatica in Azure]

 
  [Passaggi successivi]: #NextSteps
  [Informazioni sul blocco applicazione per la scalabilità automatica]: #WhatIs
  [Concetti]: #Concepts
  [Raccolta dei dati del contatore delle prestazioni dall'applicazione Azure di destinazione]: #PerfCounter
  [Configurazione di un'applicazione host per il blocco applicazione per la scalabilità automatica]: #CreateHost
  [Procedura: Creare un'istanza del blocco applicazione per la scalabilità automatica ed eseguirla]: #Instantiate
  [Procedura: Definire il modello del servizio]: #DefineServiceModel
  [Procedura: Definire le regole di scalabilità automatica]: #DefineAutoscalingRules
  [Procedura: Configurare il blocco applicazione per la scalabilità automatica]: #Configure
  [diagramma di esempio di scalabilità automatica]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png
  [Utilizzo dei contatori delle prestazioni in Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [file configurati dal pacchetto NuGet per la scalabilità automatica]: ./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png
  [immagine]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png
  [Impostare il valore di Copia in directory di output]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png
  [2]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png
  [3]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png
  [4]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png
  [5]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png
  [Archiviazione delle informazioni del servizio]: http://msdn.microsoft.com/it-it/library/hh680878(PandP.50).aspx
  [image]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png
  [6]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png
  [7]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png
  [8]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png
  [9]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png
  [Hosting del blocco applicazione per la scalabilità automatica in un ruolo di lavoro]: http://msdn.microsoft.com/it-it/library/hh680914(PandP.50).aspx
  [Implementazione del comportamento di limitazione]: http://msdn.microsoft.com/it-it/library/hh680896(PandP.50).aspx
  [Informazioni sulle classificazioni delle regole e sulla riconciliazione]: http://msdn.microsoft.com/it-it/library/hh680923(PandP.50).aspx
  [Estensione e modifica del blocco applicazione per la scalabilità automatica]: http://msdn.microsoft.com/it-it/library/hh680889(PandP.50).aspx
  [Utilizzo dello stabilizzatore dell'ottimizzazione per evitare oscillazioni di alta frequenza e ottimizzare i costi]: http://msdn.microsoft.com/it-it/library/hh680951(PandP.50).aspx
  [Utilizzo delle notifiche e della scalabilità manuale]: http://msdn.microsoft.com/it-it/library/hh680885(PandP.50).aspx
  [Definizione di gruppi di scala]: http://msdn.microsoft.com/it-it/library/hh680902(PandP.50).aspx
  [Utilizzo dei cmdlet WASABiCmdlets per modificare il blocco tramite Windows PowerShell]: http://msdn.microsoft.com/it-it/library/hh680938(PandP.50).aspx
  [Guida allo sviluppo per Enterprise Library 5.0 Integration Pack per Azure]: http://msdn.microsoft.com/it-it/library/hh680949(PandP.50).aspx
  [Riduzione dei costi di hosting di Azure da parte di Sage con la scalabilità automatica]: http://msdn.microsoft.com/it-it/library/jj838716(PandP.50).aspx
  [Riduzione dei costi di hosting di TechNet e MSDN e impatto sull'ambiente della scalabilità automatica in Azure]: http://msdn.microsoft.com/it-it/library/jj838718(PandP.50).aspx
  [Microsoft Enterprise Library 5.0 Integration Pack per Azure]:
    http://go.microsoft.com/fwlink/?LinkID=235134
