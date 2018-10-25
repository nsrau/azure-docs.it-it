---
title: Informazioni sull'autenticazione delle API di Gemelli digitali di Azure | Microsoft Docs
description: Uso di Gemelli digitali di Azure per connettersi alle API ed eseguire l'autenticazione
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: ef7838c41bb479da273123c2eb3def8e12802390
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351303"
---
# <a name="connect-and-authenticate-to-apis"></a>Connettersi alle API ed eseguire l'autenticazione

Gemelli digitali di Azure usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti e la protezione delle applicazioni. Azure AD supporta l'autenticazione per un'ampia gamma di architetture moderne, tutte basate sui protocolli standard del settore OAuth 2.0 o OpenID Connect. Azure AD offre inoltre supporto sia per gli sviluppatori che creano applicazioni line-of-business (LOB) a singolo tenant sia per quelli che vogliono sviluppare applicazioni multi-tenant.

Per una panoramica di Azure AD, visitare la [pagina relativa ai concetti fondamentali](https://docs.microsoft.com/azure/active-directory/fundamentals/index) per istruzioni dettagliate, informazioni sui concetti e guide introduttive.

Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare l'applicazione con Azure AD. Per istruzioni dettagliate e screenshot, vedere le istruzioni [qui](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Azure AD supporta [cinque scenari applicativi principali](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types):

* Applicazione a pagina singola: un utente deve accedere a un'applicazione a pagina singola protetta da Azure AD.
* Da Web browser ad applicazione Web: un utente deve accedere a un'applicazione Web protetta da Azure AD.
* Da applicazione nativa ad API Web: un'applicazione nativa in esecuzione su un telefono, un tablet o un PC deve autenticare un utente per ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione Web ad API Web: un'applicazione Web deve ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione server o daemon ad API Web: un'applicazione daemon o un'applicazione server priva di interfaccia utente Web deve ottenere le risorse da un'API Web protetta da Azure AD.

La libreria di autenticazione di Azure offre numerosi modi per acquisire i token di Active Directory. Per informazioni approfondite sulla libreria, con esempi di codice, vedere [qui](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Chiamata di Gemelli digitali da un'API Web di livello intermedio

Quando progettano soluzioni di Gemelli digitali, gli sviluppatori in genere scelgono di creare un'API o un'applicazione di livello intermedio che quindi chiama l'API di Gemelli digitali downstream. Gli utenti eseguono prima l'autenticazione nell'applicazione di livello intermedio e quindi viene usato un flusso di token on-behalf-of per la chiamata downstream. Per istruzioni dettagliate su come orchestrare il flusso on-behalf-of, vedere [questa pagina](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). È anche possibile visualizzare esempi di codice [qui](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Eseguire i test con il client Postman

Per iniziare a usare le API di Gemelli digitali, è possibile usare un client come Postman come ambiente API. Postman consente di creare rapidamente richieste HTTP complesse. Le istruzioni seguenti illustrano come ottenere un token di Azure AD necessario per chiamare Gemelli digitali nell'interfaccia utente di Postman.


1. Passare a https://www.getpostman.com/ per scaricare l'app
1. Seguire la procedura illustrata [qui](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) per creare un'applicazione di Azure Active Directory. In alternativa, è possibile scegliere di riutilizzare una registrazione esistente. 
1. In Required permissions (Autorizzazioni necessarie) aggiungere "Azure Digital Twins" (Gemelli digitali di Azure) e selezionare Delegated Permissions (Autorizzazioni delegate). Fare clic su Grant Permissions (Concedi autorizzazioni) per finalizzare l'operazione.
1. Configurare un URL di risposta a [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Selezionare la scheda **Authorization** (Autorizzazione), fare clic su **OAuth 2.0** e selezionare **Get New Access Token** (Ottieni nuovo token di accesso).

    |**Campo**  |**Valore** |
    |---------|---------|
    | Grant Type (Tipo di concessione) | Implicit (Implicita) |
    | Callback URL (URL callback) | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Auth URL (URL autorizzazione) | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Client Id | Usare l'ID applicazione per l'app di Azure AD creata o riutilizzata nel passaggio 1 |
    | Scope | lasciare vuoto |
    | Stato | lasciare vuoto |
    | Client Authentication (Autenticazione client) | Inviare come intestazione con autenticazione di base |

1. Fare clic su **Request Token** (Richiedi token).

    >[!NOTE]
    >Se viene visualizzato un messaggio di errore che indica che non è stato possibile completare il processo di OAuth 2, provare a eseguire queste operazioni:
    > * Chiudere Postman, riaprirlo e riprovare.
   
1. Scorrere verso il basso e fare clic su **Use Token** (Usa token).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla sicurezza di Gemelli digitali di Azure, vedere [Creare e gestire assegnazioni di ruolo](./security-create-manage-role-assignments.md).
