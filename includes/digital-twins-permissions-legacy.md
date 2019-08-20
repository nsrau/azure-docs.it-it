---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: ef6b395aeff18a63f52f58e2477679b48a19b002
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624758"
---
1. Nel [portale di Azure](https://portal.azure.com) aprire **Azure Active Directory** nel riquadro sinistro e quindi aprire il riquadro **Proprietà**. Copia il valore di **ID directory** in un file temporaneo. Questo valore verrà usato per configurare un'applicazione di esempio nella sezione successiva.

    ![ID directory di Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. Nel [portale di Azure](https://portal.azure.com)aprire **Azure Active Directory** dal riquadro sinistro, quindi aprire il riquadro **registrazioni app (legacy)** . Selezionare il pulsante **nuova registrazione applicazione** .

1. Assegnare un nome descrittivo a questa registrazione app nella casella **Nome**. Scegliere **Nativa** per **Tipo di applicazione** e `https://microsoft.com` per **URI di reindirizzamento**. Selezionare **Create**.

    ![Riquadro Crea](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Aprire l'app registrata e copiare il valore del campo **ID applicazione** in un file temporaneo. Questo valore identifica l'app di Azure Active Directory. L'ID applicazione verrà usato per configurare l'applicazione di esempio nelle sezioni seguenti.

    ![ID dell'applicazione Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Aprire il riquadro Registrazioni app. Selezionare **Impostazioni** > **Autorizzazioni necessarie** e quindi:

   a. Selezionare **Aggiungi** in alto a sinistra per aprire il riquadro **Aggiungi accesso all'API**.

   b. Scegliere **Selezionare un'API** e cercare **Gemelli digitali di Azure**. Se la ricerca non individua l'API, cercare invece **Azure Smart Spaces**.

   c. Selezionare l'opzione **Azure Digital Twins (Azure Smart Spaces Service)** (Gemelli digitali di Azure - servizio Azure Smart Spaces) e scegliere **Seleziona**.

   d. Scegliere **Selezionare le autorizzazioni**. Selezionare la casella di controllo delle autorizzazioni delegate **Accesso in lettura/scrittura** e scegliere **Seleziona**.

   e. Selezionare **Fine** nel riquadro **Aggiungi accesso all'API**.

   f. Nel riquadro **autorizzazioni necessarie** selezionare il pulsante **Concedi autorizzazioni** e accettare il riconoscimento visualizzato. Se l'autorizzazione non viene concessa per questa API, contattare l'amministratore.

      ![Riquadro Autorizzazioni necessarie](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 
