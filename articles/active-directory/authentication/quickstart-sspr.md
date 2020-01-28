---
title: Avvio rapido - Reimpostazione della password self-service di Azure AD
description: Questa guida di avvio rapido illustra come configurare rapidamente la reimpostazione della password self-service di Azure AD per consentire agli utenti di reimpostare le proprie password e ridurre il sovraccarico del reparto IT.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154857"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Avvio rapido: Configurare la reimpostazione della password self-service di Azure Active Directory

In questa guida di avvio rapido si configura la reimpostazione della password self-service di Azure Active Directory (AD) per consentire agli utenti di reimpostare le password o sbloccare i propri account. Con la reimpostazione della password self-service, gli utenti possono reimpostare le proprie credenziali senza assistenza da parte dell'amministratore o del supporto tecnico. Questa possibilità consente agli utenti di riottenere l'accesso al proprio account senza attendere altro supporto.

> [!IMPORTANT]
> Questa guida di avvio rapido illustra come un amministratore può abilitare la reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service e si deve ripristinare l'accesso al proprio account, passare a https://aka.ms/sspr.
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

## <a name="prerequisites"></a>Prerequisites

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata.
    * Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account con privilegi di amministratore globale.
* Un utente di test non amministratore con una password conosciuta, ad esempio *testuser*.
    * Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../add-users-azure-active-directory.md).
* Un gruppo pilota di cui è membro l'utente di test non amministratore, per l'esecuzione del test. Ad esempio, *SSPR-Test-Group*.
    * Se è necessario creare un gruppo, vedere come [creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

[Visualizzare questo processo come video su YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. Nel menu del portale di Azure o nella pagina **Home** selezionare **Azure Active Directory** e quindi scegliere **Reimpostazione password**.

1. Nella pagina **Proprietà** scegliere **Selezionato** in **Reimpostazione password self-service abilitata**.
1. Scegliere **Seleziona gruppo** e quindi selezionare il gruppo pilota creato nella sezione dei prerequisiti, ad esempio *SSPR-Test-Group*. Al termine, selezionare **Salva**.
1. Nella pagina **Metodi di autenticazione** selezionare le opzioni seguenti e quindi scegliere **Salva**.
    * Numero di metodi da reimpostare: **1**
    * Metodi disponibili per gli utenti:
        * **Codice app per dispositivi mobili**
        * **Indirizzo di posta elettronica**

    > [!div class="mx-imgBorder"]
    > ![Scelta dei metodi di autenticazione per SSPR][Authentication]

4. Nella pagina **Registrazione** selezionare le opzioni seguenti e quindi scegliere **Salva**.
   * Richiedere agli utenti di registrarsi all'accesso: **Sì**
   * Impostare il numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione: **365**

## <a name="test-self-service-password-reset"></a>Testare la reimpostazione della password self-service

Testare quindi la configurazione della reimpostazione della password self-service con un utente di test appartenente al gruppo selezionato nella sezione precedente, ad esempio *testuser*. Dato che Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure, l'esecuzione del test con un account amministratore può cambiare i risultati. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](concept-sspr-policy.md).

1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Accedere con un utente di test non amministratore, ad esempio *testuser*, e registrare il telefono per l'autenticazione.
3. Al termine, selezionare il pulsante **Sono corrette** e chiudere la finestra del browser.
4. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://aka.ms/sspr](https://aka.ms/sspr).
5. Immettere l'ID dell'utente di test non amministratore (ad esempio, *testuser*) e i caratteri dell'immagine CAPTCHA e quindi selezionare **Avanti**.
6. Seguire i passaggi di verifica per reimpostare la password.

## <a name="clean-up-resources"></a>Pulire le risorse

Per disabilitare la reimpostazione della password self-service, cercare e selezionare **Azure Active Directory** nel portale di Azure. Selezionare **Reimpostazione password** e quindi scegliere **Nessuno** in **Reimpostazione password self-service abilitata**. Al termine, selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato illustrato come configurare la reimpostazione della password self-service per utenti solo cloud. Per scoprire come gestire una implementazione più dettagliata, continuare con la guida all'implementazione.

> [!div class="nextstepaction"]
> [Roll out self-service password reset](howto-sspr-deployment.md) (Implementare la reimpostazione della password self-service)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
