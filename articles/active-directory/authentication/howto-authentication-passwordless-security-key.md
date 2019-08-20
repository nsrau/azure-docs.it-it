---
title: Abilitare l'accesso alla chiave di sicurezza senza password per Azure AD (anteprima)-Azure Active Directory
description: Abilitare la chiave di sicurezza senza password accedi a Azure AD usando le chiavi di sicurezza FIDO2 (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a063891339a46366490447b7c7a7a1a14fd81be6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828917"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Abilitare l'accesso alla chiave di sicurezza senza password per Azure AD (anteprima)

## <a name="requirements"></a>Requisiti

* Azure Multi-Factor Authentication
* Anteprima della registrazione combinata con gli utenti abilitati per SSPR
* L'anteprima della chiave di sicurezza FIDO2 richiede chiavi di sicurezza FIDO2 compatibili
* Webauthn richiede Microsoft Edge in Windows 10 versione 1809 o successiva
* L'accesso a Windows basato su FIDO2 richiede che Azure AD aggiunto Windows 10 versione 1809 o successiva

## <a name="prepare-devices-for-preview"></a>Preparare i dispositivi per l'anteprima

I dispositivi con cui si eseguirà il pilota devono eseguire Windows 10 versione 1809 o successiva. L'esperienza migliore è in Windows 10 versione 1903 o successiva.

## <a name="enable-security-keys-for-windows-sign-in"></a>Abilitare le chiavi di sicurezza per l'accesso a Windows

Le organizzazioni possono scegliere di usare uno o più dei metodi seguenti per abilitare l'uso delle chiavi di sicurezza per l'accesso a Windows.

### <a name="enable-credential-provider-via-intune"></a>Abilitare il provider di credenziali tramite Intune

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Microsoft Intune** > **registrazione del dispositivo registrazione** > **Windows** > **Proprietà** **Windows Hello for business** > .
1. In **Impostazioni** impostare **Usa chiavi di sicurezza per l'accesso** a **attivato**.

La configurazione delle chiavi di sicurezza per l'accesso non dipende dalla configurazione di Windows Hello for business.

#### <a name="enable-targeted-intune-deployment"></a>Abilitare la distribuzione di Intune di destinazione

Per specificare come destinazione gruppi di dispositivi specifici per abilitare il provider di credenziali, usare le seguenti impostazioni personalizzate tramite Intune. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Microsoft Intune** > **configurazione** > del dispositivo**profili** > **Crea profilo**.
1. Configurare il nuovo profilo con le seguenti impostazioni
   1. Nome: Chiavi di sicurezza per l'accesso a Windows
   1. Descrizione: Abilita le chiavi di sicurezza di FIDO da usare durante l'accesso a Windows
   1. Piattaforma: Windows 10 e versioni successive
   1. Tipo piattaforma: Personalizzato
   1. Impostazioni URI OMA personalizzate:
      1. Nome: Attivare le chiavi di sicurezza di FIDO per l'accesso a Windows
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Tipo di dati: Integer
      1. Valore: 1 
1. Questo criterio può essere assegnato a utenti, dispositivi o gruppi specifici. Altre informazioni sono disponibili nell'articolo [assegnare profili utente e dispositivo in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Creazione dei criteri di configurazione del dispositivo personalizzati di Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Abilitare il provider di credenziali tramite il pacchetto di provisioning

Per i dispositivi non gestiti da Intune, è possibile installare un pacchetto di provisioning per abilitare la funzionalità. L'app progettazione configurazione di Windows può essere installata dal [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Avviare Progettazione configurazione Windows.
1. Selezionare **file** > **nuovo progetto**.
1. Assegnare un nome al progetto e prendere nota del percorso in cui è stato creato il progetto.
1. Selezionare **Avanti**.
1. Lasciare il **pacchetto** di provisioning selezionato come **flusso di lavoro del progetto selezionato** e selezionare **Avanti**.
1. Selezionare **tutte le edizioni desktop di Windows** in **scegliere le impostazioni da visualizzare e configurare** e selezionare **Avanti**.
1. Selezionare **Fine**.
1. Nel progetto appena creato passare a **Impostazioni** > di runtime**WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Impostare **UseSecurityKeyForSignIn** su **Enabled**.
1. Selezionare **Esporta** > **pacchetto** di provisioning
1. Lasciare le impostazioni predefinite nella finestra **Compila** in **descrivere il pacchetto** di provisioning e selezionare **Avanti**.
1. Lasciare le impostazioni predefinite nella finestra **Compila** in **selezionare i dettagli di sicurezza per il pacchetto** di provisioning e fare clic su **Avanti**.
1. Prendere nota di o modificare il percorso nelle finestre di **compilazione** in **selezionare dove salvare il pacchetto di** provisioning e selezionare **Avanti**.
1. Selezionare **Compila** nella pagina **Compila il pacchetto** di provisioning.
1. Salvare i due file creati (ppkg e cat) in un percorso in cui è possibile applicarli ai computer in un secondo momento.
1. Per applicare il pacchetto di provisioning creato, seguire le istruzioni riportate nell'articolo [applicare un pacchetto](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)di provisioning.

## <a name="obtain-fido2-security-keys"></a>Ottenere le chiavi di sicurezza FIDO2

Per ulteriori informazioni sulle chiavi e sui produttori supportati, vedere la sezione relativa alle chiavi di sicurezza di FIDO2 [nell'articolo](concept-authentication-passwordless.md).

> [!NOTE]
> Se si acquista e si prevede di usare le chiavi di sicurezza basate su NFC, sarà necessario un lettore NFC supportato.

## <a name="enable-passwordless-authentication-method"></a>Abilita metodo di autenticazione con password

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione con password si basano sull'anteprima della registrazione combinata. Per abilitare l'anteprima della registrazione combinata, seguire la procedura descritta nell'articolo [abilitare la registrazione delle informazioni di sicurezza combinata (anteprima)](howto-registration-mfa-sspr-combined.md).

### <a name="enable-new-passwordless-authentication-method"></a>Abilita nuovo metodo di autenticazione con password

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Passare a **Azure Active Directory** >  > **Metodi**diautenticazionedisicurezzacriterimetododi > **autenticazione (anteprima)**
1. In ogni **Metodo**scegliere le opzioni seguenti
   1. **Abilita** -Sì o no
   1. **Destinazione** -tutti gli utenti o utenti selezionati
1. **Salva** ogni metodo

> [!WARNING]
> I "criteri di restrizione chiave" di FIDO2 non funzionano ancora. Questa funzionalità sarà disponibile prima della disponibilità generale. per impostazione predefinita, non modificare questi criteri.

> [!NOTE]
> Non è necessario acconsentire esplicitamente a entrambi i metodi senza password (se si desidera visualizzare in anteprima solo un metodo senza password, è possibile abilitare solo tale metodo). Si consiglia di provare entrambi i metodi poiché hanno entrambi i vantaggi.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrazione e gestione degli utenti delle chiavi di sicurezza FIDO2

1. Passare a [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Accedi se non è già stato fatto
1. Fare clic su **info di sicurezza**
   1. Se l'utente ha già registrato almeno un metodo di autenticazione a più fattori di Azure, può registrare immediatamente una chiave di sicurezza FIDO2.
   1. Se non è registrato almeno un metodo di Azure per l'autenticazione a più fattori, è necessario aggiungerne uno.
1. Aggiungere una chiave di sicurezza FIDO2 facendo clic su **Aggiungi metodo** e scegliendo **chiave di sicurezza**
1. Scegliere un **dispositivo USB** o un **dispositivo NFC**
1. Prepara la tua chiave e scegli **Avanti**
1. Verrà visualizzata una casella con la richiesta di creare/immettere un PIN per la chiave di sicurezza, quindi eseguire il gesto necessario per la chiave biometrica o touch.
1. L'esperienza di registrazione combinata verrà restituita e verrà richiesto di fornire un nome significativo per il token, in modo che sia possibile identificare quale se si dispone di più. Fare clic su **Avanti**.
1. Fare clic su **fine** per completare il processo

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gestire la chiave di sicurezza biometria, PIN o reimpostare la chiave di sicurezza

* Windows 10 versione 1809
   * Il software complementare del fornitore della chiave di sicurezza è obbligatorio
* Windows 10 versione 1903 o successiva
   * Gli utenti possono aprire **le impostazioni di Windows** nella**chiave di sicurezza** degli **account** > del dispositivo >
   * Gli utenti possono modificare il PIN, aggiornare i dati biometrici o reimpostare la chiave di sicurezza

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrazione e gestione degli utenti dell'app Microsoft Authenticator

Per configurare l'app Microsoft Authenticator per l'accesso tramite telefono, seguire le istruzioni riportate nell'articolo [accedere agli account usando l'app Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

### <a name="sign-in-at-the-lock-screen"></a>Accedere alla schermata di blocco

Nell'esempio seguente un utente Bala Sandhu ha già eseguito il provisioning della chiave di sicurezza FIDO2. Bala può scegliere il provider di credenziali della chiave di sicurezza dalla schermata di blocco di Windows 10 e inserire la chiave di sicurezza per accedere a Windows.

![Accesso alla chiave di sicurezza dalla schermata di blocco di Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Accedere al Web

Nell'esempio seguente un utente ha già eseguito il provisioning della chiave di sicurezza FIDO2. L'utente può scegliere di accedere al Web con la chiave di sicurezza FIDO2 all'interno del browser Microsoft Edge in Windows 10 versione 1809 o successiva.

![Firma della chiave di sicurezza in Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Problemi noti

### <a name="security-key-provisioning"></a>Provisioning delle chiavi di sicurezza

Il provisioning e il deprovisioning dell'amministratore delle chiavi di sicurezza non sono disponibili nell'anteprima pubblica.

### <a name="hybrid-azure-ad-join"></a>Aggiunta ad Azure AD ibrido

Gli utenti che si basano su WIA SSO che usano credenziali gestite, come le chiavi di sicurezza FIDO2 o l'accesso senza password con Microsoft Authenticator app, devono partecipare a un join ibrido in Windows 10 per sfruttare i vantaggi di SSO. Tuttavia, le chiavi di sicurezza funzionano solo per i computer Azure Active Directory aggiunti per il momento. Si consiglia di provare solo le chiavi di sicurezza di FIDO2 per la schermata di blocco di Windows in macchine virtuali Unite Azure Active Directory. Questa limitazione non è valida per il Web.

### <a name="upn-changes"></a>Modifiche UPN

Stiamo lavorando per supportare una funzionalità che consente la modifica di UPN nei dispositivi ibridi AADJ e AADJ. Se l'UPN di un utente viene modificato, non è più possibile modificare le chiavi di sicurezza di FIDO2 per tenere conto di tale valore. Quindi, l'unico approccio consiste nel reimpostare il dispositivo e l'utente deve eseguire di nuovo la registrazione.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulla registrazione dei dispositivi](../devices/overview.md)

[Informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
