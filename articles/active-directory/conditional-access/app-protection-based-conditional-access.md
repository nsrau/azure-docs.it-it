---
title: Richiedono criteri di protezione delle app per accedere all'app cloud con accesso condizionale in Azure Active Directory | Microsoft Docs
description: Informazioni su come richiedere i criteri di protezione delle app per accedere all'app cloud con accesso condizionale in Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496931"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Richiedono criteri di protezione delle app per accedere all'app cloud con l'accesso condizionale (anteprima)

I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. È importante assicurarsi che i dipendenti siano produttivi e al contempo evitare la perdita di dati. Con l'accesso condizionale di Azure Active Directory (Azure AD), è possibile proteggere i dati aziendali limitando l'accesso alle App cloud. Prima di tutto, usare le app client con un criterio di protezione delle app.

Questo articolo illustra come configurare i criteri di accesso condizionale che possono richiedere un criterio di protezione delle app prima di concessa l'accesso ai dati.

## <a name="overview"></a>Panoramica

Con l'[accesso condizionale di Azure AD](overview.md) è possibile regolare la modalità di accesso alle risorse da parte degli utenti autorizzati. Si può, ad esempio, fare in modo che solo i dispositivi attendibili accedano alle app cloud.

È possibile usare i [criteri di protezione app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali. I criteri di protezione app di Intune non richiedono una soluzione di mobile device management (MDM). È possibile proteggere i dati aziendali con o senza registrazione dei dispositivi in una soluzione di gestione di dispositivi.

Accesso condizionale di Azure Active Directory limita l'accesso alle App cloud alle applicazioni client che Intune ha segnalato ad Azure AD come la ricezione di un criterio di protezione delle app. Ad esempio, è possibile limitare l'accesso a Exchange Online per l'app Outlook con criteri di protezione app di Intune.

Nella terminologia di accesso condizionale, queste App client sono noti come criteri protetto con un *criteri di protezione delle app*.  

![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)

Per un elenco di App protette da criteri client, vedere [requisito dei criteri di protezione App](technical-reference.md#approved-client-app-requirement).

È possibile combinare criteri di accesso condizionale basato su app-protezione ad altri criteri, ad esempio [criteri di accesso condizionale basato su dispositivo](require-managed-devices.md). In questo modo, è possibile fornire flessibilità nella protezione dei dati per i dispositivi personali e aziendali.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Vantaggi del requisito di accesso condizionale basato su protezione delle app

Simile alla conformità che viene segnalata da Intune per iOS e Android per i dispositivi gestiti, a questo punto viene applicata report in Azure AD, se un criterio di protezione delle app di Intune. Accesso condizionale è possibile usare questo criterio come un controllo di accesso. Il nuovo criterio di accesso condizionale, i criteri di protezione delle app, aumenta la protezione. Protegge da errori di amministrazione, ad esempio:

- Utenti che non dispongono di una licenza di Intune.
- Utenti che non possono ricevere criteri di protezione app di Intune.
- Intune app protection App criteri che non sono configurate per la ricezione di un criterio.


## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente abbia familiarità con gli argomento seguenti:

- Il [requisito dei criteri di protezione app](technical-reference.md#app-protection-policy-requirement) tecnica.
- La guida tecnica per il [requisito delle app client approvate](technical-reference.md#approved-client-app-requirement).
- I concetti di base relativi all'[accesso condizionale in Azure Active Directory](overview.md).
- La procedura per [configurare i criteri di accesso condizionale](app-based-mfa.md).


## <a name="prerequisites"></a>Prerequisiti

Per creare un criterio di accesso condizionale basato su protezione delle app, è necessario:

- Disporre di Enterprise Mobility + Security o una sottoscrizione di Azure Active Directory premium e Intune.
- Assicurarsi che gli utenti vengono concessi in licenza per Enterprise Mobility + Security o Azure AD e Intune.
- Assicurarsi che l'app client è configurato in Intune per ricevere un criterio di protezione delle app.
- Verificare che gli utenti siano configurati in Intune per ricevere criteri di protezione app di Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Criteri basati su protezione di App per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale basato su protezione app per l'accesso a Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Configura indirizzo di posta elettronica usando un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange.
- Riceve un messaggio di posta elettronica che indica che l'accesso è disponibile solo tramite l'app Outlook.
- Scarica l'applicazione con il collegamento.
- Apre l'applicazione di Outlook e accede con credenziali di Azure AD.
- Viene richiesto di installare Microsoft Authenticator per l'uso di iOS o il portale aziendale di Intune per Android usano continuare.
- Installa l'applicazione e restituisce all'app Outlook per continuare.
- Viene richiesto di registrare un dispositivo.
- Può ricevere criteri di protezione app di Intune.
- Può accedere alla posta elettronica.

Eventuali criteri di protezione app di Intune devono essere relativo all'applicazione per accedere ai dati aziendali. I criteri potrebbero richiedere all'utente di riavviare l'applicazione o usare un PIN aggiuntivo. Ciò avviene se i criteri sono configurati per l'applicazione e piattaforma.

### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/01.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.

3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. Nelle **condizioni**, configurare **piattaforme del dispositivo** e **Client App (anteprima)**:

    a. Nelle **piattaforme del dispositivo**, selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client con autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Sotto **controlli di accesso**, selezionare **richiedono criteri di protezione delle app (anteprima)**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)
 

**Passaggio 2: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online con ActiveSync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.


3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. Nelle **condizioni**, configurare **App Client (anteprima)**. 

    a. Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client Exchange ActiveSync**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

    b. Sotto **controlli di accesso**, selezionare **richiedono criteri di protezione delle app (anteprima)**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)


**Passaggio 3: Configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere App e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Criteri di dispositivi conformi o basato su protezione delle App per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale di dispositivi conformi o basato su protezione app per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che:
 
- Un utente è già registrato, con o senza dispositivi aziendali.
- Gli utenti che non sono registrati e registrati con Azure AD usando un'app protetta dell'applicazione è necessario registrare un dispositivo per accedere alle risorse.
- Gli utenti registrati che usano l'applicazione app protetta non sono necessario registrare nuovamente il dispositivo.
- L'utente può ricevere criteri di protezione app di Intune se non è registrato.
- L'utente può accedere alla posta elettronica con un criterio di protezione app di Intune e Outlook se non è registrato.
- Se il dispositivo è registrato, l'utente può accedere tramite posta elettronica con Outlook.


### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/62.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.

3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**. 

     ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. Nelle **condizioni**, configurare **piattaforme del dispositivo** e **Client App (anteprima)**. 
 
    a. Nelle **piattaforme del dispositivo**, selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client con autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Sotto **controlli di accesso**, selezionare le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi uno dei controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/11.png)



**Passaggio 2: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online con ActiveSync**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.

3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**. 

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. Nelle **condizioni**, configurare **App Client (anteprima)**. 

    Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client Exchange ActiveSync**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

5. Sotto **controlli di accesso**, selezionare le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi uno dei controlli selezionati**

     ![Accesso condizionale](./media/app-protection-based-conditional-access/11.png)



**Passaggio 3: Configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere App e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Criteri di dispositivi conformi e basato su protezione delle App per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale di dispositivi conformi e basato su app-protezione per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:
 
-   Configura indirizzo di posta elettronica usando un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange.
-   Riceve un messaggio di posta elettronica che indica che l'accesso richiede che il dispositivo sia registrato.
-   Scarica portale aziendale di Intune e accede al portale.
-   Verifica di posta elettronica e viene richiesto di usare l'app Outlook.
-   Scarica l'app Outlook.
-   Apre l'app Outlook e immetta le credenziali usate nell'ambito della registrazione.
-   Può ricevere criteri di protezione app di Intune.
-   Può accedere alla posta elettronica con Outlook e criteri di protezione app di Intune.

Eventuali criteri di protezione app di Intune vengono attivati prima di concessa l'accesso ai dati aziendali. I criteri potrebbero richiedere all'utente di riavviare l'applicazione o usare un PIN aggiuntivo. Ciò avviene se i criteri sono configurati per l'applicazione e piattaforma.


### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/01.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.

3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**. 

     ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. Nelle **condizioni**, configurare **piattaforme del dispositivo** e **Client App (anteprima)**. 
 
    a. Nelle **piattaforme del dispositivo**, selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client con autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Sotto **controlli di accesso**, selezionare le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi tutti i controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/13.png)



