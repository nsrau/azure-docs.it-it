---
title: Configurare una pipeline di integrazione continua/distribuzione continua con l'attività di compilazione dell'emulatore di Azure Cosmos DB
description: Esercitazione su come configurare il flusso di lavoro di compilazione e rilascio in Azure DevOps usando l'attività di compilazione dell'emulatore di Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/02/2018
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: d6250b778cdaec47ccbe2f45d35adea0b676a20a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882012"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Configurare una pipeline CI/CD con l'attività di compilazione dell'emulatore di Azure Cosmos DB in Azure DevOps

L'emulatore di Azure Cosmos DB fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. L'emulatore consente di sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure né sostenere costi. 

L'attività di compilazione dell'emulatore di Azure Cosmos DB per Azure DevOps consente di eseguire la stessa operazione in un ambiente di integrazione continua. Con l'attività di compilazione, è possibile eseguire i test nell'emulatore come parte della compilazione e rilasciare dei flussi di lavoro. L'attività avvia un contenitore Docker con l'emulatore già in esecuzione e fornisce un endpoint che può essere usato dalla definizione di compilazione restante. È possibile creare e avviare le istanze dell'emulatore necessarie, ognuna in esecuzione in un contenitore separato. 

Questo articolo illustra come configurare una pipeline di integrazione continua in Azure DevOps per un'applicazione ASP.NET che usa l'attività di compilazione dell'emulatore di Cosmos DB per eseguire i test. È possibile usare un approccio simile per configurare una pipeline di integrazione continua per un'applicazione Node.js o Python. 

## <a name="install-the-emulator-build-task"></a>Installare l'attività di compilazione dell'emulatore

Per usare l'attività di compilazione, è innanzitutto necessario installarla nell'organizzazione di Azure DevOps. Trovare l'estensione **Emulatore di Azure Cosmos DB** nel [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) e fare clic su **Scarica gratuitamente**.

![Trovare e installare l'attività di compilazione dell'emulatore di Azure Cosmos DB nel Marketplace di Azure DevOps](./media/tutorial-setup-ci-cd/addExtension_1.png)

Successivamente, scegliere l'organizzazione in cui installare l'estensione. 

> [!NOTE]
> Per installare un'estensione in un'organizzazione di Azure DevOps, è necessario essere proprietario dell'account o amministratore della raccolta di progetti. Se non si dispone di autorizzazioni, ma si è membri dell'account, in alternativa è possibile richiedere delle estensioni. [Altre informazioni.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions)

![Scegliere un'organizzazione di Azure DevOps in cui installare un'estensione](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Creare una definizione di compilazione

Ora che l' estensione è installata, accedere al proprio account Azure DevOps e trovare il progetto nel dashboard dei progetti. È possibile aggiungere una [pipeline di compilazione](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) al progetto o modificarne una esistente. Se si ha già una pipeline di compilazione, è possibile passare direttamente alla sezione [Aggiungere l'attività di compilazione dell'emulatore a una definizione di compilazione](#addEmulatorBuildTaskToBuildDefinition).

1. Per creare una nuova definizione di compilazione, passare alla scheda **Compilazioni** in Azure DevOps. Selezionare **+ Nuovo**  \> **Nuova pipeline di compilazione**

   ![Creazione di una nuova pipeline di compilazione](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Selezionare le opzioni desiderate in **Selezionare un'origine**, **Progetto team**, **Repository** e **Ramo predefinito per le compilazioni manuale e pianificata**. Dopo aver selezionato le opzioni necessarie, scegliere **Continua**

   ![Selezionare il progetto team, il repository e il ramo per la pipeline di compilazione](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Selezionare infine il modello desiderato per la pipeline di compilazione. In questa esercitazione viene selezionato il modello **ASP.NET**. 

A questo punto la pipeline di compilazione può essere configurata per l'uso dell'attività di compilazione dell'emulatore di Azure Cosmos DB. 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Aggiungere l'attività a una pipeline di compilazione

1. Prima di aggiungere un'attività alla pipeline di compilazione, occorre aggiungere un processo agente. Passare alla pipeline di compilazione, selezionare **...** e scegliere **Aggiungi un processo agente**.

1. Selezionare quindi il simbolo **+** accanto al processo agente per aggiungere l'attività di compilazione dell'emulatore. Cercare **cosmos** nella casella di ricerca, selezionare **Emulatore di Azure Cosmos DB** e aggiungerlo al processo agente. L'attività di compilazione avvia un contenitore con un'istanza dall'emulatore di Cosmos DB già in esecuzione. L'attività dell'emulatore di Azure Cosmos DB deve essere posizionata prima di qualsiasi altra attività che prevede che l'emulatore sia in esecuzione.

   ![Aggiungere l'attività di compilazione dell'emulatore alla definizione di compilazione](./media/tutorial-setup-ci-cd/addExtension_3.png)

In questa esercitazione si aggiungerà l'attività all'inizio per fare in modo che l'emulatore sia disponibile prima dell'esecuzione dei test.

## <a name="configure-tests-to-use-the-emulator"></a>Configurare i test per usare l'emulatore

A questo punto, è possibile configurare i test per usare l'emulatore. L'attività di compilazione dell'emulatore esporta una variabile di ambiente, "CosmosDbEmulator.Endpoint", a cui qualsiasi attività nella pipeline di compilazione può inviare richieste. 

In questa esercitazione si userà l'[attività di test di Visual Studio](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) per eseguire unit test configurati tramite un file con estensione **runsettings**. Per altre informazioni sulla configurazione di unit test, vedere la [documentazione](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017). Il codice di esempio completo dell'applicazione Todo da usare in questo documento è disponibile su [Github](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

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

![Eseguire l'override della variabile di endpoint con l'endpoint dell'attività di compilazione dell'emulatore](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Eseguire la compilazione

A questo punto, **salvare e accodare** la compilazione. 

![Salvare ed eseguire la compilazione](./media/tutorial-setup-ci-cd/runBuild_1.png)

Dopo l'avvio della compilazione, si noti che l'attività dell'emulatore di Cosmos DB ha iniziato a spostare verso il basso l'immagine Docker con l'emulatore installato. 

![Salvare ed eseguire la compilazione](./media/tutorial-setup-ci-cd/runBuild_4.png)

Al termine della compilazione, si noti che i test vengono superati e risultano tutti in esecuzione nell'emulatore di Cosmos DB dall'attività di compilazione.

![Salvare ed eseguire la compilazione](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso dell'emulatore per operazioni di sviluppo e test locali, vedere [Usare l'emulatore di Azure Cosmos DB per sviluppo e test locali](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Per esportare i certificati SSL dell'emulatore, vedere [Esportare i certificati dell'emulatore di Azure Cosmos DB per l'uso con Java, Python e Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
