---
title: includere file
description: includere file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231481"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

1. In **Selezionare una destinazione di pubblicazione** usare le opzioni di pubblicazione specificate nella tabella seguente: 

    | Opzione      | Descrizione                                |
    | ------------ |  -------------------------------------------------- |
    | **Piano a consumo di Funzioni di Azure** | Creare un'app per le funzioni in un ambiente cloud di Azure eseguito con un [piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan). Quando si usa un piano a consumo, si paga solo per le esecuzioni dell'app per le funzioni. Altri piani di hosting comportano costi più elevati. Se si esegue un piano diverso da quello a consumo, è necessario gestire il [ridimensionamento dell'app per le funzioni](../articles/azure-functions/functions-scale.md).| 
    | **Crea nuovo** | Viene creata in Azure una nuova app per le funzioni, con le risorse correlate. <br/>Se si sceglie **Seleziona esistente**, tutti i file nell'app per le funzioni esistente in Azure vengono sovrascritti da file del progetto locale. Usare questa opzione solo quando si ripubblicano gli aggiornamenti in un'app per le funzioni esistente. |
    | **Esegui da file di pacchetto** | L'app per le funzioni viene distribuita usando la [distribuzione ZIP](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) con la modalità [esecuzione da pacchetto](../articles/azure-functions/run-functions-from-deployment-package.md) abilitata. Questa distribuzione, che garantisce migliori prestazioni, è quella consigliata per eseguire le funzioni. <br/>Se non si usa questa opzione, assicurarsi di arrestare l'esecuzione in locale del progetto di app per le funzioni prima della pubblicazione in Azure. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Creare un profilo di pubblicazione":::


1. Selezionare **Crea profilo**. Se non è già stato effettuato l'accesso all'account Azure da Visual Studio, selezionare **Accedi**. È anche possibile creare un account Azure gratuito.

1. In **Servizio app: Crea nuovo** usare i valori specificati nella tabella seguente:

    | Impostazione      | valore  | Descrizione                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. Accettare questo nome o immetterne uno nuovo. I caratteri validi sono `a-z`, `0-9` e `-`. |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione di Azure da usare. Accettare questa sottoscrizione o selezionarne una nuova dall'elenco a discesa. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** | Nome del gruppo di risorse |  Gruppo di risorse in cui creare l'app per le funzioni. Selezionare un gruppo di risorse esistente dall'elenco a discesa oppure scegliere **Nuovo** per creare un nuovo gruppo di risorse.|
    | **[Piano di hosting](../articles/azure-functions/functions-scale.md)** | Nome del piano di hosting | Selezionare **Nuovo** per configurare un piano serverless. Assicurarsi di scegliere **A consumo** in **Dimensioni**. Quando si pubblica il progetto in un'app per le funzioni eseguita in un [piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan), vengono addebitati solo i costi relativi alle esecuzioni dell'app per le funzioni. Altri piani di hosting comportano costi più elevati. Se si esegue un piano diverso da **Consumo**, è necessario gestire il [ridimensionamento dell'app per le funzioni](../articles/azure-functions/functions-scale.md).  |
    | **Posizione** | La località del piano di servizio app | Scegliere una **località** in un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |
    | **[Archiviazione di Azure](../articles/storage/common/storage-account-create.md)** | Account di archiviazione per utilizzo generico | Per il runtime di Funzioni è richiesto un account di archiviazione di Azure. Selezionare **Nuovo** per configurare un account di archiviazione per utilizzo generico. È anche possibile scegliere un account esistente che soddisfi i [requisiti dell'account di archiviazione](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Finestra di dialogo Crea servizio app](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selezionare **Crea** per creare un'app per le funzioni e le relative risorse correlate in Azure con queste impostazioni e distribuire il codice del progetto per le funzioni. 

1. Selezionare **Pubblica** e attendere il completamento della distribuzione. 

    Al termine della distribuzione, l'URL radice dell'app per le funzioni in Azure viene visualizzato nella scheda **Pubblica**. 
    
1.  Nella scheda Pubblica scegliere **Gestisci in Cloud Explorer**. Verrà visualizzata la nuova risorsa dell'app per le funzioni di Azure in Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Messaggio di esito positivo della pubblicazione":::
    
    Cloud Explorer consente di usare Visual Studio per visualizzare il contenuto del sito, avviare e arrestare l'app per le funzioni e passare direttamente alle risorse dell'app per le funzioni in Azure e nella portale di Azure. 