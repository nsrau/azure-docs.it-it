---
title: Accesso alla chiave di sicurezza senza password (anteprima) - Azure Active Directory
description: Abilitare l'accesso alla chiave di sicurezza senza password in Azure AD usando le chiavi di sicurezza FIDO2 (anteprima)Enable passwordless security key sign-in to Azure AD using FIDO2 security keys (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e088d239a91edeff34ecd1a7dc5be7a9f8628da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129156"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Abilitare l'accesso alla chiave di sicurezza senza password (anteprima)Enable passwordless security key sign-in (preview)

Per le aziende che oggi utilizzano le password e hanno un ambiente PC condiviso, le chiavi di sicurezza forniscono ai lavoratori un modo semplice per eseguire l'autenticazione senza immettere un nome utente o una password. Le chiavi di sicurezza forniscono una maggiore produttività per i lavoratori e hanno una maggiore sicurezza.

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password basata su chiave di sicurezza. Alla fine di questo articolo, sarà possibile accedere alle applicazioni basate sul Web con l'account Azure AD usando una chiave di sicurezza FIDO2.

|     |
| --- |
| FIDO2 security keys are a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere Condizioni per [l'utilizzo supplementari per le anteprime di Microsoft AzureFor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) more information about previews, see Supplemental Terms of Use for Microsoft Azure Previews|
|     |

## <a name="requirements"></a>Requisiti

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Anteprima di registrazione delle informazioni di sicurezza combinate](concept-registration-mfa-sspr-combined.md)
- Chiavi di [sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibili
- WebAuthN richiede Windows 10 versione 1809 o successiva

Per utilizzare le chiavi di sicurezza per l'accesso ad app e servizi Web, è necessario disporre di un browser che supporti il protocollo WebAuthN. Questi includono Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices-for-preview"></a>Preparare i dispositivi per l'anteprimaPrepare devices for preview

I dispositivi aggiunti ad Azure AD con cui si pilota devono eseguire Windows 10 versione 1809 o successiva. L'esperienza migliore è su Windows 10 versione 1903 o successiva.

I dispositivi aggiunti ad Azure AD ibrido devono eseguire Windows 10 Insider Build 18945 o versione più recente.

## <a name="enable-passwordless-authentication-method"></a>Abilitare il metodo di autenticazione senza passwordEnable passwordless authentication method

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinataEnable the combined registration experience

Le funzionalità di registrazione per i metodi di autenticazione senza password si basano sull'anteprima combinata della registrazione. Seguire i passaggi nell'articolo Abilitare la registrazione combinata delle informazioni di [sicurezza (anteprima)](howto-registration-mfa-sspr-combined.md)per abilitare l'anteprima combinata della registrazione.

### <a name="enable-fido2-security-key-method"></a>Abilita metodo tasto di protezione FIDO2

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Passare**Security** > a Metodi > di autenticazione di sicurezza di **Azure Active Directory** > Criteri del metodo di**autenticazione****(anteprima)**.
1. Nel metodo **FIDO2 Security Key**scegliere le seguenti opzioni:
   1. **Abilita** - Sì o No
   1. **Destinazione** - Tutti gli utenti o Seleziona utenti
1. **Salvare** la configurazione.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrazione e gestione delle chiavi di sicurezza FIDO2 da parte degli utenti

1. Passare [https://myprofile.microsoft.com](https://myprofile.microsoft.com)a .
1. Accedi se non è già stato.
1. Fare clic su **Informazioni di sicurezza**.
   1. Se l'utente ha già registrato almeno un metodo di Azure Multi-Factor Authentication, può registrare immediatamente una chiave di sicurezza FIDO2.
   1. Se non è registrato almeno un metodo di Azure Multi-Factor Authentication, è necessario aggiungerne uno.
1. Aggiungere una chiave di protezione FIDO2 facendo clic su **Aggiungi metodo** e scegliendo Chiave **di protezione**.
1. Scegliere **Dispositivo USB** o Dispositivo **NFC**.
1. Tieni pronta la chiave e scegli **Avanti**.
1. Apparirà una casella e chiederà all'utente di creare/inserire un PIN per la chiave di sicurezza, quindi eseguire il gesto richiesto per la chiave, biometrica o tocco.
1. L'utente verrà restituito all'esperienza di registrazione combinata e verrà chiesto di fornire un nome significativo per la chiave in modo che l'utente possa identificare quale se ne ha più. Fare clic su **Avanti**.
1. Fare clic su **Fine** per completare il processo.

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Nell'esempio seguente un utente ha già eseguito il provisioning della chiave di sicurezza FIDO2. L'utente può scegliere di accedere sul Web con la chiave di sicurezza FIDO2 all'interno di un browser supportato in Windows 10 versione 1809 o successiva.

![Accesso alla chiave di sicurezza Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e feedback

Se vuoi condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, condividila tramite l'app Hub di Feedback di Windows seguendo la procedura seguente:

1. Avvia **Hub di Feedback** e assicurati di aver effettuato l'accesso.
1. Inviare commenti e suggerimenti nella seguente categorizzazione:
   - Categoria: Sicurezza e Privacy
   - Sottocategoria: FIDO
1. Per acquisire i log, utilizzare l'opzione **Ricrea il problema**

## <a name="known-issues"></a>Problemi noti

### <a name="security-key-provisioning"></a>Provisioning delle chiavi di sicurezza

Il provisioning e il deprovisioning delle chiavi di sicurezza dell'amministratore non sono disponibili nell'anteprima pubblica.

### <a name="upn-changes"></a>Modifiche UPN

Stiamo lavorando per supportare una funzionalità che consente la modifica UPN nei dispositivi aggiunti ad Azure AD ibrido e ad Azure AD. Se l'UPN di un utente viene modificato, non è più possibile modificare le chiavi di protezione FIDO2 per tenere conto della modifica. La risoluzione consiste nel reimpostare il dispositivo e l'utente deve registrarsi nuovamente.

## <a name="next-steps"></a>Passaggi successivi

[Chiave di sicurezza FIDO2 Accesso a Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Abilitare l'autenticazione FIDO2 alle risorse localiEnable FIDO2 authentication to on-premises resources](howto-authentication-passwordless-security-key-on-premises.md)

[Ulteriori informazioni sulla registrazione del dispositivo](../devices/overview.md)

[Altre informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
