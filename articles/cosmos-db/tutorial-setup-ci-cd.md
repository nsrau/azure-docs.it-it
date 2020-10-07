---
title: Configurare una pipeline di integrazione continua/distribuzione continua con l'attività di compilazione dell'emulatore di Azure Cosmos DB
description: Esercitazione su come configurare il flusso di lavoro di compilazione e rilascio in Azure DevOps usando l'attività di compilazione dell'emulatore di Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34508abdfa509dc2f8238e8e3b0dbac21c26ff7d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801920"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Configurare una pipeline CI/CD con l'attività di compilazione dell'emulatore di Azure Cosmos DB in Azure DevOps

L'emulatore di Azure Cosmos DB fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. L'emulatore consente di sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure né sostenere costi. 

L'attività di compilazione dell'emulatore di Azure Cosmos DB per Azure DevOps consente di eseguire la stessa operazione in un ambiente di integrazione continua. Con l'attività di compilazione, è possibile eseguire i test nell'emulatore come parte della compilazione e rilasciare dei flussi di lavoro. L'attività avvia un contenitore Docker con l'emulatore già in esecuzione e fornisce un endpoint che può essere usato dalla definizione di compilazione restante. È possibile creare e avviare le istanze dell'emulatore necessarie, ognuna in esecuzione in un contenitore separato. 

Questo articolo illustra come configurare una pipeline di integrazione continua in Azure DevOps per un'applicazione ASP.NET che usa l'attività di compilazione dell'emulatore di Cosmos DB per eseguire i test. È possibile usare un approccio simile per configurare una pipeline di integrazione continua per un'applicazione Node.js o Python. 

## <a name="install-the-emulator-build-task"></a>Installare l'attività di compilazione dell'emulatore

Per usare l'attività di compilazione, è innanzitutto necessario installarla nell'organizzazione di Azure DevOps. Trovare l'estensione **Emulatore di Azure Cosmos DB** nel [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) e fare clic su **Scarica gratuitamente**.

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

Successivamente, scegliere l'organizzazione in cui installare l'estensione. 

> [!NOTE]
> Per installare un'estensione in un'organizzazione di Azure DevOps, è necessario essere proprietario dell'account o amministratore della raccolta di progetti. Se non si dispone di autorizzazioni, ma si è membri dell'account, in alternativa è possibile richiedere delle estensioni. [Altre informazioni.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts&preserve-view=true)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

## <a name="create-a-build-definition"></a>Creare una definizione di compilazione

