---
title: "Avvio rapido: Cambiare i tipi di account supportati da un'applicazione | Azure"
titleSuffix: Microsoft identity platform
description: In questa guida di avvio rapido si configura un'applicazione registrata con Microsoft Identity Platform per cambiare chi, o quale account, può accedere all'applicazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/27/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 2382eedcc14f683d354b88bf2eb8d53b2af40dbd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083270"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Guida introduttiva: Modificare gli account supportati da un'applicazione

Quando si registra un'applicazione con Microsoft Identity Platform, si specifica chi o quali tipi di account possono accedervi. Ad esempio, è possibile specificare gli account solo della propria organizzazione, per cui l'app è a *tenant singolo*. Oppure è possibile specificare gli account di qualsiasi organizzazione (inclusa la propria), per cui l'app è *multi-tenant*.

Questa guida di avvio rapido illustra come modificare la configurazione dell'applicazione per cambiare chi o quali tipi di account possono accedervi.

## <a name="prerequisites"></a>Prerequisiti

* Completamento di [Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Modificare la registrazione dell'applicazione per supportare account diversi

Per specificare un'impostazione diversa per i tipi di account supportati da una registrazione dell'app esistente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app** , quindi selezionare l'applicazione.
1. Specificare ora chi può usare l'applicazione, ovvero i *destinatari per l'accesso*.

    | Tipi di account supportati | Descrizione |
    |-------------------------|-------------|
    | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si intende creare un'applicazione utilizzabile solo da utenti (o guest) nel tenant *personale*.<br><br>Nota anche come applicazione *line-of-business* (LOB), si tratta di un'applicazione a **singolo tenant** in Microsoft Identity Platform. |
    | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si vuole che gli utenti in *qualsiasi* tenant di Azure AD possano usare l'applicazione. Questa opzione è appropriata se, ad esempio, si sta creando un'applicazione SaaS (Software-As-A-Service) che si intende fornire a più organizzazioni.<br><br>È nota come applicazione **multi-tenant** in Microsoft Identity Platform. |
1. Selezionare **Salva**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Perché il passaggio al multi-tenant può non riuscire

Il passaggio di una registrazione dell'app da singolo a multi-tenant a volte può non riuscire a causa di conflitti di nomi nell'URI dell'ID applicazione. Un esempio di URI dell'ID app è `https://contoso.onmicrosoft.com/myapp`.

L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo. Per un'applicazione a tenant singolo, è sufficiente che l'URI dell'ID app sia univoco all'interno del tenant. Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'app in tutti i tenant. L'univocità globale viene applicata richiedendo che il nome host dell'URI dell'ID app corrisponda a uno dei [domini verificati dell'entità di pubblicazione](howto-configure-publisher-domain.md) del tenant di Azure AD.

Se ad esempio il nome del tenant è *contoso.onmicrosoft.com* , `https://contoso.onmicrosoft.com/myapp` sarà un URI dell'ID app valido. Se il tenant ha il domini verificato *contoso.com* , un URI dell'ID app valido sarà anche `https://contoso.com/myapp`. Se l'URI dell'ID app non segue il secondo modello, `https://contoso.com/myapp`, la conversione della registrazione dell'app in multi-tenant non riesce.

Per altre informazioni sulla configurazione di un dominio dell'entità di pubblicazione verificato, vedere [Configurare un dominio verificato](quickstart-modify-supported-accounts.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: Convertire l'app in multi-tenant](howto-convert-app-to-be-multi-tenant.md)
