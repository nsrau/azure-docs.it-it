---
title: Configurare i token - Azure Active Directory B2C Documenti Microsoft
description: Informazioni su come configurare la durata del token e le impostazioni di compatibilità in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189618"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurare i token in Azure Active Directory B2CConfigure tokens in Azure Active Directory B2C

In questo articolo viene illustrato come configurare la durata e la [compatibilità di un token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="configure-token-lifetime"></a>Configurare la durata del tokenConfigure token lifetime

È possibile configurare la durata del token in qualsiasi flusso utente.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant B2C di Azure AD.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Flussi utente (criteri)**.
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.
7. In **Durata token**modificare le proprietà seguenti in base alle esigenze dell'applicazione:

    ![Impostazioni delle proprietà della durata dei token nel portale di AzureToken lifetime property settings in the Azure portal](./media/configure-tokens/token-lifetime.png)

8. Fare clic su **Salva**.

## <a name="configure-token-compatibility"></a>Configurare la compatibilità dei token

1. Selezionare **Flussi utente (criteri)**.
2. Aprire il flusso utente creato in precedenza.
3. Selezionare **Proprietà**.
4. In **Impostazioni compatibilità token**modificare le proprietà seguenti in base alle esigenze dell'applicazione:

    ![Impostazioni delle proprietà di compatibilità dei token nel portale di AzureToken compatibility property settings in the Azure portal](./media/configure-tokens/token-compatibility.png)

5. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [usare i token di accesso](access-tokens.md).



