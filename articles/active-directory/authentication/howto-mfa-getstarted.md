---
title: Introduzione ad Azure MFA nel cloud - Azure Active Directory
description: Introduzione all'accesso condizionale con Microsoft Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e6b5f9e0f69184e61c224dc7951dc0cac0ec054
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312309"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Implementare Azure Multi-Factor Authentication basato su cloud

Iniziare a usare Azure Multi-Factor Authentication (MFA di Azure) è semplice.

Prima di iniziare, verificare che i prerequisiti seguenti siano disponibili:

* Un account amministratore globale nel tenant di Azure AD. Per istruzioni su come completare questo passaggio, vedere l'articolo [Introduzione ad Azure AD](../get-started-azure-ad.md).
* Licenze corrette assegnate agli utenti. Per altre informazioni, vedere l'articolo [Come ottenere Azure Multi-Factor Authentication](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Scegliere come eseguire l'abilitazione

**Abilitato da criteri di accesso condizionale**: questo metodo è descritto in questo articolo. È il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. L'abilitazione mediante criteri di accesso condizionale funziona solo per Azure MFA nel cloud ed è una funzione Premium di Azure AD.

**Abilitato da Azure AD Identity Protection**: questo metodo usa i criteri di rischio di Azure AD Identity Protection per richiedere la verifica in due passaggi basata solo sul rischio di accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Come configurare i criteri di rischio utente](../identity-protection/howto-user-risk-policy.md).

**Abilitato modificando lo stato utente**: questo è il metodo tradizionale per richiedere la verifica in due passaggi. Può essere usato sia con Azure MFA nel cloud sia con Azure MFA Server. Con questo metodo gli utenti devono eseguire la verifica in due passaggi **ogni volta** che eseguono l'accesso e vengono ignorati i criteri di accesso condizionale. Altre informazioni su questo metodo sono disponibili in [Come richiedere la verifica in due passaggi per un utente](howto-mfa-userstates.md).

> [!Note]
> Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="choose-authentication-methods"></a>Scegliere i metodi di autenticazione

Abilitare almeno un metodo di autenticazione per gli utenti in base ai requisiti dell'organizzazione. Si è notato che, quando è abilitata per gli utenti, l'app Microsoft Authenticator offre la migliore esperienza utente. Per sapere quali metodi sono disponibili e come impostarli, vedere l'articolo [Cosa si intende per metodi di autenticazione?](concept-authentication-methods.md).

> [!IMPORTANT]
> A partire da marzo del 2019 le opzioni telefonata non sarà disponibile per gli utenti MFA e SSPR nei tenant gratuiti/versione di valutazione AD Azure. Messaggi SMS non sono interessati da questa modifica. Chiamata telefonica continuerà a essere disponibile per gli utenti a pagamento di tenant di Azure AD. Questa modifica interessa solo i tenant gratuiti/versione di valutazione AD Azure.

## <a name="get-users-to-enroll"></a>Indurre gli utenti a registrarsi

Dopo aver abilitato criteri di accesso condizionale, gli utenti saranno costretti a registrarsi all'utilizzo successivo di un'app protetta con i criteri. Se si abilita un criterio che richiede MFA per tutti gli utenti in tutte le app cloud, questa azione potrebbe causare problemi agli utenti e al supporto tecnico. È consigliabile chiedere agli utenti di registrare i metodi di autenticazione in anticipo usando il portale di registrazione all'indirizzo [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Molte organizzazioni ritengono che si possa incentivare questo comportamento mediante poster, promemoria da tavolo e messaggi di posta elettronica.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Abilitare Multi-Factor Authentication con l'accesso condizionale

Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.

### <a name="choose-verification-options"></a>Scegliere le opzioni di verifica

Prima di abilitare Azure Multi-Factor Authentication, l'organizzazione deve determinare quali opzioni di verifica consentire. Ai fini di questo esercizio, si abilita la chiamata al telefono e l'invio di SMS al telefono, che sono opzioni generiche che la maggior parte delle persone sono in grado di usare. Altre informazioni sui metodi di autenticazione e il relativo utilizzo sono disponibili nell'articolo [Cosa si intende per metodi di autenticazione?](concept-authentication-methods.md)

