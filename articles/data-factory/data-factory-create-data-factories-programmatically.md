---
title: Creare pipeline di dati usando Azure .NET SDK | Documentazione Microsoft
description: Informazioni su come creare, monitorare e gestire a livello di codice le istanze di Data factory di Azure usando Data Factory SDK.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 280737097038ff328d487477279ce4f204b0ac0c
ms.lasthandoff: 04/12/2017


---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Creazione, monitoraggio e gestione delle istanze di Azure Data Factory mediante Azure Data Factory .NET SDK
## <a name="overview"></a>Panoramica
È possibile creare, monitorare e gestire le istanze di Data factory di Azure a livello di codice mediante Data Factory .NET SDK. Questo articolo contiene una procedura dettagliata per la creazione di un'applicazione console .NET di esempio che crea e monitora un'istanza di Data factory. Per informazioni dettagliate su Data Factory .NET SDK, vedere la sezione di [riferimento sulla libreria di classi di Data factory](https://msdn.microsoft.com/library/mt415893.aspx) .

## <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2012 o 2013 o 2015
* Scaricare e installare [Azure .NET SDK](http://azure.microsoft.com/downloads/).
* Assegnare un'applicazione client nativa ad Azure Active Directory. Per la procedura di aggiunta dell'applicazione, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/active-directory-integrating-applications.md) . Annotare **ID CLIENT** e **URI di reindirizzamento** della pagina **CONFIGURAZIONE**. Per la procedura dettagliata, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando l'API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md). 
* Ottenere l'**ID sottoscrizione** e l'**ID tenant** di Azure. Per le istruzioni, vedere [Ottenere l'ID sottoscrizione e l'ID tenant di Azure](#get-azure-subscription-and-tenant-ids) .
* Scaricare e installare i pacchetti NuGet per Data factory di Azure. Le istruzioni sono disponibili nella procedura dettagliata.

## <a name="walkthrough"></a>Procedura dettagliata
1. Usando Visual Studio 2012 o 2013, creare un'applicazione console .NET in C#.
   1. Avviare **Visual Studio 2012/2013/2015**.
   2. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**.
   3. Espandere **Modelli** e quindi selezionare **Visual C#**. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET.
   4. Selezionare **Applicazione console** dall'elenco dei tipi di progetto a destra.
   5. Immettere **DataFactoryAPITestApp** per **Nome**.
   6. Selezionare **C:\ADFGetStarted** per **Percorso**.
   7. Fare clic su **OK** per creare il progetto.
2. Fare clic su **Strumenti**, scegliere **Gestione pacchetti NuGet** e quindi fare clic su **Console di Gestione pacchetti**.
3. In **Console di Gestione pacchetti**eseguire i comandi seguenti uno alla volta.

    ```
    Install-Package Microsoft.Azure.Management.DataFactories
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    ```
4. Aggiungere la sezione **appSettings** seguente al file **App.config**. Questi valori di configurazione vengono usati dal metodo **GetAuthorizationHeader** .

   > [!IMPORTANT]
   > Sostituire i valori per **AdfClientId**, **RedirectUri**, **SubscriptionId**, and **ActiveDirectoryTenantId** con valori personalizzati.

    ```XML
    <appSettings>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    
        <!-- Replace the following values with your own -->
        <add key="AdfClientId" value="Your AAD application ID" />
        <add key="RedirectUri" value="Your AAD application's redirect URI" />
        <add key="SubscriptionId" value="your subscription ID" />
        <add key="ActiveDirectoryTenantId" value="your tenant ID" />
    </appSettings>
    ```
5. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. Aggiungere al metodo **Main** il codice seguente che crea un'istanza della classe **DataPipelineManagementClient**. Si userà questo oggetto per creare una data factory, un servizio collegato, i set di dati di input e output e una pipeline. Questo oggetto viene usato anche per monitorare le sezioni di un set di dati in fase di esecuzione.

    ```csharp
    // create data factory management client
    string resourceGroupName = "resourcegroupname";
    string dataFactoryName = "APITutorialFactorySP";
    
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);
    
    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
    
    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!NOTE]
   > Sostituire **resourcegroupname** con il nome del gruppo di risorse di Azure. Per creare un gruppo di risorse, usare il cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) .
7. Aggiungere al metodo **Main** il codice seguente che crea una **data factory**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. Aggiungere il codice seguente che crea un **servizio collegato** al metodo **Main**.

   > [!NOTE]
   > Usare il **nome account** e la **chiave dell'account** dell'account di archiviazione di Azure per **ConnectionString**.

    ```csharp
    // create a linked service
    Console.WriteLine("Creating a linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "LinkedService-AzureStorage",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                )
            }
        }
    );
    ```
9. Aggiungere al metodo **Main** il codice seguente che crea **set di dati di input e output**.

    **FolderPath** per il BLOB di input è impostato su **adftutorial/**, dove **adftutorial** è il nome del contenitore nell'archivio BLOB. Se questo contenitore non esiste nell'archivio BLOB di Azure, creare un contenitore con il nome **adftutorial** e caricare un file di testo nel contenitore.

    FolderPath per il BLOB di output è impostato su **adftutorial/apifactoryoutput/{Slice}**, dove il valore **Slice** è calcolato in modo dinamico in base al valore **SliceStart** (data-ora di inizio di ogni sezione).

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "LinkedService-AzureStorage",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "LinkedService-AzureStorage",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. Aggiungere al metodo **Main** il codice seguente che **crea e attiva una pipeline**. Questa pipeline contiene una proprietà **CopyActivity** che accetta **BlobSource** come origine e **BlobSink** come sink.

    L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
11. Aggiungere alla classe **Program** il metodo helper seguente usato per il metodo **Main**. Questo metodo visualizza una finestra di dialogo che consente di specificare il **nome utente** e la **password** usati per accedere al portale di Azure.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientId: ConfigurationManager.AppSettings["AdfClientId"],
            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
            promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```
