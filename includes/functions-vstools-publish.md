---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 07/17/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e8cb5dadb7eed5eb33c15d7a4d1d4640a10099c9
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "40245786"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

2. Selezionare **App per le funzioni di Azure**, scegliere **Crea nuova** e quindi selezionare **Pubblica**.

    ![Selezionare una destinazione di pubblicazione](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

3. Se non si è ancora connesso Visual Studio al proprio account di Azure, selezionare **Aggiungi un account...**.

4. Nella finestra di dialogo **Crea servizio app** usare le impostazioni di **Hosting** specificate nella tabella sotto l'immagine:

    ![Finestra di dialogo Crea servizio app](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Impostazione      | Valore consigliato  | DESCRIZIONE                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome app** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. |
    | **Sottoscrizione** | Scegliere la sottoscrizione | Sottoscrizione di Azure da usare. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome del gruppo di risorse in cui creare l'app per le funzioni. Per creare un nuovo gruppo di risorse scegliere **Nuovo**.|
    | **[Piano di servizio app](../articles/azure-functions/functions-scale.md)** | Piano a consumo | Assicurarsi di scegliere **Consumo** in **Dimensione** dopo aver fatto clic su **Nuovo** per creare un piano senza server. Scegliere anche una **Località**  nelle [vicinanze](https://azure.microsoft.com/regions/) o vicino ad altri servizi a cui accedono le funzioni. Se si esegue un piano diverso da **Consumo**, è necessario gestire il [ridimensionamento dell'app per le funzioni](../articles/azure-functions/functions-scale.md).  |
    | **[Account di archiviazione](../articles/storage/common/storage-quickstart-create-account.md)** | Account di archiviazione per utilizzo generico | L'account di archiviazione di Azure è necessario per il runtime di Funzioni. Fare clic su **Nuovo** per creare un account di archiviazione per utilizzo generico. È anche possibile usare un account esistente che soddisfi i [requisiti dell'account di archiviazione](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Fare clic su **Crea** per creare un'app per le funzioni e le relative risorse in Azure con queste impostazioni e distribuire il codice di progetto della funzione. 

6. Al termine della distribuzione prendere nota del valore di **URL sito**, ovvero l'indirizzo dell'app per le funzioni in Azure.

    ![Messaggio di esito positivo della pubblicazione](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
