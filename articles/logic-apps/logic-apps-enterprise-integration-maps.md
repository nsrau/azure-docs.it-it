---
title: Trasformare i dati XML con le mappe XSLT - App per la logica di Azure | Microsoft Docs
description: Aggiungere mappe XSLT per trasformare i dati XML in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: d0d40ca0ae6ccd4f709d7d94d52764d4affcc215
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244706"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trasformare i dati XML con le mappe in App per la logica di Azure con Enterprise Integration Pack

Per trasferire dati XML tra i formati per gli scenari di integrazione aziendali nelle App per la logica di Azure, app per la logica possa usare mappe o, in particolare, i fogli di stile Extensible Language Transformations (XSLT) viene eseguito il mapping. Una mappa è un documento XML che descrive come convertire i dati da un documento XML in un altro formato. 

Si supponga, ad esempio, di ricevere regolarmente fatture o ordini B2B da un cliente che usa il formato AAAMMGG per le date. L'organizzazione usa tuttavia il formato MMGGAAA per le date. È possibile definire e usare una mappa che trasforma il formato di data AAAMMGG nel formato MMGGAAA prima di archiviare i dettagli dell'ordine o della fattura nel database relativo.

Per i limiti relativi agli account di integrazione e agli elementi come le mappe, vedere [Informazioni su limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) in cui archiviare le mappe e gli altri elementi per l'integrazione aziendale e le soluzioni B2B (business-to-business).

