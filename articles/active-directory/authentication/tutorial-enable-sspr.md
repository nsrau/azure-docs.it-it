---
title: Abilitare la reimpostazione della password self-service di Azure Active Directory
description: Questa esercitazione illustra come abilitare la reimpostazione della password self-service di Azure Active Directory per un gruppo di utenti e testare il processo di reimpostazione della password.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac13dc669ed20df58f05c672926b7bee55dbc90
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035027"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Esercitazione: Consentire agli utenti di sbloccare l'account o reimpostare le password con la reimpostazione della password self-service di Azure Active Directory

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di cambiare o reimpostare la password, senza intervento dell'amministratore o dell'help desk. Se un utente dimentica la password o ha l'account bloccato, può seguire le istruzioni per sbloccarlo autonomamente e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione.

> [!IMPORTANT]
> Questa esercitazione illustra in che modo un amministratore può abilitare la reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service e si deve ripristinare l'accesso al proprio account, passare a https://aka.ms/sspr.
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la reimpostazione della password self-service per un gruppo di utenti di Azure AD
> * Configurare i metodi di autenticazione e le opzioni di registrazione
> * Testare il processo di reimpostazione della password self-service come utente

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata.
    * Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account con privilegi di *amministratore globale*.
* Un utente non amministratore con una password conosciuta, ad esempio *testuser*. In questa esercitazione sarà possibile testare l'esperienza di reimpostazione della password self-service dell'utente finale usando questo account.
    * Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Un gruppo di cui è membro l'utente non amministratore, ad esempio, *SSPR-Test-Group*. In questa esercitazione verrà abilitata la reimpostazione della password self-service per questo gruppo.
    * Se è necessario creare un gruppo, vedere come [creare un gruppo e aggiungere membri in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Azure AD consente di abilitare la reimpostazione della password self-service per *Nessuno*, utente *Selezionato* o *Tutti* gli utenti. Questa funzionalità granulare consente di scegliere un subset di utenti per testare il processo e il flusso di lavoro di reimpostazione della password self-service e registrazione. Una volta acquisita familiarità con il processo e quando si è pronti per condividere i requisiti con un set più ampio di utenti, è possibile selezionare un gruppo di utenti da abilitare per la reimpostazione della password self-service. In alternativa, è possibile abilitare la reimpostazione della password self-service per tutti gli utenti nel tenant Azure AD.

> [!NOTE]
>
> È attualmente possibile abilitare un solo gruppo di Azure AD per la reimpostazione della password self-service usando il portale di Azure. Come parte di una distribuzione più ampia della reimpostazione della password self-service, sono supportati i gruppi annidati. Verificare che agli utenti del gruppo scelto siano assegnate le licenze appropriate. Attualmente non è disponibile alcun processo di convalida per questi requisiti di licenza.

In questa esercitazione verrà configurata la reimpostazione della password self-service per un set di utenti in un gruppo di test. Nell'esempio seguente viene usato il gruppo *SSPR-Test-Group*. Specificare il gruppo di Azure AD personalizzato in base alle esigenze:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account con autorizzazioni di *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Reimpostazione password** dal menu a sinistra.
1. Nella pagina **Proprietà** scegliere *Seleziona gruppo* nell'opzione **Reimpostazione password self-service abilitata**
1. Cercare e selezionare il gruppo di Azure AD, ad esempio *SSPR-Test-Group*, quindi scegliere *Seleziona*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Per abilitare la reimpostazione della password self-service per gli utenti selezionati, selezionare **Salva**.

## <a name="select-authentication-methods-and-registration-options"></a>Selezionare i metodi di autenticazione e le opzioni di registrazione

Quando gli utenti devono sbloccare l'account o reimpostare la password, viene richiesto loro un metodo di conferma aggiuntivo. Questo fattore di autenticazione aggiuntivo garantisce che vengano completati solo gli eventi di reimpostazione della password self-service approvati. È possibile scegliere quali metodi di autenticazione consentire, in base alle informazioni di registrazione fornite dall'utente.

1. Nella pagina **Metodi di autenticazione** impostare **Numero di metodi necessari per la reimpostazione** su *1*.

    Per migliorare la sicurezza, è possibile aumentare il numero di metodi di autenticazione necessari per la reimpostazione della password self-service.

1. Scegliere i **Metodi disponibili per gli utenti** che l'organizzazione vuole consentire. Per questa esercitazione, selezionare le caselle per abilitare i metodi seguenti:

    * *Notifica dell'app per dispositivi mobili*
    * *Codice app per dispositivi mobili*
    * *Indirizzo di posta elettronica*
    * *Cellulare*

    Per soddisfare i requisiti aziendali, è possibile abilitare metodi di autenticazione aggiuntivi, ad esempio *Telefono ufficio* o *Domande di sicurezza*.

1. Per applicare i metodi di autenticazione, selezionare **Salva**.

Prima di poter sbloccare l'account o reimpostare una password, gli utenti devono registrare le proprie informazioni di contatto. Queste informazioni di contatto vengono usate per i diversi metodi di autenticazione configurati nei passaggi precedenti.

Le informazioni di contatto possono essere specificate manualmente dall'amministratore o dagli utenti stessi tramite un portale di registrazione. In questa esercitazione verranno configurati gli utenti in modo che venga richiesta la registrazione all'accesso successivo.

1. Nella pagina **Registrazione** dal menu a sinistra selezionare *Sì* per **Richiedere agli utenti di registrarsi all'accesso?** .
1. È importante che le informazioni di contatto siano mantenute aggiornate. Se le informazioni di contatto non sono aggiornate al momento dell'avvio di un evento di reimpostazione della password self-service, l'utente potrebbe non riuscire a sbloccare l'account o a reimpostare la password.

    Impostare **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su *180*.
1. Per applicare le impostazioni di registrazione, selezionare **Salva**.

## <a name="configure-notifications-and-customizations"></a>Configurare notifiche e personalizzazioni

Per informare gli utenti sull'attività dell'account, è possibile configurare le notifiche tramite posta elettronica da inviare quando si verifica un evento di reimpostazione della password self-service. Queste notifiche possono essere impostate sia per gli account utente normali che per gli account amministratore. Per gli account amministratore, questa notifica fornisce un livello aggiuntivo di consapevolezza quando una password dell'account amministratore con privilegi viene reimpostata tramite la reimpostazione della password self-service. Tutti gli amministratori globali riceveranno una notifica quando in un account amministratore viene usata la reimpostazione della password self-service.

1. Nella pagina **Notifiche** dal menu a sinistra configurare le opzioni seguenti:

   * Impostare l'opzione **Notificare agli utenti le reimpostazioni delle password** su *Sì*.
   * Impostare **Notificare agli amministratori quando altri amministratori reimpostano le proprie password** su *Sì*.

1. Per applicare le preferenze di notifica, selezionare **Salva**.

Se gli utenti necessitano di ulteriore assistenza per il processo di reimpostazione della password self-service, è possibile personalizzare il collegamento per "Contattare l'amministratore". Questo collegamento viene usato nel processo di registrazione della reimpostazione della password self-service e quando un utente sblocca il proprio account o ne reimposta la password. Per assicurarsi che gli utenti ottengano il supporto necessario, è consigliabile fornire un URL o un indirizzo di posta elettronica del supporto tecnico personalizzato.

1. Nella pagina **Personalizzazione** dal menu a sinistra impostare *Personalizza collegamento al supporto tecnico* su **Sì**.
1. Nel campo **Indirizzo di posta elettronica o URL del supporto tecnico** specificare un indirizzo di posta elettronica o l'URL di una pagina Web in cui gli utenti possono ottenere ulteriore assistenza dall'organizzazione, ad esempio *`https://support.contoso.com/`*
1. Per applicare il collegamento personalizzato, selezionare **Salva**.

## <a name="test-self-service-password-reset"></a>Testare la reimpostazione della password self-service

Dopo aver abilitato e configurato la reimpostazione della password self-service, testare il processo con un utente che fa parte del gruppo selezionato nella sezione precedente, ad esempio *Test-SSPR-Group*. Nell'esempio seguente viene usato l'account *testuser*. Specificare l'account utente che fa parte del gruppo abilitato per la reimpostazione della password self-service nella prima sezione di questa esercitazione.

> [!NOTE]
> Per eseguire il test della reimpostazione della password self-service, usare un account non amministratore. Gli amministratori sono sempre abilitati per la reimpostazione della password self-service e devono usare due metodi di autenticazione per reimpostare la propria password.

1. Per visualizzare il processo di registrazione manuale, aprire una nuova finestra del browser in modalità InPrivate o anonima e passare a [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). All'accesso successivo gli utenti verranno reindirizzati a questo portale di registrazione.
1. Accedere con un utente di test non amministratore, ad esempio *testuser*, e registrare le informazioni di contatto dei metodi di autenticazione.
1. Al termine, selezionare il pulsante **Sono corrette** e chiudere la finestra del browser.
1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://aka.ms/sspr](https://aka.ms/sspr).
1. Immettere le informazioni dell'account dell'utente di test non amministratore, ad esempio, *testuser*, i caratteri dell'immagine CAPTCHA e quindi selezionare **Avanti**.

    ![Immettere le informazioni sull'account utente per reimpostare la password](media/tutorial-enable-sspr/password-reset-page.png)

1. Seguire i passaggi di verifica per reimpostare la password. Al termine, si riceverà una notifica tramite posta elettronica che segnala che la password è stata reimpostata.

## <a name="clean-up-resources"></a>Pulire le risorse

In un'esercitazione successiva di questa serie viene configurato il writeback delle password. Questa funzionalità consente di eseguire il writeback delle modifiche delle password dalla reimpostazione della password self-service di Azure AD a un ambiente Active Directory locale. Se si intende continuare con questa serie di esercitazioni per configurare il writeback delle password, non disabilitare la reimpostazione della password self-service adesso.

Se non si intende usare più la funzionalità di reimpostazione della password self-service configurata come parte di questa esercitazione, impostare lo stato della reimpostazione della password self-service su **Nessuna** con la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Reimpostazione password** dal menu a sinistra.
1. Nella pagina **Proprietà** scegliere **Nessuna** per l'opzione *Reimpostazione password self-service abilitata*.
1. Per applicare la modifica alla reimpostazione della password self-service, selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata la reimpostazione della password self-service di Azure AD per un gruppo selezionato di utenti. Si è appreso come:

> [!div class="checklist"]
> * Abilitare la reimpostazione della password self-service per un gruppo di utenti di Azure AD
> * Configurare i metodi di autenticazione e le opzioni di registrazione
> * Testare il processo di reimpostazione della password self-service come utente

> [!div class="nextstepaction"]
> [Abilitare Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
