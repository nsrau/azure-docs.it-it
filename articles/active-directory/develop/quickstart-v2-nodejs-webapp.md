---
title: Avvio rapido per l'uso di Microsoft Identity Platform con un'app Web Node.js per OIDC | Azure
description: Informazioni su come implementare l'autenticazione in un'applicazione Web Node.js con OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ac3344fe16d24ae116a5fde289421998f11f18
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182029"
---
# <a name="quickstart-add-sign-in-using-oidc-to-a-nodejs-web-app"></a>Guida introduttiva: Usare OIDC per aggiungere l'accesso a un'app Web Node.js

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Questo avvio rapido illustra come configurare l'autenticazione OpenID Connect in un'applicazione Web creata usando Node.js con Express. L'esempio è progettato per l'esecuzione in qualsiasi piattaforma.

## <a name="prerequisites"></a>Prerequisiti

Ecco i prerequisiti per eseguire questo esempio:

* Installare Node.js da http://nodejs.org/

* [Account Microsoft](https://www.outlook.com) o adesione al programma [Office 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registrare l'applicazione 
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account è presente in più di un tenant di Azure AD:
    - Selezionare il profilo dal menu nell'angolo superiore destro della pagina e quindi fare clic su **Cambia directory**.
    - Impostare la sessione sul tenant di Azure AD in cui si vuole creare l'applicazione.

1. Passare a [Azure Active Directory > Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) per registrare l'app.

1. Selezionare **Nuova registrazione**.

1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'app:
    - Nella sezione **Nome** immettere un nome significativo che verrà visualizzato agli utenti dell'app. Ad esempio:  MyWebApp
    - Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .

    Se sono presenti più URI di reindirizzamento, è necessario aggiungerli in un secondo momento dalla scheda **Autenticazione** dopo la creazione dell'app.

1. Selezionare **Registra** per creare l'app.

1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Questo valore sarà necessario per configurare l'applicazione più avanti in questo progetto.

1. Nell'elenco delle pagine per l'app selezionare **Autenticazione**.
    - Nella sezione **URI di reindirizzamento** selezionare **Web** nella casella combinata e immettere l'URI di reindirizzamento seguente: `http://localhost:3000/auth/openid/return`
    - Nella sezione **Impostazioni avanzate** impostare **URL disconnessione** su `http://localhost:3000`.
    - Nella sezione **Impostazioni avanzate > Concessione implicita** selezionare **Token ID** perché con questo esempio è richiesta l'abilitazione del [flusso di concessione implicito](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) per consentire all'utente di accedere.

1. Selezionare **Salva**.

1. Nella sezione **Segreti client** della pagina **Certificati e segreti** scegliere **Nuovo segreto client**.
    - Immettere una descrizione della chiave, ad esempio segreto dell'app.
    - Selezionare una durata per la chiave, ovvero **Tra 1 anno, Tra 2 anni** oppure **Non scade mai**.
    - Quando si fa clic sul pulsante **Aggiungi**, viene visualizzato il valore della chiave. Copiare il valore della chiave e conservarlo in un luogo sicuro.

    Questa chiave sarà necessaria più avanti per configurare l'applicazione. Il valore della chiave non verrà visualizzato di nuovo, né potrà essere recuperato in altro modo, di conseguenza è opportuno prenderne nota non appena viene visualizzato nel portale di Azure.

## <a name="download-the-sample-application-and-modules"></a>Scaricare l'applicazione e i moduli di esempio

A questo punto, clonare il repository di esempio e installare i moduli npm.

Dalla shell o dalla riga di comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

oppure

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Dalla directory radice del progetto eseguire il comando:

`$ npm install`  

## <a name="configure-the-application"></a>Configurare l'applicazione

Specificare i parametri di `exports.creds` nel file config.js come indicato.

* Aggiornare `<tenant_name>` in `exports.identityMetadata` con il nome del tenant di Azure AD nel formato \*.onmicrosoft.com.
* Aggiornare `exports.clientID` con l'ID applicazione di cui è stato preso nota durante la registrazione dell'app.
* Aggiornare `exports.clientSecret` con il segreto dell'applicazione di cui è stato preso nota durante la registrazione dell'app.
* Aggiornare `exports.redirectUrl` con l'URI di reindirizzamento di cui è stato preso nota durante la registrazione dell'app.

**Configurazione facoltativa per le app di produzione:**

* Aggiornare `exports.destroySessionUrl` nel file config. js, se si vuole usare un valore diverso per `post_logout_redirect_uri`.

* Impostare su true `exports.useMongoDBSessionStore` nel file config.js se si vuole usare [MongoDB](https://www.mongodb.com) o altri [archivi sessioni compatibili](https://github.com/expressjs/session#compatible-session-stores).
L'archivio sessioni predefinito in questo esempio è `express-session`. L'archivio sessioni predefinito non è adatto per la produzione.

* Aggiornare `exports.databaseUri`, se si vuole usare l'archivio sessioni MongoDB e un URI di database diverso.

* Aggiornare `exports.mongoDBSessionMaxAge`. In questo punto è possibile specificare per quanto tempo si vuole mantenere una sessione in MongoDB. L'unità di misura è secondo/i.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

Avviare il servizio MongoDB. Se si usa l'archivio sessioni MongoDB in questa app, è necessario [installare MongoDB](http://www.mongodb.org/) e avviare prima il servizio. Se si usa l'archivio sessioni predefinito, è possibile ignorare questo passaggio.

Per eseguire l'app, usare il comando seguente dalla riga di comando.

```
$ node app.js
```

**Se l'output del server è poco comprensibile:** per la registrazione in questo esempio viene usato `bunyan`. La console non è molto utile a meno che non si installi bunyan e si esegua il server come specificato in precedenza e si invia tramite pipe il file binario di bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>L'operazione è completata.

All'indirizzo `http://localhost:3000` sarà in esecuzione un server.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sullo scenario dell'app Web supportato da Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scenario: App Web che concede l'accesso agli utenti](scenario-web-app-sign-user-overview.md)