* Se la mappa fa riferimento a un assembly esterno, è necessario caricare *sia l'assembly che la mappa* nell'account di integrazione. Assicurarsi di aver [ *caricare l'assembly prima di tutto*](#add-assembly)e quindi caricare la mappa che fa riferimento all'assembly.

  Se le dimensioni dell'assembly sono pari a 2 MB o meno, è possibile aggiungere l'assembly all'account di integrazione *direttamente* dal portale di Azure. Se tuttavia le dimensioni dell'assembly o della mappa sono superiori a 2 MB, ma non superano il [limite per gli assembly o le mappe](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), esistono queste opzioni:

  * Per gli assembly, sono necessari un contenitore BLOB di Azure, in cui poter caricare l'assembly, e la posizione di tale contenitore. In questo modo, sarà possibile fornire tale posizione in seguito quando si aggiungerà l'assembly all'account di integrazione. 
  For questa attività, sono necessari gli elementi seguenti:

    | Elemento | Descrizione |
    |------|-------------|
    | [Account di archiviazione di Azure](../storage/common/storage-account-overview.md) | In questo account creare un contenitore BLOB di Azure per l'assembly. Altre informazioni su come [creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md). |
    | Contenitore BLOB | In questo contenitore è possibile caricare l'assembly. È necessaria anche la posizione di questo contenitore quando si aggiunge l'assembly all'account di integrazione. Informazioni su come [creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Questo strumento consente di gestire più facilmente gli account di archiviazione e i contenitori BLOB. Per usare Storage Explorer, [scaricare e installare Azure Storage Explorer](https://www.storageexplorer.com/), quindi connettere Storage Explorer all'account di archiviazione seguendo i passaggi illustrati in [Introduzione a Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Per altre informazioni, vedere [Guida introduttiva: Usare Azure Storage Explorer per creare un BLOB nell'archiviazione di oggetti](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>In alternativa, nel portale di Azure trovare e selezionare l'account di archiviazione. Dal menu dell'account di archiviazione selezionare **Storage Explorer**. |
    |||

  * Per le mappe, è attualmente possibile aggiungere mappe più grandi usando l'[API REST di App per la logica di Azure - Mappe](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Non è necessaria un'app per la logica quando si creano e si aggiungono mappe. Per usare una mappa, tuttavia, l'app per la logica deve essere collegata a un account di integrazione in cui si archivia tale mappa. Informazioni su come [collegare le app per la logica agli account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se si ha ancora un'app per la logica, vedere l'articolo su [come creare app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Aggiungere gli assembly a cui viene fatto riferimento

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Per trovare e aprire l'account di integrazione, nel menu principale di Azure selezionare **Tutti i servizi**. 
   Nella casella di ricerca, digitare "account di integrazione". 
   Selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selezionare l'account di integrazione a cui si vuole aggiungere l'assembly, ad esempio:

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Assembly**.

   ![Selezionare "Assembly"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Quando la pagina **Assembly** è aperta, scegliere **Aggiungi**.

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

In base alle dimensioni del file di assembly, seguire i passaggi per caricare un assembly di dimensioni [pari o inferiori a 2 MB](#smaller-assembly) oppure [comprese tra 2 MB e 8 MB](#larger-assembly).
Per i limiti delle quantità di assembly negli account di integrazione, vedere [Limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Se si modifica l'assembly, è necessario aggiornare anche la mappa, se la mappa contiene modifiche.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Aggiungere assembly di dimensioni pari o inferiori a 2 MB

1. In **Aggiungi assembly** immettere un nome per l'assembly. Mantenere selezionato **File piccolo**. Accanto alla casella **Assembly** scegliere l'icona della cartella. Trovare e selezionare l'assembly da caricare, ad esempio:

   ![Caricare un assembly più piccolo](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Nella proprietà **Nome assembly** il nome file dell'assembly viene automaticamente visualizzato dopo aver selezionato l'assembly.

1. Al termine, scegliere **OK**.

   Al termine del caricamento del file di assembly, l'assembly viene visualizzato nell'elenco **Assembly**.

   ![Elenco di assembly caricati](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** il riquadro **Assembly** mostra ora il numero di assembly caricati, ad esempio:

   ![Assembly caricati](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Aggiungere assembly di dimensioni superiori a 2 MB

Per aggiungere assembly più grandi, è possibile caricare l'assembly in un contenitore BLOB di Azure nell'account di archiviazione di Azure. La procedura per aggiungere gli assembly variano a seconda se il contenitore blob ha accesso in lettura pubblico. Prima di tutto, controllare quindi se il contenitore BLOB abbia l'accesso in lettura pubblico o meno seguendo questa procedura: [Impostare il livello di accesso pubblico per un contenitore BLOB](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Controllare il livello di accesso del contenitore

1. Aprire Azure Storage Explorer. Nella finestra di Explorer espandere la sottoscrizione di Azure, se necessario.

1. Espandere **Account di archiviazione** > {*account-di-archiviazione-utente*} > **Blob Containers** (Contenitori BLOB). Selezionare il contenitore BLOB.

1. Scegliere **Set Public Access Level** (Imposta livello di accesso pubblico) dal menu di scelta rapida del contenitore BLOB.

   * Se il contenitore BLOB ha almeno l'accesso pubblico, scegliere **Annulla** e seguire questa procedura più avanti nella pagina: [Caricare in contenitori con accesso pubblico](#public-access-assemblies)

     ![Accesso pubblico](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se il contenitore BLOB non ha l'accesso pubblico, scegliere **Annulla** e seguire questa procedura più avanti nella pagina: [Caricare in contenitori senza accesso pubblico](#no-public-access-assemblies)

     ![Nessun accesso pubblico](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Caricare in contenitori con accesso pubblico

1. Caricare l'assembly nell'account di archiviazione. 
   Nella finestra a destra scegliere **Carica**.

1. Al termine del caricamento, selezionare l'assembly caricato. Sulla barra degli strumenti scegliere **Copia URL** per copiare l'URL dell'assembly.

1. Tornare al portale di Azure in cui è aperto il riquadro **Aggiungi assembly**. 
   Immettere un nome per l'assembly. 
   Scegliere **File grande (dimensioni maggiori di 2 MB)** .

   Verrà ora visualizzata la casella **URI del contenuto**, invece della casella **Assembly**.

1. Nella casella **URI del contenuto** incollare l'URL dell'assembly. 
   Terminare l'aggiunta dell'assembly.

Al termine del caricamento dell'assembly, lo schema viene visualizzato nell'elenco **Assembly**.
Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** il riquadro **Assembly** mostra ora il numero di assembly caricati.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Caricare in contenitori senza accesso pubblico

1. Caricare l'assembly nell'account di archiviazione. 
   Nella finestra a destra scegliere **Carica**.

1. Al termine del caricamento, generare una firma di accesso condiviso per l'assembly. 
   Scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

1. Nel riquadro **Firma di accesso condiviso** selezionare **Generate container-level shared access signature URI** (Genera l'URI di firma di accesso condiviso a livello di contenitore) > **Crea**. 
   Dopo la generazione dell'URL di firma di accesso condiviso, accanto alla casella **URL** scegliere **Copia**.

1. Tornare al portale di Azure in cui è aperto il riquadro **Aggiungi assembly**. 
   Immettere un nome per l'assembly. 
   Scegliere **File grande (dimensioni maggiori di 2 MB)** .

   Verrà ora visualizzata la casella **URI del contenuto**, invece della casella **Assembly**.

1. Nella casella **URI del contenuto** incollare l'URI di firma di accesso condiviso generato in precedenza. Terminare l'aggiunta dell'assembly.

Al termine del caricamento dell'assembly, l'assembly viene visualizzato nell'elenco **Schemi**. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** il riquadro **Assembly** mostra ora il numero di assembly caricati.

## <a name="create-maps"></a>Creare le mappe

Per creare un documento XSLT da usare come mappa, è possibile usare Visual Studio 2015 per creare un progetto di Integrazione BizTalk usando [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). In questo progetto è possibile creare un file di mappa di integrazione, che consente di eseguire il mapping visivo degli elementi tra due file di schema XML. Dopo aver compilato il progetto, si ottiene un documento XSLT.
Per i limiti delle quantità di mappe negli account di integrazione, vedere [Limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Aggiungere le mappe

Dopo aver caricato gli assembly a cui la mappa fa riferimento, è possibile caricare la mappa.

1. Se l'accesso non è già stato eseguito, accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure. 

1. Se l'account di integrazione non è già aperto, nel menu principale di Azure selezionare **Tutti i servizi**. 
   Nella casella di ricerca, digitare "account di integrazione". 
   Selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selezionare l'account di integrazione a cui si vuole aggiungere la mappa, ad esempio:

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Mappe**.

   ![Selezionare "Mappe"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Quando la pagina **Mappe** è aperta, scegliere **Aggiungi**.

   ![Selezionare "Aggiungi"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Aggiungere mappe di dimensioni pari o inferiori a 2 MB

1. In **Aggiungi mappa** immettere un nome per la mappa. 

1. In **Tipo di mapping** selezionare il tipo, ad esempio: **Liquid**, **XSLT**, **XSLT 2.0** o **XSLT 3.0**.

1. Mantenere selezionato **File piccolo**. Accanto alla casella **Mappa** scegliere l'icona della cartella. Trovare e selezionare la mappa da caricare, ad esempio:

   ![Caricare la mappa](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Se la proprietà **Nome** è stata lasciata vuota, il nome file della mappa viene automaticamente visualizzato in tale proprietà dopo aver selezionato il file della mappa. 
   È tuttavia possibile usare qualsiasi nome univoco.

1. Al termine, scegliere **OK**. 
   Al termine del caricamento del file, la mappa viene visualizzata nell'elenco **Mappe**.

   ![Elenco di mappe caricate](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** il riquadro **Mappe** mostra ora il numero di mappe caricate, ad esempio:

   ![Mappe caricate](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Aggiungere mappe di dimensioni superiori a 2 MB

Attualmente, per aggiungere mappe più grandi, usare l'[API REST di App per la logica di Azure - Mappe](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Modificare le mappe

Per aggiornare una mappa esistente, è necessario caricare un nuovo file di mappa contenente le modifiche desiderate. È tuttavia possibile scaricare prima la mappa esistente e poi modificarla.

1. Nel [portale di Azure](https://portal.azure.com) trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca, digitare "account di integrazione". Selezionare **Account di integrazione**.

1. Selezionare l'account di integrazione in cui si vuole aggiornare la mappa.

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Mappe**.

1. Quando la pagina **Mappe** è aperta, selezionare la mappa. 
   Per scaricare e modificare la mappa prima, scegliere **Scarica** e salvare la mappa.

1. Quando si è pronti per caricare la mappa aggiornata, nella pagina **Mappe** selezionare la mappa che si vuole aggiornare e scegliere **Aggiorna**.

1. Trovare e selezionare la mappa aggiornata che si vuole caricare. 
   Al termine del caricamento del file, la mappa aggiornata viene visualizzata nell'elenco **Mappe**.

## <a name="delete-maps"></a>Eliminare le mappe

1. Nel [portale di Azure](https://portal.azure.com) trovare e aprire l'account di integrazione, se non è già aperto.

1. Nel menu principale di Azure selezionare **Tutti i servizi**. 
   Nella casella di ricerca, digitare "account di integrazione". 
   Selezionare **Account di integrazione**.

1. Selezionare l'account di integrazione in cui si vuole eliminare la mappa.

1. Nella pagina **Panoramica** dell'account di integrazione, in **Componenti** selezionare il riquadro **Mappe**.

1. Quando la pagina **Mappe** è aperta, selezionare la mappa e scegliere **Elimina**.

1. Per confermare che si vuole eliminare la mappa, scegliere **Sì**.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Altre informazioni sugli schemi](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Altre informazioni sulle trasformazioni](../logic-apps/logic-apps-enterprise-integration-transform.md)
