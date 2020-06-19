---
title: Configurare l'iscrizione e l'accesso con un account Microsoft
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con un account Microsoft alle applicazioni tramite Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 375c83445bb559efe5c797e583129cb1b0c2fb65
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83636915"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Microsoft tramite Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft

Per usare un account Microsoft come [provider di identità](openid-connect.md) in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione nel tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C. Se non si possiede già un account Microsoft, è possibile crearne uno sul sito [https://www.live.com/](https://www.live.com/).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD. A tale scopo, selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione (qualsiasi directory di Azure AD - Multi-tenant) e account Microsoft personali (ad esempio, Skype, Xbox)** .

   Per altre informazioni sulla selezione dei diversi tipi di account, vedere [Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `<tenant-name>` con il nome del tenant di Azure AD B2C.
1. Selezionare **Registra**
1. Annotare **l'ID applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.
1. Selezionare **Certificati e segreti**
1. Fare clic su **Nuovo segreto client**
1. Immettere una **Descrizione** per il segreto, ad esempio *Password applicazione 1*, quindi fare clic su **Aggiungi**.
1. Annotare la password dell'applicazione visualizzata nella colonna **Valore**. Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurare un account Microsoft come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Provider di identità**, quindi selezionare **Account Microsoft**.
1. Immettere un **Nome**. Ad esempio, *Account del servizio gestito*.
1. Per **ID client**, immettere l'ID dell'applicazione (client) Azure AD creata in precedenza.
1. Per **Segreto client** immettere il segreto client annotato in precedenza.
1. Selezionare **Salva**.
