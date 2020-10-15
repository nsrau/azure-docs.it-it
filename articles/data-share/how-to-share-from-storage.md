---
title: Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage
description: Informazioni su come condividere e ricevere dati dall'archiviazione BLOB di Azure e Azure Data Lake Storage
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: a54a9d4c50852fe78fd245723dd29f487d58f4b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89270122"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Condividere e ricevere dati da Archiviazione BLOB di Azure e Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

La condivisione di dati di Azure supporta la condivisione basata su snapshot dall'account di archiviazione. Questo articolo illustra come condividere e ricevere dati dalle origini seguenti: archiviazione BLOB di Azure, Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2.

Condivisione dati di Azure supporta la condivisione di file, cartelle e file System da Azure Data Lake Gen1 e Azure Data Lake Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori dall'archiviazione BLOB di Azure. Attualmente è supportato solo il BLOB in blocchi. I dati condivisi da queste origini possono essere ricevuti in Azure Data Lake Gen2 o nell'archiviazione BLOB di Azure.

Quando i file System, i contenitori o le cartelle sono condivisi nella condivisione basata su snapshot, il consumer di dati può scegliere di eseguire una copia completa dei dati di condivisione o sfruttare la funzionalità di snapshot incrementale per copiare solo i file nuovi o aggiornati. Gli snapshot incrementali si basano sull'ora dell'Ultima modifica dei file. I file esistenti con lo stesso nome verranno sovrascritti.

## <a name="share-data"></a>Condividere dati

### <a name="prerequisites-to-share-data"></a>Prerequisiti per la condivisione di dati

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Indirizzo di posta elettronica per l'accesso ad Azure dei destinatari (gli alias di posta elettronica non funzionano).
* Se l'archivio dati di Azure di origine si trova in una sottoscrizione di Azure diversa da quella che verrà usata per creare la risorsa Condivisione dati, registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione in cui risiede l'archivio dati di Azure. 

### <a name="prerequisites-for-source-storage-account"></a>Prerequisiti per l'account di archiviazione di origine

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo Collaboratore.
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo Proprietario. 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Selezionare il pulsante del menu nell'angolo superiore sinistro del portale e quindi selezionare **+ Crea una risorsa**.

1. Cercare *Condivisione dati*.

1. Selezionare Condivisione dati e quindi **Crea**.

1. Compilare i dettagli di base della risorsa di condivisione dati di Azure con le informazioni seguenti. 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'account di condivisione dati.|
    | Gruppo di risorse | *test-resource-group* | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Posizione | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | Nome | *datashareaccount* | Specificare un nome per l'account di condivisione dati. |
    | | |

1. Selezionare **Rivedi e crea**, quindi **Crea** per effettuare il provisioning dell'account di condivisione dati. Il completamento del provisioning di un nuovo account di condivisione dati in genere impiega al massimo 2 minuti. 

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

### <a name="create-a-share"></a>Creare una condivisione

1. Passare alla pagina di panoramica della condivisione dati.

    ![Condividere i dati](./media/share-receive-data.png "Condividere i dati") 

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Crea**.   

1. Immettere i dettagli della condivisione. Specificare un nome, un tipo di condivisione, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![Immettere i dettagli della condivisione](./media/enter-share-details.png "Immettere i dettagli della condivisione") 

1. Selezionare **Continua**.

1. Per aggiungere set di dati alla condivisione, selezionare **Aggiungi set di dati**. 

    ![Aggiungere set di dati alla condivisione](./media/datasets.png "Set di dati")

1. Selezionare il tipo di set di dati da aggiungere. Verrà visualizzato un elenco di tipi di set di dati diverso a seconda del tipo di condivisione (snapshot o sul posto) selezionato nel passaggio precedente. 

    ![Aggiungere i set di dati](./media/add-datasets.png "Aggiungere i set di dati")    

1. Passare all'oggetto che si vuole condividere e selezionare "Add Datasets" (Aggiungi set di dati). 

    ![Selezionare i set di dati](./media/select-datasets.png "Selezionare i set di dati")    

1. Nella scheda Destinatari immettere l'indirizzo di posta elettronica del consumer di dati selezionando "+ Aggiungi il destinatario". 

    ![Aggiungere i destinatari](./media/add-recipient.png "Aggiungere destinatari") 

1. Selezionare **Continua**.

1. Se è stato selezionato tipo di condivisione snapshot, è possibile configurare la pianificazione degli snapshot per fornire aggiornamenti dei dati al consumer di dati. 

    ![Abilitare gli snapshot](./media/enable-snapshots.png "Abilitare gli snapshot") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**.

1. Nella scheda Rivedi e crea verificare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Selezionare **Crea**.

La condivisione dati di Azure è stata creata e il suo destinatario è ora pronto ad accettare l'invito. 

## <a name="receive-data"></a>Ricevere dati

### <a name="prerequisites-to-receive-data"></a>Prerequisiti per la ricezione dei dati
Prima di accettare un invito alla condivisione dei dati, è necessario effettuare il provisioning di una serie di risorse di Azure, elencate di seguito. 

Assicurarsi che tutti i prerequisiti siano soddisfatti prima di accettare un invito alla condivisione dei dati. 

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un invito alla Condivisione dati: un invito da Microsoft Azure con un oggetto denominato "Azure Data Share invitation from **<yourdataprovider@domain.com>** " (Invito alla Condivisione dati di Azure da...).
* Registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione di Azure verrà creata una risorsa Condivisione dati e nella sottoscrizione di Azure in cui si trovano gli archivi dati di Azure di destinazione.

