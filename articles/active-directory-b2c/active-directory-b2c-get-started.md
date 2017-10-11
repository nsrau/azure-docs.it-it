---
title: 'Azure Active Directory B2C: creare un tenant di Azure Active Directory B2C | Microsoft Docs'
description: Un argomento su come creare un tenant di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: patricka
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/07/2017
ms.author: swkrish
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 213d532f484056f3833c743d25c5e6faa5b732e6
ms.contentlocale: it-it
ms.lasthandoff: 09/01/2017

---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Creare un tenant di Azure Active Directory B2C nel portale di Azure

Questa guida introduttiva permette di creare un tenant di Microsoft Azure Active Directory (Azure AD) B2C in pochi minuti. Al termine, sarà disponibile un tenant B2C, noto anche come directory, da usare per la registrazione di applicazioni B2C.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

##  <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

Le funzionalità B2C non possono essere abilitate nei tenant esistenti. A questo scopo, è necessario creare un tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Congratulazioni, è stato creato un tenant di Azure Active Directory B2C. L'amministratore globale del tenant può aggiungere altri amministratori globali in base alle esigenze. Per passare al nuovo tenant, fare clic sul *collegamento per gestire il nuovo tenant*.

![Collegamento per gestire il nuovo tenant](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Se si prevede di usare un tenant B2C per un'app di produzione, vedere l'articolo sui [tenant B2C a livello di produzione e di anteprima](active-directory-b2c-reference-tenant-type.md). Quando si elimina un tenant B2C esistente e lo si crea di nuovo con lo stesso nome di dominio, si verificano alcuni problemi noti. È necessario creare un tenant B2C con un nome di dominio diverso.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Collegare il tenant alla sottoscrizione

È necessario collegare il tenant di Azure AD B2C alla sottoscrizione di Azure per abilitare tutte le funzionalità B2C e pagare i costi di utilizzo. Per altre informazioni, vedere [questo articolo](active-directory-b2c-how-to-enable-billing.md). Se si non collega il tenant di Azure AD B2C alla sottoscrizione di Azure, alcune funzionalità saranno bloccate e verrà visualizzato un messaggio di avviso ("Nessuna sottoscrizione collegata a questo tenant B2C oppure la sottoscrizione richiede attenzione") nelle impostazioni B2C. È importante eseguire questo passaggio prima dell'invio delle applicazioni alla produzione.

## <a name="easy-access-to-settings"></a>Accedere facilmente alle impostazioni

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Per accedere al pannello, è anche possibile immettere `Azure AD B2C` in **Cerca risorse** nella parte superiore del portale. Nell'elenco dei risultati selezionare **Azure AD B2C** per accedere al pannello delle impostazioni B2C.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrare l'applicazione B2C in un tenant B2C](active-directory-b2c-app-registration.md)
