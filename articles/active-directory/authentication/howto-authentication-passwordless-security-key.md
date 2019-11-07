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
ms.openlocfilehash: 50af82e79e7ba8b979ab28a1b3f608ec7e41bfb2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603440"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Abilitare l'accesso alla chiave di sicurezza senza password (anteprima)

Per le aziende che usano oggi le password e hanno un ambiente PC condiviso, le chiavi di sicurezza forniscono agli utenti un modo semplice per eseguire l'autenticazione senza immettere un nome utente o una password. Le chiavi di sicurezza forniscono una maggiore produttività per i ruoli di lavoro e offrono una maggiore sicurezza.

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password basata sulla chiave di sicurezza. Alla fine di questo articolo, sarà possibile accedere alle applicazioni basate sul Web con l'account Azure AD usando una chiave di sicurezza FIDO2.

|     |
| --- |
| Le chiavi di sicurezza di FIDO2 sono una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisiti

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Anteprima di registrazione delle informazioni di sicurezza combinata](concept-registration-mfa-sspr-combined.md)
- [Chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibili
- Webauthn richiede Windows 10 versione 1809 o successiva * *

Per usare le chiavi di sicurezza per l'accesso a servizi e app Web, è necessario disporre di un browser che supporti il protocollo webauthn. Sono inclusi Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices-for-preview"></a>Preparare i dispositivi per l'anteprima

I dispositivi con cui si eseguirà il pilota devono eseguire Windows 10 versione 1809 o successiva. L'esperienza migliore è in Windows 10 versione 1903 o successiva.

## <a name="enable-passwordless-authentication-method"></a>Abilita metodo di autenticazione con password

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione con password si basano sull'anteprima della registrazione combinata. Per abilitare l'anteprima della registrazione combinata, seguire la procedura descritta nell'articolo [abilitare la registrazione delle informazioni di sicurezza combinata (anteprima)](howto-registration-mfa-sspr-combined.md).

### <a name="enable-fido2-security-key-method"></a>Abilitare il metodo della chiave di sicurezza FIDO2

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory** > **sicurezza** > **metodi di autenticazione** > **criterio metodo di autenticazione (anteprima)** .
1. Sotto il metodo **FIDO2 Security Key**scegliere le opzioni seguenti:
   1. **Abilita** -Sì o no
   1. **Destinazione** -tutti gli utenti o utenti selezionati
1. **Salvare** la configurazione.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrazione e gestione degli utenti delle chiavi di sicurezza FIDO2

1. Passare a [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Accedere se non è già stato fatto.
1. Fare clic su **info di sicurezza**.
   1. Se l'utente ha già registrato almeno un metodo di Multi-Factor Authentication di Azure, può registrare immediatamente una chiave di sicurezza FIDO2.
   1. Se non hanno almeno un metodo di Multi-Factor Authentication di Azure registrato, è necessario aggiungerne uno.
1. Aggiungere una chiave di sicurezza FIDO2 facendo clic su **Aggiungi metodo** e scegliendo **chiave di sicurezza**.
1. Scegliere **dispositivo USB** o **dispositivo NFC**.
1. Preparare la chiave e scegliere **Avanti**.
1. Verrà visualizzata una casella per chiedere all'utente di creare/immettere un PIN per la chiave di sicurezza, quindi eseguire il gesto necessario per la chiave, ovvero biometrica o touch.
1. L'utente verrà restituito all'esperienza di registrazione combinata e chiederà di fornire un nome significativo per la chiave in modo che l'utente possa identificare quello che ha più. Fare clic su **Avanti**.
1. Fare clic su **fine** per completare il processo.

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Nell'esempio seguente un utente ha già eseguito il provisioning della chiave di sicurezza FIDO2. L'utente può scegliere di accedere al Web con la chiave di sicurezza FIDO2 all'interno di un browser supportato in Windows 10 versione 1809 o successiva.

![Accesso alla chiave di sicurezza Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e commenti e suggerimenti

Per condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, è possibile condividerla tramite l'app hub di feedback di Windows.

1. Avviare **feedback Hub** e assicurarsi di aver eseguito l'accesso.
1. Inviare commenti e suggerimenti nella categorizzazione seguente:
   1. Categoria: sicurezza e privacy
   1. Sottocategoria: FIDO
1. Per acquisire i log, usare l'opzione: **ricrea il problema**

## <a name="known-issues"></a>Problemi noti

### <a name="security-key-provisioning"></a>Provisioning delle chiavi di sicurezza

Il provisioning e il deprovisioning dell'amministratore delle chiavi di sicurezza non sono disponibili nell'anteprima pubblica.

### <a name="upn-changes"></a>Modifiche UPN

Se l'UPN di un utente viene modificato, non è più possibile modificare le chiavi di sicurezza di FIDO2 per tenere conto della modifica. La risoluzione consiste nel reimpostare il dispositivo e l'utente deve registrare di nuovo le chiavi di sicurezza FIDO2.

## <a name="next-steps"></a>Passaggi successivi

[Chiave di sicurezza FIDO2 accesso a Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Abilitare l'autenticazione FIDO2 per le risorse locali](howto-authentication-passwordless-security-key-on-premises.md)

[Altre informazioni sulla registrazione del dispositivo](../devices/overview.md)

[Scopri di più su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
