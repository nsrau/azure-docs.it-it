---
title: 'Azure Active Directory B2C: registrazione dell&quot;applicazione | Documentazione Microsoft'
description: Come registrare l&quot;applicazione con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: fd22e9596feecbc12e577a4abfb47552e1b6e520
ms.openlocfilehash: da8f083cb7bca59501df080036e789a0fb75731e


---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrare l'applicazione
## <a name="prerequisite"></a>Prerequisito
Per compilare un'applicazione che accetta l'iscrizione e l'accesso dell'utente, è necessario innanzi tutto registrarla con tenant di Azure Active Directory B2C. Per ottenere il tenant, seguire la procedura illustrata in [Azure Active Directory B2C: creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md). Dopo avere eseguito tutti i passaggi nell'articolo, si disporrà del pannello delle funzionalità B2C aggiunto alla schermata iniziale.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Passare al pannello delle funzionalità B2C
Se il pannello delle funzionalità B2C è stato aggiunto a Schermata iniziale, verrà visualizzato appena si accede al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant B2C.

È possibile accedere al pannello anche facendo clic su **Altri servizi** e quindi cercare **Azure AD B2C** nel riquadro di spostamento a sinistra del [portale di Azure](https://portal.azure.com/).

> [!IMPORTANT]
> È necessario avere diritti di amministratore globale del tenant B2C per poter accedere al pannello delle funzionalità B2C. Un amministratore globale o un utente di qualsiasi altro tenant non può accedere.  È possibile passare al tenant B2C usando l'apposito controllo nell'angolo superiore destro del portale di Azure.
> 
> 

## <a name="register-an-application"></a>Registrare un'applicazione
1. Nel pannello delle funzionalità B2C nel portale di Azure fare clic su **Applicazioni**.
2. Fare clic su **+Aggiungi** nella parte superiore del pannello.
3. Immettere un **nome** per l'applicazione che descriva l'applicazione agli utenti. Ad esempio, immettere "App Contoso B2C".
4. Se si sta scrivendo un'applicazione basata sul Web, impostare l'opzione **Includi app Web/API Web** su **Sì**. Gli **URL di risposta** sono gli endpoint a cui Azure AD B2C restituirà qualsiasi token richiesto dall'applicazione. Ad esempio, immettere `https://localhost:44316/`. Se l'applicazione Web chiamerà un'API Web protetta da Azure AD B2C, è opportuno creare anche un **segreto dell'applicazione** facendo clic sul pulsante **Genera chiave**.
   
   > [!NOTE]
   > Un **segreto dell'applicazione** è una credenziale di sicurezza importante e deve essere protetto in modo appropriato.
   > 
   > 
5. Se si sta scrivendo un'applicazione per dispositivi mobili, impostare l'opzione **Includi client nativo** su **Sì**. Copiare l' **URI di reindirizzamento** predefinito creato automaticamente.
6. Fare clic su **Crea** per registrare l'applicazione.
7. Fare clic sull'applicazione appena creata e copiare l' **ID client applicazione** univoco globale che verrà usato in seguito nel codice.

> [!IMPORTANT]
> Le applicazioni create nel pannello delle funzionalità B2C devono essere gestite nella stessa posizione. Le applicazioni B2C, se vengono modificate usando PowerShell o un altro portale, non sono più supportate ed è probabile che non funzionino con Azure AD B2C.
> 
> 

## <a name="build-a-quick-start-application"></a>Creare un'applicazione di avvio rapido
Dopo aver creato un'applicazione registrata con Azure AD B2C, è possibile completare una delle esercitazioni di avvio rapido per essere subito operativi. Di seguito sono elencati alcuni suggerimenti:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]




<!--HONumber=Feb17_HO1-->


