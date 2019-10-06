---
title: File di inclusione
description: File di inclusione
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978595"
---
## <a name="azure-active-directory-legacy-registration"></a>Azure Active Directory registrazione legacy

1. Nel [portale di Azure](https://portal.azure.com) aprire **Azure Active Directory** nel riquadro sinistro e quindi aprire il riquadro **Proprietà**. Copia il valore di **ID directory** in un file temporaneo. Questo valore verrà usato per configurare un'applicazione di esempio nella sezione successiva.

    [ID directory Active Directory ![Azure](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. Nel [portale di Azure](https://portal.azure.com)aprire **Azure Active Directory** dal riquadro sinistro, quindi aprire il riquadro **registrazioni app (legacy)** . Selezionare il pulsante **nuova registrazione applicazione** .

1. Assegnare un nome descrittivo a questa registrazione app nella casella **Nome**. Scegliere **Nativa** per **Tipo di applicazione** e `https://microsoft.com` per **URI di reindirizzamento**. Selezionare **Create**.

    [![Riquadro Crea](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Aprire l'app registrata e copiare il valore del campo **ID applicazione** in un file temporaneo. Questo valore identifica l'app di Azure Active Directory. L'ID applicazione verrà usato per configurare l'applicazione di esempio nelle sezioni seguenti.

    [![ID dell'applicazione Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Aprire il riquadro Registrazioni app. Selezionare **Impostazioni** > **Autorizzazioni necessarie** e quindi:

   a. Selezionare **Aggiungi** in alto a sinistra per aprire il riquadro **Aggiungi accesso all'API**.

   b. Scegliere **Selezionare un'API** e cercare **Gemelli digitali di Azure**. Se la ricerca non individua l'API, cercare invece **Azure Smart Spaces**.

   c. Selezionare l'opzione **Azure Digital Twins (Azure Smart Spaces Service)** (Gemelli digitali di Azure - servizio Azure Smart Spaces) e scegliere **Seleziona**.

   d. Scegliere **Selezionare le autorizzazioni**. Selezionare la casella di controllo delle autorizzazioni delegate **Accesso in lettura/scrittura** e scegliere **Seleziona**.

   e. Selezionare **Fine** nel riquadro **Aggiungi accesso all'API**.

   f. Nel riquadro **autorizzazioni necessarie** selezionare il pulsante **Concedi autorizzazioni** e accettare il riconoscimento visualizzato. Se l'autorizzazione non viene concessa per questa API, contattare l'amministratore.

      [riquadro autorizzazioni ![Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
