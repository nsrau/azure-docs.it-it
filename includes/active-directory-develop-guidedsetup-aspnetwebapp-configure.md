---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180755"
---
## <a name="register-your-application"></a>Registrare l'applicazione

Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, sono possibili due opzioni:

### <a name="option-1-express-mode"></a>Opzione 1: modalità Rapida

È possibile registrare rapidamente l'applicazione seguendo questa procedura:

1. Passare al nuovo riquadro [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Immettere un nome per l'applicazione e fare clic su **Registra**.
1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.

### <a name="option-2-advanced-mode"></a>Opzione 2: modalità Avanzata

Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:

1. Passare a Visual Studio e:
   1. in Esplora soluzioni selezionare il progetto e controllare la finestra Proprietà (se non viene visualizzata la finestra Proprietà, premere F4).
   1. Impostare il valore di SSL abilitato su `True`.
   1. Fare clic con il pulsante destro del mouse sul progetto in Visual Studio e quindi scegliere **Proprietà** e la scheda **Web**. Nella sezione *Server* modificare *URL progetto* specificando l'URL SSL.
   1. Copiare l'URL SSL. Aggiungerlo all'elenco di URL di reindirizzamento nell'elenco corrispondente del portale di registrazione nel passaggio successivo:<br/><br/>![Proprietà del progetto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
   1. Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `ASPNET-Tutorial`.
   1. Aggiungere l'URL SSL copiato da Visual Studio nel passaggio 1 (ad esempio `https://localhost:44368/`) in **URL di risposta** e fare clic su **Registra**.
1. Selezionare il menu **Autenticazione**, impostare **Token ID** in **Concessione implicita** e quindi selezionare **Salva**.
1. Aggiungere il codice seguente in `web.config`, disponibile nella sezione `configuration\appSettings` della cartella radice:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Sostituire `ClientId` con l'ID applicazione appena registrato.
1. Sostituire `redirectUri` con l'URL SSL del progetto.
