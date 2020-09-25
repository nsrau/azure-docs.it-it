---
title: Configurare l'iscrizione e l'accesso con un account LinkedIn
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account LinkedIn alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259459"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account LinkedIn tramite Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Creare un'applicazione su LinkedIn

Per usare un account LinkedIn come [provider di identità](authorization-code-flow.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si dispone già di un account LinkedIn, è possibile iscriversi all'indirizzo [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Accedere al [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) con le credenziali dell'account LinkedIn.
1. Selezionare **app personali**e quindi fare clic su **Crea app**.
1. Immettere **nome dell'app**, **pagina LinkedIn**, **URL dell'informativa sulla privacy**e **logo dell'app**.
1. Accettare le condizioni per l' **utilizzo dell'API** LinkedIn e fare clic su **Crea app**.
1. Selezionare la scheda **autenticazione** . In **chiavi di autenticazione**copiare i valori per **ID client** e **segreto client**. Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant. **Client Secret** è un'importante credenziale di sicurezza.
1. Selezionare la matita di modifica accanto a **URL di reindirizzamento autorizzati per l'app**e quindi selezionare **Aggiungi URL di reindirizzamento**. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` , sostituendo `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C. Selezionare **Aggiorna**.
2. Per impostazione predefinita, l'app LinkedIn non è approvata per gli ambiti correlati all'accesso. Per richiedere una verifica, selezionare la scheda **prodotti** e quindi selezionare **Accedi con LinkedIn**. Al termine della verifica, gli ambiti necessari verranno aggiunti all'applicazione.
   > [!NOTE]
   > È possibile visualizzare gli ambiti attualmente consentiti per l'app nella scheda **autenticazione** della sezione **ambiti di OAuth 2,0** .

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurare un account LinkedIn come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi fare clic su **LinkedIn**.
1. Immettere un **Nome**. Ad esempio, *LinkedIn*.
1. Per **ID client**, immettere l'ID client dell'applicazione LinkedIn creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.

## <a name="migration-from-v10-to-v20"></a>Migrazione dalla versione 1.0 alla versione 2.0

LinkedIn ha [aggiornato di recente le API dalla versione 1.0 alla versione 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Come parte della migrazione, Azure AD B2C è in grado di ottenere solo il nome completo dell'utente LinkedIn durante l'iscrizione. Se un indirizzo di posta elettronica è uno degli attributi raccolti durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo di posta elettronica e convalidarlo.
