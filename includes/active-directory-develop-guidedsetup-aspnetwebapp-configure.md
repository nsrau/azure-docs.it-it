---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7c052f30e564700b0a99b76342dc6c34ec5983b7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "33901279"
---
## <a name="register-your-application"></a>Registrare l'applicazione

Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, sono possibili due opzioni:

### <a name="option-1-express-mode"></a>Opzione 1: Modalità rapida

È possibile registrare rapidamente l'applicazione seguendo questa procedura:

1. Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'installazione guidata sia selezionata
4.  Seguire le istruzioni per aggiungere un URL di reindirizzamento all'applicazione

### <a name="option-2-advanced-mode"></a>Opzione 2: Modalità avanzata

Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, seguire questa procedura:

1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica 
3. Assicurarsi che l'opzione per l'installazione guidata sia deselezionata
4. Fare clic su `Add Platform`, quindi selezionare `Web`
5. Tornare a Visual Studio e in Esplora soluzioni selezionare il progetto, quindi esaminare la finestra Proprietà (se la finestra Proprietà non è visualizzata, premere F4)
6. Impostare il valore di SSL abilitato su `True`
7. Fare clic con il pulsante destro sul progetto in Visual Studio e quindi scegliere **Proprietà** e la scheda **Web**. Nella sezione *Server* modificare *URL progetto* specificando l'URL SSL.
8. Copiare l'URL SSL e aggiungere tale URL all'elenco di URL di reindirizzamento nell'elenco corrispondente del portale di registrazione:<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Aggiungere il codice seguente in `web.config`, disponibile nella sezione `configuration\appSettings` della cartella radice:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Sostituire `ClientId` con l'ID applicazione appena registrato
11. Sostituire `redirectUri` con l'URL SSL del progetto

