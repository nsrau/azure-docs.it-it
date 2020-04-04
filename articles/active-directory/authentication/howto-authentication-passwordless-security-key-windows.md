---
title: Accesso alla chiave di sicurezza senza password di Windows - Azure Active Directory
description: Informazioni su come abilitare l'accesso alla chiave di sicurezza senza password in Azure Active Directory usando le chiavi di sicurezza FIDO2 (anteprima)Learn how to enable passwordless security key sign-in to Azure Active Directory using FIDO2 security keys (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653993"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Abilitare l'accesso alla chiave di sicurezza senza password per i dispositivi Windows 10 con Azure Active Directory (anteprima)Enable passwordless security key sign-in to Windows 10 devices with Azure Active Directory (preview)

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password basata su chiave di sicurezza FIDO2 con i dispositivi Windows 10.This document focuses on enabling FIDO2 security key based passwordless authentication with Windows 10 devices. Alla fine di questo articolo, sarà possibile accedere sia ad Azure AD che ai dispositivi ibridi di Azure AD e ad Azure AD aggiunti a Windows 10 con l'account Azure AD usando una chiave di sicurezza FIDO2.

|     |
| --- |
| FIDO2 security keys are a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere Condizioni per [l'utilizzo supplementari per le anteprime di Microsoft AzureFor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) more information about previews, see Supplemental Terms of Use for Microsoft Azure Previews|
|     |

## <a name="requirements"></a>Requisiti

| Tipo di dispositivo | Aggiunta ad Azure AD | Aggiunta a Azure AD ibrido |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Anteprima di registrazione delle informazioni di sicurezza combinate](concept-registration-mfa-sspr-combined.md) | X | X |
| Chiavi di [sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibili | X | X |
| WebAuthN richiede Windows 10 versione 1809 o successiva | X | X |
| [I dispositivi aggiunti ad Azure AD](../devices/concept-azure-ad-join.md) richiedono Windows 10 versione 1903 o successivaAzure AD joined devices require Windows 10 version 1903 or versione successiva | X |   |
| [I dispositivi aggiunti ad Azure AD ibrido](../devices/concept-azure-ad-join-hybrid.md) richiedono Windows 10 Insider Build 18945 o versione successivaHybrid Azure AD joined devices require Windows 10 Insider Build 18945 or versione successiva |   | X |
| Controller di dominio windows Server 2016/2019 completamente patchati. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) versione 1.4.32.0 o successivaAzure AD Connect version 1.4.32.0 or later |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (facoltativo) | X | X |
| Pacchetto di provisioning (facoltativo)Provisioning package (Optional) | X | X |
| Criteri di gruppo (facoltativo) |   | X |

### <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti non sono supportati:The following scenarios aren't supported:

- Distribuzione di Servizi di dominio Active Directory di Windows Server aggiunto a un dominio (solo dispositivi locali).
- RDP, VDI e Citrix utilizzando una chiave di sicurezza.
- S/MIME utilizzando una chiave di protezione.
- "Esegui come" utilizzando una chiave di sicurezza.
- Accedere a un server utilizzando una chiave di sicurezza.
- Se non hai usato la chiave di sicurezza per accedere al dispositivo mentre sei online, non puoi usarlo per accedere o sbloccarlo offline.
- Accesso o sblocco di un dispositivo Windows 10 con una chiave di sicurezza contenente più account Azure AD. Questo scenario utilizza l'ultimo account aggiunto alla chiave di sicurezza. WebAuthN consente agli utenti di scegliere l'account che desiderano utilizzare.
- Sbloccare un dispositivo che esegue Windows 10 versione 1809. Per un'esperienza ottimale, usa Windows 10 versione 1903 o successiva.

## <a name="prepare-devices-for-preview"></a>Preparare i dispositivi per l'anteprimaPrepare devices for preview

I dispositivi aggiunti ad Azure AD che si stanno pilotando durante l'anteprima della funzionalità con devono eseguire Windows 10 versione 1809 o successiva. L'esperienza migliore è su Windows 10 versione 1903 o successiva.

I dispositivi aggiunti ad Azure AD ibrido devono eseguire Windows 10 Insider Build 18945 o versione più recente.

## <a name="enable-security-keys-for-windows-sign-in"></a>Abilitare le chiavi di sicurezza per l'accesso a WindowsEnable security keys for Windows sign-in

