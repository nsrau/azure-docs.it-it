---
title: 'Avvio rapido: Creare risorse di Azure Cosmos DB dal portale di Azure'
description: Questa guida di avvio rapido mostra come creare un database, un contenitore ed elementi di Azure Cosmos con il portale di Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: d169c638869cd9488605117552c9017dfd10c911
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118322"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Avvio rapido: Creare un account, un database, un contenitore ed elementi di Azure Cosmos dal portale di Azure

> [!div class="op_single_selector"]
> * [Azure portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile usare Cosmos DB per creare ed eseguire rapidamente query su chiavi/valori, database di documenti e database a grafo che sfruttano i vantaggi delle funzionalità di distribuzione globale e scalabilità orizzontale alla base di Azure Cosmos DB. 

Questo argomento di avvio rapido illustra come usare il portale di Azure per creare un account [API SQL](sql-api-introduction.md) di Azure Cosmos DB, creare un database e un contenitore di documenti e aggiungere dati al contenitore. 

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure o un account di prova gratuito di Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Passare al [portale di Azure](https://portal.azure.com/) per creare un account Azure Cosmos DB. Cercare e selezionare **Azure Cosmos DB**.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Riquadro Database nel portale di Azure":::

1. Selezionare **Aggiungi**.
1. Nella pagina **Crea account Cosmos DB** immettere le impostazioni base per il nuovo account Azure Cosmos. 

    |Impostazione|valore|Descrizione |
    |---|---|---|
    |Subscription|Nome sottoscrizione|Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos. |
    |Gruppo di risorse|Nome del gruppo di risorse|Selezionare un gruppo di risorse oppure fare clic su **Crea nuovo**, quindi immettere un nome univoco per il nuovo gruppo di risorse. |
    |Nome account|Un nome univoco|Immettere un nome per identificare l'account Azure Cosmos. Dato che al nome specificato viene aggiunto *documents.azure.com* per creare l'URI, usare un nome univoco.<br><br>Il nome può contenere solo lettere minuscole, numeri e il segno meno (-). Deve avere una lunghezza compresa tra 3 e 31 caratteri.|
    |API|Il tipo di account da creare|Selezionare **Core (SQL)** per creare un database di documenti e una query con la sintassi SQL. <br><br>L'API determina il tipo di account da creare. Azure Cosmos DB offre cinque API: Core (SQL) e MongoDB per dati dei documenti, Gremlin per i dati dei grafici, Tabelle di Azure e Cassandra. Attualmente, è necessario creare un account separato per ogni API. <br><br>[Altre informazioni sull'API SQL](introduction.md).|
    |Applica sconto per il livello gratuito|Applica o Non applicare|Azure Cosmos DB livello gratuito offre i primi 400 UR/s e 5 GB di spazio di archiviazione gratuiti per account. Altre informazioni sul [livello gratuito](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Location|Area più vicina ai propri utenti|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.|
    |Tipo di account|Produzione o Non di produzione|Selezionare **Produzione** se l'account verrà usato per un carico di lavoro di produzione. Selezionare **Non di produzione** se l'account non verrà usato per un carico di lavoro di produzione, ad esempio per lo sviluppo, il test, il controllo di qualità o lo staging. Si tratta di un'impostazione di tag delle risorse di Azure che consente di ottimizzare l'esperienza del portale, ma non influisce sull'account Azure Cosmos DB sottostante. È possibile modificare questo valore in qualsiasi momento.|
    |Ridondanza geografica|Abilitare o disabilitare|Abilitare o disabilitare la distribuzione globale nell'account associando la propria area a un'altra area. È possibile aggiungere altre aree al proprio account in un secondo momento.|
    |Scritture in più aree|Abilitare o disabilitare|La funzionalità Scritture in più aree consente di sfruttare la velocità effettiva di cui è stato effettuato il provisioning per i database e i contenitori in tutto il mondo.|
    |Zone di disponibilità|Abilitare o disabilitare|Le zone di disponibilità consentono di migliorare ulteriormente la disponibilità e la resilienza dell'applicazione.|


> [!NOTE]
> È possibile avere fino a un account Azure Cosmos DB del livello gratuito per ogni sottoscrizione di Azure ed è necessario acconsentire esplicitamente durante la creazione dell'account. Se l'opzione per l'applicazione dello sconto per il livello gratuito non è visualizzata, un altro account nella sottoscrizione è già stato abilitato per il livello gratuito.
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png" alt-text="Pagina del nuovo account per Azure Cosmos DB":::

1. Selezionare **Rivedi e crea**. È possibile ignorare le sezioni **Rete** e **Tag**.

1. Esaminare le impostazioni dell'account e quindi selezionare **Crea**. La creazione dell'account richiede alcuni minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Riquadro Notifiche del portale di Azure":::

1. Selezionare **Vai alla risorsa** per passare alla pagina dell'account Azure Cosmos DB. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="Pagina dell'account Azure Cosmos DB":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Aggiungere un database e un contenitore 

È possibile usare Esplora dati nel portale di Azure per creare un database e un contenitore. 

1.  Scegliere **Esplora dati** dal menu di spostamento sinistro nella pagina dell'account Azure Cosmos DB, quindi selezionare **Nuovo contenitore**. 
    
    Può essere necessario scorrere verso destra per visualizzare la finestra **Aggiungi contenitore**.
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Esplora dati nel portale di Azure, riquadro Aggiungi contenitore":::
    
1.  Nel riquadro **Aggiungi contenitore** immettere le impostazioni per il nuovo contenitore.
    
    |Impostazione|Valore consigliato|Descrizione
    |---|---|---|
    |**ID database**|ToDoList|Immettere *ToDoList* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/, \\, #, ?` o spazi finali. Selezionare l'opzione **Provision database throughput** (Provisioning delle unità di elaborazione del database) che consente di condividere le unità di elaborazione del database con tra tutti i contenitori al suo interno. Questa opzione permette anche di risparmiare sui costi. |
    |**Velocità effettiva**|400|Lasciare la velocità effettiva a 400 unità di richiesta al secondo (UR/s). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.| 
    |**ID contenitore**|Items|Immettere *Items* come nome per il nuovo contenitore. Gli ID contenitore prevedono gli stessi requisiti relativi ai caratteri dei nomi di database.|
    |**Chiave di partizione**| /category| L'esempio descritto in questo articolo usa */category* come chiave di partizione.|

    
    Non aggiungere **chiavi univoche** per questo esempio. Le chiavi univoche consentono di aggiungere un livello di integrità dei dati al database garantendo l'univocità di uno o più valori per ogni chiave di partizione. Per altre informazioni, vedere [Chiavi univoche in Azure Cosmos DB](unique-keys.md).
    
1.  Selezionare **OK**. In Esplora dati vengono visualizzati il nuovo database e il nuovo contenitore creati.

## <a name="add-data-to-your-database"></a>Aggiungere dati al database

Aggiungere dati al nuovo database usando Esplora dati.

1. In **Esplora dati** espandere il database **ToDoList** e quindi il contenitore **Elementi**. Selezionare **Elementi** e quindi **Nuovo elemento**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Creare nuovi documenti in Esplora dati nel portale di Azure":::
   
1. Aggiungere la struttura seguente al documento sul lato destro del riquadro **Documenti**:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Selezionare **Salva**.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Copiare i dati JSON e selezionare Salva in Esplora dati nel portale di Azure":::
   
1. Selezionare **Nuovo documento** e creare e salvare un altro documento con un `id` univoco ed eventuali altri valori e proprietà desiderati. I documenti possono avere qualsiasi struttura perché Azure Cosmos DB non impone alcuno schema ai dati.

## <a name="query-your-data"></a>Eseguire query sui dati

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Se si vuole eliminare solo il database e usare l'account Azure Cosmos in futuro, è possibile eliminare il database con i passaggi seguenti:

* Accedere all'account Azure Cosmos.
* Aprire **Esplora dati**, fare clic con il pulsante destro del mouse sul database che si vuole eliminare e selezionare **Elimina database**.
* Immettere l'ID o il nome del database per confermare l'operazione di eliminazione. 

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB e come creare un database e un contenitore con Esplora dati. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)
