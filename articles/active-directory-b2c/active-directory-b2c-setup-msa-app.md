---
title: Configurazione di iscrizione e l'accesso con un account Microsoft - Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Microsoft alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 82c1be335bfd39d641f0203116e68a4cb4c0a674
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654223"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Microsoft tramite Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft

Per usare un account Microsoft come un [provider di identità](active-directory-b2c-reference-oidc.md) in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione nel tenant di Azure AD. Il tenant di Azure AD non corrisponde al tenant di Azure AD B2C. Se non si ha già un account Microsoft, è possibile ottenere uno alla [ https://www.live.com/ ](https://www.live.com/).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory contenente il tenant di Azure AD facendo clic sul **filtro per directory e sottoscrizione** nel menu in alto e scegliendo tale directory.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
1. Selezionare **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *MSAapp1*.
1. Sotto **tipi di account supportati**, selezionare **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** . Questa opzione è destinato il set più ampio di identità Microsoft.

   Per altre informazioni sulle selezioni del tipo di account diversi, vedere [Guida introduttiva: Registrare un'applicazione con la piattaforma delle identità Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Sotto **URI di reindirizzamento (facoltativo)** , selezionare **Web** e immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nella casella di testo. Sostituire `your-tenant-name` con il nome del tenant Azure AD B2C.
1. Selezionare **registrare**
1. Record di **ID applicazione (client)** illustrato nella pagina di panoramica dell'applicazione. È necessario quando si configura il provider di identità nella sezione successiva.
1. Selezionare **i segreti e certificati**
1. Fare clic su **nuovo segreto client**
1. Immettere un **Description** per la chiave privata, ad esempio *password applicazione 1*, quindi fare clic su **Add**.
1. Registrare la password dell'applicazione visualizzata nei **valore** colonna. È necessario quando si configura il provider di identità nella sezione successiva.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurare un account Microsoft come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
1. Specificare un **Nome**. Ad esempio, immettere *MSA*.
1. Fare clic su **Tipo di provider di identità**, selezionare **Account Microsoft** e fare clic su **OK**.
1. Selezionare **configura questo provider di identità** e immettere l'ID applicazione (client) che è stata registrata in precedenza nella **ID Client** testo casella e immettere il segreto client registrato nel **Client segreto** casella di testo.
1. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione dell'account Microsoft.
