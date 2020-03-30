---
title: Connettersi ad Archiviazione BLOB di Azure
description: Creare e gestire BLOB negli account di archiviazione di Azure usando le app per la logica di AzureCreate and manage blobs in Azure storage accounts by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247358"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Creare e gestire BLOB in Archiviazione BLOB di Azure usando le app per la logica di AzureCreate and manage blobs in Azure Blob Storage by using Azure Logic Apps

Questo articolo illustra come accedere ai file archiviati come BLOB nell'account di archiviazione di Azure da un'app per la logica e come gestire tali file con il connettore di Archiviazione BLOB di Azure. In questo modo, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per la gestione dei file. È ad esempio possibile compilare app per la logica che creano, ottengono, aggiornano ed eliminano file nell'account di archiviazione.

Si supponga di avere uno strumento che viene aggiornato in un sito Web di Azure. L'evento funge da trigger per l'app per la logica. Quando l'evento si verifica, è possibile impostare l'app per la logica per aggiornare alcuni file nel contenitore di archiviazione BLOB, eseguendo un'azione nell'app per la logica.

Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, vedere le [informazioni di riferimento sul connettore di Archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Le app per la logica non possono accedere direttamente agli account di archiviazione che si trovano dietro i firewall se si trovano entrambi nella stessa area. In alternativa, è possibile avere le app per la logica e l'account di archiviazione in aree diverse. Per altre informazioni sull'abilitazione dell'accesso dalle app per la logica di Azure agli account di archiviazione protetti da firewall, vedere la sezione [Accesso agli account di archiviazione dietro i firewall](#storage-firewalls) più avanti in questo argomento.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limiti

* Per impostazione predefinita, le azioni di Archiviazione BLOB di Azure possono leggere o scrivere file di dimensioni pari o inferiori a *50 MB.* Per gestire file di dimensioni superiori a 50 MB ma fino a 1024 MB, le azioni di Archiviazione BLOB di Azure supportano la [suddivisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md)dei messaggi. L'azione **Ottieni contenuto BLOB** usa in modo implicito la suddivisione in blocchi.

* I trigger di Archiviazione BLOB di Azure non supportano la suddivisione in blocchi. Quando si richiede il contenuto del file, i trigger selezionano solo i file di dimensioni pari o inferiori a 50 MB. Per recuperare file di dimensione superiore a 50 MB, seguire questo modello:

  * Usare un trigger di Archiviazione BLOB di Azure che restituisce proprietà di file, ad esempio **Quando un BLOB viene aggiunto o modificato (solo proprietà).**

  * Seguire il trigger con l'azione **Ottieni contenuto BLOB** di Archiviazione BLOB di Azure, che legge il file completo e usa in modo implicito la suddivisione in blocchi.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di archiviazione di Azure e un contenitore di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md)

* L'app per la logica da cui accedere all'account di archiviazione BLOB di Azure. Per avviare l'app per la logica con un trigger di Archiviazione BLOB di Azure, è necessaria un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Aggiungere un trigger di archiviazione BLOB

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

Questo esempio mostra come avviare un flusso di lavoro dell'app per la logica con il trigger **Quando un BLOB viene aggiunto o modificato (solo proprietà)** quando le proprietà di un BLOB vengono aggiunte o aggiornate nel contenitore di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica vuota che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nella casella di ricerca immettere "BLOB di Azure" come filtro. Nell'elenco di trigger selezionare il trigger desiderato.

   Questo esempio usa questo trigger: **quando un BLOB viene aggiunto o modificato (solo proprietà)This example uses this trigger: When a blob is added or modified (properties only)**

   ![Selezionare il trigger di Archiviazione BLOB di AzureSelect Azure Blob Storage trigger](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se vengono chiesti i dati della connessione, [creare la connessione all'archiviazione BLOB](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per il trigger.

   Per questo esempio, selezionare il contenitore e la cartella da monitorare.

   1. Nella casella **Contenitore** selezionare l'icona di cartella.

   2. Nell'elenco delle cartelle scegliere la **>** parentesi angolare destra ( ), quindi individuare e selezionare la cartella desiderata.

      ![Selezionare la cartella di archiviazione da utilizzare con il triggerSelect storage folder to use with trigger](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selezionare la frequenza e l'intervallo in base a cui si vuole che il trigger controlli le modifiche alla cartella.

4. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

5. Continuare ad aggiungere una o più azioni all'app per la logica per le attività da eseguire con i risultati del trigger.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Aggiungere un'azione di archiviazione BLOB

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio l'app per la logica viene avviata con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md).

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). Questo esempio usa il portale di Azure.

2. In Progettazione app per la logica scegliere **Nuovo passaggio**sotto il trigger o l'azione .

   ![Aggiungere un nuovo passaggio al flusso di lavoro dell'app per la logicaAdd new step to logic app workflow](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Scegliere il segno**+** più ( ) visualizzato e selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "BLOB di Azure" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

   Questo esempio usa questa azione: **Get blob content**

   ![Selezionare l'azione Archiviazione BLOB di AzureSelect Azure Blob Storage action](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se vengono chiesti i dati della connessione, [creare la connessione ad Archiviazione BLOB di Azure](#create-connection).
Se la connessione è già presente, fornire le informazioni necessarie per l'azione.

   Per questo esempio, selezionare il file desiderato.

   1. Nella casella **BLOB** selezionare l'icona di cartella.
  
      ![Selezionare la cartella di archiviazione da utilizzare con l'azione](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Trovare e selezionare il file desiderato in base al numero **ID** del BLOB. È possibile trovare questo numero **di ID** nei metadati del BLOB restituiti dal trigger di archiviazione BLOB descritto in precedenza.

5. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.
Per testare l'app per la logica, verificare che la cartella selezionata contenga un BLOB.

Questo esempio ottiene solo i contenuti di un BLOB. Per visualizzare i contenuti, aggiungere un'altra azione che crea un file con il BLOB usando un altro connettore. Aggiungere, ad esempio, un'azione di OneDrive che crea un file in base ai contenuti del BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Eseguire la connessione all'account di archiviazione

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando viene richiesto di creare la connessione, fornire queste informazioni:When you're prompted to created the connection, provide this information:

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome connessione** | Sì | <*nome della connessione*> | Nome creato per la connessione |
   | **Account di archiviazione** | Sì | <*account di archiviazione*> | Selezionare l'account di archiviazione dall'elenco. |
   ||||

   Ad esempio:

   ![Creare la connessione dell'account di archiviazione BLOB di AzureCreate Azure Blob storage account connection](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Quando sei pronto, seleziona **Crea**

1. Dopo aver creato la connessione, continuare con [Il trigger di archiviazione BLOB](#add-trigger) O [l'azione Aggiungi archiviazione BLOB.](#add-action)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/azureblobconnector/)

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Accedere agli account di archiviazione protetti da firewallAccess storage accounts behind firewalls

È possibile aggiungere la sicurezza di rete a un account di archiviazione di Azure limitando l'accesso con regole del firewall e del [firewall.](../storage/common/storage-network-security.md) Tuttavia, questa configurazione crea una sfida per Azure e altri servizi Microsoft che devono accedere all'account di archiviazione. La comunicazione locale nel data center astrae gli indirizzi IP interni, pertanto non è possibile impostare regole del firewall con restrizioni IP. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../storage/common/storage-network-security.md).

Ecco varie opzioni per accedere agli account di archiviazione tramite firewall da App per la logica di Azure usando il connettore di archiviazione BLOB di Azure o altre soluzioni:Here are various options for accessing storage accounts behind firewalls from Azure Logic Apps by using either the Azure Blob Storage connector or other solutions:

* Connettore BLOB di archiviazione di Azure

  * [Accedere agli account di archiviazione in altre areeAccess storage accounts in other regions](#access-other-regions)
  * [Accedere agli account di archiviazione tramite una rete virtuale attendibileAccess storage accounts through a trusted virtual network](#access-trusted-virtual-network)

* Altre soluzioni

  * [Accedere agli account di archiviazione come servizio attendibile con identità gestiteAccess storage accounts as a trusted service with managed identities](#access-trusted-service)
  * [Accedere agli account di archiviazione tramite Gestione API di AzureAccess storage accounts through Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemi di accesso agli account di archiviazione nella stessa area

Le app per la logica non possono accedere direttamente agli account di archiviazione tramite firewall quando si trovano entrambe nella stessa area. Per risolvere il problema, inserire le app per la logica in un'area diversa dall'account di archiviazione e concedere l'accesso agli [indirizzi IP in uscita per i connettori gestiti nell'area.](../logic-apps/logic-apps-limits-and-config.md#outbound)

> [!NOTE]
> Questa soluzione non si applica al connettore Archiviazione tabelle di Azure e al connettore Archiviazione code di Azure.This solution doesn't apply to the Azure Table Storage connector and Azure Queue Storage connector. Al contrario, per accedere all'archiviazione tabelle o all'archiviazione delle code, usare il trigger HTTP incorporato e le azioni.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Accedere agli account di archiviazione tramite una rete virtuale attendibileAccess storage accounts through a trusted virtual network

È possibile inserire l'account di archiviazione in una rete virtuale di Azure gestita e quindi aggiungere tale rete virtuale all'elenco delle reti virtuali attendibili. Per fare in modo che l'app per la logica acceda all'account di archiviazione tramite una [rete virtuale attendibile,](../virtual-network/virtual-networks-overview.md)è necessario distribuire tale app per la logica in un ambiente del [servizio di integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), che può connettersi alle risorse in una rete virtuale. È quindi possibile aggiungere le subnet in tale ISE all'elenco attendibile. I connettori di Archiviazione di Azure, ad esempio il connettore di archiviazione BLOB, possono accedere direttamente al contenitore di archiviazione. Questa configurazione è la stessa esperienza dell'utilizzo degli endpoint del servizio da un ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Accedere agli account di archiviazione come servizio attendibile con identità gestiteAccess storage accounts as a trusted service with managed identities

Per concedere ai servizi attendibili Microsoft l'accesso a un account di archiviazione tramite un firewall, è possibile configurare un'eccezione su tale account di archiviazione per tali servizi. Questa soluzione consente ai servizi di Azure che supportano [le identità gestite per l'autenticazione di](../active-directory/managed-identities-azure-resources/overview.md) accedere agli account di archiviazione dietro i firewall come servizi attendibili. In particolare, per un'app per la logica in Azure multi-tenant globale per accedere a questi account di archiviazione, è necessario innanzitutto abilitare il [supporto dell'identità gestita](../logic-apps/create-managed-service-identity.md) nell'app per la logica. Quindi, usare l'azione HTTP o il trigger nell'app per la logica e [impostarne il tipo di autenticazione per l'utilizzo dell'identità gestita dell'app per la logica.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) Per questo scenario, è possibile utilizzare *solo* l'azione HTTP o trigger.

Per impostare l'eccezione e il supporto delle identità gestite, attenersi alla seguente procedura generale:

1. Nell'account di archiviazione, in **Impostazioni**selezionare **Firewall e reti virtuali.** In **Consenti accesso da**selezionare l'opzione Reti **selezionate** in modo che vengano visualizzate le impostazioni correlate.

1. In **Eccezioni**selezionare Consenti **ai servizi Microsoft attendibili di accedere a questo account di archiviazione**e quindi selezionare **Salva**.

   ![Selezionare un'eccezione che consente i servizi attendibili Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nelle impostazioni dell'app per la logica [abilitare il supporto per l'identità gestita.](../logic-apps/create-managed-service-identity.md)

1. Nel flusso di lavoro dell'app per la logica aggiungere e configurare l'azione o il trigger HTTP per accedere all'account di archiviazione o all'entità.

   > [!IMPORTANT]
   > Per le chiamate di azione o trigger HTTP in uscita `x-ms-version` agli account di archiviazione di Azure, assicurarsi che l'intestazione della richiesta includa la proprietà e la versione dell'API per l'operazione che si vuole eseguire nell'account di archiviazione. Per altre informazioni, vedere [Autenticare l'accesso con l'identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) e Controllo delle versioni per i servizi di archiviazione di Azure.For more information, see Authenticate access with managed identity and [Versioning for Azure Storage services.](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)

1. Su tale [azione, selezionare l'identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) da utilizzare per l'autenticazione.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Accedere agli account di archiviazione tramite Gestione API di AzureAccess storage accounts through Azure API Management

Se si utilizza un livello dedicato per [Gestione API](../api-management/api-management-key-concepts.md), è possibile eseguire il front-the Storage API utilizzando Gestione API e consentendo gli indirizzi IP di quest'ultimo attraverso il firewall. Fondamentalmente, aggiungere la rete virtuale di Azure usata da Gestione API all'impostazione del firewall dell'account di archiviazione. È quindi possibile usare l'azione Gestione API o l'azione HTTP per chiamare le API di Archiviazione di Azure.You can then use either the API Management action or the HTTP action to call the Azure Storage APIs. Tuttavia, se si sceglie questa opzione, è necessario gestire manualmente il processo di autenticazione. Per altre informazioni, vedere [Architettura di integrazione aziendale semplice](https://aka.ms/aisarch).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
