---
title: Gestire un piano di servizio app - Azure | Microsoft Docs
description: Informazioni su come eseguire diverse attività per gestire un piano di servizio app.
keywords: servizio app, servizio app di azure, ridimensionare, piano di servizio app, modificare, creare, gestire, gestione
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 936abe80a66c1dbe99e7d8a255fe8995a2df0803
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715661"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gestire un piano di servizio app in Azure

Un [piano di servizio app di Azure](overview-hosting-plans.md) fornisce le risorse che un'app del servizio app deve eseguire. Questa guida illustra come gestire un piano di servizio app.

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

> [!TIP]
> Se è disponibile l'opzione Ambiente del servizio app di Azure, vedere [Creare un'app Web in un ambiente del servizio app](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

È possibile creare un piano di servizio app vuoto, o creare un piano come parte della creazione di app.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Nuovo** > **Web e dispositivi mobili** e quindi selezionare **App Web** o un altro tipo di app del servizio app.

2. Selezionare un piano di servizio app esistente o creare un piano per la nuova app.

   ![Creare un'app nel portale di Azure.][createWebApp]

   Per creare un piano:

   a. Selezionare **[+] Create New** ([+] Crea nuovo).

      ![Creare un piano di servizio app.][createASP] 

   b. Immettere il nome del piano in **Piano di servizio app**.

   c. In **Percorso** selezionare un percorso appropriato.

   d. In **Piano tariffario** selezionare un piano tariffario appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare altre opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**. Dopo aver scelto il piano tariffario, fare clic sul pulsante **Seleziona** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Spostare un'app in un altro piano di servizio app

È possibile spostare un'app in un altro piano di servizio app, purché il piano di origine e il piano di destinazione si trovino _nello stesso gruppo di risorse e nella stessa area geografica_.

> [!NOTE]
> Azure distribuisce ogni nuovo piano di servizio app in un'unità di distribuzione chiamata internamente "spazio Web". Ogni area geografica può avere molti spazi Web, ma l'app può essere spostata solo tra piani creati nello stesso spazio Web. Un ambiente del servizio app è uno spazio Web isolato; le app, quindi, possono essere spostate tra piani dello stesso ambiente del servizio app, ma non tra piani di ambienti del servizio app diversi.
>
> Non è possibile specificare lo spazio Web desiderato quando si crea un piano, ma è possibile fare in modo che un piano venga creato nello stesso spazio Web di un piano esistente. In breve, tutti i piani creati con la stessa combinazione di gruppo di risorse e area geografica vengono distribuiti nello stesso spazio Web. Se, ad esempio, è stato creato un piano nel gruppo di risorse A e nell'area geografica B, qualsiasi piano successivamente creato nel gruppo di risorse A e nell'area geografica B verrà distribuito nello stesso spazio Web. Un piano non può essere spostato in uno spazio Web dopo essere stato creato, pertanto non è possibile spostare un piano nello "stesso spazio Web" di un altro spostandolo in un altro gruppo di risorse.
> 

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'app che si intende spostare.

1. Nel menu cercare la sezione **Piano di servizio app**.

1. Selezionare **Change App Service plan** (Cambia il piano di servizio app) per aprire il selettore **Piano di servizio app**.

   ![Pannello di selezione Piano di servizio app.][change] 

1. Nel selettore **Piano di servizio App** selezionare un piano esistente in cui spostare l'app.   

La pagina **Selezionare un piano di servizio app** mostra solo i piani che si trovano nello stesso gruppo di risorse e nella stessa area geografica del piano di servizio app dell'app corrente.

È previsto un piano tariffario diverso per ogni piano. Quando si sposta un sito dal livello **Gratuito** al livello **Standard**, ad esempio, tutte le app assegnate ad esso possono usare le funzionalità e le risorse del livello **Standard**. Tuttavia, spostando un'app da un piano a più livelli superiore a un piano a più livelli inferiore non si ha più accesso a determinate funzionalità. Se l'app usa una funzionalità non presente nel piano di destinazione, viene visualizzato un errore che mostra quale funzionalità in uso non è disponibile. 

Ad esempio, se una delle app usa i certificati SSL, si può visualizzare questo messaggio di errore:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

In questo caso, prima di poter spostare l'app nel piano di destinazione, è necessario:
- Portare il piano tariffario del piano di destinazione al livello **Basic** o superiore, oppure
- Rimuovere tutte le connessioni SSL dell'app.

## <a name="move-an-app-to-a-different-region"></a>Spostare un'app in un'area diversa

L'area in cui viene eseguita l'app è l'area del piano di servizio app. Tuttavia, non è possibile modificare l'area di un piano servizio app. Se si vuole eseguire l'app in un'area diversa, un'alternativa consiste nel clonarla. Con la clonazione si crea una copia dell'app in un piano di servizio app nuovo o esistente di qualsiasi area.

**Clona app** è disponibile nella sezione **Strumenti di sviluppo** del menu.

> [!IMPORTANT]
> La clonazione presenta alcune limitazioni. Per altre informazioni vedere [Clonazione di app del servizio app di Azure con PowerShell](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Scalare un piano di servizio app

Per passare a un piano tariffario superiore per un piano di servizio app, vedere [Aumentare le prestazioni di un'app in Azure](web-sites-scale.md).

Per scalare il numero di istanze di un'app, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminare un piano di servizio app

Per evitare costi imprevisti, quando si elimina l'ultima app in un piano di servizio app, servizio app elimina anche il piano per impostazione predefinita. Se invece si sceglie di mantenere il piano, si consiglia di cambiare il suo livello in **Gratuito**, in modo che non venga addebitato alcun costo.

> [!IMPORTANT]
> Anche i piani di servizio app a cui non sono associate app vengono addebitati, in quanto continuano a riservare le istanze VM configurate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aumentare le prestazioni di un'app in Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