12. Aggiungere al metodo **Main** il codice seguente per ottenere lo stato di una sezione di dati del set di dati di output. In questo esempio è prevista solo una sezione.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(facoltativo)** Aggiungere al metodo **Main** il codice seguente per ottenere i dettagli dell'esecuzione di una sezione di dati.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. In Esplora soluzioni espandere il progetto **DataFactoryAPITestApp**, fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Aggiungi riferimento**. Selezionare la casella di controllo per l'assembly `System.Configuration` e fare clic su **OK**.
15. Compilare l'applicazione console. Scegliere **Compila** dal menu e fare clic su **Compila soluzione**.
16. Verificare che esista almeno un file nel contenitore adftutorial nell'archiviazione BLOB di Azure. In caso contrario, creare il file Emp.txt nel Blocco note con il contenuto seguente e caricarlo nel contenitore adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
17. Eseguire l'esempio scegliendo **Debug** -> **Avvia debug** dal menu. Quando viene visualizzato un messaggio simile ad **Acquisizione dettagli dell'esecuzione di una sezione di dati**, attendere qualche minuto e premere **INVIO**.
18. Usare il portale di Azure per verificare che la data factory **APITutorialFactory** venga creata con gli elementi seguenti:
    * Servizio collegato: **LinkedService_AzureStorage**.
    * Set di dati: **DatasetBlobSource** e **DatasetBlobDestination**.
    * Pipeline: **PipelineBlobSample**
19. Verificare che venga creato un file di output nella cartella **apifactoryoutput** nel contenitore **adftutorial**.

## <a name="log-in-without-popup-dialog-box"></a>Accedere senza la finestra di dialogo popup
Nel codice di esempio della procedura dettagliata viene avviata una finestra di dialogo per immettere le credenziali di Azure. Se è necessario l'accesso a livello di codice senza usare una finestra di dialogo, vedere [Autenticazione di un'entità servizio con Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

> [!IMPORTANT]
> Aggiungere un'applicazione Web ad Azure Active Directory e annotare l'ID client e il segreto client dell'applicazione.
>
>

### <a name="example"></a>Esempio
Creare il metodo GetAuthorizationHeaderNoPopup.

```csharp
public static async Task<string> GetAuthorizationHeaderNoPopup()
{
    var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
    var context = new AuthenticationContext(authority.AbsoluteUri);
    var credential = new ClientCredential(
        ConfigurationManager.AppSettings["AdfClientId"],
    ConfigurationManager.AppSettings["AdfClientSecret"]);
    
    AuthenticationResult result = await context.AcquireTokenAsync(
        ConfigurationManager.AppSettings["WindowsManagementUri"],
    credential);

    if (result != null)
        return result.AccessToken;

    throw new InvalidOperationException("Failed to acquire token");
}
```

Sostituire la chiamata **GetAuthorizationHeader** con una chiamata a **GetAuthorizationHeaderNoPopup** nella funzione **Main**:

```csharp
TokenCloudCredentials aadTokenCredentials =
    new TokenCloudCredentials(
    ConfigurationManager.AppSettings["SubscriptionId"],
    GetAuthorizationHeaderNoPopup().Result);
```

Ecco come è possibile creare l'applicazione Active Directory, l'entità servizio, e quindi assegnarla al ruolo Collaboratore di Data Factory:

1. Creare l'applicazione AD.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
    ```
2. Creare l'entità servizio di AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
3. Aggiungere l'entità servizio al ruolo Collaboratore di Data Factory.

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
4. Ottenere l'ID applicazione.

    ```PowerShell
    $azureAdApplication
    ```

Annotare l'ID dell'applicazione e la password (segreto client) e usarli nella procedura dettagliata.

## <a name="get-azure-subscription-and-tenant-ids"></a>Ottenere l'ID sottoscrizione e l'ID tenant di Azure
Per installare la versione più recente di Azure PowerShell nel computer, seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) .

1. Aprire Azure PowerShell ed eseguire il comando seguente
2. Eseguire il comando seguente e immettere il nome utente e la password usati per accedere al portale di Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```

    Se si dispone di una sola sottoscrizione di Azure associata a questo account, non è necessario eseguire i due passaggi seguenti.
3. Eseguire il comando seguente per visualizzare tutte le sottoscrizioni per l'account.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **NameOfAzureSubscription** con il nome della sottoscrizione di Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext
    ```PowerShell

Note down the **SubscriptionId** and **TenantId** values.

