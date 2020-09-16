---
title: Criteri di protezione delle app con accesso condizionale - Azure Active Directory
description: Informazioni su come richiedere criteri di protezione app per l'accesso alle app cloud con Accesso condizionale in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82ab9bc0159528446a9de95769f1e433f03acb56
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601946"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Procedura: Richiedere criteri di protezione delle app e un'app client approvata per l'accesso alle app cloud con Accesso condizionale

Gli utenti usano spesso i dispositivi mobili sia per le attività personali che per quelle di lavoro. Le organizzazioni vogliono garantire che il personale sia produttivo, ma anche impedire la perdita di dati da applicazioni potenzialmente non sicure. Con Accesso condizionale le organizzazioni possono limitare l'accesso alle app client approvate (che supportano l'autenticazione moderna) alle quali sono applicati i criteri di protezione delle app di Intune.

Questo articolo presenta tre scenari per configurare i criteri di accesso condizionale per risorse quali Microsoft 365, Exchange Online e SharePoint.

- [Scenario 1: le app Microsoft 365 richiedono app approvate con i criteri di protezione delle app](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: le app browser richiedono app approvate con criteri di protezione delle app](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 3: Exchange Online e SharePoint richiedono un'app client approvata e i criteri di protezione delle app](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

In Accesso condizionale è noto che queste app client sono protette con criteri di protezione delle app. Altre informazioni sui criteri di protezione delle app sono disponibili nell'articolo [Panoramica dei criteri di protezione app](/intune/apps/app-protection-policy).

> [!WARNING]
> Non tutte le applicazioni sono supportate come applicazioni approvate o supportano i criteri di protezione delle applicazioni. Per un elenco delle app client idonee, vedere [Requisiti dei criteri di protezione app](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> "Richiedi uno dei controlli selezionati" nei controlli di concessione è simile a una clausola o. Questa opzione viene usata all'interno dei criteri per consentire agli utenti di usare le app che supportano i **criteri di protezione delle app require** o **Require client approvati** . È necessario applicare i **criteri di protezione delle app** se un'app è supportata in entrambi i criteri. Per altre informazioni sulle app che supportano il controllo di concessione **Richiedi criteri di protezione delle app**, vedere [Requisiti dei criteri di protezione app](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: le app Microsoft 365 richiedono app approvate con i criteri di protezione delle app

In questo scenario, Contoso ha deciso che tutti gli accessi per dispositivi mobili alle risorse Microsoft 365 devono usare app client approvate come Outlook Mobile e OneDrive, protette da un criterio di protezione delle app prima di ricevere l'accesso. Tutti gli utenti accedono già con credenziali di Azure AD e hanno licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili, le organizzazioni devono completare la procedura seguente.

**Passaggio 1: configurare un criterio di accesso condizionale Azure AD per Microsoft 365**

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti** o gli **Utenti e gruppi** ai quale si vuole applicare questi criteri. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Office 365 (anteprima)** .
1. In **Condizioni** selezionare **Piattaforme del dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includere **Android** e **iOS**.
1. In **condizioni**selezionare **app client**.
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **app per dispositivi mobili e client desktop** e deselezionare tutte le altre operazioni.
1. In **Controlli di accesso** > **Concedi** selezionare le opzioni seguenti:
   - **Richiedi app client approvata**
   - **Richiedi criteri di protezione delle app (anteprima)**
   - **Richiedi tutti i controlli selezionati**
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: configurare criteri di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti** o gli **Utenti e gruppi** ai quale si vuole applicare questi criteri. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Office 365 Exchange Online**.
1. In **condizioni**selezionare **app client**:
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **client di Exchange ActiveSync** e deselezionare tutti gli altri elementi.
1. In **Controlli di accesso** > **Concedi** selezionare **Concedi accesso**, **Richiedi criteri di protezione dell'app** e quindi **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

Vedere l'articolo [Come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies) per la procedura di creazione di criteri di protezione delle app per Android e iOS. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 2: Le app browser richiedono app approvate con criteri di protezione delle app

In questo scenario, Contoso ha deciso che tutti gli accessi Web Browsing per dispositivi mobili alle risorse Microsoft 365 devono usare un'app client approvata, ad esempio Edge per iOS e Android, protetta da un criterio di protezione delle app prima di ricevere l'accesso. Tutti gli utenti accedono già con credenziali di Azure AD e hanno licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili, le organizzazioni devono completare la procedura seguente.

**Passaggio 1: configurare un criterio di accesso condizionale Azure AD per Microsoft 365**

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti** o gli **Utenti e gruppi** ai quale si vuole applicare questi criteri. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Office 365 (anteprima)** .
1. In **Condizioni** selezionare **Piattaforme del dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includere **Android** e **iOS**.
1. In **condizioni**selezionare **app client**.
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **browser** e deselezionare tutti gli altri elementi.
1. In **Controlli di accesso** > **Concedi** selezionare le opzioni seguenti:
   - **Richiedi app client approvata**
   - **Richiedi criteri di protezione delle app (anteprima)**
   - **Richiedi tutti i controlli selezionati**
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: configurare criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

Vedere l'articolo [Come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies) per la procedura di creazione di criteri di protezione delle app per Android e iOS. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 3: Exchange Online e SharePoint richiedono un'app client approvata e i criteri di protezione delle app

In questo scenario, Contoso ha deciso che gli utenti possono accedere solo ai dati di posta elettronica e di SharePoint nei dispositivi mobili, a condizione che usino un'app client approvata come Outlook Mobile protetta da criteri di protezione delle app prima di ottenere l'accesso. Tutti gli utenti accedono già con credenziali di Azure AD e hanno licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili e nei client Exchange ActiveSync, le organizzazioni devono completare la procedura seguente.

**Passaggio 1: criteri per i client di autenticazione moderni basati su Android e iOS che richiedono l'uso di un'app client approvata e dei criteri di protezione delle app per l'accesso a Exchange Online e SharePoint.**

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti** o gli **Utenti e gruppi** ai quale si vuole applicare questi criteri. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**.
1. In **Condizioni** selezionare **Piattaforme del dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includere **Android** e **iOS**.
1. In **condizioni**selezionare **app client**.
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **app per dispositivi mobili e client desktop** e deselezionare tutte le altre operazioni.
1. In **Controlli di accesso** > **Concedi** selezionare le opzioni seguenti:
   - **Richiedi app client approvata**
   - **Richiedi criteri di protezione delle app (anteprima)**
   - **Richiedi uno dei controlli selezionati**
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: criteri per i client di Exchange ActiveSync che richiedono l'uso di un'app client approvata.**

1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti** o gli **Utenti e gruppi** ai quale si vuole applicare questi criteri. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Office 365 Exchange Online**.
1. In **condizioni**selezionare **app client**:
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **client di Exchange ActiveSync** e deselezionare tutti gli altri elementi.
1. In **Controlli di accesso** > **Concedi** selezionare **Concedi accesso**, **Richiedi criteri di protezione dell'app** e quindi **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare criteri di protezione delle app di Intune per le applicazioni client iOS e Android.**

Vedere l'articolo [Come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies) per la procedura di creazione di criteri di protezione delle app per Android e iOS. 

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sull'accesso condizionale](overview.md)

[Componenti dell'accesso condizionale](concept-conditional-access-policies.md)

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

