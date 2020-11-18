---
title: "Esercitazione: Aggiungere l'autenticazione a un'app Web nel Servizio app di Azure | Azure"
description: Questa esercitazione illustra come abilitare l'autenticazione e l'autorizzazione per un'app Web in esecuzione nel Servizio app di Azure.  Limitare l'accesso all'app Web agli utenti dell'organizzazione.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a1b2809371a01b1c6fd822e5c4aaa197d0de3c1b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428294"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Esercitazione: Aggiungere l'autenticazione all'app Web in esecuzione nel Servizio app di Azure

Informazioni su come abilitare l'autenticazione per l'app Web in esecuzione nel Servizio app di Azure e limitare l'accesso agli utenti dell'organizzazione.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Consentire l'accesso degli utenti" border="false":::

Servizio app di Azure fornisce supporto integrato per l'autenticazione e l'autorizzazione ed è quindi possibile consentire l'accesso degli utenti e l'accesso ai dati senza scrivere codice, o con una minima quantità di codice, nell'app Web.  L'uso del modulo di autenticazione/autorizzazione del servizio app non è obbligatorio, ma consente di semplificare l'autenticazione e l'autorizzazione per l'app. Questo articolo illustra come proteggere l'app Web con il modulo di autenticazione/autorizzazione del servizio app usando Azure Active Directory come provider di identità.

Il modulo di autenticazione/autorizzazione è abilitato e configurato tramite il portale di Azure e le impostazioni dell'app. Non sono necessari SDK, linguaggi specifici o modifiche al codice dell'applicazione. Sono supportati numerosi provider di identità, tra cui: Azure AD, account Microsoft, Facebook, Google e Twitter. Quando il modulo di autenticazione/autorizzazione è abilitato, ogni richiesta HTTP in ingresso passa attraverso tale modulo prima di essere gestita dal codice dell'app.  Per altre informazioni, vedere [Autenticazione e autorizzazione nel Servizio app di Azure](overview-authentication-authorization.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Configurare l'autenticazione per l'app Web
> * Limitare l'accesso all'app Web agli utenti dell'organizzazione

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Creare e pubblicare un'app Web nel servizio app

Per questa esercitazione è necessaria un'app Web distribuita nel Servizio app di Azure.  È possibile usare un'app Web esistente oppure è possibile seguire la guida di [avvio rapido per ASP.NET Core](quickstart-dotnetcore.md) per creare e pubblicare una nuova app Web nel servizio app.

Sia che si usi un'app Web esistente o che se ne crei una nuova, prendere nota del nome dell'app Web e del nome del gruppo di risorse in cui è distribuita l'app Web. Questi nomi saranno necessari nel corso di questa esercitazione. In questa esercitazione i nomi di esempio nelle procedure e negli screenshot contengono *SecureWebApp*.

## <a name="configure-authentication-and-authorization"></a>Configurare l'autenticazione e l'autorizzazione

A questo punto, è disponibile un'app Web in esecuzione nel servizio app.  Si procederà quindi all'abilitazione dell'autenticazione e dell'autorizzazione per l'app Web. Come provider di identità viene usato Azure Active Directory. Per altre informazioni, vedere [Configurare l'autenticazione di Azure Active Directory per un'applicazione dei servizi app](configure-authentication-provider-aad.md).

Nel menu del [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse** oppure cercare e selezionare *Gruppi di risorse* da qualsiasi pagina.

In **Gruppi di risorse** cercare e selezionare il gruppo di risorse desiderato. In **Panoramica** selezionare la pagina di gestione dell'app.

:::image type="content" alt-text="Selezione del servizio app" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

Nel menu a sinistra dell'app selezionare **Autenticazione/Autorizzazione** e quindi abilitare l'autenticazione del servizio app scegliendo **Sì**.

In **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

In **Provider di autenticazione** fare clic su **Azure Active Directory**. Selezionare **Rapida**, quindi accettare le impostazioni predefinite per creare una nuova app AD e scegliere **OK**.

:::image type="content" alt-text="Autenticazione rapida" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

Nella pagina **Autenticazione/Autorizzazione** selezionare **Salva**.

Quando viene visualizzata la notifica con il messaggio `Successfully saved the Auth Settings for <app-name> App`, aggiornare la pagina del portale.

A questo punto si dispone di un'app protetta tramite l'autenticazione e l'autorizzazione del servizio app.

## <a name="verify-limited-access-to-the-web-app"></a>Verificare l'accesso limitato all'app Web

Quando è stato abilitato il modulo di autenticazione e autorizzazione del servizio app, è stata creata una registrazione app nel tenant di Azure AD.  Il nome visualizzato della registrazione app corrisponde a quello dell'app Web. Per verificare le impostazioni, selezionare **Azure Active Directory** dal menu del portale e quindi scegliere **Registrazioni app**.  Selezionare la registrazione app creata in precedenza.  Nella panoramica verificare che l'opzione **Tipi di account supportati** sia impostata su **Solo l'organizzazione personale**.

:::image type="content" alt-text="Verificare l'accesso" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Per verificare che l'accesso all'app sia limitato agli utenti dell'organizzazione, avviare un browser in modalità privata o anonima e passare a `https://<app-name>.azurewebsites.net`.  Si verrà reindirizzati indirizzati a una pagina di accesso protetta per verificare che gli utenti non autenticati non siano autorizzati ad accedere al sito.  Eseguire l'accesso come utente nell'organizzazione per ottenere l'accesso al sito.  È anche possibile avviare un nuovo browser e provare ad accedere con un account personale per verificare che gli utenti esterni all'organizzazione non siano autorizzati ad accedere.  

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'esercitazione è stata completata e l'app Web o le risorse associate non sono più necessarie, [pulire le risorse create](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Configurare l'autenticazione per l'app Web
> * Limitare l'accesso all'app Web agli utenti dell'organizzazione

> [!div class="nextstepaction"]
> [Il servizio app accede alle risorse di archiviazione](scenario-secure-app-access-storage.md)