**Passaggio 2: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online con ActiveSync**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.

3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**. 

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. Nelle **condizioni**, configurare **App Client (anteprima)**. 

    Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client Exchange ActiveSync**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

5. Sotto **controlli di accesso**, selezionare le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi tutti i controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/13.png)




**Passaggio 3: Configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere App e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Criteri basato su app o la protezione delle App per Exchange Online e SharePoint Online

Questo scenario è costituito da un criterio di App approvate o basata su protezione app per l'accesso a Exchange Online e SharePoint Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Consente di configurare le applicazioni client che si trovano nell'elenco di App che supportano il requisito dei criteri di protezione app o il requisito di App approvate.  
- Usa le applicazioni client che soddisfano il requisito dei criteri di protezione app e possono ricevere criteri di protezione app di Intune.
- Usa le applicazioni client che soddisfano il requisito dei criteri per le app approvate supporta criteri di protezione app di Intune.
- Apre l'applicazione di accedere a posta elettronica o documenti.
- Apre l'applicazione di Outlook e accede con credenziali di Azure AD.
- Viene richiesto di installare Microsoft Authenticator per l'uso di iOS o App portale aziendale Intune per Android usano se essi non sono già installati.
- Installa l'applicazione e possa tornare all'app Outlook per continuare.
- Viene richiesto di registrare un dispositivo.
- Può ricevere criteri di protezione app di Intune.
- Può accedere alla posta elettronica con Outlook e criteri di protezione app di Intune.
- Può accedere a siti e documenti con un'app non in al requisito dei criteri di protezione app ma elencate nel requisito app approvata.

Eventuali criteri di protezione app di Intune sono necessari prima di concessa l'accesso ai dati aziendali. I criteri potrebbero richiedere all'utente di riavviare l'applicazione o usare un PIN aggiuntivo. Ciò avviene se i criteri sono configurati per l'applicazione e piattaforma.

**Osservazioni**

- Se si desidera supportano entrambi i criteri di accesso condizionale basato su protezione e basato su app, è possibile utilizzare questo scenario.
- In questo *o* criteri, le app con un requisito dei criteri di protezione app vengono valutati per l'accesso prima di usare il requisito per le app client approvate.

### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/62.png)

1. Immettere il nome del criterio di accesso condizionale.

2. Sotto **assegnazioni**, nel **utenti e gruppi**, selezionare almeno un utente o il gruppo per ogni criterio di accesso condizionale.

3. Nelle **le app Cloud**, selezionare **Office 365 Exchange Online**. 

     ![Accesso condizionale](./media/app-protection-based-conditional-access/02.png)

4. Nelle **condizioni**, configurare **piattaforme del dispositivo** e **Client App (anteprima)**. 
 
    a. Nelle **piattaforme del dispositivo**, selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Nelle **App Client (anteprima)**, selezionare **App per dispositivi mobili e client desktop** e **client con autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Sotto **controlli di accesso**, selezionare le opzioni seguenti:

   - **Richiedere app client approvata**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi uno dei controlli selezionati**
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/12.png)


**Passaggio 2: Configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [proteggere App e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 