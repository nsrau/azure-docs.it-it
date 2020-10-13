---
title: Creare aree di lavoro nel portale
titleSuffix: Azure Machine Learning
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d2885c6cc259cba74ab991ecf5046856984824f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631248"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Creare e gestire le aree di lavoro di Azure Machine Learning nel portale di Azure


Questo articolo illustra come creare, visualizzare ed eliminare [**Azure Machine Learning aree di lavoro**](concept-workspace.md) nel portale di Azure per [Azure Machine Learning](overview-what-is-azure-ml.md).  Il portale è il modo più semplice per iniziare a usare le aree di lavoro, ma in base alle esigenze modificate o ai requisiti per l'aumento dell'automazione è anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando, [con codice Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) o [tramite l'estensione vs code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della sottoscrizione di Azure. 

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **+ Crea una risorsa**.

      ![Creare una nuova risorsa](./media/how-to-manage-workspace/create-workspace.gif)

1. Usare la barra di ricerca per trovare **Machine Learning**.

1. Selezionare **Machine Learning**.

1. Nel riquadro **Machine Learning** selezionare **Crea** per iniziare.

1. Specificare le informazioni seguenti per configurare la nuova area di lavoro:

   Campo|Descrizione 
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti. Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.
   Subscription |Selezionare la sottoscrizione di Azure da usare.
   Resource group | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. Per usare un gruppo di risorse esistente, è necessario un ruolo di *collaboratore* o *proprietario* .  Per ulteriori informazioni sull'accesso, vedere [gestire l'accesso a un'area di lavoro Azure Machine Learning](how-to-assign-roles.md).
   Region | Per creare l'area di lavoro, selezionare l'area di Azure più vicina agli utenti e alle risorse di dati.
   Edizione dell'area di lavoro | Selezionare **Basic** o **Enterprise**.  Questa edizione dell'area di lavoro determina le funzionalità a cui si avrà accesso e i prezzi. Altre informazioni su [Azure Machine Learning](overview-what-is-azure-ml.md). 

    ![Configurare l'area di lavoro](./media/how-to-manage-workspace/select-edition.png)

1. Al termine della configurazione dell'area di lavoro, selezionare **Verifica + crea**. Facoltativamente, usare le sezioni [rete](#networking) e [Avanzate](#advanced) per configurare altre impostazioni per l'area di lavoro.

1. Rivedere le impostazioni e apportare eventuali modifiche o correzioni aggiuntive. Quando si è soddisfatti delle impostazioni, selezionare **Crea**.

   > [!Warning] 
   > La creazione dell'area di lavoro nel cloud può richiedere diversi minuti.

   Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, 
 
 1. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

### <a name="networking"></a>Funzionalità di rete  

> [!IMPORTANT]  
> Per ulteriori informazioni sull'utilizzo di un endpoint privato e di una rete virtuale con l'area di lavoro, vedere [isolamento e privacy della rete](how-to-enable-virtual-network.md).
    
1. La configurazione di rete predefinita prevede l'uso di un __endpoint pubblico__accessibile dalla rete Internet pubblica. Per limitare l'accesso all'area di lavoro a una rete virtuale di Azure creata, è invece possibile selezionare __endpoint privato__ come __metodo di connettività__e quindi usare __+ Aggiungi__ per configurare l'endpoint. 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Selezione endpoint privato":::  

1. Nel modulo __Crea endpoint privato__ impostare il percorso, il nome e la rete virtuale da usare. Se si vuole usare l'endpoint con una zona DNS privato, selezionare __integra con zona DNS privata__ e selezionare la zona usando il campo __area DNS privato__ . Selezionare __OK__ per creare l'endpoint.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Selezione endpoint privato":::   

1. Al termine della configurazione della rete, è possibile selezionare __Verifica + crea__o passare alla configurazione __avanzata__ facoltativa. 

    > [!WARNING]    
    > Quando si crea un endpoint privato, viene creata una nuova zona DNS privato denominata __privatelink.API.azureml.ms__ . Contiene un collegamento alla rete virtuale. Se si creano più aree di lavoro con endpoint privati nello stesso gruppo di risorse, è possibile aggiungere solo la rete virtuale per il primo endpoint privato alla zona DNS. Per aggiungere voci per le reti virtuali usate dalle aree di lavoro o dagli endpoint privati aggiuntivi, seguire questa procedura: 
    >   
    > 1. Nella [portale di Azure](https://portal.azure.com)selezionare il gruppo di risorse che contiene l'area di lavoro. Quindi selezionare la risorsa DNS privato zona denominata __privatelink.API.azureml.ms__.    
    > 2. In __Impostazioni__selezionare collegamenti alla __rete virtuale__. 
    > 3. Selezionare __Aggiungi__. Nella pagina __Aggiungi collegamento rete virtuale__ specificare un nome univoco per il __collegamento__, quindi selezionare la __rete virtuale__ da aggiungere. Selezionare __OK__ per aggiungere il collegamento di rete.    
    >   
    > Per altre informazioni, vedere [configurazione DNS dell'endpoint privato di Azure](/azure/private-link/private-endpoint-dns).   

### <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità

Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. È consigliabile consentire al centro sicurezza di Azure di analizzare le risorse e seguire le indicazioni. Per altre informazioni, vedere  [azure container Registry Image Scan by Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) and [Azure Kubernetes Services Integration with Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

### <a name="advanced"></a>Avanzato    

Per impostazione predefinita, le metriche e i metadati per l'area di lavoro vengono archiviati in un'istanza di Azure Cosmos DB gestita da Microsoft. Questi dati vengono crittografati tramite chiavi gestite da Microsoft.  

Per limitare i dati raccolti da Microsoft nell'area di lavoro, selezionare __area di lavoro a elevato__uso di business. Per altre informazioni su questa impostazione, vedere [crittografia](concept-enterprise-security.md#encryption-at-rest)dei dati inattivi.

> [!IMPORTANT]  
> Quando si crea un'area di lavoro, è possibile selezionare un elevato effetto aziendale. Questa impostazione non può essere modificata dopo la creazione dell'area di lavoro.   
Se si usa la versione __Enterprise__ di Azure Machine Learning, è invece possibile specificare la propria chiave. In questo modo viene creata l'istanza Azure Cosmos DB che archivia le metriche e i metadati nella sottoscrizione di Azure. Per usare la propria chiave, attenersi alla procedura seguente:    

> [!IMPORTANT]  
> Prima di seguire questa procedura, è necessario eseguire le azioni seguenti:   
>   
> 1. Autorizzare l' __App Machine Learning__ (in gestione delle identità e degli accessi) con le autorizzazioni di collaboratore per la sottoscrizione.  
> 1. Seguire i passaggi in [configurare le chiavi gestite dal cliente](/azure/cosmos-db/how-to-setup-cmk) per:   
>     * Registrare il provider di Azure Cosmos DB   
>     * Creare e configurare un Azure Key Vault 
>     * Genera una chiave  
>   
>     Non è necessario creare manualmente l'istanza di Azure Cosmos DB, ne verrà creata una automaticamente durante la creazione dell'area di lavoro. Questa istanza di Azure Cosmos DB verrà creata in un gruppo di risorse distinto usando un nome basato su questo modello: `<your-workspace-resource-name>_<GUID>` .   
>   
> Questa impostazione non può essere modificata dopo la creazione dell'area di lavoro. Se si elimina il Azure Cosmos DB usato dall'area di lavoro, è necessario eliminare anche l'area di lavoro che lo usa.

1. Selezionare __chiavi gestite dal cliente__, quindi selezionare __fare clic per selezionare la chiave__.   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Selezione endpoint privato":::   

1. Nel modulo __Seleziona chiave da Azure Key Vault__ selezionare una Azure Key Vault esistente, una chiave che contiene e la versione della chiave. Questa chiave viene usata per crittografare i dati archiviati in Azure Cosmos DB. Usare infine il pulsante __Seleziona__ per usare questa chiave. 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Selezione endpoint privato":::

### <a name="download-a-configuration-file"></a>Scaricare un file di configurazione

1. Se si vuole creare un' [istanza di calcolo](tutorial-1st-experiment-sdk-setup.md#azure), ignorare questo passaggio.

1. Se si prevede di usare codice nell'ambiente locale che fa riferimento a questa area di lavoro, selezionare **Scarica config json** dalla sezione **Panoramica** dell'area di lavoro.  

   ![Scarica config.json](./media/how-to-manage-workspace/configure.png)
   
   Inserire il file nella struttura di directory che contiene gli script Python o i notebook di Jupyter. Può trattarsi della stessa directory, in una sottodirectory denominata *.azureml* o in una directory padre. Quando si crea un'istanza di calcolo, questo file viene aggiunto alla directory corretta nella macchina virtuale.
## <a name="find-a-workspace"></a><a name="view"></a>Trovare un'area di lavoro

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel campo di ricerca superiore digitare **Machine Learning**.  

1. Selezionare **Machine Learning**.

   ![Cerca area di lavoro Azure Machine Learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.

## <a name="delete-a-workspace"></a>Eliminare un'area di lavoro

Nella [portale di Azure](https://portal.azure.com/)selezionare **Elimina**  nella parte superiore dell'area di lavoro che si vuole eliminare.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Selezione endpoint privato":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Spostamento dell'area di lavoro

> [!WARNING]
> Lo spostamento dell’area di lavoro di Azure Machine Learning in una diversa sottoscrizione o della sottoscrizione proprietaria su un nuovo tenant non è supportato in quanto ciò può provocare errori.

### <a name="deleting-the-azure-container-registry"></a>Eliminazione del Registro Azure Container

L'area di lavoro di Azure Machine Learning usa il Registro Azure Container (ACR) per alcune operazioni. Verrà creata automaticamente un'istanza di ACR quando è necessaria per la prima volta.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per creare, eseguire il training e distribuire modelli con Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
