---
title: Criteri di protezione delle app con accesso condizionale-Azure Active Directory
description: Informazioni su come richiedere i criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale in Azure Active Directory.
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
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080069"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Procedura: richiedere i criteri di protezione delle app e un'app client approvata per l'accesso alle app cloud con accesso condizionale

I dispositivi mobili vengono usati regolarmente per le attività personali e di lavoro. Garantendo al tempo stesso che il personale possa essere produttivo, le organizzazioni vogliono anche impedire la perdita di dati da applicazioni potenzialmente non sicure. Con l'accesso condizionale, le organizzazioni possono limitare l'accesso alle app client approvate che supportano l'autenticazione moderna con i criteri di protezione delle app di Intune applicati.

Questo articolo presenta due scenari per configurare i criteri di accesso condizionale per risorse quali Office 365, Exchange Online e SharePoint Online.

- [Scenario 1: le app di Office 365 richiedono app approvate con i criteri di protezione delle app](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata e i criteri di protezione delle app](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

Nell'accesso condizionale queste app client sono note come protette con i criteri di protezione delle app. Altre informazioni sui criteri di protezione delle app sono disponibili nell'articolo [Panoramica dei criteri di protezione delle app](/intune/apps/app-protection-policy)

Per un elenco delle app client idonee, vedere [requisito dei criteri di protezione delle app](concept-conditional-access-grant.md).

> [!NOTE]
>    La clausola OR viene usata all'interno del criterio per consentire agli utenti di usare le app che supportano i **criteri di protezione delle app require** o **Require client approvati** . Per altre informazioni sulle app che supportano il controllo Richiedi concessione dei **criteri di protezione delle app** , vedere Requisiti per i criteri di protezione delle [app](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: le app di Office 365 richiedono app approvate con i criteri di protezione delle app

In questo scenario, Contoso ha deciso che tutti gli accessi mobili alle risorse di Office 365 devono usare le app client approvate, ad esempio Outlook Mobile, OneDrive e i team Microsoft protetti da un criterio di protezione delle app prima di ricevere l'accesso. Tutti gli utenti hanno già accesso con Azure AD credenziali e dispongono di licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili, le organizzazioni devono completare i passaggi seguenti.

**Passaggio 1: configurare un criterio di accesso condizionale Azure AD per Office 365**

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
1. In **controlli di accesso** > **concedere**Selezionare le opzioni seguenti:
   - **Richiedi app client approvata**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi uno dei controlli selezionati**
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 2: configurare un Azure AD criteri di accesso condizionale per Exchange Online con ActiveSync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

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
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi criteri di protezione delle app**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

Vedere l'articolo [come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies)per i passaggi per la creazione di criteri di protezione delle app per Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 2: Exchange Online e SharePoint Online richiedono un'app client approvata e i criteri di protezione delle app

In questo scenario, Contoso ha deciso che gli utenti possono accedere solo ai dati di posta elettronica e SharePoint nei dispositivi mobili, purché usino un'app client approvata come Outlook Mobile protetto da un criterio di protezione delle app prima di ricevere l'accesso. Tutti gli utenti hanno già accesso con Azure AD credenziali e dispongono di licenze assegnate che includono Azure AD Premium P1 o P2 e Microsoft Intune.

Per richiedere l'uso di un'app client approvata nei dispositivi mobili e nei client Exchange ActiveSync, le organizzazioni devono completare i tre passaggi seguenti.

**Passaggio 1: criteri per i client di autenticazione moderni basati su Android e iOS che richiedono l'uso di un'app client approvata e dei criteri di protezione delle app per l'accesso a Exchange Online e SharePoint Online.**

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
1. In **controlli di accesso** > **concedere**Selezionare le opzioni seguenti:
   - **Richiedi app client approvata**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi uno dei controlli selezionati**
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
1. In **controlli di accesso** > **concedere**Selezionare **Concedi accesso**, **Richiedi criteri di protezione delle app**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare i criteri.

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android.**

Vedere l'articolo [come creare e assegnare criteri di protezione delle app](/intune/apps/app-protection-policies)per i passaggi per la creazione di criteri di protezione delle app per Android e iOS. 

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è l'accesso condizionale?](overview.md)

[Componenti di accesso condizionale](concept-conditional-access-policies.md)

[Criteri di accesso condizionale comuni](concept-conditional-access-policy-common.md)