1. Passare ad **Azure Active Directory**, **Utenti**, **Multi-Factor Authentication**.

   ![Accesso al portale Multi-Factor Authentication dal pannello Utenti di Azure AD nel portale di Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Nella nuova scheda che si apre passare a **Impostazioni del servizio**.
1. In **Opzioni di verifica** selezionare tutte le caselle per i metodi disponibili per gli utenti.

   ![Configurare i metodi di verifica nella scheda delle impostazioni del servizio Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Fare clic su **Save**.
5. Chiudere la scheda **Impostazioni del servizio**.

### <a name="create-conditional-access-policy"></a>Creare criteri di accesso condizionale

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Passare ad **Azure Active Directory** , **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Immettere un nome significativo per i criteri.
1. In **Utenti e gruppi**:
   * Nella scheda **Includere** selezionare il pulsante di opzione **Tutti gli utenti**
   * CONSIGLIATO: nella scheda **Escludere** selezionare la casella per **Utenti e gruppi** e scegliere un gruppo da usare per le esclusioni quando gli utenti non hanno accesso ai loro metodi di autenticazione.
   * Fare clic su **Done**.
1. In **App cloud** selezionare il pulsante di opzione **Tutte le app cloud**.
   * FACOLTATIVAMENTE: nella scheda **Escludere** scegliere le app cloud per le quali l'organizzazione non richiede MFA.
   * Fare clic su **Done**.
1. Nella sezione **Condizioni**:
   * FACOLTATIVAMENTE: se è stato abilitato Azure Identity Protection, è possibile scegliere di valutare il rischio di accesso come parte dei criteri.
   * FACOLTATIVAMENTE: se sono state configurate posizioni attendibili o località denominate, è possibile specificare di includere o escludere tali percorsi dai criteri.
1. In **Concedi** assicurarsi che il pulsante di opzione **Concedi accesso** sia selezionato.
    * Selezionare la casella **Richiedi autenticazione a più fattori**.
    * Fare clic su **Seleziona**.
1. Ignorare la sezione **Sessione**.
1. Impostare l'interruttore **Abilita criterio** su **Sì**.
1. Fare clic su **Create**(Crea).

![Creare un criterio di accesso condizionale per abilitare MFA per gli utenti del portale di Azure nel gruppo pilota](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Testare Azure Multi-Factor Authentication

Per verificare che i criteri di accesso condizionale funzionino, testare l'accesso a una risorsa che non richiede l'autenticazione MFA e quindi al portale di Azure che richiede l'autenticazione MFA.

1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Eseguire l'accesso con l'utente di prova creato nella sezione dei prerequisiti di questo articolo e notare che non deve essere richiesto di completare l'autenticazione MFA.
   * Chiudere la finestra del browser.
2. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://portal.azure.com](https://portal.azure.com).
   * Accedere con l'utente di prova creato nella sezione dei prerequisiti di questo articolo, notare che ora deve essere richiesto di eseguire la registrazione per l'autenticazione MFA e usare tale autenticazione.
   * Chiudere la finestra del browser.

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni: Azure Multi-Factor Authentication è configurato nel cloud.

Perché a un utente è stato richiesto o non è stato richiesto di eseguire MFA? Vedere la sezione [Report sugli accessi ad Azure AD nel documento Report in Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Per configurare impostazioni aggiuntive, ad esempio gli indirizzi IP attendibili, i messaggi vocali personalizzati e gli avvisi di illeciti, vedere l'articolo [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Informazioni sulla gestione delle impostazioni utente per Azure Multi-Factor Authentication sono disponibili nell'articolo [Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

[Abilitare la registrazione convergente per Azure Multi-Factor Authentication e la reimpostazione della password self-service di Azure AD](concept-registration-mfa-sspr-converged.md).
