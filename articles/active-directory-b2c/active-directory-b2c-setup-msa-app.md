---
title: Configurare l'iscrizione e l'accesso con un account Microsoft-Azure Active Directory B2C
description: Fornire l'iscrizione e l'accesso ai clienti con account Microsoft nelle applicazioni usando Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 134ca92671a41eb53201101a3622215466a60c09
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065103"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Microsoft tramite Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft

Per usare un account Microsoft come [provider di identità](active-directory-b2c-reference-oidc.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant Azure ad. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C. Se non si dispone già di una account Microsoft, è possibile ottenerne [https://www.live.com/](https://www.live.com/)una in.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. In **tipi di account supportati**selezionare **account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** . Questa opzione è destinata al set più ampio di identità Microsoft.

   Per ulteriori informazioni sulle diverse selezioni del tipo di conto, vedere [Guida introduttiva: Registrare un'applicazione con la piattaforma](../active-directory/develop/quickstart-register-app.md)di identità Microsoft.
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `your-tenant-name` con il nome del tenant Azure ad B2C.
1. Seleziona **Registro**
1. Registrare l' **ID dell'applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.
1. Selezionare i **certificati & segreti**
1. Fare clic su **nuovo segreto client**
1. Immettere una **Descrizione** per il segreto, ad esempio *password applicazione 1*, quindi fare clic su **Aggiungi**.
1. Registrare la password dell'applicazione visualizzata nella colonna **valore** . Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurare un account Microsoft come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi selezionare **account Microsoft**.
1. Immettere un **Nome**. Ad esempio, *MSA*.
1. Per **ID client**, immettere l'ID applicazione (client) dell'applicazione Azure ad creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.
