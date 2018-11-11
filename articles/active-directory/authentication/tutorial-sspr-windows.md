---
title: Reimpostazione della password self-service di Azure AD dalla schermata di accesso di Windows 10
description: In questa esercitazione verrà abilitata la reimpostazione della password dalla schermata di accesso di Windows 10 per ridurre le chiamate al supporto tecnico.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: bec94e2017660e9804bbc232e0a3163afdaafcb6
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277767"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Esercitazione: Reimpostazione della password self-service di Azure AD dalla schermata di accesso

In questa esercitazione viene descritto come consentire agli utenti di reimpostare le password dalla schermata di accesso di Windows 10. Con il nuovo aggiornamento del 10 aprile 2018 di Windows 10, gli utenti con dispositivi **aggiunti ad Azure AD** o **aggiunti ad Azure AD in modalità ibrida** possono usare il collegamento "Reimposta password" nella schermata di accesso. Facendo clic su questo collegamento, viene visualizzata l'esperienza di reimpostazione della password self-service che gli utenti conoscono.

> [!div class="checklist"]
> * Configurare il collegamento di reimpostazione della password con Intune
> * Configurazione facoltativa con il Registro di sistema di Windows
> * Comprendere quello che vedranno gli utenti

## <a name="prerequisites"></a>Prerequisiti

* Aggiornamento di aprile 2018 di Windows 10 o un client più recente:
   * [Aggiunto ad Azure AD](../device-management-azure-portal.md) o 
   * [Aggiunto a Azure AD in modalità ibrida](../device-management-hybrid-azuread-joined-devices-setup.md)
* È necessario abilitare la reimpostazione password self-service di Azure AD.

## <a name="configure-reset-password-link-using-intune"></a>Configurare il collegamento di reimpostazione della password con Intune

Distribuire la modifica della configurazione per abilitare la reimpostazione della password dalla schermata di accesso con Intune è il metodo più flessibile. Intune consente di distribuire la modifica della configurazione a uno specifico gruppo di computer definito. Questo metodo richiede la registrazione del dispositivo a Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Creare criteri di configurazione dei dispositivi in Intune

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Intune**.
2. Creare un nuovo profilo di configurazione dei dispositivi passando a **Configurazione del dispositivo** > **Profili** > **Crea profilo**
   * Immettere un nome significativo per il profilo
   * Specificare eventualmente una descrizione significativa per il profilo
   * Piattaforma **Windows 10 e versioni successive**
   * Tipo profilo **Personalizzato**

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

1. Accedere al PC Windows con credenziali amministrative
2. Eseguire **regedit** come amministratore
3. Impostare la chiave del Registro di sistema seguente
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Ora che i criteri sono stati configurati e assegnati, cosa cambia per l'utente? Come capisce che può reimpostare la password nella schermata di accesso?

![Schermata di accesso][LoginScreen]

Quando gli utenti provano ad accedere, viene ora visualizzato un collegamento per la reimpostazione della password che apre l'esperienza di reimpostazione della password self-service nella schermata di accesso. Questa funzionalità consente agli utenti di reimpostare la password senza dover usare un altro dispositivo per accedere a un Web browser.

Gli utenti troveranno indicazioni per l'uso di questa funzionalità in [Reimpostare la password per un account aziendale o dell'istituto di istruzione](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)

## <a name="common-issues"></a>Problemi comuni

Durante il test di questa funzionalità con Hyper-V, il collegamento "Reimposta password" non viene visualizzato.

* Passare alla macchina virtuale usata per il test e fare clic su **Visualizza**, quindi deselezionare **Sessione avanzata**.

Durante il test di questa funzionalità con Desktop remoto, il collegamento "Reimposta password" non viene visualizzato.

* La reimpostazione della password non è attualmente supportata da Desktop remoto.

Se la schermata di blocco di Windows è stata disabilitata tramite una chiave del Registro di sistema o Criteri di gruppo, l'opzione **Reimposta password** non è disponibile.

Se i criteri richiedono la combinazione Ctrl + Alt + Canc o se le notifiche della schermata di blocco sono disattivate **Reimposta password** non funziona.

Il log di controllo di Azure AD includerà informazioni sull'indirizzo IP e sul tipo di client in cui si è verificata la reimpostazione della password.

![Reimpostazione della password nella schermata di accesso di esempio nel log di controllo di Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Se i computer Windows 10 sono protetti da un server proxy o da un firewall, deve essere consentito il traffico HTTPS (443) verso passwordreset.microsoftonline.com e ajax.aspnetcdn.com.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si decide di non voler più usare la funzionalità configurata come parte di questa esercitazione, eliminare il profilo di configurazione dei dispositivi di Intune che è stato creato o la chiave del Registro di sistema.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come consentire agli utenti di reimpostare le password dalla schermata di accesso di Windows 10. Continuare con l'esercitazione successiva per informazioni su come è possibile integrare Azure Identity Protection nelle esperienze di reimpostazione della password self-service e Multi-Factor Authentication.

> [!div class="nextstepaction"]
> [Valutare i rischi all'accesso](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Assegnare criteri di configurazione dei dispositivi Intune a un gruppo di dispositivi Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Collegamento di reimpostazione della password nella schermata di accesso di Windows 10"
