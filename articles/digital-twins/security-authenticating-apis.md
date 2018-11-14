---
title: Informazioni sull'autenticazione delle API di Gemelli digitali di Azure | Microsoft Docs
description: Usare Gemelli digitali di Azure per eseguire la connessione e l'autenticazione alle API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016217"
---
# <a name="connect-and-authenticate-to-apis"></a>Connettersi alle API ed eseguire l'autenticazione

Gemelli digitali di Azure usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti e la protezione delle applicazioni. Azure AD supporta l'autenticazione per un'ampia gamma di architetture moderne. Tutte le architetture sono basate sui protocolli standard OAuth 2.0 oppure OpenID Connect. Inoltre, gli sviluppatori possono usare Azure AD per compilare applicazioni a tenant singolo e line-of-business (LOB). Gli sviluppatori possono usare Azure AD anche per sviluppare applicazioni multi-tenant.

Per una panoramica di Azure AD, visitare la [pagina relativa ai concetti fondamentali](https://docs.microsoft.com/azure/active-directory/fundamentals/index) per guide dettagliate, informazioni sui concetti e progetti di avvio rapido.

Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare l'applicazione con Azure AD. Per istruzioni dettagliate e gli screenshot, vedere [questo avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

Azure AD supporta [cinque scenari applicativi principali](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types):

* Applicazione a pagina singola: un utente ha necessità di accedere a un'applicazione a pagina singola protetta da Azure AD.
* Da Web browser ad applicazione Web: un utente ha necessità di accedere a un'applicazione Web protetta da Azure AD.
* Da applicazione nativa ad API Web: un'applicazione nativa in esecuzione su un telefono, un tablet o un PC deve autenticare un utente per ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione Web ad API Web: un'applicazione Web deve ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione server o daemon ad API Web: un'applicazione daemon o un'applicazione server priva di interfaccia utente Web deve ottenere le risorse da un'API Web protetta da Azure AD.

La libreria di autenticazione di Azure offre numerosi modi per acquisire i token di Active Directory. Per informazioni dettagliate sugli esempi di codice e libreria, vedere [questo articolo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chiamare Gemelli digitali da un'API Web di livello intermedio

Quando progettano soluzioni per Gemelli digitali, in genere gli sviluppatori creano un'API o un'applicazione di livello intermedio. L'app o l'API chiama quindi l'API di Gemelli digitali downstream. Gli utenti eseguono prima l'autenticazione nell'applicazione di livello intermedio e quindi viene usato un flusso di token on-behalf-of per la chiamata downstream. Per istruzioni su come orchestrare il flusso on-behalf-of, vedere [questa pagina](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Esempi di codice sono disponibili anche in [questa pagina](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Eseguire i test con il client Postman

Per iniziare a usare le API di Gemelli digitali, è possibile usare un client come Postman come ambiente API. Postman consente di creare rapidamente richieste HTTP complesse. I passaggi seguenti illustrano come ottenere un token di Azure AD che è necessario per chiamare Gemelli digitali nell'interfaccia utente di Postman.


1. Accedere a https://www.getpostman.com/ per scaricare l'app.
1. Seguire i passaggi di questo [avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) per creare un'applicazione Azure AD. In alternativa è possibile riusare una registrazione esistente. 
1. In **Required permissions** (Autorizzazioni necessarie) immettere "Azure Digital Twins" (Gemelli digitali di Azure) e selezionare **Delegated Permissions** (Autorizzazioni delegate). Selezionare quindi **Grant Permissions** (Concedi autorizzazioni).
1. Aprire il manifesto dell'applicazione e impostare **oauth2AllowImplicitFlow** su true.
1. Configurare un URL di risposta su [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Selezionare la scheda **Authorization** (Autorizzazione), **OAuth 2.0** e quindi **Get New Access Token** (Ottieni nuovo token di accesso).

    |**Campo**  |**Valore** |
    |---------|---------|
    | Tipo di concessione | Implicit (Implicita) |
    | Callback URL (URL callback) | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Auth URL (URL autorizzazione) | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | ID client | Usare l'ID applicazione per l'app di Azure AD creata o riconfigurata nel passaggio 2. |
    | Scope | Lasciare vuoto. |
    | Stato | Lasciare vuoto. |
    | Autenticazione client | Inviare come intestazione con autenticazione di base. |

1. Selezionare **Request Token** (Richiedi token).

    >[!NOTE]
    >Se viene visualizzato un messaggio di errore che indica che non è stato possibile completare il processo di OAuth 2, provare a eseguire queste operazioni:
    > * Chiudere Postman, riaprirlo e riprovare.
   
1. Scorrere verso il basso e selezionare **Use Token** (Usa token).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla sicurezza di Gemelli digitali di Azure, vedere [Creare e gestire assegnazioni di ruolo](./security-create-manage-role-assignments.md).
