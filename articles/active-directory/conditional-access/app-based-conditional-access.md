---
title: App client approvate con accesso condizionale-Azure Active Directory
description: Informazioni su come richiedere app client approvate per l'accesso alle app cloud con accesso condizionale in Azure Active Directory.
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
ms.openlocfilehash: 3a869f4fa82999192f75f2c114720151bae55680
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298429"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procedura: richiedere app client approvate per l'accesso alle app cloud con accesso condizionale

I dispositivi mobili vengono usati regolarmente per le attività personali e di lavoro. Garantendo al tempo stesso che il personale possa essere produttivo, le organizzazioni vogliono anche impedire la perdita di dati da applicazioni potenzialmente non sicure. Con l'accesso condizionale, le organizzazioni possono limitare l'accesso alle app client approvate che supportano l'autenticazione moderna.

Questo articolo presenta due scenari per configurare i criteri di accesso condizionale per risorse quali Office 365, Exchange Online e SharePoint Online.

- [Scenario 1: le app di Office 365 richiedono un'app client approvata](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

Nell'accesso condizionale questa funzionalità è nota come richiesta di un'app client approvata. Per un elenco di app client approvate, vedere [Requisito per le app client approvate](concept-conditional-access-grant.md#require-approved-client-app).

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Scenario 1: le app di Office 365 richiedono un'app client approvata

In questo scenario, Contoso ha deciso che gli utenti che usano dispositivi mobili possono accedere a tutti i servizi di Office 365, purché usino app client approvate come Outlook Mobile, OneDrive e Microsoft teams. Tutti gli utenti hanno già accesso con Azure AD credenziali e dispongono di licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Le organizzazioni devono completare i tre passaggi seguenti per richiedere l'uso di un'app client approvata nei dispositivi mobili.

**Passaggio 1: criteri per i client di autenticazione moderni basati su Android e iOS che richiedono l'uso di un'applicazione client approvata per l'accesso a Exchange Online.**

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti** o i **gruppi e gli utenti** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **Includi**Selezionare **Office 365 (anteprima)** .
1. In **condizioni**selezionare **piattaforme del dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includere **Android** e **iOS**.
1. In **condizioni**selezionare **app client (anteprima)** .
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **app per dispositivi mobili e client desktop** e **client di autenticazione moderna**.
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: configurare un Azure AD criteri di accesso condizionale per Exchange Online con ActiveSync (EAS)**

1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti** o i **gruppi e gli utenti** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **Includi**selezionare **Office 365 Exchange Online**.
1. In **condizioni**:
   1. **App client (anteprima)** :
      1. Impostare **Configura** su **Sì**.
      1. Selezionare **app per dispositivi mobili e client desktop** e **client di Exchange ActiveSync**.
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android.**

Vedere l'articolo [come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies)per i passaggi per la creazione di criteri di protezione delle app per Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata

In questo scenario, Contoso ha deciso che gli utenti possono accedere solo ai dati di posta elettronica e SharePoint nei dispositivi mobili, purché usino un'app client approvata come Outlook Mobile. Tutti gli utenti hanno già accesso con Azure AD credenziali e dispongono di licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili e nei client Exchange ActiveSync, le organizzazioni devono completare i tre passaggi seguenti.

**Passaggio 1: criteri per i client di autenticazione moderni basati su Android e iOS che richiedono l'uso di un'applicazione client approvata per l'accesso a Exchange Online e SharePoint Online.**

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti** o i **gruppi e gli utenti** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **includere**selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**.
1. In **condizioni**selezionare **piattaforme del dispositivo**.
   1. Impostare **Configura** su **Sì**.
   1. Includere **Android** e **iOS**.
1. In **condizioni**selezionare **app client (anteprima)** .
   1. Impostare **Configura** su **Sì**.
   1. Selezionare **app per dispositivi mobili e client desktop** e **client di autenticazione moderna**.
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: criteri per i client di Exchange ActiveSync che richiedono l'uso di un'app client approvata.**

1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti** o i **gruppi e gli utenti** specifici a cui si desidera applicare questo criterio. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** > **Includi**selezionare **Office 365 Exchange Online**.
1. In **condizioni**:
   1. **App client (anteprima)** :
      1. Impostare **Configura** su **Sì**.
      1. Selezionare **app per dispositivi mobili e client desktop** e **client di Exchange ActiveSync**.
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi app client approvata**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android.**

Vedere l'articolo [come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies)per i passaggi per la creazione di criteri di protezione delle app per Android e iOS. 

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è l'accesso condizionale?](overview.md)

[Componenti di accesso condizionale](concept-conditional-access-policies.md)

[Criteri di accesso condizionale comuni](concept-conditional-access-policy-common.md)
