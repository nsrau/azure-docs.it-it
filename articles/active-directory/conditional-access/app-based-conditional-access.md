---
title: App client approvate con accesso condizionale-Azure Active Directory
description: Informazioni su come richiedere app client approvate per l'accesso alle app cloud con accesso condizionale in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a3cdb851ca00300d995bcf2075252a360242197
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601963"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procedura: richiedere app client approvate per l'accesso alle app cloud con accesso condizionale

Gli utenti usano spesso i dispositivi mobili sia per le attività personali che per quelle di lavoro. Le organizzazioni vogliono garantire che il personale sia produttivo, ma anche impedire la perdita di dati da applicazioni potenzialmente non sicure. Con l'accesso condizionale, le organizzazioni possono limitare l'accesso alle app client approvate che supportano l'autenticazione moderna.

Questo articolo presenta due scenari per configurare i criteri di accesso condizionale per risorse quali Microsoft 365, Exchange Online e SharePoint Online.

- [Scenario 1: Microsoft 365 app richiedono un'app client approvata](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

Nell'accesso condizionale questa funzionalità è nota come richiesta di un'app client approvata. Per un elenco di app client approvate, vedere [Requisito per le app client approvate](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Per richiedere le app client approvate per dispositivi iOS e Android, è necessario che questi dispositivi si registrino prima in Azure AD.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Scenario 1: Microsoft 365 app richiedono un'app client approvata

In questo scenario, Contoso ha deciso che gli utenti che usano dispositivi mobili possono accedere a tutti i servizi di Microsoft 365, purché usino app client approvate, come Outlook Mobile, OneDrive e Microsoft teams. Tutti gli utenti accedono già con credenziali di Azure AD e hanno licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Le organizzazioni devono completare i tre passaggi seguenti per richiedere l'uso di un'app client approvata nei dispositivi mobili.

**Passaggio 1: criteri per i client di autenticazione moderni basati su Android e iOS che richiedono l'uso di un'applicazione client approvata per l'accesso a Exchange Online.**

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
1. In **Condizioni** selezionare **App client (anteprima)** .
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **App per dispositivi mobili e client desktop** e **Client con autenticazione moderna**.
1. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: configurare criteri di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti** o gli **Utenti e gruppi** ai quale si vuole applicare questi criteri. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Office 365 Exchange Online**.
1. In **Condizioni**:
   1. **App client (anteprima)** :
      1. Impostare **Configura** su **Sì**.
      1. Selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.
1. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare criteri di protezione delle app di Intune per le applicazioni client iOS e Android.**

Vedere l'articolo [Come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies) per la procedura di creazione di criteri di protezione delle app per Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata

In questo scenario, Contoso ha deciso che gli utenti possono accedere solo ai dati di posta elettronica e SharePoint nei dispositivi mobili, purché usino un'app client approvata come Outlook Mobile. Tutti gli utenti accedono già con credenziali di Azure AD e hanno licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili e nei client Exchange ActiveSync, le organizzazioni devono completare la procedura seguente.

**Passaggio 1: criteri per i client di autenticazione moderni basati su Android e iOS che richiedono l'uso di un'applicazione client approvata per l'accesso a Exchange Online e SharePoint Online.**

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
1. In **Condizioni** selezionare **App client (anteprima)** .
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **App per dispositivi mobili e client desktop** e **Client con autenticazione moderna**.
1. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
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
1. In **Condizioni**:
   1. **App client (anteprima)** :
      1. Impostare **Configura** su **Sì**.
      1. Selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.
1. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare criteri di protezione delle app di Intune per le applicazioni client iOS e Android.**

Vedere l'articolo [Come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies) per la procedura di creazione di criteri di protezione delle app per Android e iOS. 

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sull'accesso condizionale](overview.md)

[Componenti dell'accesso condizionale](concept-conditional-access-policies.md)

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)
