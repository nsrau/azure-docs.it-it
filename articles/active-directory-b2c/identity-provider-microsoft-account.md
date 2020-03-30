---
title: Configurare l'iscrizione e l'accesso con un account Microsoft
titleSuffix: Azure AD B2C
description: Fornire l'iscrizione e l'accesso ai clienti con account Microsoft nelle applicazioni usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188019"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Microsoft tramite Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft

Per usare un account Microsoft come provider di [identità](openid-connect.md) in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C. Se non si dispone già di un account [https://www.live.com/](https://www.live.com/)Microsoft, è possibile ottenerne uno all'indirizzo .

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. In **Tipi di account supportati**selezionare Account in qualsiasi directory **dell'organizzazione e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com).** Questa opzione è destinata al set più ampio di identità Microsoft.This option targets the wide set of Microsoft identities.

   Per ulteriori informazioni sulle diverse selezioni dei tipi di account, vedere [Guida introduttiva: Registrare un'applicazione con la piattaforma di identità Microsoft](../active-directory/develop/quickstart-register-app.md).
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `your-tenant-name` con il nome del tenant B2C di Azure AD.
1. Seleziona **Registrazione**
1. Registrare **l'ID applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.
1. Selezionare **Certificati & segreti**
1. Fare clic su **Nuovo segreto client**
1. Immettere una **Descrizione** per il segreto, ad esempio *Password applicazione 1*, quindi fare clic su **Aggiungi**.
1. Registrare la password dell'applicazione visualizzata nella colonna **Valore.** Questa operazione è necessaria quando si configura il provider di identità nella sezione successiva.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurare un account Microsoft come provider di identità

1. Accedere al [portale](https://portal.azure.com/) di Azure come amministratore globale del tenant B2C di Azure AD.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Provider di identità**, quindi **Account Microsoft**.
1. Immettere un **Nome**. Ad esempio, *MSA*.
1. Per **ID client**immettere l'ID applicazione (client) dell'applicazione Azure AD creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.
