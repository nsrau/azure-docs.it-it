---
title: App client approvate con accesso condizionale - Azure Active Directory
description: Informazioni su come richiedere app client approvate per l'accesso alle app cloud con accesso condizionale in Azure Active Directory.Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
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
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480896"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procedura: Richiedere app client approvate per l'accesso alle app cloud con accesso condizionaleHow to: Require approved client apps for cloud app access with Conditional Access

Le persone usano regolarmente i loro dispositivi mobili sia per le attività personali che per le attività lavorative. Pur assicurandosi che il personale possa essere produttivo, le organizzazioni desiderano anche evitare la perdita di dati da applicazioni potenzialmente non sicure. Con l'accesso condizionale, le organizzazioni possono limitare l'accesso alle app client approvate (con funzionalità di autenticazione moderna).

In questo articolo vengono presentate due scenari per configurare i criteri di accesso condizionale per risorse come Office 365, Exchange Online e SharePoint Online.

- [Scenario 1: le app di Office 365 richiedono un'app client approvata](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

Nell'accesso condizionale questa funzionalità è nota come richiesta di un'app client approvata. Per un elenco di app client approvate, vedere [Requisito per le app client approvate](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Per richiedere app client approvate per dispositivi iOS e Android, questi dispositivi devono prima registrarsi in Azure AD.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Scenario 1: le app di Office 365 richiedono un'app client approvata

In questo scenario, Contoso ha deciso che gli utenti che utilizzano dispositivi mobili possono accedere a tutti i servizi di Office 365, purché utilizzino app client approvate, come Outlook mobile, OneDrive e Microsoft Teams. Tutti gli utenti accedono già con le credenziali di Azure AD e dispongono di licenze assegnate a loro che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Le organizzazioni devono completare i tre passaggi seguenti per richiedere l'utilizzo di un'app client approvata nei dispositivi mobili.

**Passaggio 1: Criteri per i client di autenticazione moderna basati su Android e iOS che richiedono l'utilizzo di un'applicazione client approvata per l'accesso a Exchange Online.**

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
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi app client **approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online con ActiveSync (EAS)**

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
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi app client **approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android.Step 3: Configure Intune app protection policy for iOS and Android client applications.**

Per informazioni sulla procedura per la creazione di criteri di protezione delle app per la creazione di criteri di protezione delle app per Android e iOS, vedere l'articolo [Come creare e assegnare criteri](/intune/apps/app-protection-policies)di protezione delle app. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata

In questo scenario, Contoso ha deciso che gli utenti possono accedere solo alla posta elettronica e ai dati di SharePoint nei dispositivi mobili, purché utilizzino un'app client approvata come Outlook Mobile. Tutti gli utenti accedono già con le credenziali di Azure AD e dispongono di licenze assegnate a loro che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Le organizzazioni devono completare i tre passaggi seguenti per richiedere l'utilizzo di un'app client approvata nei dispositivi mobili e nei client Exchange ActiveSync.

**Passaggio 1: Criteri per i client di autenticazione moderna basati su Android e iOS che richiedono l'utilizzo di un'applicazione client approvata per l'accesso a Exchange Online e SharePoint Online.**

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
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi app client **approvata**e selezionare **Seleziona**.
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
1. In **Controlli di** > accesso**Concedi**, selezionare **Concedi accesso**, Richiedi app client **approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android.Step 3: Configure Intune app protection policy for iOS and Android client applications.**

Per informazioni sulla procedura per la creazione di criteri di protezione delle app per la creazione di criteri di protezione delle app per Android e iOS, vedere l'articolo [Come creare e assegnare criteri](/intune/apps/app-protection-policies)di protezione delle app. 

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è l'accesso condizionale?](overview.md)

[Componenti di accesso condizionaleConditional access components](concept-conditional-access-policies.md)

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)
