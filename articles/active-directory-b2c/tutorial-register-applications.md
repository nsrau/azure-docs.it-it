---
title: "Esercitazione: registrare un'applicazione-Azure Active Directory B2C"
description: Informazioni su come registrare un'applicazione Web in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 651c15c8206f7956bb35520f9c5837cb0c9308f9
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980699"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Esercitazione: Registrare un'applicazione in Azure Active Directory B2C

Prima che le [applicazioni](active-directory-b2c-apps.md) possano interagire con Azure Active Directory (Azure AD) B2C, devono essere registrate in un tenant gestito. Questa esercitazione mostra come registrare un'applicazione Web usando il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se non è ancora stato creato un [tenant Azure AD B2C](tutorial-create-tenant.md), crearlo ora. Usare un tenant Azure AD B2C esistente.

## <a name="register-a-web-application"></a>Registrare un'applicazione Web

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapp1*.
1. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. Ad esempio, è possibile impostarlo in modo che sia `https://localhost:44316`in ascolto localmente in. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento.

    A scopo di test come questa esercitazione, è possibile impostarlo in modo da `https://jwt.ms` visualizzare il contenuto di un token per l'ispezione. Per questa esercitazione, impostare l' **URL** di risposta `https://jwt.ms`su.

    Agli URL di risposta si applicano le restrizioni seguenti:

    * L'URL di risposta deve iniziare con lo `https`schema.
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser considera i percorsi come distinzione tra maiuscole e minuscole, i cookie associati a possono essere esclusi se reindirizzati all' `.../abc/response-oidc` `.../ABC/response-oidc` URL senza corrispondenza tra maiuscole e minuscole.

1. Fare clic su **Crea** per completare la registrazione dell'applicazione.

## <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione scambia un codice per un token, è necessario creare un segreto dell'applicazione.

1. Nella pagina **Azure ad B2C-applicazioni** selezionare l'applicazione creata, ad esempio *app Web 1*.
1. Selezionare **chiavi** e quindi selezionare **Genera chiave**.
1. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Usare questo valore come segreto dell'applicazione nel codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

Successivamente, si apprenderà come creare flussi utente per consentire agli utenti di iscriversi, accedere e gestire i propri profili.

> [!div class="nextstepaction"]
> [Creare flussi utente in Azure Active Directory B2C >](tutorial-create-user-flows.md)
