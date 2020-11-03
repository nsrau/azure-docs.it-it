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
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754182"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con l'applicazione Workday per dispositivi mobili

Questa esercitazione illustra come integrare Azure Active Directory (Azure AD), l'accesso condizionale e Intune con le app Workday per dispositivi mobili. Integrando le app Workday per dispositivi mobili con Microsoft, è possibile:

* Assicurarsi che i dispositivi siano conformi ai propri criteri prima dell'accesso.
* Aggiungere controlli all'app Workday per assicurarsi che gli utenti accedano in modo sicuro ai dati aziendali. 
* Controllare in Azure AD chi può accedere a Workday.
* Abilitare gli utenti per l'accesso automatico a Workday con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Integrare Workday con Azure AD
* Esercitazione: [Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono configurati e testati i criteri di accesso condizionale di Microsoft e Intune con le applicazioni Workday per dispositivi mobili.

* È ora possibile configurare l'applicazione federata Workday con Azure AD per abilitare l'accesso SSO. Per altri dettagli sulla procedura di configurazione, seguire questo [collegamento](workday-tutorial.md).

> [!NOTE] 
> Workday non supporta i criteri di protezione delle app di Intune. Per sfruttare l'accesso condizionale, è necessario usare la gestione di dispositivi mobili.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Assicurarsi che gli utenti abbiano accesso all'app Workday pe dispositivi mobili:

Configurare Workday per consentire l'accesso alle offerte di app per dispositivi mobili. Sarà necessario configurare i criteri seguenti per dispositivi mobili:

È possibile eseguire la configurazione seguendo queste istruzioni:

1. Accedere al report Criteri di sicurezza del dominio per area funzionale.
2. Selezionare un criterio di sicurezza.
    * Utilizzo di dispositivi mobili Android
    * Utilizzo di dispositivi mobili iPad
    * Utilizzo di dispositivi mobili iPhone
3. Fare clic su Modifica autorizzazioni.
4. Selezionare la casella di controllo View (Visualizza) o Edit (Modifica) per concedere ai gruppi di sicurezza l'accesso al report o assegnare gli elementi da proteggere.
5. Selezionare la casella di controllo Get (Ottieni) o Put (Inserisci) per concedere ai gruppi di sicurezza l'accesso all'integrazione e al report o assegnare le azioni da proteggere.

Attivare le modifiche in sospeso ai criteri di sicurezza eseguendo l'attività **Activate Pending Security Policy Changes** (Attiva le modifiche in sospeso ai criteri di sicurezza).

## <a name="open-workday-login-page-in-mobile-browser"></a>Aprire la pagina di accesso di Workday nel browser per dispositivi mobili:

Per applicare l'accesso condizionale all'app Workday per dispositivi mobili, è necessario che l'app venga aperta in un browser esterno. A questo scopo, selezionare la casella **Enable Mobile Browser SSO for Native Apps** (Abilita SSO nel browser per dispositivi mobili per le app native) in **Edit Tenant Setup - Security** (Modifica la configurazione del tenant - Sicurezza). È necessario che nel dispositivo per iOS e nel profilo di lavoro per Android sia installato un browser approvato da Intune

![Accesso al browser per dispositivi mobili](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Configurare i criteri di accesso condizionale:

Questi criteri influiranno solo sull'accesso ai dispositivi iOS e Android. Per estenderli a tutte le piattaforme, selezionare **Qualsiasi dispositivo**. È necessario che il dispositivo sia conforme a questi criteri ed è possibile verificarlo tramite Microsoft Intune. In Android i profili di lavoro dovrebbero impedire agli utenti di accedere a Workday (Web o app) a meno che non eseguano l'accesso tramite i profili di lavoro e abbiano installato l'app tramite il Portale aziendale Intune. Per iOS è necessario eseguire un passaggio aggiuntivo per assicurarsi che venga applicata la stessa situazione. Ecco alcuni screenshot della configurazione dell'accesso condizionale.

**Workday supporta i controlli di accesso seguenti:**
* Richiedi autenticazione a più fattori
* Richiedere che i dispositivi siano contrassegnati come conformi

**L'app Workday non supporta i criteri seguenti:**
* Richiedere app client approvata
* Richiedi criteri di protezione delle app (anteprima)

Per configurare **Workday** come **dispositivo gestito** eseguire questa procedura:

![Configurare i criteri di accesso condizionale](./media/workday-tutorial/managed-devices-only.png)

1. Fare clic su **Home > Microsoft Intune > Criteri di accesso condizionale > Managed Devices Only** (Solo dispositivi gestiti) 

1. Nella pagina **Managed Devices Only** assegnare `Managed Devices Only` come valore del campo **Name** (Nome) e fare clic su **Cloud apps or actions** (App o azioni cloud).

1. Nella pagina **Cloud apps or actions** seguire questa procedura.

    a. Impostare l'interruttore **Select what this policy applies to** (Applica questi criteri a) su **Cloud apps** (App cloud).

    b. In Include (Includi) fare clic su **Select apps** (Seleziona app).

    c. Scegliere **Workday** nell'elenco di selezione.

    d. Fare clic su **Done**.

1. Attivare l'opzione **Enable policy** (Abilita criteri).

1. Fare clic su **Salva**.

Per **Concedi** seguire questa procedura:

![Configurare i criteri di accesso condizionale per Workday](./media/workday-tutorial/managed-devices-only-2.png)

1. Fare clic su **Home > Microsoft Intune > Criteri di accesso condizionale > Managed Devices Only** (Solo dispositivi gestiti) 

1. Nella pagina **Managed Devices Only** assegnare `Managed Devices Only` come valore del campo **Name** (Nome) e fare clic su **Controlli di accesso > Concedi**.

1. Nella pagina **Concedi** seguire questa procedura.

    a. Selezionare i controlli da applicare in **Concedi accesso**.

    b. Selezionare la casella **Richiedi che i dispositivi siano contrassegnati come conformi**.

    c. Selezionare **Richiedi uno dei controlli selezionati**.

    d. Fare clic su **Seleziona**.

1. Attivare l'opzione **Enable policy** (Abilita criteri).

1. Fare clic su **Save** (Salva).

## <a name="set-up-device-compliance-policy"></a>Configurare i criteri di conformità del dispositivo:

Per assicurarsi che i dispositivi iOS siano in grado di accedere solo tramite un'app Workday controllata da gestione di dispositivi mobili, è necessario bloccare l'app nell'App Store aggiungendo **com.workday.workdayapp** all'elenco delle app con restrizioni. In questo modo, solo i dispositivi che hanno l'app Workday installata tramite il portale aziendale potranno accedere a Workday. Per il browser, potranno accedere a Workday solo se il dispositivo è gestito da Intune e se usano un browser gestito.

![Configurare i criteri di conformità del dispositivo in Workday](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Configurare i criteri di configurazione app di Microsoft Intune:

| Scenario | Coppie chiave-valore |
|----------------------------------------------------------------------------------------   |-----------|
| Popolare automaticamente i campi Tenant e Indirizzo Web per:<br>● Workday in Android quando si abilitano i profili di lavoro di Android.<br>● Workday in iPad e iPhone.     | Usare questi valori per configurare il tenant: <br>● Chiave di configurazione = UserGroupCode<br>● Tipo di valore = stringa <br>● Valore di configurazione = il nome del tenant. Esempio: gms<br>Usare questi valori per configurare l'indirizzo Web:<br>● Chiave di configurazione = AppServiceHost<br>● Tipo di valore = stringa<br>● Valore di configurazione = URL di base per il tenant. Esempio: https://www.myworkday.com                              |   |
| Disabilitare queste azioni per Workday in iPad e iPhone:<br>● Taglia, Copia e Incolla<br>● Stampa                       | Impostare il valore (booleano) su False in queste chiavi per disabilitare la funzionalità:<br>● AllowCutCopyPaste<br>● AllowPrint  |
| Disabilitare gli screenshot per Workday in Android. |Impostare il valore (booleano) su False nella chiave AllowScreenshots per disabilitare la funzionalità.|
| Disabilitare gli aggiornamenti suggeriti per gli utenti.|Impostare il valore (booleano) su False nella chiave AllowSuggestedUpdates per disabilitare la funzionalità.|
|Personalizzare l'URL dell'App Store per indirizzare gli utenti di dispositivi mobili a uno store specifico.|Usare questi valori per cambiare l'URL dell'Spp Store:<br>● Chiave di configurazione = AppUpdateURL<br>● Tipo di valore = stringa<br> ● Valore di configurazione = URL dell'App Store|
|       |


## <a name="ios-configuration-policies"></a>Criteri di configurazione di iOS:

1. Passare a https://portal.azure.com/ e accedere
2. Cercare **Intune** oppure fare clic sul widget nell'elenco.
3. Passare a **App client-> App-> Criteri di configurazione dell'app-> + Aggiungi-> Dispositivi gestiti**
4. Immettere un nome.
5. In **Piattaforma** scegliere **iOS/iPadOS**
6. In **App associata** scegliere l'app Workday per iOS aggiunta
7. Fare clic su **Impostazioni di configurazione** e in **Formato delle impostazioni di configurazione** scegliere **Immettere i dati XML**
8. Ecco un esempio di file XML. Aggiungere la configurazione da applicare. Sostituire **STRING_VALUE** con la stringa da usare. Sostituire `<true />` o `<false />` con `<true />` o `<false />`. Se non si aggiunge una configurazione, si avrà lo stesso effetto dell'impostazione su True.

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
9. Fare clic su Aggiungi.
10. Aggiornare la pagina e fare clic sui criteri appena creati.
11. Fare clic su Assegnazioni e scegliere a chi applicare l'app.
12. Fare clic su Salva.

## <a name="android-configuration-policies"></a>Criteri di configurazione di Android:

1. Passare a `https://portal.azure.com/` e accedere.
2. Cercare **Intune** oppure fare clic sul widget nell'elenco.
3. Passare a **App client-> App-> Criteri di configurazione dell'app-> + Aggiungi-> Dispositivi gestiti**
5. Immettere un nome. 
6. In **Piattaforma** scegliere **Android**
7. In **App associata** scegliere l'app Workday per Android aggiunta
8. Fare clic su **Impostazioni di configurazione** e in **Formato delle impostazioni di configurazione** scegliere **Immettere i dati JSON**