### <a name="prerequisites-for-target-storage-account"></a>Prerequisiti per l'account di archiviazione di destinazione

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo Proprietario.  

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="open-invitation"></a>Aprire l'invito

1. È possibile aprire l'invito da un messaggio di posta elettronica o direttamente dal portale di Azure. 

   Per aprire l'invito da un messaggio di posta elettronica, controllare la posta in arrivo per verificare di aver ricevuto un invito dal provider di dati. L'invito proviene da Microsoft Azure con l'oggetto **Azure Data Share invitation from<yourdataprovider@domain.com>** (Invito alla Condivisione dati di Azure da...). Fare clic su **Visualizza invito** per visualizzare l'invito in Azure. 

   Per aprire direttamente l'invito dal portale di Azure, cercare **Inviti alla condivisione dati** nel portale di Azure. In questo modo viene visualizzato l'elenco di inviti alla condivisione dati.

   ![Elenco di inviti](./media/invitations.png "Elenco di inviti") 

1. Selezionare la condivisione che si vuole visualizzare. 

### <a name="accept-invitation"></a>Accettare l'invito
1. Assicurarsi di rivedere tutti i campi, tra cui le **condizioni per l'utilizzo**. Se si accettano le condizioni per l'utilizzo, verrà richiesto di selezionare la casella per confermare l'accettazione. 

   ![Condizioni d'uso](./media/terms-of-use.png "Condizioni per l'utilizzo") 

1. In *Target Data Share Account* (Account di condivisione dati di destinazione), selezionare la sottoscrizione e il gruppo di risorse in cui verrà distribuita la condivisione dati. 

   Nel campo **Data Share Account** (Account di condivisione dati), selezionare **Create new** (Crea nuovo) se non si ha un account di Condivisione dati esistente. In caso contrario, selezionare un account esistente di Condivisione dati in cui si vuole accettare la condivisione dei dati. 

   Nel campo **Received Share Name** (Nome condivisione ricevuta) si può lasciare il valore predefinito specificato dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

   Dopo aver accettato le condizioni per l'utilizzo e specificato un account Condivisione dati per gestire la condivisione ricevuta, selezionare **Accept and Configure** (Accetta e configura). Verrà creata una nuova sottoscrizione di condivisione. 

   ![Opzioni di accettazione](./media/accept-options.png "Opzioni di accettazione") 

   Si verrà reindirizzati alla condivisione ricevuta nell'account Condivisione dati. 

   Se non si vuole accettare l'invito, selezionare *Reject* (Rifiuta). 

### <a name="configure-received-share"></a>Configurare la condivisione ricevuta
Seguire questa procedura per configurare la posizione in cui si desidera ricevere i dati.

1. Selezionare la scheda **Set di dati**. Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **+ Esegui mapping alla destinazione** per scegliere un archivio dati di destinazione. 

   ![Esegui mapping alla destinazione](./media/dataset-map-target.png "Esegui mapping alla destinazione") 

1. Selezionare un archivio dati di destinazione in cui si vogliono inserire i dati. Eventuali file di dati nell'archivio dati di destinazione con lo stesso percorso e nome verranno sovrascritti. 

   ![Account di archiviazione di destinazione](./media/map-target.png "Archiviazione di destinazione") 

1. Per la condivisione basata su snapshot, se il provider di dati ha creato una pianificazione degli snapshot per fornire aggiornamenti regolari ai dati, è anche possibile abilitare la pianificazione degli snapshot selezionando la scheda **Pianificazione degli snapshot**. Selezionare la casella accanto alla pianificazione degli snapshot e selezionare **+ Abilita**.

   ![Abilitare la pianificazione degli snapshot](./media/enable-snapshot-schedule.png "Abilitare la pianificazione degli snapshot")

### <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot
Questa procedura si applica solo alla condivisione basata su snapshot.

1. È possibile attivare uno snapshot selezionando la scheda **Dettagli** e quindi **Attiva snapshot**. Qui è possibile attivare uno snapshot completo o incrementale dei dati. Se è la prima volta che si ricevono i dati dal provider, selezionare Full copy (Copia completa). 

   ![Attivazione dello snapshot](./media/trigger-snapshot.png "Attivazione dello snapshot") 

1. Quando l'ultimo stato di esecuzione è *riuscito*, passare all'archivio dati di destinazione per visualizzare i dati ricevuti. Selezionare **Set di dati** e fare clic sul collegamento nel percorso di destinazione. 

   ![Set di dati consumer](./media/consumer-datasets.png "Mapping dei set di dati consumer") 

### <a name="view-history"></a>Visualizzare la cronologia
Questo passaggio si applica solo alla condivisione basata su snapshot. Per visualizzare la cronologia degli snapshot, selezionare la scheda **Cronologia**. Qui è presente una cronologia di tutti gli snapshot generati negli ultimi 30 giorni. 

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come condividere e ricevere dati dall'account di archiviazione usando il servizio di condivisione dati di Azure. Per ulteriori informazioni sulla condivisione da altre origini dati, continuare con gli [archivi dati supportati](supported-data-stores.md).

