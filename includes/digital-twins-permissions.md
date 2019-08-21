---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: fc2e2fc05de66de6f428e6b8ca7c94f82003ba2a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012168"
---
>[!NOTE]
>Questa sezione fornisce istruzioni relative alla [nuova esperienza di registrazione app Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Se sono ancora presenti registrazioni app native legacy, è possibile continuare a usarle se sono ancora supportate. Se inoltre per qualche motivo la nuova modalità di registrazione app non funziona nella configurazione in uso, è possibile provare a creare un'app AAD nativa legacy. Vedere [Registrare un'app Gemelli digitali di Azure con Azure Active Directory legacy](../articles/digital-twins/how-to-use-legacy-aad.md) per altre informazioni. 

1. Nel [portale di Azure](https://portal.azure.com) aprire **Azure Active Directory** dal riquadro sinistro e quindi aprire il riquadro **Registrazioni app**. Selezionare il pulsante **Nuova registrazione**.

    ![App registrata](./media/digital-twins-permissions/aad-app-register.png)

1. Assegnare un nome descrittivo a questa registrazione app nella casella **Nome**. Nella sezione **URI di reindirizzamento (facoltativo)** scegliere **Client pubblico (per dispositivi mobili e desktop)** nel menu a discesa a sinistra e immettere `https://microsoft.com` nella casella di testo a destra. Selezionare **Registra**.

    ![Riquadro Crea](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Per assicurarsi che [l'app sia registrata come *app nativa*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), aprire il riquadro **Autenticazione** per la registrazione app e scorrere verso il basso. Nella sezione **Tipo di client predefinito** scegliere **Sì** per **Consente di gestire l'applicazione come un client pubblico**. 

    ![Tipo nativo predefinito](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Aprire il riquadro**Panoramica** dell'app registrata e copiare i valori delle entità seguenti in un file temporaneo. Questi valori verranno usati per configurare l'applicazione di esempio nelle sezioni seguenti.

    - **ID applicazione (client)**
    - **ID della directory (tenant)**

    ![ID dell'applicazione Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Aprire il riquadro **Autorizzazioni API** per la app registrazione app. Selezionare il pulsante **Aggiungi un'autorizzazione**. Nel riquadro **Richiedi le autorizzazioni dell'API** selezionare la scheda **API usate dall'organizzazione** e quindi cercare:
    
    1. **Gemelli digitali di Azure**. Selezionare l'API **Gemelli digitali di Azure**.

        ![Cercare l'API o Gemelli digitali di Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)

    1. In alternativa, è possibile cercare **Azure Smart Spaces**. Selezionare l'API **Azure Smart Spaces Service** (Servizio Spazi intelligenti Azure).

        ![Cercare l'API per Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)

    > [!NOTE]
    > Il nome esatto che verrà visualizzato durante la ricerca può variare a seconda del tenant di Azure a cui si appartiene.

1. L'API selezionata viene visualizzata come **Gemelli digitali di Azure** nello stesso riquadro **Richiedi le autorizzazioni dell'API**. Selezionare il menu a discesa **Lettura (1)** e quindi la casella di controllo **Read.Write**. Selezionare il pulsante **Aggiungi autorizzazioni**.

    ![Aggiungere autorizzazioni API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. A seconda delle impostazioni dell'organizzazione, potrebbe essere necessario eseguire passaggi aggiuntivi per concedere l'accesso amministratore a questa API. Contattare l'amministratore per altre informazioni. Una volta approvato l'accesso amministratore, la colonna **CONSENSO AMMINISTRATORE OBBLIGATORIO** nel riquadro **Autorizzazioni API** sarà simile a quanto illustrato di seguito per le API:

    ![Aggiungere autorizzazioni API](./media/digital-twins-permissions/aad-app-admin-consent.png)

