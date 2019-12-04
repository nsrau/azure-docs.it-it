---
title: Convalidare XML con schemi
description: Aggiungere schemi per convalidare i documenti XML in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 6cde620b4949da8a6cff4ad89a863c80f0514f1c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792396"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Convalida XML con gli schemi in App per la logica di Azure con Enterprise Integration Pack

Per controllare che i documenti usino codice XML valido e contengano i dati previsti nel formato predefinito per gli scenari di integrazione aziendale di App per la logica di Azure, l'app per la logica può usare schemi. Uno schema consente anche di convalidare i messaggi scambiati dalle app per la logica all'interno di scenari Business to Business (B2B).

Per i limiti relativi agli account di integrazione e agli artefatti, ad esempio gli schemi, vedere [Informazioni su limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) in cui archiviare gli schemi e gli altri artefatti per l'integrazione aziendale e le soluzioni B2B (Business to Business). 

  Se le dimensioni dello schema corrispondono a [2 MB al massimo](#smaller-schema), è possibile aggiungere lo schema all'account di integrazione direttamente dal portale di Azure. Se tuttavia lo schema è di dimensioni superiori a 2 MB, ma non è maggiore del [limite delle dimensioni dello schema](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), è possibile caricarlo in un account di archiviazione di Azure. 
  Per aggiungere lo schema all'account di integrazione, è quindi possibile creare un collegamento all'account di archiviazione dall'account di integrazione. 
  Per questa attività, ecco gli elementi necessari: 

  * Un [account di archiviazione di Azure](../storage/common/storage-account-overview.md) in cui creare un contenitore BLOB per lo schema. Informazioni su come [creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md). 

  * Un contenitore BLOB per l'archiviazione dello schema. Informazioni su come [creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). 
  L'URI del contenuto del contenitore è necessario in un secondo momento, quando si aggiunge lo schema all'account di integrazione.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), che è possibile usare per la gestione degli account di archiviazione e dei contenitori BLOB. 
  Per usare Storage Explorer, scegliere una di queste opzioni:
  
    * Nel portale di Azure trovare e selezionare l'account di archiviazione. 
    Dal menu dell'account di archiviazione selezionare **Storage Explorer**.

    * Per la versione desktop, [scaricare e installare Azure Storage Explorer](https://www.storageexplorer.com/), 
    quindi connettere Storage Explorer all'account di archiviazione seguendo i passaggi illustrati in [Introduzione a Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Per altre informazioni, vedere [Guida introduttiva: creare un BLOB nell'archivio oggetti con Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Quando si creano e si aggiungono schemi, non è necessaria un'app per la logica. Per usare uno schema, tuttavia, l'app per la logica deve essere collegata all'account di integrazione in cui si archivia tale schema. Informazioni su come [collegare le app per la logica agli account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se si ha ancora un'app per la logica, vedere l'articolo su [come creare app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Aggiungere schemi

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> con le credenziali dell'account Azure.

1. Per trovare e aprire l'account di integrazione, nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca, digitare "account di integrazione". Selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selezionare l'account di integrazione a cui si vuole aggiungere lo schema, ad esempio:

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Schemi**.

   ![Selezionare "Schemi"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Quando la pagina **Schemi** è aperta, scegliere **Aggiungi**.

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

In base alle dimensioni del file di schema (con estensione xsd), seguire i passaggi per caricare uno schema di dimensioni [pari o inferiori a 2 MB](#smaller-schema) oppure [comprese tra 2 MB e 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Aggiungere schemi fino a 2 MB

1. In **Aggiungi schema** immettere un nome per lo schema. 
   Mantenere selezionato **File piccolo**. Accanto alla casella **Schema** scegliere l'icona della cartella. Trovare e selezionare lo schema da caricare, ad esempio:

   ![Caricare uno schema più piccolo](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Al termine, scegliere **OK**.

   Al termine del caricamento dello schema, lo schema viene visualizzato nell'elenco **Schemi**.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Aggiungere schemi con dimensioni maggiori di 2 MB

Per aggiungere schemi più grandi, è possibile caricare lo schema in un contenitore BLOB di Azure nell'account di archiviazione di Azure. I passaggi per l'aggiunta di schemi variano a seconda che il contenitore BLOB disponga di accesso in lettura pubblico. Verificare prima di tutto se il contenitore BLOB dispone di accesso in lettura pubblico attenendosi alla procedura seguente: [impostare il livello di accesso pubblico per il contenitore BLOB](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Controllare il livello di accesso del contenitore

1. Aprire Azure Storage Explorer. Nella finestra di Explorer espandere la sottoscrizione di Azure, se necessario.

1. Espandere **Account di archiviazione** > {*account-di-archiviazione-utente*} > **Blob Containers** (Contenitori BLOB). Selezionare il contenitore BLOB.

1. Scegliere **Set Public Access Level** (Imposta livello di accesso pubblico) dal menu di scelta rapida del contenitore BLOB.

   * Se il contenitore BLOB dispone almeno dell'accesso pubblico, scegliere **Annulla**e seguire la procedura riportata più avanti in questa pagina: [caricare nei contenitori con accesso pubblico](#public-access)

     ![Accesso pubblico](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se il contenitore BLOB non ha accesso pubblico, scegliere **Annulla**e seguire la procedura riportata più avanti in questa pagina: [caricare nei contenitori senza accesso pubblico](#public-access)

     ![Nessun accesso pubblico](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Caricare in contenitori con accesso pubblico

1. Caricare lo schema nell'account di archiviazione. 
   Nella finestra a destra scegliere **Carica**.

1. Al termine del caricamento, selezionare lo schema caricato. Sulla barra degli strumenti scegliere **Copia URL** per copiare l'URL dello schema.

1. Tornare al portale di Azure in cui è aperto il riquadro **Aggiungi schema**. 
   Immettere un nome per lo schema. 
   Scegliere **File grande (dimensioni maggiori di 2 MB)** . 

   Verrà ora visualizzata la casella **URI del contenuto**, invece della casella **Schema**.

1. Nella casella **URI del contenuto** incollare l'URL dello schema. 
   Terminare l'aggiunta dello schema.

Al termine del caricamento dello schema, lo schema viene visualizzato nell'elenco **Schemi**. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** il riquadro **Schema** visualizza ora il numero di schemi caricati.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Caricare in contenitori senza accesso pubblico

1. Caricare lo schema nell'account di archiviazione. 
   Nella finestra a destra scegliere **Carica**.

1. Al termine del caricamento, generare una firma di accesso condiviso per lo schema. 
   Dal menu di scelta rapida scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

1. Nel riquadro **Firma di accesso condiviso** selezionare **Generate container-level shared access signature URI** (Genera l'URI di firma di accesso condiviso a livello di contenitore) > **Crea**. 
   Dopo la generazione dell'URL di firma di accesso condiviso, accanto alla casella **URL** scegliere **Copia**.

1. Tornare al portale di Azure in cui è aperto il riquadro **Aggiungi schema**. Scegliere **File grande**.

   Verrà ora visualizzata la casella **URI del contenuto**, invece della casella **Schema**.

1. Nella casella **URI del contenuto** incollare l'URI di firma di accesso condiviso generato in precedenza. Terminare l'aggiunta dello schema.

Al termine del caricamento dello schema, lo schema viene visualizzato nell'elenco **Schemi**. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** il riquadro **Schema** visualizza ora il numero di schemi caricati.

## <a name="edit-schemas"></a>Modificare gli schemi

Per aggiornare uno schema esistente, è necessario caricare un nuovo file di schema contenente le modifiche desiderate. È tuttavia possibile scaricare prima lo schema esistente e poi modificarlo.

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel menu principale di Azure selezionare **Tutti i servizi**. 
   Nella casella di ricerca, digitare "account di integrazione". 
   Selezionare **Account di integrazione**.

1. Selezionare l'account di integrazione in cui si vuole aggiornare lo schema.

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Schemi**.

1. Quando la pagina **Schemi** è aperta, selezionare lo schema. 
   Per scaricare e modificare lo schema prima, scegliere **Scarica** e salvare lo schema.

1. Quando lo schema aggiornato è pronto per il caricamento, nella pagina **Schemi** selezionare lo schema che si vuole aggiornare e scegliere **Aggiorna**.

1. Trovare e selezionare lo schema aggiornato che si vuole caricare. 
   Al termine del caricamento del file dello schema, lo schema aggiornato viene visualizzato nell'elenco **Schemi**.

## <a name="delete-schemas"></a>Eliminare gli schemi

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel menu principale di Azure selezionare **Tutti i servizi**. 
   Nella casella di ricerca, digitare "account di integrazione". 
   Selezionare **Account di integrazione**.

1. Selezionare l'account di integrazione in cui si vuole eliminare lo schema.

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Schemi**.

1. Quando la pagina **Schemi** è aperta, selezionare lo schema e scegliere **Elimina**.

1. Per confermare che si vuole eliminare lo schema, scegliere **Sì**.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Altre informazioni sulle mappe](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Altre informazioni sulle trasformazioni](../logic-apps/logic-apps-enterprise-integration-transform.md)
