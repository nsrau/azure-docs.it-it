---
title: Configurare i token - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare le impostazioni di compatibilità e durata token in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 69a6284ea7b8905bb5efdb1f4c9f26027bd6f9d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689600"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurare i token in Azure Active Directory B2C

In questo articolo descrive come configurare il [durata e la compatibilità di un token](active-directory-b2c-reference-tokens.md) in Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="configure-token-lifetime"></a>Configurare la durata dei token

È possibile configurare la durata dei token in qualsiasi flusso utente.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi che si usa la directory che contiene il tenant di Azure AD B2C. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **flussi utente, criteri,**.
5. Aprire il flusso utente creato in precedenza. 
6. Selezionare **Proprietà**.
7. Sotto **Token lifetime**, regolare le proprietà seguenti per soddisfare le esigenze dell'applicazione:

    ![Configurare la durata dei token](./media/configure-tokens/token-lifetime.png)

8. Fare clic su **Save**.

## <a name="configure-token-compatibility"></a>Configurare la compatibilità del token

1. Selezionare **flussi utente, criteri,**.
2. Aprire il flusso utente creato in precedenza. 
3. Selezionare **Proprietà**.
4. Sotto **impostazioni di compatibilità dei Token**, regolare le proprietà seguenti per soddisfare le esigenze dell'applicazione:

    ![Configurare la compatibilità del token](./media/configure-tokens/token-compatibility.png)

5. Fare clic su **Save**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](active-directory-b2c-access-tokens.md).



