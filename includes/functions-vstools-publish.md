---
title: includere file
description: includere file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ef617b9ed6c35d33350b173efe6189b5b79b6ae6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008194"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. In **Destinazione** selezionare **Azure** :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Selezionare Azure come destinazione":::

1. In **Destinazione specifica** selezionare **App per le funzioni di Azure (Windows)**

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Selezionare App per le funzioni":::

1. In **Istanza di Funzioni** selezionare **Crea una nuova applicazione Funzioni di Azure** e quindi usare i valori specificati nella tabella seguente:

    | Impostazione      | Valore  | Descrizione                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. Accettare questo nome o immetterne uno nuovo. I caratteri validi sono `a-z`, `0-9` e `-`. |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione di Azure da usare. Accettare questa sottoscrizione o selezionarne una nuova dall'elenco a discesa. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** | Nome del gruppo di risorse |  Gruppo di risorse in cui creare l'app per le funzioni. Selezionare un gruppo di risorse esistente dall'elenco a discesa oppure scegliere **Nuovo** per creare un nuovo gruppo di risorse.|
    | **[Tipo di piano](../articles/azure-functions/functions-scale.md)** | Consumo | Quando si pubblica il progetto in un'app per le funzioni eseguita in un [piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan), vengono addebitati solo i costi relativi alle esecuzioni dell'app per le funzioni. Altri piani di hosting comportano costi più elevati. |
    | **Posizione** | La località del piano di servizio app | Scegliere una **località** in un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |
    | **[Archiviazione di Azure](../articles/storage/common/storage-account-create.md)** | Account di archiviazione per utilizzo generico | Per il runtime di Funzioni è richiesto un account di archiviazione di Azure. Selezionare **Nuovo** per configurare un account di archiviazione per utilizzo generico. È anche possibile scegliere un account esistente che soddisfi i [requisiti dell'account di archiviazione](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Finestra di dialogo Crea servizio app](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selezionare **Crea** per creare un'app per le funzioni e le relative risorse in Azure. 
1. In **Istanza di Funzioni** assicurarsi che sia selezionata l'opzione **Esegui da file di pacchetto**. L'app per le funzioni viene distribuita usando la [distribuzione ZIP](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) con la modalità [esecuzione da pacchetto](../articles/azure-functions/run-functions-from-deployment-package.md) abilitata. Questo è il metodo di distribuzione consigliato per il progetto di funzioni, perché offre prestazioni più elevate. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Completare la creazione del profilo":::

1. Selezionare **Fine** e quindi, nella pagina Pubblica, selezionare **Pubblica** per distribuire il pacchetto contenente i file del progetto nella nuova app per le funzioni in Azure. 

    Al termine della distribuzione, l'URL radice dell'app per le funzioni in Azure viene visualizzato nella scheda **Pubblica**. 
    
1.  Nella scheda Pubblica scegliere **Gestisci in Cloud Explorer**. Verrà visualizzata la nuova risorsa dell'app per le funzioni di Azure in Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Messaggio di esito positivo della pubblicazione":::
    
    Cloud Explorer consente di usare Visual Studio per visualizzare il contenuto del sito, avviare e arrestare l'app per le funzioni e passare direttamente alle risorse dell'app per le funzioni in Azure e nella portale di Azure. 
