---
title: Criteri di protezione delle app con accesso condizionale - Azure Active Directory
description: Informazioni su come richiedere criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale in Azure Active Directory.Learn how to require app protection policy for cloud app access with Conditional Access in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9859c884f6a1e22a1ac2bd21106ef51ead23fa41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080069"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Procedura: Richiedere criteri di protezione delle app e un'app client approvata per l'accesso alle app cloud con accesso condizionaleHow to: Require app protection policy and an approved client app for cloud app access with Conditional Access

Le persone usano regolarmente i loro dispositivi mobili sia per le attività personali che per le attività lavorative. Pur assicurandosi che il personale possa essere produttivo, le organizzazioni desiderano anche evitare la perdita di dati da applicazioni potenzialmente non sicure. Con l'accesso condizionale, le organizzazioni possono limitare l'accesso alle app client approvate (con supporto per l'autenticazione moderna) con i criteri di protezione delle app di Intune applicati.

In questo articolo vengono presentate due scenari per configurare i criteri di accesso condizionale per risorse come Office 365, Exchange Online e SharePoint Online.

- [Scenario 1: le app di Office 365 richiedono app approvate con criteri di protezione delle app](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata e criteri di protezione delle app](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

Nell'accesso condizionale queste app client sono note per essere protette con un criterio di protezione delle app. Ulteriori informazioni sui criteri di protezione delle app sono disponibili nell'articolo [Panoramica](/intune/apps/app-protection-policy) dei criteri di protezione delle app

Per un elenco delle app client idonee, vedere Requisiti dei criteri di [protezione delle app](concept-conditional-access-grant.md).

> [!NOTE]
>    La clausola or viene utilizzata all'interno dei criteri per consentire agli utenti di utilizzare app che supportano i **controlli Richiedi protezione app** o Richiedi **concessione app client approvata.** Per altre informazioni su quali app supportano il controllo di concessione dei criteri di **protezione delle app,** vedere Requisiti dei criteri di [protezione delle app](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: le app di Office 365 richiedono app approvate con criteri di protezione delle app

In questo scenario, Contoso ha deciso che tutti gli accessi mobili alle risorse di Office 365 devono utilizzare app client approvate, ad esempio Outlook mobile, OneDrive e Microsoft Teams protetti da un criterio di protezione delle app prima di ricevere l'accesso. Tutti gli utenti accedono già con le credenziali di Azure AD e dispongono di licenze assegnate a loro che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Le organizzazioni devono completare i passaggi seguenti per richiedere l'utilizzo di un'app client approvata nei dispositivi mobili.

**Passaggio 1: Configurare un criterio di accesso condizionale di Azure AD per Office 365Step 1: Configure an Azure AD Conditional Access policy for Office 365**

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti** o gli utenti e i **gruppi** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare **Office 365 (anteprima)**.
1. In **Condizioni**selezionare **Piattaforme dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includi **Android** e **iOS**.
1. In **Condizioni**selezionare **App client (anteprima)**.
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **App per dispositivi mobili e client desktop** e **Client con autenticazione moderna**.
1. In **Controlli di** > accesso**Concedi**selezionare le opzioni seguenti:
   - **Richiedere app client approvata**
   - **Richiedi criteri di protezione delle pp (anteprima)**
   - **Richiedere uno dei controlli selezionati**
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online con ActiveSync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti** o gli utenti e i **gruppi** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare **Office 365 Exchange Online**.
1. In **Condizioni**:
   1. **App client (anteprima)**:
      1. Impostare **Configura** su **Sì**.
      1. Selezionare **App per dispositivi mobili e client desktop** e client Exchange **ActiveSync**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi criteri di protezione **app**e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e AndroidStep 3: Configure Intune app protection policy for iOS and Android client applications**

Per informazioni sulla procedura per la creazione di criteri di protezione delle app per la creazione di criteri di protezione delle app per Android e iOS, vedere l'articolo [Come creare e assegnare criteri](/intune/apps/app-protection-policies)di protezione delle app. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata e criteri di protezione delle app

In questo scenario, Contoso ha deciso che gli utenti possono accedere solo alla posta elettronica e ai dati di SharePoint nei dispositivi mobili, purché utilizzino un'app client approvata come Outlook Mobile protetta da un criterio di protezione delle app prima di ricevere l'accesso. Tutti gli utenti accedono già con le credenziali di Azure AD e dispongono di licenze assegnate a loro che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Le organizzazioni devono completare i tre passaggi seguenti per richiedere l'utilizzo di un'app client approvata nei dispositivi mobili e nei client Exchange ActiveSync.

**Passaggio 1: Criteri per i client di autenticazione moderna basati su Android e iOS che richiedono l'utilizzo di un'app client approvata e dei criteri di protezione delle app quando accedono a Exchange Online e SharePoint Online.**

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti** o gli utenti e i **gruppi** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare **Office 365 Exchange Online** e Office **365 SharePoint Online.**
1. In **Condizioni**selezionare **Piattaforme dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includi **Android** e **iOS**.
1. In **Condizioni**selezionare **App client (anteprima)**.
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **App per dispositivi mobili e client desktop** e **Client con autenticazione moderna**.
1. In **Controlli di** > accesso**Concedi**selezionare le opzioni seguenti:
   - **Richiedere app client approvata**
   - **Richiedi criteri di protezione delle pp (anteprima)**
   - **Richiedere uno dei controlli selezionati**
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: Criteri per i client Exchange ActiveSync che richiedono l'utilizzo di un'app client approvata.**

1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti** o gli utenti e i **gruppi** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Fatto**.
1. In **Includi app o azioni** > **Include**cloud selezionare **Office 365 Exchange Online**.
1. In **Condizioni**:
   1. **App client (anteprima)**:
      1. Impostare **Configura** su **Sì**.
      1. Selezionare **App per dispositivi mobili e client desktop** e client Exchange **ActiveSync**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi criteri di protezione **app**e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android.Step 3: Configure Intune app protection policy for iOS and Android client applications.**

Per informazioni sulla procedura per la creazione di criteri di protezione delle app per la creazione di criteri di protezione delle app per Android e iOS, vedere l'articolo [Come creare e assegnare criteri](/intune/apps/app-protection-policies)di protezione delle app. 

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è l'accesso condizionale?](overview.md)

[Componenti di accesso condizionaleConditional access components](concept-conditional-access-policies.md)

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

