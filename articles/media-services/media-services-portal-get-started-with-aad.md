---
title: Introduzione all'autenticazione di Azure AD tramite il portale di Azure| Microsoft Docs
description: Informazioni su come usare il portale di Azure per accedere all'autenticazione di Azure Active Directory (Azure AD) per usare l'API Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0a32c758693c1889d7acfa6510bffba46b7eabc2
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introduzione all'autenticazione di Azure AD tramite il portale di Azure

Informazioni su come usare il portale di Azure per accedere all'autenticazione di Azure Active Directory (Azure AD) per accedere all'API Servizi multimediali di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si dispone di un account, iniziare con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
- Assicurarsi di rivedere l'argomento di [panoramica dell'accesso all'API Servizi multimediali di Azure con autenticazione Azure Active Directory](media-services-use-aad-auth-to-access-ams-api.md). 

Quando si usa l'autenticazione di Azure AD con Servizi multimediali di Azure, sono disponibili due opzioni di autenticazione:

- **Autenticazione utente**. Consente di autenticare una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 
- **Autenticazione basata su entità servizio**. Consente di autenticare un servizio. Le applicazioni che solitamente usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio App Web, app per le funzioni, app per la logica, API o microservizi.

> [!IMPORTANT]
> Servizi multimediali supporta attualmente il modello di autenticazione del Servizio di controllo di accesso di Azure. L'autorizzazione di Controllo di accesso, tuttavia, verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire al più presto la migrazione al modello di autenticazione di Azure AD.

## <a name="select-the-authentication-method"></a>Selezionare il metodo di autenticazione

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali.
2. Scegliere come connettersi all'API Servizi multimediali.

    ![Selezionare la pagina del metodo di connessione](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Autenticazione utente

Per connettersi all'API Servizi multimediali usando l'opzione di autenticazione utente, l'app client deve richiedere un token di Azure AD che contenga i parametri seguenti:  

* Endpoint del tenant di Azure AD
* URI di risorsa di Servizi multimediali
* ID client dell'applicazione Servizi multimediali (nativa) 
* URI di reindirizzamento dell'applicazione Servizi multimediali (nativa) 
* URI di risorsa per Servizi multimediali REST

È possibile ottenere i valori per questi parametri nella pagina **API Servizi multimediali con l'autenticazione utente**. 

![Connettersi con la pagina di autenticazione utente](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Se ci si connette all'API Servizi multimediali con Media Services Microsoft .NET SDK, i valori richiesti sono disponibili nell'SDK. Per altre informazioni, vedere [Use Azure AD authentication to access the Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Azure con .NET).

Se non si usa l'SDK del client .NET di Servizi multimediali, è necessario creare manualmente una richiesta di token di Azure AD tramite i parametri descritti in precedenza. Per altre informazioni, vedere [Come usare Azure AD Authentication Library per ottenere il token di Azure AD](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per connettersi all'API Servizi multimediali di Azure con l'opzione dell'entità servizio, l'app di livello intermedio (API o applicazione Web) deve richiedere un token di Azure AD che abbia i parametri seguenti:  

* Endpoint del tenant di Azure AD
* URI di risorsa di Servizi multimediali 
* URI di risorsa per Servizi multimediali REST
* Valori dell'applicazione Azure AD: **ID client** e **Segreto client**

È possibile ottenere i valori per questi parametri nella pagina **Connettersi all'API Servizi multimediali con l'entità servizio** . In questa pagina è possibile creare una nuova applicazione Azure AD o selezionarne una esistente. Dopo avere selezionato l'app Azure AD, è possibile ottenere l'ID client (ID applicazione) e generare i valori del segreto client (chiave). 

![Connettersi alla pagina entità servizio](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Quando si apre il pannello **Entità servizio**, viene selezionata la prima applicazione Azure AD che soddisfa i criteri seguenti:

- Si tratta di un'applicazione Azure AD registrata.
- Dispone di autorizzazioni di controllo degli accessi in base al ruolo di proprietario o collaboratore per l'account.

Dopo avere creato o selezionato un'app Azure AD, è possibile creare e copiare un segreto client (chiave) e l'ID client (ID applicazione). L'ID client e il segreto client sono necessari per ottenere il token di accesso in questo scenario.

Se non si dispone delle autorizzazioni per creare app Azure AD nel dominio, i controlli del pannello dell'app Azure AD non vengono visualizzati e viene visualizzato un messaggio di avviso.

Se ci si connette all'API Servizi multimediali con Media Services .NET SDK, vedere [Use Azure AD authentication to access the Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Azure con .NET).

Se non si usa l'SDK del client .NET di Servizi multimediali, è necessario creare una richiesta di token di Azure AD manualmente specificando i parametri descritti in precedenza. Per altre informazioni, vedere [Come usare Azure AD Authentication Library per ottenere il token di Azure AD](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Ottenere l'ID client e il segreto client

Dopo avere selezionato un'app Azure AD esistente o avere selezionato l'opzione per crearne una nuova, vengono visualizzati i pulsanti seguenti:

![Pulsante Gestisci autorizzazioni e pulsante Gestisci l'applicazione](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Per aprire il pannello dell'applicazione Azure AD, fare clic su **Gestisci l'applicazione**. Nel pannello **Gestisci l'applicazione** è possibile ottenere l'ID client (ID applicazione) dell'app. Per generare un segreto client (chiave), selezionare **Chiavi**.

![Opzione Chiavi del pannello Gestisci l'applicazione](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Gestire le autorizzazioni e l'applicazione

Dopo avere selezionato l'applicazione Azure AD, è possibile gestire l'applicazione e le autorizzazioni. Per configurare l'applicazione Azure AD in modo che acceda ad altre applicazioni, fare clic su **Gestisci autorizzazioni**. Per le attività di gestione, ad esempio la modifica di chiavi e gli URL di risposta, o per modificare il manifesto dell'applicazione, fare clic su **Gestisci l'applicazione**.

### <a name="edit-the-apps-settings-or-manifest"></a>Modificare le impostazioni o il manifesto dell'app

Per modificare le impostazioni o il manifesto dell'app, fare clic su **Gestisci l'applicazione**.

![Pagina Gestisci l'applicazione](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-portal-upload-files.md).

