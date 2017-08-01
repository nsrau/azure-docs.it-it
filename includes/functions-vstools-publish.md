In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Selezionare **Crea nuovo** e quindi fare clic su **Pubblica**. 

![Pubblicare creando una nuova app per le funzioni](./media/functions-vstools-publish/functions-vstools-publish-new-function-app.png)

Se non si è ancora connesso Visual Studio al proprio account Azure, fare clic su **Aggiungi un account**.  

Nella finestra **Crea servizio app** usare le impostazioni di hosting specificate nella tabella. 

![Runtime locale di Azure](./media/functions-vstools-publish/functions-vstools-publish.png)

| Impostazione      | Valore consigliato  | Descrizione                                |
| ------------ |  ------- | -------------------------------------------------- |
| **Nome app** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. |
| **Sottoscrizione** | Scegliere la sottoscrizione | Sottoscrizione di Azure da usare. |
| **[Gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome del gruppo di risorse in cui creare l'app per le funzioni. |
| **[Piano di servizio app](../articles/azure-functions/functions-scale.md)** | Piano a consumo | Quando si crea un nuovo piano, assicurarsi di scegliere **A consumo** in **Dimensioni**.  |
| **[Account di archiviazione](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** | Nome globalmente univoco | Usare un account di archiviazione esistente o crearne uno nuovo.   |

Fare clic su **Crea** per creare un'app per le funzioni con queste impostazioni in Azure. Al termine del provisioning prendere nota del valore di **URL sito**, che è l'indirizzo dell'app per le funzioni in Azure. 

![Runtime locale di Azure](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
