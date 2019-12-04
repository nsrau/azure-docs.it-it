---
title: Connettersi all'archivio BLOB di Azure
description: Creare e gestire BLOB negli account di archiviazione di Azure usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/28/2019
tags: connectors
ms.openlocfilehash: 86e8415cf2076819e23226e5e7878a2c96343f69
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789914"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Creare e gestire i BLOB nell'archiviazione BLOB di Azure usando app per la logica di Azure

Questo articolo illustra come accedere ai file archiviati come BLOB nell'account di archiviazione di Azure da un'app per la logica e come gestire tali file con il connettore di Archiviazione BLOB di Azure. In questo modo, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per la gestione dei file. È ad esempio possibile compilare app per la logica che creano, ottengono, aggiornano ed eliminano file nell'account di archiviazione.

Si supponga di avere uno strumento che viene aggiornato in un sito Web di Azure. L'evento funge da trigger per l'app per la logica. Quando l'evento si verifica, è possibile impostare l'app per la logica per aggiornare alcuni file nel contenitore di archiviazione BLOB, eseguendo un'azione nell'app per la logica.

Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, vedere le [informazioni di riferimento sul connettore di Archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Per abilitare l'accesso dalle app per la logica di Azure agli account di archiviazione dietro ai firewall, vedere la sezione [accedere agli account di archiviazione dietro ai firewall](#storage-firewalls) più avanti in questo argomento.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>limiti

* Per impostazione predefinita, le azioni di archiviazione BLOB di Azure possono leggere o scrivere file di *dimensioni di 50 MB o inferiori*. Per gestire file di dimensioni superiori a 50 MB ma fino a 1024 MB, le azioni di archiviazione BLOB di Azure supportano la [suddivisione in blocchi dei messaggi](../logic-apps/logic-apps-handle-large-messages.md). L'azione **Ottieni contenuto BLOB** utilizza in modo implicito la suddivisione in blocchi.

* I trigger di archiviazione BLOB di Azure non supportano la suddivisione in blocchi. Quando si richiede il contenuto del file, i trigger selezionano solo i file di 50 MB o inferiori. Per recuperare file di dimensione superiore a 50 MB, seguire questo modello:

  * Usare un trigger di archiviazione BLOB di Azure che restituisce le proprietà del file, ad esempio **quando un BLOB viene aggiunto o modificato (solo proprietà)** .

  * Seguire il trigger con l'azione **Ottieni contenuto BLOB** di archiviazione BLOB di Azure, che legge il file completo e USA in modo implicito la suddivisione in blocchi.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di archiviazione di Azure e un contenitore di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md)

* L'app per la logica da cui accedere all'account di archiviazione BLOB di Azure. Per avviare l'app per la logica con un trigger di Archiviazione BLOB di Azure, è necessaria un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Aggiungere un trigger di archiviazione BLOB

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

Questo esempio illustra come avviare un flusso di lavoro di un'app per la logica con il trigger **quando un BLOB viene aggiunto o modificato (solo proprietà)** quando si aggiungono o si aggiornano le proprietà di un BLOB nel contenitore di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica vuota, che apre progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nella casella di ricerca immettere "BLOB di Azure" come filtro. Nell'elenco di trigger selezionare il trigger desiderato.

   Questo esempio usa questo trigger: **quando un BLOB viene aggiunto o modificato (solo proprietà)**

   ![Selezionare il trigger di archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se vengono chiesti i dati della connessione, [creare la connessione all'archiviazione BLOB](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per il trigger.

   Per questo esempio, selezionare il contenitore e la cartella da monitorare.

   1. Nella casella **Contenitore** selezionare l'icona di cartella.

   2. Nell'elenco delle cartelle scegliere la parentesi uncinata chiusa ( **>** ) e quindi individuare e selezionare la cartella desiderata.

      ![Selezionare la cartella di archiviazione da usare con il trigger](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selezionare la frequenza e l'intervallo in base a cui si vuole che il trigger controlli le modifiche alla cartella.

4. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

5. Continuare ad aggiungere una o più azioni all'app per la logica per le attività da eseguire con i risultati del trigger.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Aggiungere un'azione di archiviazione BLOB

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio l'app per la logica viene avviata con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md).

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). Questo esempio usa il portale di Azure.

2. Nella finestra di progettazione dell'app per la logica, sotto il trigger o l'azione, scegliere **nuovo passaggio**.

   ![Aggiungi nuovo passaggio al flusso di lavoro dell'app per la logica](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Scegliere il segno più ( **+** ) visualizzato e selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "BLOB di Azure" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

   Questo esempio usa questa azione: **Ottieni contenuto BLOB**

   ![Selezionare l'azione di archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se vengono chiesti i dati della connessione, [creare la connessione ad Archiviazione BLOB di Azure](#create-connection).
Se la connessione è già presente, fornire le informazioni necessarie per l'azione.

   Per questo esempio, selezionare il file desiderato.

   1. Nella casella **BLOB** selezionare l'icona di cartella.
  
      ![Selezionare la cartella di archiviazione da usare con l'azione](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Trovare e selezionare il file desiderato in base al numero **ID** del BLOB. È possibile trovare questo numero **ID** nei metadati del BLOB restituiti dal trigger di archiviazione BLOB descritto in precedenza.

5. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.
Per testare l'app per la logica, verificare che la cartella selezionata contenga un BLOB.

Questo esempio ottiene solo i contenuti di un BLOB. Per visualizzare i contenuti, aggiungere un'altra azione che crea un file con il BLOB usando un altro connettore. Aggiungere, ad esempio, un'azione di OneDrive che crea un file in base ai contenuti del BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Eseguire la connessione all'account di archiviazione

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando viene richiesto di creare la connessione, fornire le seguenti informazioni:

   | Proprietà | Obbligatoria | Value | Description |
   |----------|----------|-------|-------------|
   | **Connection Name** (Nome connessione) | SÌ | <*nome connessione*> | Nome creato per la connessione |
   | **Storage Account** | SÌ | <*storage-account*> | Selezionare l'account di archiviazione dall'elenco. |
   ||||

   ad esempio:

   ![Creare una connessione all'account di archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png)  

1. Quando si è pronti, selezionare **Crea**

1. Dopo aver creato la connessione, continuare con [Aggiungi trigger di archiviazione BLOB](#add-trigger) o [Aggiungi un'azione di archiviazione BLOB](#add-action).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file Open API (in precedenza spavalderia) del connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/azureblobconnector/).

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Accedere agli account di archiviazione dietro i firewall

È possibile aggiungere la sicurezza di rete a un account di archiviazione di Azure limitando l'accesso con [regole firewall e firewall](../storage/common/storage-network-security.md). Tuttavia, questa configurazione crea un problema per Azure e altri servizi Microsoft che necessitano dell'accesso all'account di archiviazione. La comunicazione locale nel Data Center astrae gli indirizzi IP interni, quindi non è possibile configurare le regole del firewall con restrizioni IP. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../storage/common/storage-network-security.md).

Ecco diverse opzioni per accedere agli account di archiviazione dietro i firewall da app per la logica di Azure usando il connettore di archiviazione BLOB di Azure o altre soluzioni:

* Connettore BLOB di archiviazione di Azure

  * [Accedere agli account di archiviazione in altre aree](#access-other-regions)
  * [Accedere agli account di archiviazione tramite una rete virtuale attendibile](#access-trusted-virtual-network)

* Altre soluzioni

  * [Accedere agli account di archiviazione come servizi attendibili con identità gestite](#access-trusted-service)
  * [Accedere agli account di archiviazione tramite gestione API di Azure](#access-api-management)

<a name="access-other-regions"></a>

### <a name="access-to-storage-accounts-in-other-regions"></a>Accesso agli account di archiviazione in altre aree

Le app per la logica non possono accedere direttamente agli account di archiviazione con regole del firewall e si trovano nella stessa area. Tuttavia, se si consente l'accesso per gli [indirizzi IP in uscita per i connettori gestiti nella propria area](../logic-apps/logic-apps-limits-and-config.md#outbound), le app per la logica possono accedere agli account di archiviazione in un'area diversa tranne quando si usa il connettore di archiviazione tabelle di Azure o il connettore di archiviazione code di Azure. Per accedere all'archiviazione tabelle o all'archiviazione code, è comunque possibile usare il trigger HTTP predefinito e le azioni.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Accedere agli account di archiviazione tramite una rete virtuale attendibile

È possibile inserire l'account di archiviazione in una rete virtuale di Azure gestita e quindi aggiungere la rete virtuale all'elenco reti virtuali attendibili. Per fare in modo che l'app per la logica acceda all'account di archiviazione tramite una [rete virtuale attendibile](../virtual-network/virtual-networks-overview.md), è necessario distribuire tale app per la logica a [Integration Services Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), che può connettersi alle risorse in una rete virtuale. È quindi possibile aggiungere le subnet in tale ISE all'elenco attendibile. I connettori di archiviazione di Azure, ad esempio il connettore di archiviazione BLOB, possono accedere direttamente al contenitore di archiviazione. Questa configurazione ha la stessa esperienza dell'uso degli endpoint di servizio di ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Accedere agli account di archiviazione come servizi attendibili con identità gestite

Per concedere ai servizi attendibili Microsoft l'accesso a un account di archiviazione tramite un firewall, è possibile configurare un'eccezione in tale account di archiviazione per tali servizi. Questa soluzione consente ai servizi di Azure che supportano [identità gestite di eseguire l'autenticazione](../active-directory/managed-identities-azure-resources/overview.md) per accedere agli account di archiviazione protetti da firewall come servizi attendibili. In particolare, per un'app per la logica in Azure multi-tenant globale per accedere a questi account di archiviazione, è necessario abilitare prima il supporto per le [identità gestite](../logic-apps/create-managed-service-identity.md) nell'app per la logica. Si usa quindi l'azione o il trigger HTTP nell'app per la logica e si [imposta il tipo di autenticazione per usare l'identità gestita dell'app per la logica](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Per questo scenario, è possibile usare *solo* l'azione o il trigger http.

Per configurare l'eccezione e il supporto dell'identità gestita, attenersi alla procedura generale seguente:

1. Nell'account di archiviazione, in **Impostazioni**, selezionare **firewall e reti virtuali**. In **Consenti accesso da**selezionare l'opzione **reti selezionate** in modo che vengano visualizzate le impostazioni correlate.

1. In **eccezioni**selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**e quindi selezionare **Salva**.

   ![Selezionare l'eccezione che consente servizi attendibili Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nelle impostazioni dell'app per la logica, [abilitare il supporto per l'identità gestita](../logic-apps/create-managed-service-identity.md).

1. Nel flusso di lavoro dell'app per la logica aggiungere e configurare l'azione o il trigger HTTP per accedere all'entità o all'account di archiviazione.

   > [!IMPORTANT]
   > Per le chiamate all'azione HTTP in uscita o al trigger per gli account di archiviazione di Azure, assicurarsi che l'intestazione della richiesta includa la proprietà `x-ms-version` e la versione dell'API per l'operazione che si vuole eseguire nell'account di archiviazione. Per altre informazioni, vedere [autenticare l'accesso con l'identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) e il [controllo delle versioni per i servizi di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. In tale azione [selezionare l'identità gestita](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) da usare per l'autenticazione.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Accedere agli account di archiviazione tramite gestione API di Azure

Se si usa un livello dedicato per [gestione API](../api-management/api-management-key-concepts.md), è possibile passare all'API di archiviazione usando gestione API e consentire gli indirizzi IP di quest'ultimo attraverso il firewall. In sostanza, aggiungere la rete virtuale di Azure usata da gestione API all'impostazione del firewall dell'account di archiviazione. È quindi possibile usare l'azione gestione API o l'azione HTTP per chiamare le API di archiviazione di Azure. Tuttavia, se si sceglie questa opzione, è necessario gestire autonomamente il processo di autenticazione. Per altre informazioni, vedere [Architettura di integrazione aziendale semplice](https://aka.ms/aisarch).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
