---
title: Guida introduttiva per la reimpostazione self-service della password di Azure AD
description: In questa guida introduttiva si vedrà come configurare rapidamente la reimpostazione della password self-service di Azure AD per consentire agli utenti di reimpostare le proprie password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a863c8fe81a08f00385cb267bf360994918fe82
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212115"
---
# <a name="quickstart-self-service-password-reset"></a>Avvio rapido: Reimpostazione della password self-service

In questa guida introduttiva vengono presentate le procedure per configurare la reimpostazione della password self-service, che offre agli amministratori IT un modo semplice per consentire agli utenti di reimpostare le password o sbloccare i loro account.

## <a name="prerequisites"></a>Prerequisiti

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata.
* Un account con privilegi di amministratore globale.
* Un utente di prova non amministratore con una password nota. Se è necessario creare un utente, vedere l'articolo [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../add-users-azure-active-directory.md).
* Un gruppo pilota per il test di cui fare parte l'utente di prova non amministratore. Se è necessario creare un gruppo, vedere l'articolo [Creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

[Visualizzare questo processo come video su YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. Nel tenant di Azure AD esistente selezionare **Reimpostazione password** nel **portale di Azure** in **Azure Active Directory**.

2. Nella pagina **Proprietà** scegliere **Selezionato** in **Reimpostazione password self-service abilitata**.
    * In **Seleziona gruppo** scegliere il gruppo pilota creato nella sezione dei prerequisiti di questo articolo.
    * Fare clic su **Save**.

3. Nella pagina **Metodi di autenticazione** effettuare le scelte seguenti:
   * Numero di metodi da reimpostare: **1**
   * Metodi disponibili per gli utenti:
      * **Indirizzo di posta elettronica**
      * **Codice dell'app per dispositivi mobili (anteprima)**
   * Fare clic su **Save**.

     ![Scelta dei metodi di autenticazione per SSPR][Authentication]

4. Nella pagina **Registrazione** effettuare le scelte seguenti:
   * Richiedere agli utenti di registrarsi all'accesso: **Sì**
   * Impostare il numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione: **365**

## <a name="test-self-service-password-reset"></a>Testare la reimpostazione della password self-service

La configurazione della reimpostazione della password self-service verrà ora testata con un utente di prova. Dato che Microsoft applica requisiti di autenticazione avanzata per gli account di tipo amministratore di Azure, l'esecuzione del test con un account amministratore può cambiare i risultati. Per altre informazioni sui criteri delle password amministratore, vedere l'[articolo sui criteri delle password](concept-sspr-policy.md).

1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Accedere con un utente di prova non amministratore e registrare il telefono per l'autenticazione.
3. Al termine, fare clic sul pulsante **sono corrette** e chiudere la finestra del browser.
4. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://aka.ms/sspr](https://aka.ms/sspr).
5. Immettere l'ID dell'utente di prova non amministratore, i caratteri nell'immagine CAPTCHA e quindi fare clic su **Avanti**.
6. Seguire i passaggi di verifica per reimpostare la password

## <a name="clean-up-resources"></a>Pulire le risorse

La disabilitazione della reimpostazione password self-service è un'operazione semplice. Aprire il tenant di Azure Active Directory e passare a **Proprietà** > **Reimpostazione password** e scegliere **Nessuno** in **Reimpostazione password self-service abilitata**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato illustrato come configurare rapidamente la reimpostazione della password self-service per gli utenti solo nel cloud. Per scoprire come gestire una implementazione più dettagliata, continuare con la guida all'implementazione.

> [!div class="nextstepaction"]
> [Roll out self-service password reset](howto-sspr-deployment.md) (Implementare la reimpostazione della password self-service)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
