---
title: Ottenere gli endpoint per una registrazione dell'app Azure AD
titleSuffix: Microsoft identity platform
description: Come trovare gli endpoint di autenticazione per un'applicazione personalizzata che si sta sviluppando o registrando con Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926681"
---
# <a name="how-to-discover-endpoints"></a>Come individuare gli endpoint

È possibile trovare gli endpoint di autenticazione per l'applicazione nel [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory**.
1. In **Gestisci**selezionare **registrazioni app**e quindi selezionare **endpoint** nel menu in alto.

    Viene visualizzata la pagina **endpoint** che Mostra gli endpoint di autenticazione per il tenant.
    
    Usare l'endpoint che corrisponde al protocollo di autenticazione usato in combinazione con l' **ID applicazione (client)** per creare la richiesta di autenticazione specifica per l'applicazione.

I **cloud nazionali** (ad esempio Azure ad Cina, Germania e governo degli Stati Uniti) hanno il proprio portale di registrazione delle app e Azure ad endpoint di autenticazione. Per altre informazioni, vedere [Panoramica dei cloud nazionali](authentication-national-cloud.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli endpoint nei diversi ambienti di Azure, vedere Panoramica dei [cloud nazionali](authentication-national-cloud.md).