Le organizzazioni possono scegliere di utilizzare uno o più dei metodi seguenti per abilitare l'utilizzo delle chiavi di protezione per l'accesso a Windows in base ai requisiti dell'organizzazione:

- [Abilitare con IntuneEnable with Intune](#enable-with-intune)
- [Distribuzione di Intune di destinazione](#targeted-intune-deployment)
- [Abilitare con un pacchetto di provisioningEnable with a provisioning package](#enable-with-a-provisioning-package)
- [Abilitare con Criteri di gruppo (solo dispositivi aggiunti ad Azure AD ibrido)Enable with Group Policy (Hybrid Azure AD joined devices only)](#enable-with-group-policy)

> [!IMPORTANT]
> Le organizzazioni con **dispositivi aggiunti ad Azure AD ibridi** devono inoltre completare i passaggi descritti nell'articolo [Abilitare l'autenticazione FIDO2 alle risorse locali](howto-authentication-passwordless-security-key-on-premises.md) prima del funzionamento dell'autenticazione della chiave di sicurezza FIDO2 di Windows 10.Organizations with hybrid Azure AD joined devices must **also** complete the steps in the article, Enable FIDO2 authentication to on-premises resources before Windows 10 FIDO2 security key authentication works.
>
> Le organizzazioni con **dispositivi aggiunti ad Azure AD** devono eseguire questa operazione prima che i dispositivi possano eseguire l'autenticazione alle risorse locali con chiavi di sicurezza FIDO2.Organizations with Azure AD joined devices must do this before their devices can authenticate to on-premises resources with FIDO2 security keys.

### <a name="enable-with-intune"></a>Abilitare con IntuneEnable with Intune

Per abilitare l'uso delle chiavi di sicurezza tramite Intune, completare i passaggi seguenti:To enable the use of security keys using Intune, complete the following steps:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a Proprietà di Windows per**la registrazione** > di Windows per la**registrazione** > di dispositivi **Microsoft Intune** > , ad esempio Windows**Hello for Business** > **Properties**.
1. In **Impostazioni**impostare **Usa chiavi di sicurezza per l'accesso su** **Abilitato**.

La configurazione delle chiavi di sicurezza per l'accesso non dipende dalla configurazione di Windows Hello for Business.

### <a name="targeted-intune-deployment"></a>Distribuzione di Intune di destinazione

To target specific device groups to enable the credential provider, use the following custom settings via Intune:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a**Profili** > di**configurazione** > dei dispositivi **di Microsoft Intune** > **Crea profilo**.
1. Configurare il nuovo profilo con le seguenti impostazioni:
   - Nome: Chiavi di sicurezza per l'accesso a Windows
   - Descrizione: abilita l'utilizzo delle chiavi di sicurezza FIDO durante l'accesso a Windows
   - Piattaforma: Windows 10 e versioni successive
   - Tipo di profilo: Personalizzato
   - Impostazioni URI OMA personalizzate:
      - Nome: Attivare le chiavi di sicurezza FIDO per l'accesso a Windows
      - URI OMA: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Tipo di dati: IntegerData Type: Integer
      - Valore: 1
1. Questo criterio può essere assegnato a utenti, dispositivi o gruppi specifici. Per altre informazioni, vedere [Assegnare profili utente e dispositivo in Microsoft Intune.For](https://docs.microsoft.com/intune/device-profile-assign)more information, see Assign user and device profiles in Microsoft Intune .

![Creazione di criteri di configurazione dei dispositivi personalizzati di IntuneIntune custom device configuration policy creation](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Abilitare con un pacchetto di provisioningEnable with a provisioning package

Per i dispositivi non gestiti da Intune, è possibile installare un pacchetto di provisioning per abilitare la funzionalità. L'app Progettazione configurazione di Windows può essere installata da [Microsoft Store.](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22) Completa i passaggi seguenti per creare un pacchetto di provisioning:Complete the following steps to create a provisioning package:

1. Avviare Progettazione configurazione di Windows.Launch the Windows Configuration Designer.
1. Selezionare **File** > **Nuovo progetto**.
1. Assegnare un nome al progetto e prendere nota del percorso in cui viene creato il progetto, quindi selezionare **Avanti**.
1. Lasciare *pacchetto di provisioning* selezionato come flusso di lavoro progetto **selezionato** e selezionare **Avanti**.
1. Selezionare *Tutte le edizioni desktop di Windows* in Scegliere le impostazioni da visualizzare e **configurare**, quindi selezionare **Avanti**.
1. Fare clic su **Fine**.
1. Nel progetto appena creato, passare alle **impostazioni** > di Runtime**WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Impostare **UseSecurityKeyForSignIn** su *Abilitato*.
1. Selezionare **Esporta** > **pacchetto di provisioning**
1. Lasciare le impostazioni predefinite nella finestra **Compila** in **Descrivere il pacchetto**di provisioning , quindi selezionare **Avanti**.
1. Lasciare le impostazioni predefinite nella finestra **Compila** in **Selezionare i dettagli** di sicurezza per il pacchetto di provisioning e selezionare **Avanti**.
1. Prendere nota o modificare il percorso nelle finestre **Compila** in **Selezionare dove salvare il pacchetto** di provisioning e selezionare **Avanti**.
1. Seleziona **Compila** nella pagina Crea il pacchetto di **provisioning.**
1. Salvare i due file creati (*ppkg* e *cat*) in una posizione in cui è possibile applicarli alle macchine in un secondo momento.
1. Per applicare il pacchetto di provisioning creato, consultate Applicare un pacchetto di [provisioning.](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)

> [!NOTE]
> I dispositivi che eseguono Windows 10 versione 1809 devono abilitare anche la modalità PC condiviso (*EnableSharedPCMode*). Per altre informazioni sull'abilitazione di questa funzionalità, vedere [Configurare un PC condiviso o guest con Windows 10.](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)

### <a name="enable-with-group-policy"></a>Abilita con Criteri di gruppo

Per i **dispositivi aggiunti ad Azure AD ibridi,** le organizzazioni possono configurare l'impostazione di Criteri di gruppo seguente per abilitare l'accesso alla chiave di sicurezza FIDO. L'impostazione è disponibile in **Configurazione** > computer**Modelli** > amministrativi**Accesso** > **sistema** > **Attivare l'accesso**alla chiave di protezione :

- L'impostazione di questo criterio su **Abilitato** consente agli utenti di accedere con le chiavi di sicurezza.
- L'impostazione di questo criterio su **Disabilitato** o **Non configurato** impedisce agli utenti di accedere con le chiavi di sicurezza.

Questa impostazione di Criteri di `credentialprovider.admx` gruppo richiede una versione aggiornata del modello Criteri di gruppo. Questo nuovo modello è disponibile con la prossima versione di Windows Server e con Windows 10 20H1. Questa impostazione può essere gestita con un dispositivo che esegue una di queste versioni più recenti di Windows o centralmente seguendo le indicazioni riportate nell'argomento di supporto [Come creare e gestire l'archivio centrale per](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)i modelli amministrativi di Criteri di gruppo in Windows .

## <a name="sign-in-with-fido2-security-key"></a>Accedere con la chiave di sicurezza FIDO2

Nell'esempio seguente, un utente denominato Bala Sandhu ha già eseguito il provisioning della chiave di protezione FIDO2 seguendo la procedura descritta nell'articolo [precedente, Abilitare l'accesso](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)alla chiave di protezione senza password . Per i dispositivi aggiunti ad Azure AD ibridi, assicurarsi di aver abilitato anche [l'accesso alla chiave](howto-authentication-passwordless-security-key-on-premises.md)di sicurezza senza password per le risorse locali. Bala può scegliere il provider di credenziali della chiave di sicurezza dalla schermata di blocco di Windows 10 e inserire la chiave di sicurezza per accedere a Windows.

![Accesso alla chiave di sicurezza nella schermata di blocco di Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gestire la chiave di sicurezza biometrica, PIN o reimpostazione della chiave di sicurezza

* Windows 10 versione 1903 o successiva
   * Gli utenti possono aprire **Impostazioni di Windows** sul proprio dispositivo > Chiave di**protezione** **Account** > 
   * Gli utenti possono modificare il PIN, aggiornare la biometria o reimpostare la chiave di sicurezza

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e feedback

Se vuoi condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, condividila tramite l'app Hub di Feedback di Windows seguendo la procedura seguente:

1. Avvia **Hub di Feedback** e assicurati di aver effettuato l'accesso.
1. Inviare commenti e suggerimenti nella seguente categorizzazione:
   - Categoria: Sicurezza e Privacy
   - Sottocategoria: FIDO
1. Per acquisire i log, utilizzare l'opzione **Ricrea il problema**

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso alle risorse locali per Azure AD e ai dispositivi aggiunti ad Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Ulteriori informazioni sulla registrazione del dispositivo](../devices/overview.md)

[Altre informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
