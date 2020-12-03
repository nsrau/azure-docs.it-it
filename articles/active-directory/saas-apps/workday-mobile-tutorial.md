---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con l'applicazione Workday per dispositivi mobili | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e l'applicazione Workday per dispositivi mobili.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182020"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con l'applicazione Workday per dispositivi mobili

Questa esercitazione illustra come integrare Azure Active Directory (Azure AD), l'accesso condizionale e Intune con l'applicazione Workday per dispositivi mobili. Integrando l'applicazione Workday per dispositivi mobili con Microsoft, è possibile:

* Assicurarsi che i dispositivi siano conformi ai propri criteri prima dell'accesso.
* Aggiungere controlli all'applicazione Workday per dispositivi mobili per assicurarsi che gli utenti accedano in modo sicuro ai dati aziendali. 
* Controllare in Azure AD chi può accedere a Workday.
* Abilitare gli utenti per l'accesso automatico a Workday con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Attività iniziali

* Integrare Workday con Azure AD.
* Read [Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Descrizione dello scenario

Questa esercitazione illustra come configurare e testare i criteri di accesso condizionale di Azure AD e Intune con l'applicazione Workday per dispositivi mobili.

Per abilitare l'accesso Single Sign-On (SSO), è possibile configurare l'applicazione Workday Federated con Azure AD. Per altre informazioni, vedere [Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workday](./workday-tutorial.md).

> [!NOTE] 
> Workday non supporta i criteri di protezione delle app di Intune. Per sfruttare l'accesso condizionale, è necessario usare il software per la gestione di dispositivi mobili.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Assicurarsi che gli utenti abbiano accesso all'applicazione Workday per dispositivi mobili

Configurare Workday per consentire l'accesso alle app per dispositivi mobili. È necessario configurare i criteri seguenti per l'applicazione Workday per dispositivi mobili:

1. Accedere al report dei criteri di sicurezza del dominio per area funzionale.
1. Selezionare i criteri di sicurezza appropriati:
    * Utilizzo di dispositivi mobili Android
    * Utilizzo di dispositivi mobili iPad
    * Utilizzo di dispositivi mobili iPhone
1. Selezionare **Edit Permissions** (Modifica autorizzazioni).
1. Selezionare la casella di controllo **View or Edit** (Visualizzazione o Modifica) per concedere ai gruppi di sicurezza l'accesso al report o assegnare gli elementi da proteggere.
1. Selezionare la casella di controllo **Get or Put** (Recupero o Inserimento) per concedere ai gruppi di sicurezza l'accesso all'integrazione e al report o assegnare le azioni da proteggere.

Attivare le modifiche in sospeso ai criteri di sicurezza eseguendo **Activate Pending Security Policy Changes** (Attiva le modifiche in sospeso ai criteri di sicurezza).

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Aprire la pagina di accesso di Workday nel browser di Workday per dispositivi mobili

Per applicare l'accesso condizionale all'applicazione Workday per dispositivi mobili, è necessario aprire l'app in un browser esterno. In **Edit Tenant Setup - Security** (Modifica la configurazione del tenant - Sicurezza) selezionare **Enable Mobile Browser SSO for Native Apps** (Abilita SSO nel browser per le app native). È necessario che nel dispositivo per iOS e nel profilo di lavoro per Android sia installato un browser approvato da Intune.

![Screenshot relativo alla pagina di accesso del browser di Workday per dispositivi mobili.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Configurare i criteri di accesso condizionale

Questi criteri influiscono solo sull'accesso ai dispositivi iOS e Android. Per estenderli a tutte le piattaforme, selezionare **Any Device** (Qualsiasi dispositivo). È necessario che il dispositivo sia conforme a questi criteri ed è possibile verificarlo tramite Intune. In Android i profili di lavoro dovrebbero impedire agli utenti di accedere a Workday a meno che non eseguano l'accesso tramite i profili di lavoro e abbiano installato l'app tramite il Portale aziendale di Intune. Per iOS è necessario eseguire un passaggio aggiuntivo per assicurarsi che venga applicata la stessa situazione.

Workday supporta i controlli di accesso seguenti:
* Richiedere l'autenticazione a più fattori
* Richiedere che i dispositivi siano contrassegnati come conformi

L'app Workday non supporta i criteri seguenti:
* Richiedere app client approvata
* Richiedi criteri di protezione delle pp (anteprima)

Per configurare Workday come dispositivo gestito seguire questa procedura:

![Screenshot di Managed Devices Only e Applicazioni cloud o azioni.](./media/workday-tutorial/managed-devices-only.png)

1. Selezionare **Home** > **Microsoft Intune** > **Criteri di accesso condizionale**. Selezionare quindi **Managed Devices Only** (Solo dispositivi gestiti). 

1. In **Managed Devices Only** (Solo dispositivi gestiti) selezionare **Managed Devices Only** in **Nome** e quindi selezionare **Applicazioni cloud o azioni**.

1. In **Applicazioni o azioni cloud**:

    a. Impostare l'interruttore **Selezionare a cosa si applicano i criteri** su **App cloud**.

    b. In **Includi** scegliere **Selezionare le app**.

    c. Nell'elenco **Seleziona** scegliere **Workday**.

    d. Selezionare **Fine**.

1. Impostare **Abilita criteri** su **Sì**.

1. Selezionare **Salva**.

Per **Concedi** seguire questa procedura:

![Screenshot di Managed Devices Only e Concedi.](./media/workday-tutorial/managed-devices-only-2.png)

1. Selezionare **Home** > **Microsoft Intune** > **Criteri di accesso condizionale**. Selezionare quindi **Managed Devices Only** (Solo dispositivi gestiti). 

1. In **Managed Devices Only** (Solo dispositivi gestiti) selezionare **Managed Devices Only** in **Nome**. In **Controlli di accesso** selezionare **Concedi**.

1. In **Concedi**:

    a. Selezionare i controlli da applicare in **Concedi accesso**.

    b. Selezionare **Richiedi che i dispositivi siano contrassegnati come conformi**.

    c. Selezionare **Richiedi uno dei controlli selezionati**.

    d. Scegliere **Seleziona**.

1. Impostare **Abilita criteri** su **Sì**.

1. Selezionare **Salva**.

## <a name="set-up-device-compliance-policy"></a>Configurare i criteri di conformità dei dispositivi

Per assicurarsi che i dispositivi iOS siano in grado di accedere solo tramite Workday controllato da software per la gestione di dispositivi mobili, è necessario bloccare l'app nell'App Store aggiungendo **com.workday.workdayapp** all'elenco delle app con restrizioni. In questo modo, solo i dispositivi in cui l'app Workday è stata installata tramite il portale aziendale potranno accedere a Workday. Per il browser, i dispositivi potranno accedere a Workday solo se il dispositivo è gestito da Intune e usa un browser gestito.

![Screenshot dei criteri di conformità per i dispositivi iOS.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Configurare i criteri di configurazione app di Intune

| Scenario | Coppie chiave-valore |
|----------------------------------------------------------------------------------------   |-----------|
| Popolare automaticamente i campi Tenant e Indirizzo Web per:<br>● Workday in Android quando si abilitano i profili di lavoro di Android.<br>● Workday in iPad e iPhone.     | Usare questi valori per configurare il tenant: <br>● Chiave di configurazione = `UserGroupCode`<br>● Tipo di valore = stringa <br>● Valore di configurazione = il nome del tenant. Esempio: `gms`<br>Usare questi valori per configurare l'indirizzo Web:<br>● Chiave di configurazione = `AppServiceHost`<br>● Tipo di valore = stringa<br>● Valore di configurazione = URL di base per il tenant. Esempio: `https://www.myworkday.com`                                |   |
| Disabilitare queste azioni per Workday in iPad e iPhone:<br>● Taglia, Copia e Incolla<br>● Stampa                       | Impostare il valore (booleano) su `False` in queste chiavi per disabilitare la funzionalità:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Disabilitare gli screenshot per Workday in Android. |Impostare il valore (booleano) su `False` nella chiave `AllowScreenshots` per disabilitare la funzionalità.|
| Disabilitare gli aggiornamenti suggeriti per gli utenti.|Impostare il valore (booleano) su `False` nella chiave `AllowSuggestedUpdates` per disabilitare la funzionalità.|
|Personalizzare l'URL dell'App Store per indirizzare gli utenti di dispositivi mobili a uno store specifico.|Usare questi valori per cambiare l'URL dell'Spp Store:<br>● Chiave di configurazione = `AppUpdateURL`<br>● Tipo di valore = stringa<br> ● Valore di configurazione = URL dell'App Store|
|       |


## <a name="ios-configuration-policies"></a>Criteri di configurazione di iOS

1. Passare al [portale di Azure](https://portal.azure.com/) ed eseguire l'accesso.
1. Cercare **Intune** oppure fare clic sul widget nell'elenco.
1. Passare a **App client** > **App** > **Criteri di configurazione dell'app**. Selezionare quindi **+ Aggiungi** > **Dispositivi gestiti**.
1. Immettere un nome.
1. In **Piattaforma** scegliere **iOS/iPadOS**.
1. In **App associata** scegliere l'app Workday per iOS aggiunta.
1. Selezionare **Impostazioni di configurazione**. In **Formato delle impostazioni di configurazione** selezionare **Immettere i dati XML**.
1. Ecco un esempio di file XML. Aggiungere le configurazioni da applicare. Sostituire `STRING_VALUE` con la stringa da usare. Sostituire `<true /> or <false />` con `<true />` o `<false />`. Se non si aggiunge una configurazione, si avrà lo stesso effetto dell'impostazione su `True`.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Selezionare **Aggiungi**.
1. Aggiornare la pagina e selezionare i criteri appena creati.
1. Selezionare **Assegnazioni** e scegliere a chi applicare l'app.
1. Selezionare **Salva**.

## <a name="android-configuration-policies"></a>Criteri di configurazione di Android

1. Passare al [portale di Azure](https://portal.azure.com/) ed eseguire l'accesso.
2. Cercare **Intune** oppure fare clic sul widget nell'elenco.
3. Passare a **App client** > **App** > **Criteri di configurazione dell'app**. Selezionare quindi **+ Aggiungi** > **Dispositivi gestiti**.
5. Immettere un nome. 
6. In **Piattaforma** scegliere **Android**.
7. In **App associata** scegliere l'app Workday per Android aggiunta.
8. Selezionare **Impostazioni di configurazione**. In **Formato delle impostazioni di configurazione** selezionare **Immettere i dati JSON**.