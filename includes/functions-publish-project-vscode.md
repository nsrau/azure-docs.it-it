---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ec0425ff2188ecf1816d5f5841394c8e32f301d2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132245"
---
## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

Visual Studio Code consente di pubblicare il progetto di Funzioni direttamente in Azure. Durante il processo vengono create un'app per le funzioni e le risorse correlate nella sottoscrizione di Azure. L'app per le funzioni fornisce un contesto di esecuzione per le funzioni. Il progetto viene inserito in un pacchetto e distribuito nella nuova app per le funzioni nella sottoscrizione di Azure.

In questo articolo si presuppone che venga creata una nuova app per le funzioni. 

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.

1. Nell'area **Azure: Funzioni** selezionare l'icona per la distribuzione nell'app per le funzioni.

    ![Impostazioni dell'app per le funzioni](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Se non è stato ancora eseguito l'accesso, viene visualizzata la richiesta per **accedere ad Azure**. È anche possibile **creare un account di Azure gratuito**. Dopo l'accesso dal browser, tornare a Visual Studio Code. 

1. Se si hanno più sottoscrizioni, **selezionare una sottoscrizione** per l'app per le funzioni e quindi scegliere l'opzione **+ Create New Function App in Azure** (+ Crea nuova app per le funzioni in Azure).

1. Digitare un nome univoco globale che identifichi l'app per le funzioni, quindi premere INVIO. I caratteri validi per un nome di app per le funzioni sono `a-z`, `0-9` e `-`.

1. Scegliere **+ Crea nuovo gruppo di risorse**, digitare un nome di gruppo di risorse, ad esempio `myResourceGroup`, quindi premere INVIO. È anche possibile usare un gruppo di risorse esistente.

1. Scegliere **+Crea un nuovo account di archiviazione**, digitare un nome univoco globale per il nuovo account di archiviazione usato dall'app per le funzioni e quindi premere INVIO. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È possibile anche usare un account esistente.

1. Scegliere una località in un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni.

    Quando si preme INVIO, nella sottoscrizione vengono create le risorse di Azure seguenti:

    * **[Gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)** : contiene tutte le risorse di Azure create. Il nome si basa sul nome dell'app per le funzioni.
    * **[Account di archiviazione](../articles/storage/common/storage-quickstart-create-account.md)** : viene creato un account di archiviazione standard con un nome univoco basato sul nome dell'app per le funzioni.
    * **[Piano di hosting](../articles/azure-functions/functions-scale.md)** : nell'area Stati Uniti occidentali viene creato un piano a consumo per l'hosting dell'app per le funzioni.
    * **App per le funzioni**: il progetto viene distribuito ed eseguito nella nuova app per le funzioni.

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create.

1. Nell'area **Azure: Funzioni** espandere la nuova app per le funzioni nella sottoscrizione. Espandere **Funzioni**, fare clic con il pulsante destro del mouse su **HttpTrigger** e quindi scegliere **Copy function URL** (Copia l'URL della funzione).

    ![Copiare l'URL della funzione per il nuovo trigger HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
