---
title: Distribuzione di eventi con l'identità del servizio gestita
description: Questo articolo descrive come abilitare l'identità del servizio gestito per un argomento di Griglia di eventi di Azure e per usarla per inviare eventi alle destinazioni supportate.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700648"
---
# <a name="event-delivery-with-managed-identity"></a>Distribuzione di eventi con Identità gestita
Questo articolo descrive come abilitare l'[identità del servizio gestito](../active-directory/managed-identities-azure-resources/overview.md) per un dominio o un argomento di Griglia di eventi di Azure. Usare l'identità per inviare eventi a destinazioni supportate, ad esempio code e argomenti del bus di servizio, Hub eventi e account di archiviazione.

Di seguito sono elencati i passaggi illustrati nel dettaglio in questo articolo:
1. Creare un argomento o un dominio con un'identità assegnata dal sistema oppure aggiornare un argomento o un dominio esistente per abilitare l'identità. 
2. Aggiungere l'identità a un ruolo appropriato (ad esempio: Mittente dei dati del bus di servizio) nella destinazione (ad esempio: una coda del bus di servizio)
3. Quando si creano sottoscrizioni di eventi, abilitare l'utilizzo dell'identità per recapitare gli eventi alla destinazione. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Creazione di un argomento o di un dominio con un'identità
Per prima cosa si vedrà creare un argomento o un dominio con un'identità gestita dal sistema.

### <a name="using-azure-portal"></a>Uso del portale di Azure
È possibile abilitare l'identità assegnata dal sistema per un argomento o un dominio mentre viene creata nel portale di Azure. Nell'immagine seguente viene illustrato come abilitare l'identità gestita dal sistema per un argomento. In pratica, selezionare l'opzione **Abilita identità assegnata dal sistema** nella pagina **Avanzate** della procedura guidata di creazione dell'argomento. Questa opzione verrà visualizzata nella pagina **Avanzate** della procedura di creazione guidata del dominio. 

