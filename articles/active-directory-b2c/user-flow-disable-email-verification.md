---
title: Disabilitare la verifica della posta elettronica durante l'iscrizione del cliente
titleSuffix: Azure AD B2C
description: Informazioni su come disabilitare la verifica della posta elettronica durante l'iscrizione dei clienti in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126739"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica della posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Segui questi passaggi per disabilitare la verifica email:

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Usare il filtro **Directory e sottoscrizione** nel menu superiore per selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso utente per il quale si desidera disabilitare la verifica della posta elettronica. Ad esempio, *B2C_1_signinsignup*.
1. Selezionare **Layout di pagina**.
1. Selezionare **Pagina di iscrizione all'account locale**.
1. In **Attributi utente**selezionare Indirizzo di posta **elettronica**.
1. Nell'elenco a discesa **REQUIRES VERIFICATION** selezionare **No**.
1. Selezionare **Salva**. La verifica della posta elettronica è ora disabilitata per questo flusso utente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare l'interfaccia utente in Azure Active Directory B2C](customize-ui-overview.md)

