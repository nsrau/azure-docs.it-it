---
title: Connettersi ad Azure per enti pubblici con Visual Studio | Documentazione Microsoft
description: Informazioni sulla gestione della sottoscrizione in Azure per enti pubblici tramite la connessione a Visual Studio
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Connessione tramite Visual Studio
Visual Studio viene usato dagli sviluppatori per gestire facilmente le sottoscrizioni di Azure durante la compilazione di soluzioni.  Visual Studio attualmente non consente di configurare una connessione ad Azure per enti pubblici nell'interfaccia utente.  

### <a name="updating-visual-studio-for-azure-government"></a>Aggiornamento di Visual Studio per Azure per enti pubblici
Per consentire la connessione di Visual Studio ad Azure per enti pubblici, è necessario aggiornare il Registro di sistema.

1. Chiudere Visual Studio.
2. Creare un file di testo denominato **VisualStudioForAzureGov.reg**.
3. Copiare il testo seguente e incollarlo in **VisualStudioForAzureGov.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Salvare il file e quindi eseguirlo facendo doppio clic su di esso.  Verrà richiesto di unire il file al Registro di sistema.
5. Avviare Visual Studio e iniziare a usare [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> Dopo l'impostazione della chiave del Registro di sistema, saranno accessibili solo le sottoscrizioni di Azure per enti pubblici.  Le sottoscrizioni configurate in precedenza sono ancora visibili, ma non funzionano perché Visual Studio è ora connesso ad Azure per enti pubblici anziché alla versione pubblica di Azure.  Per annullare le modifiche, vedere la procedura illustrata nella sezione seguente.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Annullamento della connessione di Visual Studio ad Azure per enti pubblici
Per consentire la connessione di Visual Studio alla versione pubblica di Azure, è necessario rimuovere le impostazioni del Registro di sistema che abilitano la connessione ad Azure per enti pubblici.

1. Chiudere Visual Studio.
2. Creare un file di testo denominato **VisualStudioForAzureGov_Remove.reg**.
3. Copiare il testo seguente e incollarlo in **VisualStudioForAzureGov_Remove.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Salvare il file e quindi eseguirlo facendo doppio clic su di esso.  Verrà richiesto di unire il file al Registro di sistema.
5. Avviare Visual Studio.

> [!NOTE]
> Dopo l'annullamento della chiave del Registro di sistema, le sottoscrizioni di Azure per enti pubblici vengono visualizzate ma non sono accessibili.  e possono essere rimosse.
> 
> 

