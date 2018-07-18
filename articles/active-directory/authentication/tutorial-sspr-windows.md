---
title: Reimpostazione password self-service di Azure AD dalla schermata di accesso di Windows 10 | Microsoft Docs
description: Configurare la reimpostazione password self-service di Azure AD dalla schermata di accesso di Windows 10
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 3b767da394f6337a1110ad171f19ad789fa044d2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972982"
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Reimpostazione password self-service di Azure AD dalla schermata di accesso

La reimpostazione password self-service di Azure AD è già stata distribuita, ma gli utenti continuano a chiamare il supporto tecnico quando dimenticano la password, perché non possono aprire un Web browser per accedere alla reimpostazione password self-service.

Con il nuovo aggiornamento del 10 aprile 2018 di Windows 10, gli utenti con dispositivi **aggiunti ad Azure AD** o **aggiunti all'identità ibrida di Azure AD** possono visualizzare e usare il collegamento "Reimposta password" nella schermata di accesso. Facendo clic su questo collegamento, viene visualizzata l'esperienza di reimpostazione password self-service (SSPR) che gli utenti conoscono.

Per consentire agli utenti di reimpostare la password di Azure AD dalla schermata di accesso di Windows 10, è necessario soddisfare i requisiti seguenti:

* Aggiornamento del 10 aprile 2018 di Windows 10 o un client più recente [aggiunto ad Azure AD](../device-management-azure-portal.md) o [aggiunto all'identità ibrida di Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md).
* È necessario abilitare la reimpostazione password self-service di Azure AD.
* Configurare e distribuire l'impostazione per abilitare il collegamento di reimpostazione della password tramite uno dei metodi seguenti:
   * [Profilo di configurazione dei dispositivi di Intune](tutorial-sspr-windows.md#configure-reset-password-link-using-intune). Questo metodo richiede la registrazione del dispositivo a Intune.
   * [Chiave del Registro di sistema](tutorial-sspr-windows.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Configurare il collegamento di reimpostazione della password con Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Creare criteri di configurazione dei dispositivi in Intune

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Intune**.
2. Creare un nuovo profilo di configurazione dei dispositivi passando a **Configurazione del dispositivo** > **Profili** > **Crea profilo**
   * Immettere un nome significativo per il profilo
   * Specificare eventualmente una descrizione significativa per il profilo
   * Piattaforma **Windows 10 e versioni successive**
   * Tipo profilo **Personalizzato**

   ![Creare il profilo][CreateProfile]

3. Configurare le **impostazioni**
   * **Aggiungere** le impostazioni URI OMA seguenti per abilitare il collegamento di reimpostazione della password
      * Specificare un nome significativo per spiegare l'effetto dell'impostazione
      * Specificare eventualmente una descrizione significativa per l'impostazione
      * Impostare **URI OMA** su `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * Impostare **Tipo di dati** su **Integer**
      * Impostare **Valore** su **1**
      * Fare clic su **OK**.
   * Fare clic su **OK**.
4. Fare clic su **Crea**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Assegnare criteri di configurazione dei dispositivi in Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Creare un gruppo al quale applicare i criteri di configurazione dei dispositivi

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Azure Active Directory**.
2. Passare a **Utenti e gruppi** > **Tutti gli utenti** > **Nuovo gruppo**
3. Specificare un nome per il gruppo e in **Tipo di appartenenza** scegliere **Assegnato**
   * In **Membri** scegliere i dispositivi Windows 10 aggiunti ad Azure AD ai quali si vogliono applicare i criteri.
   * Fare clic su **Seleziona**
4. Fare clic su **Crea**

Per altre informazioni sulla creazione di gruppi, vedere l'articolo [Gestire l'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Assegnare criteri di configurazione dei dispositivi a un gruppo di dispositivi

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Intune**.
2. Trovare il profilo di configurazione dei dispositivi creato in precedenza passando a **Configurazione del dispositivo** > **Profili** > Fare clic sul profilo creato in precedenza
3. Assegnare il profilo a un gruppo di dispositivi 
   * Fare clic su **Assegnazioni** > in **Includi** > **Selezionare i gruppi da includere**
   * Selezionare il gruppo creato in precedenza e fare clic su **Seleziona**
   * Fare clic su **Salva**

   ![Assegnazione][Assignment]

Ora sono stati creati e assegnati criteri di configurazione dei dispositivi per abilitare il collegamento di reimpostazione della password nella schermata di accesso con Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Configurare il collegamento di reimpostazione della password con il Registro di sistema

È consigliabile usare questo metodo solo per testare la modifica dell'impostazione.

1. Accedere al dispositivo aggiunto ad Azure AD con credenziali amministrative
2. Eseguire **regedit** come amministratore
3. Impostare la chiave del Registro di sistema seguente
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Ora che i criteri sono stati configurati e assegnati, cosa cambia per l'utente? Come capisce che può reimpostare la password nella schermata di accesso?

![Schermata di accesso][LoginScreen]

Quando gli utenti provano ad accedere, viene ora visualizzato un collegamento per la reimpostazione della password che apre l'esperienza di reimpostazione password self-service nella schermata di accesso. Questa funzionalità consente agli utenti di reimpostare la password senza dover usare un altro dispositivo per accedere a un Web browser.

Gli utenti troveranno indicazioni per l'uso di questa funzionalità in [Reimpostare la password per un account aziendale o dell'istituto di istruzione](../active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problemi comuni

Durante il test di questa funzionalità con Hyper-V, il collegamento "Reimposta password" non viene visualizzato.

* Passare alla macchina virtuale usata per il test e fare clic su **Visualizza**, quindi deselezionare **Sessione avanzata**.

Durante il test di questa funzionalità con Desktop remoto, il collegamento "Reimposta password" non viene visualizzato

* La reimpostazione della password non è attualmente supportata da Desktop remoto.

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [Come si esegue la distribuzione della reimpostazione password self-service?](howto-sspr-deployment.md)
* [Come si abilita la reimpostazione del PIN dalla schermata di accesso?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [Altre informazioni sui criteri di autenticazione MDM](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Creare un profilo di configurazione dei dispositivi Intune per abilitare il collegamento di reimpostazione della password nella schermata di accesso di Windows 10"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Assegnare criteri di configurazione dei dispositivi Intune a un gruppo di dispositivi Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Collegamento di reimpostazione della password nella schermata di accesso di Windows 10"