Ora che l'estensione è installata, accedere all'organizzazione di Azure DevOps e trovare il progetto nel dashboard dei progetti. È possibile aggiungere una [pipeline di compilazione](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&preserve-view=true&tabs=new-nav) al progetto o modificarne una esistente. Se si ha già una pipeline di compilazione, è possibile passare direttamente alla sezione [Aggiungere l'attività di compilazione dell'emulatore a una definizione di compilazione](#addEmulatorBuildTaskToBuildDefinition).

1. Per creare una nuova definizione di compilazione, passare alla scheda **Compilazioni** in Azure DevOps. Selezionare **+ Nuovo** \> **Nuova pipeline di compilazione**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

2. Selezionare le opzioni desiderate in **Selezionare un'origine**, **Progetto team**, **Repository** e **Ramo predefinito per le compilazioni manuale e pianificata**. Dopo aver selezionato le opzioni necessarie, scegliere **Continua**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

3. Selezionare infine il modello desiderato per la pipeline di compilazione. In questa esercitazione viene selezionato il modello **ASP.NET**. A questo punto la pipeline di compilazione può essere configurata per l'uso dell'attività di compilazione dell'emulatore di Azure Cosmos DB. 

> [!NOTE]
> Il pool di agenti da selezionare per questa integrazione continua deve avere Docker per Windows installato, a meno che l'installazione non venga eseguita manualmente in un'attività precedente come parte dell'integrazione continua. Per un elenco di pool di agenti tra cui scegliere, vedere l'articolo [Agenti ospitati da Microsoft](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&preserve-view=true&tabs=yaml). È consigliabile iniziare con `Hosted VS2017`.

L'emulatore di Azure Cosmos DB non supporta attualmente il pool di agenti VS2019 ospitati. Tuttavia, l'emulatore viene già installato con VS2019 e per usarlo è possibile avviarlo con i cmdlet di PowerShell seguenti. Se si verificano problemi durante l'uso di VS2019, rivolgersi al team di [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) per assistenza:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Aggiungere l'attività a una pipeline di compilazione

1. Prima di aggiungere un'attività alla pipeline di compilazione, occorre aggiungere un processo agente. Passare alla pipeline di compilazione, selezionare **...** e scegliere **Aggiungi un processo agente**.

1. Selezionare quindi il simbolo **+** accanto al processo agente per aggiungere l'attività di compilazione dell'emulatore. Cercare **cosmos** nella casella di ricerca, selezionare **Emulatore di Azure Cosmos DB** e aggiungerlo al processo agente. L'attività di compilazione avvia un contenitore con un'istanza dall'emulatore di Cosmos DB già in esecuzione. L'attività dell'emulatore di Azure Cosmos DB deve essere posizionata prima di qualsiasi altra attività che prevede che l'emulatore sia in esecuzione.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

In questa esercitazione si aggiungerà l'attività all'inizio per fare in modo che l'emulatore sia disponibile prima dell'esecuzione dei test.

### <a name="add-the-task-using-yaml"></a>Aggiungere l'attività con YAML

Questo passaggio è facoltativo ed è richiesto solo se si configura la pipeline CI/CD usando un'attività YAML. In questi casi è possibile definire l'attività YAML come illustrato nel codice seguente:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Configurare i test per usare l'emulatore

A questo punto, è possibile configurare i test per usare l'emulatore. L'attività di compilazione dell'emulatore esporta una variabile di ambiente, "CosmosDbEmulator.Endpoint", a cui qualsiasi attività nella pipeline di compilazione può inviare richieste. 

In questa esercitazione si userà l'[attività di test di Visual Studio](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) per eseguire unit test configurati tramite un file con estensione **runsettings**. Per altre informazioni sulla configurazione di unit test, vedere la [documentazione](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017&preserve-view=true). Il codice di esempio completo dell'applicazione Todo da usare in questo documento è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Di seguito è riportato un esempio di un file con estensione **runsettings** che definisce i parametri da passare agli unit test di un'applicazione. Si noti che la variabile `authKey` usata è la [chiave nota](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) per l'emulatore. `authKey` è la chiave prevista dall'attività di compilazione dell'emulatore e deve essere definita nel file con estensione **runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Se si configura una pipeline CI/CD per un'applicazione che usa l'API di Azure Cosmos DB per MongoDB, la stringa di connessione include il numero di porta 10255 per impostazione predefinita. Questa porta, tuttavia, non è attualmente aperta. Per stabilire la connessione, è necessario usare in alternativa la porta 10250. La stringa di connessione dell'API di Azure Cosmos DB per MongoDB rimane la stessa, tranne per il fatto che il numero di porta supportato è 10250 anziché 10255.

Ai parametri `TestRunParameters` viene fatto riferimento tramite una proprietà `TestContext` nel progetto di test dell'applicazione. Di seguito è riportato un esempio di un test in esecuzione in Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Passare alle opzioni di esecuzione nell'attività di test di Visual Studio. Nell'opzione **File di impostazioni** specificare che i test sono configurati tramite il file con estensione **runsettings**. Nell'opzione **Esegui override parametri di esecuzione dei test** aggiungere `-endpoint $(CosmosDbEmulator.Endpoint)`. In questo modo, l'attività di test viene configurata per fare riferimento all'endpoint dell'attività di compilazione dell'emulatore, anziché a quello definito nel file con estensione **runsettings**.  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

## <a name="run-the-build"></a>Eseguire la compilazione

A questo punto, **salvare e accodare** la compilazione. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

Dopo l'avvio della compilazione, si noti che l'attività dell'emulatore di Cosmos DB ha iniziato a spostare verso il basso l'immagine Docker con l'emulatore installato. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

Al termine della compilazione, si noti che i test vengono superati e risultano tutti in esecuzione nell'emulatore di Cosmos DB dall'attività di compilazione.

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso dell'emulatore per operazioni di sviluppo e test locali, vedere [Usare l'emulatore di Azure Cosmos DB per sviluppo e test locali](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Per esportare i certificati TLS/SSL dell'emulatore, vedere [Esportare i certificati dell'emulatore di Azure Cosmos DB per l'uso con Java, Python e Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
