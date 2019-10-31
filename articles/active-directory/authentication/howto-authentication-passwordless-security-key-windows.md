---
title: Abilitare l'accesso alla chiave di sicurezza senza password per Azure AD (anteprima)-Azure Active Directory
description: Abilitare l'accesso alla chiave di sicurezza senza password per Azure AD usando le chiavi di sicurezza FIDO2 (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5758b1fbb9d311219e3dc4dd483691f6c9d80c1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172174"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Abilitare la chiave di sicurezza senza password accedere ai dispositivi Windows 10 (anteprima)

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password basata sulla chiave di sicurezza FIDO2 con i dispositivi Windows 10. Alla fine di questo articolo, sarà possibile accedere alle applicazioni basate sul Web e ai Azure AD aggiunti ai dispositivi Windows 10 con l'account di Azure AD usando una chiave di sicurezza FIDO2.

|     |
| --- |
| Le chiavi di sicurezza di FIDO2 sono una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisiti

| Tipo di dispositivo | Aggiunta ad Azure AD | Aggiunta a Azure AD ibrido |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Anteprima di registrazione delle informazioni di sicurezza combinata](concept-registration-mfa-sspr-combined.md) | X | X |
| [Chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibili | X | X |
| Webauthn richiede Windows 10 versione 1809 o successiva | X | X |
| [Azure ad dispositivi aggiunti](../devices/concept-azure-ad-join.md) richiedono Windows 10 versione 1809 o successiva | X |   |
| [Azure ad ibrido dispositivi aggiunti](../devices/concept-azure-ad-join-hybrid.md) richiedono Windows 10 Insider Build 18945 o versione successiva |   | X |
| Controller di dominio Windows Server 2016/2019 con patch complete. |   | X |
| Eseguire l'aggiornamento alla versione più recente di [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (facoltativo) | X | X |
| Pacchetto di provisioning (facoltativo) | X | X |
| Criteri di gruppo (facoltativo) |   | X |

### <a name="unsupported-scenarios"></a>Scenari non supportati

- La distribuzione di Windows Server Active Directory Domain Services (AD DS) aggiunta a un dominio (solo dispositivi locali) **non è supportata**.
- Gli scenari RDP, VDI e Citrix **non sono supportati** con la chiave di sicurezza.
- S/MIME **non è supportato** con la chiave di sicurezza.
- "Esegui come" **non è supportato** con la chiave di sicurezza.
- L'accesso a un server con la chiave di sicurezza **non è supportato**.
- Se la chiave di sicurezza non è stata usata per accedere al dispositivo mentre è online, non sarà possibile usarla per accedere o sbloccare offline.

## <a name="prepare-devices-for-preview"></a>Preparare i dispositivi per l'anteprima

Azure AD i dispositivi aggiunti con cui si eseguirà il pilota devono eseguire Windows 10 versione 1809 o successiva. L'esperienza migliore è in Windows 10 versione 1903 o successiva.

Azure AD ibrido i dispositivi aggiunti con cui si eseguirà il pilota devono eseguire Windows 10 Insider Build 18945 o versione successiva.

## <a name="enable-security-keys-for-windows-sign-in"></a>Abilitare le chiavi di sicurezza per l'accesso a Windows

Le organizzazioni possono scegliere di usare uno o più dei metodi seguenti per consentire l'uso di chiavi di sicurezza per l'accesso a Windows in base ai requisiti dell'organizzazione.

- [Abilitare con Intune](#enable-with-intune)
   - [Distribuzione di Intune di destinazione](#targeted-intune-deployment)
- [Abilitare con un pacchetto di provisioning](#enable-with-a-provisioning-package)
- [Abilita con Criteri di gruppo (solo Azure AD ibrido dispositivi aggiunti)](#enable-with-group-policy)

> [!IMPORTANT]
> Le organizzazioni con **dispositivi ibridi Azure ad aggiunti** devono **anche** completare la procedura descritta nell'articolo, [abilitare l'autenticazione FIDO2 per le risorse locali prima che](howto-authentication-passwordless-security-key-on-premises.md) l'autenticazione della chiave di sicurezza FIDO2 di Windows 10 funzioni.
>
> Le organizzazioni con **Azure ad dispositivi aggiunti** devono eseguire questa operazione prima che i dispositivi possano eseguire l'autenticazione alle risorse locali con le chiavi di sicurezza FIDO2.

### <a name="enable-with-intune"></a>Abilitare con Intune

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Microsoft Intune** > **registrazione del dispositivo** > la **registrazione di Windows** > **Windows Hello for business** > **Proprietà**.
1. In **Impostazioni** impostare **Usa chiavi di sicurezza per l'accesso** a **attivato**.

La configurazione delle chiavi di sicurezza per l'accesso non dipende dalla configurazione di Windows Hello for business.

#### <a name="targeted-intune-deployment"></a>Distribuzione di Intune di destinazione

Per specificare come destinazione gruppi di dispositivi specifici per abilitare il provider di credenziali, usare le seguenti impostazioni personalizzate tramite Intune.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Microsoft Intune** > **configurazione del dispositivo** > **profili** > **Crea profilo**.
1. Configurare il nuovo profilo con le seguenti impostazioni
   1. Nome: chiavi di sicurezza per l'accesso a Windows
   1. Descrizione: Abilita le chiavi di sicurezza di FIDO da usare durante l'accesso a Windows
   1. Piattaforma: Windows 10 e versioni successive
   1. Tipo di profilo: personalizzato
   1. Impostazioni URI OMA personalizzate:
      1. Nome: attivare le chiavi di sicurezza di FIDO per l'accesso a Windows
      1. URI OMA:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Tipo di dati: integer
      1. Valore: 1
1. Questo criterio può essere assegnato a utenti, dispositivi o gruppi specifici. Altre informazioni sono disponibili nell'articolo [assegnare profili utente e dispositivo in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Creazione dei criteri di configurazione del dispositivo personalizzati di Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Abilitare con un pacchetto di provisioning

Per i dispositivi non gestiti da Intune, è possibile installare un pacchetto di provisioning per abilitare la funzionalità. L'app progettazione configurazione di Windows può essere installata dal [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Avviare Progettazione configurazione Windows.
1. Selezionare **File** > **nuovo progetto**.
1. Assegnare un nome al progetto e prendere nota del percorso in cui è stato creato il progetto.
1. Selezionare **Avanti**.
1. Lasciare il **pacchetto di provisioning** selezionato come **flusso di lavoro del progetto selezionato** e selezionare **Avanti**.
1. Selezionare **tutte le edizioni desktop di Windows** in **scegliere le impostazioni da visualizzare e configurare** e selezionare **Avanti**.
1. Selezionare **Fine**.
1. Nel progetto appena creato passare a impostazioni di **Runtime** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Impostare **UseSecurityKeyForSignIn** su **Enabled**.
1. Selezionare **esporta** > **pacchetto di provisioning**
1. Lasciare le impostazioni predefinite nella finestra **Compila** in **descrivere il pacchetto di provisioning** e selezionare **Avanti**.
1. Lasciare le impostazioni predefinite nella finestra **Compila** in **selezionare i dettagli di sicurezza per il pacchetto di provisioning e fare** clic su **Avanti**.
1. Prendere nota di o modificare il percorso nelle finestre di **compilazione** in **selezionare dove salvare il pacchetto di provisioning** e selezionare **Avanti**.
1. Selezionare **Compila** nella pagina **Compila il pacchetto di provisioning** .
1. Salvare i due file creati (ppkg e cat) in un percorso in cui è possibile applicarli ai computer in un secondo momento.
1. Per applicare il pacchetto di provisioning creato, seguire le istruzioni riportate nell'articolo [applicare un pacchetto di provisioning](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> I dispositivi che eseguono Windows 10 versione 1809 devono abilitare anche la modalità EnableSharedPCMode (Shared PC Mode). Per informazioni sull'abilitazione di questo funzionalità, vedere l'articolo [configurare un PC condiviso o Guest con Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Abilita con Criteri di gruppo

Per i **dispositivi ibridi Azure ad aggiunti** , le organizzazioni possono configurare la seguente impostazione di criteri di gruppo per abilitare l'accesso alla chiave di sicurezza di fido.

L'impostazione si trova in **Configurazione Computer** > **Modelli amministrativi** > **sistema** > accesso > attiva l' **accesso** alla **chiave di sicurezza**.

- L'impostazione di questo criterio su **abilitato** consente agli utenti di accedere con le chiavi di sicurezza.
- L'impostazione di questo criterio su **disabilitato** o **non configurato** impedirà agli utenti di eseguire l'accesso con le chiavi di sicurezza.

Questa impostazione di Criteri di gruppo richiede una versione aggiornata del modello di Criteri di gruppo `credentialprovider.admx`. Questo nuovo modello è disponibile con la versione successiva di Windows Server e con Windows 10 20H1. Questa impostazione può essere gestita con un dispositivo che esegue una delle versioni più recenti di Windows o in modo centralizzato seguendo le istruzioni disponibili nell'argomento del supporto, [come creare e gestire l'archivio centrale per Criteri di gruppo modelli amministrativi in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Accedi con la chiave di sicurezza FIDO2

Nell'esempio seguente un utente Bala Sandhu ha già eseguito il provisioning della chiave di sicurezza FIDO2 usando la procedura descritta nell'articolo precedente, [abilitare la chiave di sicurezza senza password accedi](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala può scegliere il provider di credenziali della chiave di sicurezza dalla schermata di blocco di Windows 10 e inserire la chiave di sicurezza per accedere a Windows.

![Accesso alla chiave di sicurezza dalla schermata di blocco di Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gestire la chiave di sicurezza biometria, PIN o reimpostare la chiave di sicurezza

* Windows 10 versione 1903 o successiva
   * Gli utenti possono aprire **le impostazioni di Windows** sul dispositivo > **account** > **chiave di sicurezza**
   * Gli utenti possono modificare il PIN, aggiornare i dati biometrici o reimpostare la chiave di sicurezza

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e commenti e suggerimenti

Per condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, è possibile condividerla tramite l'app hub di feedback di Windows.

1. Avviare **feedback Hub** e assicurarsi di aver eseguito l'accesso.
1. Inviare commenti e suggerimenti nella categorizzazione seguente:
   1. Categoria: sicurezza e privacy
   1. Sottocategoria: FIDO
1. Per acquisire i log, usare l'opzione: **ricrea il problema**

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso alle risorse locali per i dispositivi Azure AD e ibridi Azure AD aggiunti](howto-authentication-passwordless-security-key-on-premises.md)

[Altre informazioni sulla registrazione del dispositivo](../devices/overview.md)

[Scopri di più su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
