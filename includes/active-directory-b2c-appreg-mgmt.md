---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184352"
---
Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **Applicazioni** corrente o la nuova esperienza **Registrazioni app (anteprima)** unificata. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel portale di Azure cercare e selezionare **Azure Active Directory**.
1. In **Gestisci**selezionare **Registrazioni app (legacy)**.
1. Selezionare **Registrazione nuova applicazione**.
1. Immettere un nome per l'applicazione. Ad esempio, *managementapp1*.
1. Per **Tipo di applicazione**, selezionare App Web / **API**.
1. Immettere un URL valido in **URL di accesso**. Ad esempio: `https://localhost`. Non è necessario che l'endpoint sia raggiungibile, ma deve essere un URL valido.
1. Selezionare **Crea**.
1. Registrare **l'ID applicazione** visualizzato nella pagina **Panoramica dell'app registrata.** Questo valore viene utilizzato in un passaggio successivo.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *managementapp1*.
1. Selezionare **Solo account in questa directory dell'organizzazione**.
1. In **Autorizzazioni**deselezionare la casella di controllo *Concedi il consenso dell'amministratore alle autorizzazioni openid e offline_access.*
1. Selezionare **Registra**.
1. Registrare **l'ID applicazione (client)** visualizzato nella pagina di panoramica dell'applicazione. Questo valore viene utilizzato in un passaggio successivo.