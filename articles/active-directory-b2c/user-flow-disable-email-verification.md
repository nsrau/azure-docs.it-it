---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente
titleSuffix: Azure AD B2C
description: Informazioni su come disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C.
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126739"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Per disabilitare la verifica tramite posta elettronica, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Usare il filtro **directory + sottoscrizione** nel menu in alto per selezionare la directory che contiene il tenant del Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso utente per il quale si desidera disabilitare la verifica tramite posta elettronica. Ad esempio, *B2C_1_signinsignup*.
1. Selezionare **layout di pagina**.
1. Selezionare **la pagina di iscrizione dell'account locale**.
1. In **attributi utente**selezionare **indirizzo di posta elettronica**.
1. Nell'elenco a discesa **richiesta di verifica** selezionare **No**.
1. Selezionare **Salva**. La verifica tramite posta elettronica è ora disabilitata per questo flusso utente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [personalizzare l'interfaccia utente in Azure Active Directory B2C](customize-ui-overview.md)

