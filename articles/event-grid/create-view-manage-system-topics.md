---
title: Creare, visualizzare e gestire gli argomenti di sistema in griglia di eventi di Azure (portale)
description: Questo articolo illustra come visualizzare l'argomento di sistema esistente, creare argomenti del sistema di griglia di eventi di Azure usando il portale di Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115112"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Creare, visualizzare e gestire gli argomenti del sistema di griglia di eventi nel portale di Azure
Questo articolo illustra come creare e gestire gli argomenti di sistema usando il portale di Azure. Per una panoramica degli argomenti di sistema, vedere [argomenti di sistema](system-topics.md).

## <a name="create-a-system-topic"></a>Creare un argomento di sistema
È possibile creare un argomento di sistema per una risorsa di Azure (account di archiviazione, spazio dei nomi di hub eventi e così via) in due modi:

- Uso della pagina **eventi** di una risorsa, ad esempio, l'account di archiviazione o lo spazio dei nomi di hub eventi. Quando si usa la pagina **eventi** nel portale di Azure per creare una sottoscrizione di eventi per un evento generato da un'origine di Azure, ad esempio account di archiviazione di Azure, il portale crea un argomento di sistema per la risorsa di Azure e quindi crea una sottoscrizione per l'argomento di sistema. Specificare il nome dell'argomento di sistema se si sta creando una sottoscrizione di eventi nella risorsa di Azure per la prima volta. Dalla seconda volta in poi, il nome dell'argomento di sistema viene visualizzato in modalità di sola lettura. Vedere [Guida introduttiva: indirizzare gli eventi di archiviazione BLOB all'endpoint Web con la portale di Azure per i](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) passaggi dettagliati.
- Utilizzare la pagina **argomenti del sistema griglia di eventi** . La procedura seguente illustra la creazione di un argomento di sistema utilizzando la pagina degli **argomenti di sistema di griglia di eventi** . 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella di ricerca nella parte superiore digitare **argomenti del sistema griglia di eventi**, quindi premere **invio**. 

    ![Cerca argomenti di sistema](./media/create-view-manage-system-topics/search-system-topics.png)
3. Nella pagina **argomenti del sistema di griglia di eventi** selezionare **+ Aggiungi** sulla barra degli strumenti.

    ![Aggiungi argomento di sistema-pulsante della barra degli strumenti](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Nella pagina **crea l'argomento del sistema griglia di eventi** seguire questa procedura:
    1. Selezionare il **tipo di argomento**. Nell'esempio seguente è selezionata l'opzione **account di archiviazione** . 
    2. Selezionare la **sottoscrizione di Azure** che contiene la risorsa dell'account di archiviazione. 
    3. Selezionare il **gruppo di risorse** con l'account di archiviazione. 
    4. Selezionare l'**account di archiviazione**. 
    5. Immettere un **nome** per l'argomento di sistema da creare. 
    
        > [!NOTE]
        > È possibile usare questo nome dell'argomento di sistema per cercare metriche e log di diagnostica.
    6. Selezionare **Rivedi e crea**.

        ![Crea argomento di sistema](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Verificare le impostazioni e selezionare **Crea**. 
        
        ![Esaminare e creare un argomento di sistema](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Una volta completata la distribuzione, selezionare **Vai alla risorsa** per visualizzare la pagina dell'argomento sistema di **griglia di eventi** per l'argomento di sistema creato. 

        ![Pagina dell'argomento di sistema](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Visualizza tutti gli argomenti di sistema
Per visualizzare tutti gli argomenti di sistema di griglia di eventi esistenti, seguire questa procedura. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella di ricerca nella parte superiore digitare **argomenti del sistema griglia di eventi**, quindi premere **invio**. 

    ![Cerca argomenti di sistema](./media/create-view-manage-system-topics/search-system-topics.png)
3. Nella pagina **argomenti del sistema di griglia di eventi** vengono visualizzati tutti gli argomenti di sistema. 

    ![Elenco di argomenti di sistema](./media/create-view-manage-system-topics/list-system-topics.png)
4. Selezionare un **argomento di sistema** nell'elenco per visualizzarne i dettagli. 

    ![Dettagli argomento sistema](./media/create-view-manage-system-topics/system-topic-details.png)

    In questa pagina vengono visualizzati i dettagli sull'argomento di sistema, ad esempio le informazioni seguenti: 
    - Origine. Nome della risorsa in cui è stato creato l'argomento di sistema.
    - Tipo di origine. Tipo di risorsa. Ad esempio: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` `Microsoft.Resources.ResourceGroups` e così via.
    - Tutte le sottoscrizioni create per l'argomento di sistema.

    Questa pagina consente operazioni come quelle riportate di seguito:
    - Creare una sottoscrizione di eventi selezionare **+ sottoscrizione di eventi** sulla barra degli strumenti. 
    - Eliminare una sottoscrizione di eventi. Selezionare **Elimina** sulla barra degli strumenti. 
    - Aggiungere i tag per l'argomento di sistema. Selezionare **tag** nel menu a sinistra e specificare i nomi e i valori dei tag. 


## <a name="delete-a-system-topic"></a>Eliminare un argomento di sistema
1. Seguire le istruzioni della sezione [Visualizza argomenti di sistema](#view-all-system-topics) per visualizzare tutti gli argomenti di sistema e selezionare l'argomento di sistema che si desidera eliminare dall'elenco. 
2. Nella pagina **argomento del sistema griglia di eventi** selezionare **Elimina** sulla barra degli strumenti. 

    ![Argomento sistema-pulsante Elimina](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Nella pagina Conferma fare clic su **OK** per confermare l'eliminazione. Elimina l'argomento di sistema e anche tutte le sottoscrizioni di eventi per l'argomento di sistema.  

## <a name="create-an-event-subscription"></a>Creare una sottoscrizione di eventi
1. Seguire le istruzioni della sezione [Visualizza argomenti di sistema](#view-all-system-topics) per visualizzare tutti gli argomenti di sistema e selezionare l'argomento di sistema che si desidera eliminare dall'elenco. 
2. Nella pagina **argomento del sistema di griglia di eventi** selezionare **+ sottoscrizione di eventi** sulla barra degli strumenti. 

    ![Argomento di sistema-pulsante Aggiungi sottoscrizione evento](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Verificare che il **tipo di argomento**, la **risorsa di origine**e il **nome dell'argomento** vengano popolati automaticamente. Immettere un nome, selezionare un **tipo di endpoint**e specificare l' **endpoint**. Selezionare quindi **Crea** per creare la sottoscrizione di eventi. 

    ![Argomento di sistema-creare una sottoscrizione di eventi](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere la sezione [argomenti di sistema in griglia di eventi di Azure](system-topics.md) per altre informazioni sugli argomenti di sistema e i tipi di argomento supportati da griglia di eventi di Azure. 
