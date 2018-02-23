---
title: Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
description: Informazioni su come distribuire l'app nel servizio app di Azure tramite la sincronizzazione dei contenuti da una cartella nel cloud.
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
Questa esercitazione illustra come sincronizzare i contenuti dei servizi di archiviazione cloud più diffusi, ad esempio Dropbox e OneDrive, con il [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="overview"></a>Panoramica della distribuzione per la sincronizzazione dei contenuti
La distribuzione per la sincronizzazione dei contenuti on demand si basa sul [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki) integrato con il servizio app. Nel [portale di Azure](https://portal.azure.com) è possibile designare una cartella del servizio di archiviazione cloud, usare il codice e il contenuto dell'app nella cartella e procedere alla sincronizzazione con il servizio app facendo semplicemente clic su un pulsante. La sincronizzazione usa il processo Kudu per la compilazione e la distribuzione. 

## <a name="contentsync"></a>Come abilitare la distribuzione per la sincronizzazione dei contenuti
Per abilitare la sincronizzazione dei contenuti nel [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Nella pagina dell'app nel portale di Azure fare clic su **Impostazioni** > **Origine distribuzione**. Fare clic su **Scegliere l'origine** e quindi selezionare **OneDrive** o **Dropbox** come origine per la distribuzione. 
   
    ![Sincronizzazione dei contenuti](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > A causa delle differenze sottostanti nelle API, **OneDrive for Business** non è attualmente supportato. 
   > 
   > 
2. Completare il flusso di lavoro di autorizzazione per consentire al servizio app di accedere a uno specifico percorso designato predefinito per OneDrive o Dropbox in cui sono archiviati tutti i contenuti del servizio app. Dopo l'autorizzazione, la piattaforma del servizio app consente di creare una cartella nel percorso dei contenuti designato oppure di selezionare una cartella esistente in questo stesso percorso. I percorsi dei contenuti designati negli account di archiviazione cloud usati per la sincronizzazione del servizio app sono i seguenti:  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. Dopo la sincronizzazione iniziale dei contenuti, la sincronizzazione può essere avviata on demand dal portale di Azure. La cronologia delle distribuzioni è disponibile nella pagina **Distribuzioni**.
   
    ![Cronologia di distribuzione](./media/app-service-deploy-content-sync/onedrive_sync.png)

Altre informazioni per la distribuzione di Dropbox sono disponibili in [Deploy from Dropbox] (Distribuire da Dropbox) (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

