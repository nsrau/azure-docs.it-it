---
title: Gestire un piano di servizio app in Azure | Microsoft Docs
description: "Informazioni su come i piani di servizio app eseguono diverse attività per gestire un piano di servizio app."
keywords: servizio app, servizio app di azure, ridimensionare, piano di servizio app, modificare, creare, gestire, gestione
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: c1b832895476e2f64bbae638db76f89890e5c804
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gestire un piano di servizio app in Azure

Un [piano di servizio app](azure-web-sites-web-hosting-plans-in-depth-overview.md) fornisce le risorse che un'app del servizio app deve eseguire. Questa guida dettagliata illustra come gestire un piano di servizio app. 

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

> [!TIP]
> Se è disponibile l'opzione Ambiente del servizio app di Azure, vedere [Creare un'app Web in un ambiente del servizio app](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

È possibile creare un piano di servizio app vuoto o come parte della creazione di app.

Nel [portale di Azure](https://portal.azure.com) fare clic su **Nuovo** > **Web e dispositivi mobili** e quindi selezionare **App Web** o un altro tipo di app del servizio app.

![Creare un'app nel portale di Azure.][createWebApp]

È quindi possibile selezionare un piano di servizio app esistente o creare un piano per la nuova app.

 ![Creare un piano di servizio app.][createASP]

Per creare un piano di servizio app, fare clic su **[+] Crea nuovo**, digitare il nome in **Piano di servizio app** e quindi selezionare una **Località** appropriata. Fare clic su **Piano tariffario**e quindi selezionare un piano tariffario appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare altre opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**. 

Dopo aver scelto il piano tariffario, fare clic sul pulsante **Seleziona** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Spostare un'app in un altro piano di servizio app

È possibile spostare un'app in un altro piano di servizio app, purché il piano di origine e il piano di destinazione si trovino _nello stesso gruppo di risorse e nella stessa area geografica_.

Per spostare un'app in un altro piano, passare all'app che si vuole spostare nel [portale di Azure](https://portal.azure.com).

Nel **Menu** cercare la sezione **Piano di servizio app**.

Selezionare **Cambia il piano di servizio app** per avviare il processo.

**Cambia il piano di servizio app** apre il selettore **Piano di servizio app**. Selezionare un piano esistente in cui spostare questa app. 

> [!IMPORTANT] 
> La pagina **Select App Service plan** (Seleziona piano di servizio app) viene filtrata in base ai criteri seguenti: 
> - È presente nello stesso gruppo di risorse 
> - È presente nella stessa area geografica 
> - È presente nello stesso spazio Web  
> 
> Uno _spazio Web_ è un costrutto logico all'interno del servizio app che definisce un raggruppamento di risorse del server. Un'area geografica (ad esempio gli Stati Uniti occidentali) contiene molti spazi Web per allocare i clienti che usano il servizio app. Attualmente le risorse del servizio app non possono essere spostate tra spazi Web. 
> 

![Pannello di selezione Piano di servizio app.][change]

È previsto un piano tariffario diverso per ogni piano. Quando si sposta un sito dal livello **Gratuito** al livello **Standard**, ad esempio, tutte le app assegnate ad esso possono usare le funzionalità e le risorse del livello **Standard**. Tuttavia, spostando un'app da un piano a più livelli superiore a un piano a più livelli inferiore non si ha più accesso a determinate funzionalità. Se l'app usa una funzionalità non presente nel piano di destinazione, viene visualizzato un errore che mostra quale funzionalità in uso non è disponibile. Ad esempio, se una delle app usa i certificati SSL, si potrebbe visualizzare il messaggio di errore: `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`In questo caso è necessario aumentare il piano tariffario del piano di destinazione su **Basic** o versione successiva, oppure rimuovere tutte le connessioni SSL nell'app prima di poter spostare l'app nel piano di destinazione.

## <a name="move-an-app-to-a-different-region"></a>Spostare un'app in un'area diversa

L'area in cui viene eseguita l'app è l'area del piano di servizio app. Tuttavia, non è possibile modificare l'area di un piano servizio app. Se si vuole eseguire l'app in un'area diversa, un'alternativa consiste nel clonarla. Con la clonazione si crea una copia dell'app in un piano di servizio app nuovo o esistente di qualsiasi area.

**Clona app** è disponibile nella sezione **Strumenti di sviluppo** del menu.

> [!IMPORTANT]
> La clonazione presenta alcune limitazioni, illustrate nell'articolo [Clonare un'app](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Scalare un piano di servizio app

Per aumentare il piano tariffario di un piano di servizio app, vedere [Aumentare le prestazioni di un'app in Azure](web-sites-scale.md).

Per scalare il numero di istanze di un'app, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminare un piano di servizio app

Per evitare costi imprevisti, quando si elimina l'ultima app in un piano di servizio app, servizio app elimina anche il piano per impostazione predefinita. Se invece si sceglie di conservare il piano è consigliabile cambiare il suo livello in **Gratuito**, in modo che non venga addebitato alcun costo.

> [!IMPORTANT]
> Anche i **piani di servizio app** a cui non sono associate app vengono addebitati, in quanto continuano a riservare le istanze VM configurate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aumentare le prestazioni di un'app in Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