![Abilitare l'identità durante la creazione di un argomento](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile anche usare l'interfaccia della riga di comando di Azure per creare un argomento o un dominio con un'identità assegnata dal sistema. Usare il comando `az eventgrid topic create` con il parametro `--identity` impostato su `systemassigned`. Se non si specifica un valore per questo parametro, viene usato il valore predefinito `noidentity`. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Analogamente, è possibile usare il comando `az eventgrid domain create` per creare un dominio con un'identità gestita dal sistema.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Abilitare l'identità per un argomento o un dominio esistente
Nella sezione precedente si è appreso come abilitare un'identità gestita dal sistema durante la creazione di un argomento o di un dominio. Questa sezione illustra come abilitare l'identità gestita dal sistema per un argomento o un dominio esistente. 

### <a name="using-azure-portal"></a>Uso del portale di Azure
1. Passare al [portale di Azure](https://portal.azure.com)
2. Cercare **argomenti di Griglia di eventi** nella barra di ricerca.
3. Selezionare l'**argomento** per cui si vuole abilitare l'identità gestita. 
4. Passare alla scheda **Identità**. 
5. Attivare l'opzione per abilitare l'identità. 

    È possibile seguire una procedura simile per abilitare un'identità per un dominio di Griglia di eventi.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Usare il comando `az eventgrid topic update` con `--identity` impostato su `systemassigned` per abilitare un'identità assegnata dal sistema per un argomento esistente. Se si vuole disabilitare l'identità, specificare il valore `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Il comando per l'aggiornamento di un dominio esistente è simile (`az eventgrid domain update`).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Destinazioni supportate e ruoli Controllo degli accessi in base al ruolo
Dopo aver abilitato un'identità per un argomento o un dominio di Griglia di eventi, Azure crea automaticamente un'identità in Azure Active Directory (Azure AD). Aggiungere questa identità ai ruoli Controllo degli accessi in base al ruolo appropriati, in modo che l'argomento o il dominio possa inoltrare eventi alle destinazioni supportate. Ad esempio, aggiungere l'identità al ruolo **Mittente dei dati di Hub eventi di Azure** per uno spazio dei nomi di Hub eventi in modo che l'argomento di Griglia di eventi possa inoltrare eventi ad Hub eventi nello spazio dei nomi di pertinenza.  

Griglia di eventi di Azure supporta attualmente argomenti o domini configurati con un'identità gestita assegnata dal sistema per inoltrare eventi alle destinazioni seguenti. Questa tabella elenca anche i ruoli in cui deve trovarsi l'identità per consentire all'argomento di inoltrare eventi.

| Destination | Ruolo di controllo degli accessi in base al ruolo | 
| ----------- | --------- | 
| Code e argomenti del bus di servizio | [Mittente dei dati del bus di servizio di Azure](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Hub eventi | [Mittente dei dati di Hub eventi di Azure](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Archiviazione BLOB | [Collaboratore ai dati del BLOB di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Archiviazione code |[Mittente dei messaggi sui dati della coda di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Aggiungere un'identità ai ruoli Controllo degli accessi in base al ruolo nelle destinazioni
Questa sezione descrive come aggiungere un'identità relativa a un argomento o un dominio a un ruolo Controllo degli accessi in base al ruolo. 

### <a name="using-azure-portal"></a>Uso del portale di Azure
È possibile usare il **portale di Azure** per assegnare l'identità dell'argomento/dominio a un ruolo appropriato, in modo che l'argomento/dominio possa inoltrare eventi alla destinazione. 

Nell'esempio seguente viene aggiunta un'identità gestita relativa a un argomento di Griglia di eventi denominato **msitesttopic** al ruolo **Mittente dei dati del bus di servizio di Azure** per uno **spazio dei nomi** del bus di servizio contenente una risorsa della coda o dell'argomento. Quando si aggiunge l'identità a un ruolo a livello di spazio dei nomi, l'argomento può inoltrare eventi a tutte le entità con quel ruolo nello spazio dei nomi. 

1. Passare allo  **spazio dei nomi del bus di servizio** nel [portale di Azure](https://portal.azure.com). 
2. Selezionare **Controllo di accesso** nel riquadro a sinistra. 
3. Nella sezione **Aggiungi un'assegnazione di ruolo** selezionare **Aggiungi**. 
4. Nella pagina **Aggiungi un'assegnazione di ruolo** seguire questa procedura:
    1. Selezionare il ruolo. In questo caso: **Mittente dei dati del bus di servizio di Azure**. 
    2. Selezionare l'**identità** relativa all'argomento o al dominio. 
    3. Selezionare **Salva** per salvare la configurazione.

Per aggiungere un'identità agli altri ruoli specificati nella tabella, sarà necessario seguire una procedura simile. 

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
L'esempio riportato in questa sezione illustra come usare l'**interfaccia della riga di comando di Azure** per aggiungere un'identità a un ruolo Controllo degli accessi in base al ruolo. I comandi di esempio fanno riferimento ad argomenti di Griglia di eventi, ma sono simili a quelli per i domini di Griglia di eventi. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Ottenere l'ID entità di sicurezza per l'identità di sistema dell'argomento 
Per prima cosa, ottenere l'ID entità di sicurezza dell'identità gestita dal sistema dell'argomento e assegnare l'identità ai ruoli appropriati.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Creare un'assegnazione di ruolo per Hub eventi in diversi ambiti 
Nell'esempio di interfaccia della riga di comando seguente viene illustrato come aggiungere l'identità di un argomento al ruolo **Mittente dei dati di Hub eventi di Azure** a livello di spazio dei nomi o di Hub eventi. Se si crea l'assegnazione di ruolo a livello di spazio dei nomi, l'argomento potrà inviare eventi a tutti gli Hub eventi compresi in quello spazio dei nomi. Se invece si crea l'assegnazione di ruolo a livello di Hub eventi, l'argomento potrà inoltrare gli eventi solo a quello specifico Hub eventi. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Creare un'assegnazione di ruolo per l'argomento del bus di servizio in vari ambiti 
Nell'esempio di interfaccia della riga di comando seguente viene illustrato come aggiungere l'identità di un argomento al ruolo **Mittente dei dati del bus di servizio di Azure** a livello di spazio dei nomi o di argomento del bus di servizio. Se si crea l'assegnazione di ruolo a livello di spazio dei nomi, l'argomento di Griglia di eventi potrà inoltrare gli eventi a tutte le entità (code o argomenti del bus di servizio) comprese in quello spazio dei nomi. Se invece si crea l'assegnazione a livello di coda o argomento del bus di servizio, l'argomento di Griglia di eventi potrà inoltrare gli eventi solo alla coda o all'argomento di quello specifico bus di servizio. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Creare sottoscrizioni di eventi che usano l'identità
Se si ha un argomento o un dominio con un'identità gestita dal sistema e si aggiunge l'identità al ruolo appropriato nella destinazione, si è pronti per creare sottoscrizioni che usino tale identità. 

### <a name="using-azure-portal"></a>Uso del portale di Azure
Quando si crea una sottoscrizione evento, nella sezione **DETTAGLI ENDPOINT** viene visualizzata un'opzione che consente di abilitare l'utilizzo dell'identità assegnata dal sistema per un endpoint. 

![Abilitare l'identità durante la creazione della sottoscrizione evento per la coda del bus di servizio](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Nella scheda **Funzionalità aggiuntive** è possibile anche abilitare l'utilizzo dell'identità assegnata dal sistema da usare per i messaggi non recapitabili. 

![Abilitare l'identità assegnata dal sistema per l'inserimento nella coda di messaggi non recapitabili](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Uso dell'interfaccia della riga di comando di Azure - Coda del bus di servizio 
Questa sezione illustra come usare l'**interfaccia della riga di comando di Azure** per abilitare l'utilizzo dell'identità assegnata dal sistema per recapitare gli eventi a una coda del bus di servizio. L'identità deve essere un membro del ruolo **Mittente dei dati del bus di servizio di Azure**. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili. 

#### <a name="define-variables"></a>Definire le variabili
Per prima cosa, specificare i valori per le variabili seguenti da usare nel comando dell'interfaccia della riga di comando. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>creare una sottoscrizione evento usando l'identità gestita per il recapito 
Questo comando di esempio crea una sottoscrizione evento per un argomento di Griglia di eventi con il tipo di endpoint impostato su **Coda del bus di servizio**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>creare una sottoscrizione evento usando l'identità gestita per il recapito e l'inserimento nella coda di messaggi non recapitabili
Questo comando di esempio crea una sottoscrizione evento per un argomento di Griglia di eventi con il tipo di endpoint impostato su **Coda del bus di servizio**. Specifica inoltre che l'identità gestita dal sistema deve essere usata anche per i messaggi non recapitabili. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Interfaccia della riga di comando di Azure - Hub eventi 
Questa sezione illustra come usare l'**interfaccia della riga di comando di Azure** per abilitare l'utilizzo dell'identità assegnata dal sistema per recapitare gli eventi a un Hub eventi. L'identità deve essere un membro del ruolo **Mittente dei dati di Hub eventi di Azure**. Deve essere anche un membro del ruolo **Collaboratore ai dati del BLOB di archiviazione** nell'account di archiviazione usato per i messaggi non recapitabili. 

#### <a name="define-variables"></a>Definire le variabili
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>creare una sottoscrizione evento usando l'identità gestita per il recapito 
Questo comando di esempio crea una sottoscrizione evento per un argomento di Griglia di eventi con il tipo di endpoint impostato su **Hub eventi**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Creare una sottoscrizione evento usando l'identità gestita per il recapito e l'inserimento nella coda di messaggi non recapitabili 
Questo comando di esempio crea una sottoscrizione evento per un argomento di Griglia di eventi con il tipo di endpoint impostato su **Hub eventi**. Specifica inoltre che l'identità gestita dal sistema deve essere usata anche per i messaggi non recapitabili. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Interfaccia della riga di comando di Azure - Coda di Archiviazione di Azure 
Questa sezione illustra come usare l'**interfaccia della riga di comando di Azure** per abilitare l'utilizzo dell'identità assegnata dal sistema per recapitare gli eventi a una coda di Archiviazione di Azure. L'identità deve essere un membro di **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione.

#### <a name="define-variables"></a>Definire le variabili  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Creare una sottoscrizione evento usando l'identità gestita per il recapito 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Creare una sottoscrizione evento usando l'identità gestita per il recapito e l'inserimento nella coda di messaggi non recapitabili 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle identità del servizio gestito, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). 