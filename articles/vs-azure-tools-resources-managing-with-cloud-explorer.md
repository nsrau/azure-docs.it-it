---
title: Gestione delle risorse di Azure con Cloud Explorer | Documentazione Microsoft
description: Informazioni su come usare Cloud Explorer per esplorare e gestire le risorse di Azure in Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/25/2017
ms.author: kraigb
ms.openlocfilehash: 6e6d8d559f0251b71bfa6d529ead82a246cb3235
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Gestire le risorse associate agli account di Azure in Visual Studio Cloud Explorer
Cloud Explorer consente di visualizzare le risorse di Azure e i gruppi di risorse, controllare le relative proprietà ed eseguire azioni di diagnostica fondamentali per gli sviluppatori dall'interno di Visual Studio. 

Cloud Explorer è basato sullo stack di Azure Resource Manager, proprio come il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Pertanto riconosce risorse, come i gruppi di risorse di Azure e i servizi di Azure, come le app per la logica e le app per le API. Supporta infine il [controllo degli accessi in base al ruolo](active-directory/role-based-access-control-configure.md) (RBAC). 

## <a name="prerequisites"></a>Prerequisiti
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il **carico di lavoro di Azure** selezionato o una versione precedente di Visual Studio con [Microsoft Azure SDK per .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Account di Microsoft Azure: se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](http://go.microsoft.com/fwlink/?LinkId=623901) oppure [attivare i vantaggi della sottoscrizione di Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Per visualizzare Cloud Explorer, selezionare **visualizza** > **Cloud Explorer** nella barra dei menu.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Aggiungere un account di Azure a Cloud Explorer
Per visualizzare le risorse associate a un account di Azure, è innanzitutto necessario aggiungere l'account a Cloud Explorer. 

1. In **Cloud Explorer**, selezionare **Impostazioni account Azure**.

    ![Icona delle impostazioni account di Azure di Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Selezionare **Aggiungi un nuovo account**. 

    ![Collegamento per l'aggiunta dell'account di Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Accedere all'account di Azure di cui si vogliono esplorare le risorse. 

1. Una volta effettuato l'accesso all'account di Azure, vengono visualizzate le sottoscrizioni associate all'account. Selezionare le caselle di controllo relative alle sottoscrizioni dell'account da esplorare, quindi selezionare **Applica**. 
 
    ![Cloud Explorer: selezionare le sottoscrizioni di Azure da visualizzare](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Dopo aver selezionato le sottoscrizioni di cui si desidera visualizzare le risorse, tali sottoscrizioni e risorse vengono visualizzate in Cloud Explorer.

    ![Elenco delle risorse di Cloud Explorer per un account Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Rimuovere un account di Azure da Cloud Explorer 

1. In **Cloud Explorer**, selezionare **Impostazioni account Azure**.

    ![Icona delle impostazioni account di Azure di Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Accanto all'account che si desidera rimuovere, selezionare **Rimuovi**.

    ![Icona delle impostazioni account di Azure di Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Visualizzare i tipi o i gruppi di risorse
Per visualizzare le risorse di Azure, è possibile scegliere la visualizzazione **Tipi di risorsa** o **Gruppi di risorse**.

1. In **Cloud Explorer**, selezionare l'elenco a discesa per la visualizzazione delle risorse.

    ![Elenco a discesa di Cloud Explorer per selezionare la visualizzazione di risorse desiderata](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. Dal menu di scelta rapida selezionare la visualizzazione desiderata: 

    - Visualizzazione **Tipi di risorsa**: la visualizzazione comune usata nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), mostra le risorse di Azure classificate in base al tipo, ad esempio app Web, account di archiviazione e macchine virtuali. 
    - Visualizzazione **Gruppi di risorse**: classifica le risorse di Azure in base al gruppo di risorse di Azure a cui sono associate. Un gruppo di risorse è un bundle di risorse di Azure, in genere usate da un'applicazione specifica. Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    L'immagine seguente illustra un confronto tra le visualizzazioni di due risorse:

    ![Confronto tra visualizzazioni delle risorse di Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Visualizzare ed esplorare le risorse in Cloud Explorer
Per passare a una risorsa di Azure e visualizzare le informazioni corrispondenti in Cloud Explorer, espandere il tipo o il gruppo di risorse associato dell'elemento, quindi selezionare la risorsa. Quando si seleziona una risorsa, le informazioni vengono visualizzate nelle due schede: **Azioni** e **Proprietà** nella parte inferiore di Cloud Explorer. 

- La scheda **Azioni** mostra le azioni che possono essere eseguite in Cloud per la risorsa selezionata. È inoltre possibile visualizzare queste opzioni facendo clic con pulsante destro del mouse sulla risorsa per visualizzare il menu di scelta rapida.

- La scheda **Proprietà** mostra le proprietà della risorsa, ad esempio il tipo, le impostazioni locali e il gruppo di risorse a cui è associata.

Di seguito viene illustrato un confronto di esempio di ciò che viene visualizzato in ogni scheda per un servizio App:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

L'azione **Open in portal**è disponibile per ogni risorsa. Quando si sceglie questa azione, Cloud Explorer mostra la risorsa selezionata nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). La funzionalità **Apri nel portale** è particolarmente utile per l'esplorazione di risorse con molti livelli di nidificazione.

È possibile che vengano visualizzate azioni aggiuntive e altri valori di proprietà, in base alla risorsa di Azure. Ad esempio, per le app Web e le app per la logica sono disponibili anche le azioni **Apri nel browser** e **Collega debugger**, oltre ad **Apri nel portale**. Quando si sceglie un BLOB, una coda o una tabella dell'account di archiviazione, vengono visualizzate le azioni per l'apertura di editor. Per le app Azure sono disponibili le proprietà relative a **URL** e **stato**, mentre per le risorse di archiviazione sono disponibili le proprietà relative alle chiavi e alle stringhe di connessione.

## <a name="find-resources-in-cloud-explorer"></a>Cercare risorse in Cloud Explorer
Per trovare risorse con un nome specifico nelle sottoscrizioni dell'account di Azure, immettere il nome nella casella **Cerca** di Cloud Explorer.

![Ricerca di risorse in Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

Quando si immettono caratteri nella casella **Cerca**, nell'albero delle risorse vengono visualizzate solo le risorse corrispondenti ai caratteri immessi.
