---
title: Configurare i token-Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare le impostazioni di compatibilità e durata dei token in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2ed1426bea920d1ef30fb28ff4a975d1268c12ff
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846920"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurare i token in Azure Active Directory B2C

Questo articolo illustra come configurare la [durata e la compatibilità di un token](active-directory-b2c-reference-tokens.md) in Azure Active Directory (Azure ad) B2C.

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="configure-token-lifetime"></a>Configurare la durata del token

È possibile configurare la durata dei token in qualsiasi flusso utente.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant del Azure AD B2C. Selezionare il **filtro directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant Azure ad B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **flussi utente (criteri)** .
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.
7. In **Durata token**modificare le proprietà seguenti per adattarle alle esigenze dell'applicazione:

    ![Impostazioni delle proprietà di durata del token nel portale di Azure](./media/configure-tokens/token-lifetime.png)

8. Fare clic su **Save**.

## <a name="configure-token-compatibility"></a>Configurare la compatibilità del token

1. Selezionare **flussi utente (criteri)** .
2. Aprire il flusso utente creato in precedenza.
3. Selezionare **Proprietà**.
4. In **impostazioni di compatibilità dei token**modificare le proprietà seguenti per soddisfare le esigenze dell'applicazione:

    ![Impostazioni delle proprietà di compatibilità dei token nel portale di Azure](./media/configure-tokens/token-compatibility.png)

5. Fare clic su **Save**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](active-directory-b2c-access-tokens.md).



