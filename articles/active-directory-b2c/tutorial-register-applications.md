---
title: "Esercitazione: Registrare un'applicazione - Azure Active Directory B2C | Microsoft Docs"
description: Informazioni su come registrare un'applicazione Web in Azure Active Directory B2C usando il portale di Azure.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3858a6eac349a13ef122a723b3cc13964415ac55
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697831"
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
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Applicazioni** e quindi **Aggiungi**.
4. Immettere un nome per l'applicazione. Ad esempio, *webapp1*.
5. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
6. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. È ad esempio possibile impostarlo per l'ascolto in locale su `https://localhost:44316`. Se non si conosce ancora il numero di porta, è possibile immettere un valore segnaposto e modificarlo in un secondo momento. A scopo di test, è possibile impostare l'URL su `https://jwt.ms`, in modo da visualizzare il contenuto di un token per l'ispezione. Per questa esercitazione, impostarlo su `https://jwt.ms`. 

    L'URL di risposta deve iniziare con lo schema `https` e tutti i valori degli URL di risposta devono condividere un singolo dominio DNS. Se ad esempio l'applicazione ha un URL di risposta `https://login.contoso.com`, è possibile aggiungerne altri, come questo URL: `https://login.contoso.com/new`. In alternativa, è possibile fare riferimento a un sottodominio DNS di `login.contoso.com`, ad esempio `https://new.login.contoso.com`. Se si vuole avere un'applicazione con `login-east.contoso.com` e `login-west.contoso.com` come URL di risposta, è necessario aggiungere tali URL nell'ordine seguente: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Gli ultimi due URL possono essere aggiunti perché sono sottodomini del primo, ovvero `contoso.com`.

7. Fare clic su **Create**(Crea).

## <a name="create-a-client-secret"></a>Creare un segreto client

Se l'applicazione scambia un codice per un token, è necessario creare un segreto dell'applicazione.

1. Selezionare **Chiavi** e quindi fare clic su **Genera chiave**.
2. Selezionare **Salva** per visualizzare la chiave. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Registrare un'applicazione Web
> * Creare un segreto client

> [!div class="nextstepaction"]
> [Creare flussi utente in Azure Active Directory B2C](tutorial-create-user-flows.md)
