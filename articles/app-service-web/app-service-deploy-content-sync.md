---
title: Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
description: Informazioni su come distribuire l&quot;app nel servizio app di Azure tramite la sincronizzazione dei contenuti da una cartella nel cloud.
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ddda0e60dc9d5414142791175b77a5a65e3f40e3


---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
Questa esercitazione spiega come effettuare una distribuzione nel [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) sincronizzando i contenuti dai servizi di archiviazione cloud più diffusi, come Dropbox e OneDrive. 

## <a name="a-nameoverviewaoverview-of-content-sync-deployment"></a><a name="overview"></a>Panoramica della distribuzione per la sincronizzazione dei contenuti
La distribuzione per la sincronizzazione dei contenuti on demand si basa sul [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki) integrato con il servizio app. Nel [portale di Azure](https://portal.azure.com)è possibile designare una cartella speciale nel servizio di archiviazione cloud, utilizzare il codice e il contenuto dell'applicazione disponibile in tale cartella e procedere alla sincronizzazione con il servizio app con un semplice clic del mouse. La sincronizzazione usa il processo Kudu per la compilazione e la distribuzione. 

## <a name="a-namecontentsyncahow-to-enable-content-sync-deployment"></a><a name="contentsync"></a>Come abilitare la distribuzione per la sincronizzazione dei contenuti
Per abilitare la sincronizzazione del contenuto dal [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Nel pannello dell'app del portale di Azure fare clic su **Impostazioni** > **Origine distribuzione**. Fare clic su **Scegliere l'origine** e quindi selezionare **OneDrive** o **Dropbox** come origine per la distribuzione. 
   
    ![Sincronizzazione dei contenuti](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > A causa delle differenze sottostanti nelle API, **OneDrive for Business** non è attualmente supportato. 
   > 
   > 
2. Completare il flusso di lavoro di autorizzazione per consentire al servizio app di accedere a un determinato percorso designato predefinito per OneDrive o Dropbox in cui verranno archiviati tutti i contenuti del servizio app.  
    Dopo l'autorizzazione la piattaforma del servizio app consentirà di creare una cartella dei contenuti nel percorso dei contenuti designato oppure di selezionare una cartella esistente in questo stesso percorso. I percorsi dei contenuti designati negli account di archiviazione cloud usati per la sincronizzazione del servizio app sono i seguenti:  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. Dopo la sincronizzazione dei contenuti iniziale, la sincronizzazione può essere avviata su richiesta dal portale di Azure. La cronologia di distribuzione è disponibile nel pannello **Distribuzioni** .
   
    ![Cronologia di distribuzione](./media/app-service-deploy-content-sync/onedrive_sync.png)

Altre informazioni sulla distribuzione con Dropbox sono disponibili in [Distribuzione tramite Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). 




<!--HONumber=Nov16_HO3-->


