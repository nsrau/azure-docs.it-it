---
title: Criteri di protezione delle app con accesso condizionale-Azure Active Directory
description: Informazioni su come richiedere i criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3cbb6afb96ccea32aa78d1f587377e5d67e1a5b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381033"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Richiedi i criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale (anteprima)

I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. È importante assicurarsi che i dipendenti siano produttivi e al contempo evitare la perdita di dati. Con l'accesso condizionale Azure Active Directory (Azure AD), è possibile proteggere i dati aziendali limitando l'accesso alle app cloud. Usare prima le app client con i criteri di protezione delle app.

Questo articolo illustra come configurare i criteri di accesso condizionale che possono richiedere un criterio di protezione delle app prima che l'accesso venga concesso ai dati.

## <a name="overview"></a>Overview

Con [l'accesso condizionale Azure ad](overview.md), è possibile ottimizzare il modo in cui gli utenti autorizzati possono accedere alle risorse. Si può, ad esempio, fare in modo che solo i dispositivi attendibili accedano alle app cloud.

È possibile usare i [criteri di protezione app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali. I criteri di protezione delle app di Intune non richiedono una soluzione di gestione dei dispositivi mobili (MDM). È possibile proteggere i dati aziendali con o senza la registrazione di dispositivi in una soluzione di gestione dei dispositivi.

Azure Active Directory accesso condizionale limita l'accesso alle app cloud alle applicazioni client che Intune ha segnalato per Azure AD come la ricezione di un criterio di protezione delle app. Ad esempio, è possibile limitare l'accesso a Exchange Online all'app Outlook con i criteri di protezione delle app di Intune.

Nella terminologia di accesso condizionale, queste app client sono note come criteri protetti con i *criteri di protezione delle app*.  

![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)

Per un elenco di app client protette da criteri, vedere [requisito dei criteri di protezione delle app](technical-reference.md#approved-client-app-requirement).

È possibile combinare i criteri di accesso condizionale basato sulla protezione delle app con altri criteri, ad esempio i [criteri di accesso condizionale basati su dispositivo](require-managed-devices.md). In questo modo, è possibile fornire flessibilità per la protezione dei dati sia per i dispositivi personali che per quelli aziendali.

> [!NOTE]
> I criteri di protezione delle app con accesso condizionale non possono essere applicati agli utenti B2B perché l'organizzazione che invia l'invito non ha visibilità sull'organizzazione principale dell'utente B2B.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Vantaggi del requisito di accesso condizionale basato sulla protezione delle app

Analogamente alla conformità segnalata da Intune per iOS e Android per un dispositivo gestito, Intune ora segnala a Azure AD se viene applicato un criterio di protezione delle app. L'accesso condizionale può usare questo criterio come controllo di accesso. I nuovi criteri di accesso condizionale, i criteri di protezione delle app, aumentano la sicurezza. Protegge da errori amministrativi, ad esempio:

- Utenti che non dispongono di una licenza di Intune.
- Utenti che non possono ricevere i criteri di protezione delle app di Intune.
- App dei criteri di protezione delle app di Intune che non sono configurate per ricevere un criterio.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente abbia familiarità con gli argomento seguenti:

- Riferimento tecnico per i [requisiti dei criteri di protezione delle app](technical-reference.md#app-protection-policy-requirement) .
- La guida tecnica per il [requisito delle app client approvate](technical-reference.md#approved-client-app-requirement).
- I concetti di base dell' [accesso condizionale in Azure Active Directory](overview.md).
- Come [configurare un criterio di accesso condizionale](app-based-mfa.md).

## <a name="prerequisites"></a>prerequisiti

Per creare un criterio di accesso condizionale basato sulla protezione delle app, è necessario:

- Avere un Enterprise Mobility + Security o una sottoscrizione Azure Active Directory Premium + Intune.
- Verificare che gli utenti dispongano della licenza per Enterprise Mobility + Security o Azure AD + Intune.
- Assicurarsi che l'app client sia configurata in Intune per ricevere i criteri di protezione delle app.
- Assicurarsi che gli utenti siano configurati in Intune per ricevere i criteri di protezione delle app di Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Criteri basati sulla protezione delle app per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale basato sulla protezione delle app per l'accesso a Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Configura la posta elettronica usando un'applicazione di posta elettronica nativa in iOS o Android per connettersi a Exchange.
- Riceve un messaggio di posta elettronica che indica che l'accesso è disponibile solo tramite l'app Outlook.
- Scarica l'applicazione con il collegamento.
- Apre l'applicazione Outlook e accede con Azure AD credenziali.
- Viene richiesto di installare l' **app Microsoft Authenticator** o l' **portale aziendale Intune** per continuare.
- Installa l'applicazione e torna all'app Outlook per continuare.
- Viene richiesto di registrare un dispositivo.
- Può ricevere i criteri di protezione delle app di Intune.
- Può accedere alla posta elettronica.

Tutti i criteri di protezione delle app di Intune devono trovarsi nell'applicazione per accedere ai dati aziendali. I criteri potrebbero richiedere all'utente di riavviare l'applicazione o di usare un PIN aggiuntivo. Questa situazione si verifica se i criteri sono configurati per l'applicazione e la piattaforma.

### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un Azure AD criteri di accesso condizionale per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/01.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**.

   ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

1. In **condizioni**configurare le **piattaforme del dispositivo** e le **app client (anteprima)** :
   1. In **piattaforme dispositivo**selezionare **Android** e **iOS**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

   1. In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di autenticazione moderna**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

1. In **controlli di accesso**selezionare **Richiedi criteri di protezione delle app (anteprima)** .

   ![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)

**Passaggio 2: configurare un Azure AD criteri di accesso condizionale per Exchange Online con ActiveSync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**.

   ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

1. In **condizioni**configurare le **app client (anteprima)** . 

   1. In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di Exchange ActiveSync**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

   1. In **controlli di accesso**selezionare **Richiedi criteri di protezione delle app (anteprima)** .

      ![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Criteri per i dispositivi conformi o basati sulla protezione delle app per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale basato sulla protezione delle app o sul dispositivo conforme per l'accesso a Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che:
 
- Un utente è già registrato, con o senza dispositivi aziendali.
- Gli utenti che non sono iscritti e registrati con Azure AD usando un'applicazione protetta da app devono registrare un dispositivo per accedere alle risorse.
- Gli utenti iscritti che usano l'applicazione protetta da app non devono ripetere la registrazione del dispositivo.
- Se non è registrato, l'utente può ricevere i criteri di protezione delle app di Intune.
- Se non è registrato, l'utente può accedere alla posta elettronica con Outlook e i criteri di protezione delle app di Intune.
- L'utente può accedere alla posta elettronica con Outlook se il dispositivo è registrato.

### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un Azure AD criteri di accesso condizionale per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/62.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**. 

   ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

1. In **condizioni**configurare **piattaforme del dispositivo** e **app client (anteprima)** . 
 
   1. In **piattaforme dispositivo**selezionare **Android** e **iOS**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

   1. In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di autenticazione moderna**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. In **controlli di accesso**selezionare le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi uno dei controlli selezionati**   
 
      ![Accesso condizionale](./media/app-protection-based-conditional-access/11.png)

**Passaggio 2: configurare un Azure AD criteri di accesso condizionale per Exchange Online con ActiveSync**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**. 

   ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

1. In **condizioni**configurare le **app client (anteprima)** . 

   In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di Exchange ActiveSync**.

   ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

1. In **controlli di accesso**selezionare le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi uno dei controlli selezionati**

      ![Accesso condizionale](./media/app-protection-based-conditional-access/11.png)

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Criteri dei dispositivi conformi e basati sulla protezione delle app per Exchange Online

Questo scenario è costituito da criteri di accesso condizionale basato sulla protezione delle app e conformi per l'accesso a Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:
 
- Configura la posta elettronica usando un'applicazione di posta elettronica nativa in iOS o Android per connettersi a Exchange.
- Riceve un messaggio di posta elettronica che indica che l'accesso richiede che il dispositivo sia registrato.
- Scarica Portale aziendale Intune e accede al portale.
- Verifica la posta elettronica e viene chiesto di usare l'app Outlook.
- Scarica l'app Outlook.
- Apre l'app Outlook e immette le credenziali usate nella registrazione.
- Può ricevere i criteri di protezione delle app di Intune.
- Può accedere alla posta elettronica con Outlook e i criteri di protezione delle app di Intune.

Tutti i criteri di protezione delle app di Intune vengono attivati prima che venga concesso l'accesso ai dati aziendali. I criteri potrebbero richiedere all'utente di riavviare l'applicazione o di usare un PIN aggiuntivo. Questa situazione si verifica se i criteri sono configurati per l'applicazione e la piattaforma.

### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un Azure AD criteri di accesso condizionale per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/01.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**. 

   ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

1. In **condizioni**configurare **piattaforme del dispositivo** e **app client (anteprima)** . 
   1. In **piattaforme dispositivo**selezionare **Android** e **iOS**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

   1. In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di autenticazione moderna**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

1. In **controlli di accesso**selezionare le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi tutti i controlli selezionati**   
 
      ![Accesso condizionale](./media/app-protection-based-conditional-access/13.png)

**Passaggio 2: configurare un Azure AD criteri di accesso condizionale per Exchange Online con ActiveSync**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**. 

   ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

1. In **condizioni**configurare le **app client (anteprima)** . 

   In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di Exchange ActiveSync**.

   ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

1. In **controlli di accesso**selezionare le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi tutti i controlli selezionati**   
 
      ![Accesso condizionale](./media/app-protection-based-conditional-access/13.png)

**Passaggio 3: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Criteri basati sulla protezione delle app o basati su app per Exchange Online e SharePoint Online

Questo scenario è costituito da criteri app basati sulla protezione delle app o approvati per l'accesso a Exchange Online e SharePoint Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Configura le applicazioni client che si trovano nell'elenco di app che supportano il requisito dei criteri di protezione delle app o il requisito delle app approvate.  
- Usa le applicazioni client che soddisfano i requisiti dei criteri di protezione delle app e possono ricevere i criteri di protezione delle app di Intune.
- USA applicazioni client che soddisfano i requisiti dei criteri delle app approvate che supportano i criteri di protezione delle app di Intune.
- Apre l'applicazione per accedere alla posta elettronica o ai documenti.
- Apre l'applicazione Outlook e accede con Azure AD credenziali.
- Viene richiesto di installare Microsoft Authenticator per iOS usare o Portale aziendale Intune per l'uso di Android se non sono già installati.
- Installa l'applicazione e può tornare all'app Outlook per continuare.
- Viene richiesto di registrare un dispositivo.
- Può ricevere i criteri di protezione delle app di Intune.
- Può accedere alla posta elettronica con Outlook e i criteri di protezione delle app di Intune.
- Può accedere a siti e documenti con un'app non in conformità ai requisiti dei criteri di protezione delle app, ma è elencato nel requisito dell'app approvata.

Tutti i criteri di protezione delle app di Intune sono necessari prima di concedere l'accesso ai dati aziendali. I criteri potrebbero richiedere all'utente di riavviare l'applicazione o di usare un PIN aggiuntivo. Questa situazione si verifica se i criteri sono configurati per l'applicazione e la piattaforma.

**Osservazioni**

- È possibile usare questo scenario se si vogliono supportare i criteri di accesso condizionale basato su app e sulla protezione delle app.
- In questo criterio *o* , le app con un requisito dei criteri di protezione delle app vengono valutate per l'accesso prima del requisito delle app client approvate.

### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un Azure AD criteri di accesso condizionale per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/62.png)

1. Immettere il nome dei criteri di accesso condizionale.
1. In **assegnazioni**, in **utenti e gruppi**, selezionare almeno un utente o un gruppo per ogni criterio di accesso condizionale.
1. In **app Cloud**selezionare **Office 365 Exchange Online**. 

   ![Accesso condizionale](./media/app-protection-based-conditional-access/02.png)

1. In **condizioni**configurare **piattaforme del dispositivo** e **app client (anteprima)** . 
   1. In **piattaforme dispositivo**selezionare **Android** e **iOS**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

   1. In **app client (anteprima)** selezionare **app per dispositivi mobili e client desktop** e **client di autenticazione moderna**.

      ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

1. In **controlli di accesso**selezionare le opzioni seguenti:
   - **Richiedi app client approvata**
   - **Richiedi i criteri di protezione delle app (anteprima)**
   - **Richiedi uno dei controlli selezionati**
 
      ![Accesso condizionale](./media/app-protection-based-conditional-access/12.png)

**Passaggio 2: configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
