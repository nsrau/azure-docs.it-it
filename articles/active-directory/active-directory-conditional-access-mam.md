---
title: Accesso condizionale basato su app di Azure Active Directory | Microsoft Docs
description: Informazioni su come funziona l'accesso condizionale basato su app di Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: aaf2da57d8653371ab0b46e47474442aa4be1d65
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Accesso condizionale basato su app di Azure Active Directory  

I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. È importante assicurarsi che i dipendenti siano produttivi e al contempo evitare la perdita di dati. Con l'accesso condizionale basato su app di Azure Active Directory (Azure AD) è possibile fare in modo che l'accesso alle app cloud sia limitato solo alle app client in grado di proteggere i dati aziendali.  

L'argomento descrive come configurare l'accesso condizionale basato su app di Azure AD.

## <a name="overview"></a>Panoramica

Con l'[accesso condizionale di Azure AD](active-directory-conditional-access-azure-portal.md) è possibile regolare la modalità di accesso alle risorse da parte degli utenti autorizzati. Si può, ad esempio, fare in modo che solo i dispositivi attendibili accedano alle app cloud.

È possibile usare i [criteri di protezione app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali. I criteri di protezione app di Intune non richiedono una soluzione di gestione dei dispositivi mobili (MDM), che consente di proteggere i dati aziendali con o senza la registrazione dei dispositivi in una soluzione di gestione di dispositivi.

L'accesso condizionale basato su app di Azure Active Directory consente di fare in modo che l'accesso alle app cloud sia limitato solo alle app client che supportano i criteri di protezione app di Intune. È possibile, ad esempio, limitare l'accesso a Exchange Online all'app Outlook.

Nella terminologia relativa all'accesso condizionale queste app client sono note come **app client approvate**.  


![Accesso condizionale](./media/active-directory-conditional-access-mam/05.png)


Per un elenco di app client approvate, vedere [Requisito per le app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


È possibile combinare i criteri di accesso condizionale basato su app con altri criteri, ad esempio i [criteri di accesso condizionale basato su dispositivi](active-directory-conditional-access-policy-connected-applications.md), per offrire flessibilità nel modo di proteggere i dati sia nei dispositivi personali che in quelli aziendali.

 


##<a name="before-you-begin"></a>Prima di iniziare

Questo argomento presuppone che l'utente abbia familiarità con:

- La guida tecnica per il [requisito delle app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


- I concetti di base relativi all'[accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- La procedura per [configurare i criteri di accesso condizionale](active-directory-conditional-access-azure-portal-get-started.md).

- La [migrazione dei criteri di accesso condizionale](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Prerequisiti

Per creare i criteri di accesso condizionale basato su app, è necessario disporre di una sottoscrizione Premium per Enterprise Mobility + Security o Azure Active Directory e gli utenti devono avere una licenza per EMS o Azure AD. 


## <a name="exchange-online-policy"></a>Criteri per Exchange Online 

Questo scenario è costituito dai criteri di accesso condizionale basato su app per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Configuri la posta elettronica con un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange

- Riceva un messaggio di posta elettronica in cui è indicato che l'accesso è disponibile solo tramite l'app Outlook

- Esegua il download dell'applicazione con il collegamento

- Apra l'applicazione Outlook e acceda con le credenziali di Azure AD

- Riceva un messaggio in cui viene chiesto di installare Authenticator (iOS) o Portale aziendale (Android) per continuare

- Installi l'applicazione e possa tornare all'app Outlook per continuare

- Riceva un messaggio in cui viene chiesto di registrare un dispositivo

- Sia in grado di accedere alla posta elettronica

I criteri di protezione delle app di Intune vengono attivati al momento dell'accesso ai dati aziendali e possono richiedere all'utente di riavviare l'applicazione, usare un PIN aggiuntivo e altro ancora, se configurato per l'applicazione e la piattaforma.

### <a name="configuration"></a>Configurazione 

**Passaggio 1: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/01.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**:

    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/03.png)

    b. Come **App client** selezionare **App per dispositivi mobili e client desktop**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/04.png)

5. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/05.png)
 

**Passaggio 2: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/06.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.


3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client**. 

    a. Come **App client** selezionare **Exchange Active Sync**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/08.png)

    b. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/05.png)


**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**


![Accesso condizionale](./media/active-directory-conditional-access-mam/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="exchange-online-and-sharepoint-online-policy"></a>Criteri per Exchange Online e SharePoint Online

Questo scenario prevede la definizione di criteri di accesso condizionale con gestione delle app mobili per l'accesso a Exchange Online e SharePoint Online con app approvate.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Provi a usare l'app SharePoint per connettersi a siti aziendali e visualizzarli

- Provi ad accedere con le stesse credenziali usate per l'app Outlook

- Non debba ripetere la registrazione e riesca a ottenere l'accesso alle risorse


### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online e SharePoint Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/71.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.


3. **App cloud:** come app cloud è necessario selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/02.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**:

    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/03.png)

    b. Come **App client** selezionare **App per dispositivi mobili e client desktop**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/04.png)

5. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/05.png)




**Passaggio 2: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/06.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. Online 

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client**:

    a. Come **App client** selezionare **Exchange Active Sync**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/08.png)

    b. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/05.png)




**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**


![Accesso condizionale](./media/active-directory-conditional-access-mam/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Criteri di accesso basato su dispositivi conformi o basato su app per Exchange Online e SharePoint Online

Questo scenario è costituito dai criteri di accesso condizionale basato su dispositivi conformi o basato su app per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che:
 
- Alcuni utenti abbiano già registrato il proprio dispositivo (aziendale o meno)

- Gli utenti che non hanno registrato il dispositivo e non hanno eseguito la registrazione ad Azure AD usando un'applicazione protetta tramite app debbano registrare un dispositivo per accedere alle risorse

- Gli utenti che hanno eseguito la registrazione usando l'applicazione protetta tramite app non debbano registrare nuovamente il dispositivo


### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online e SharePoint Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/62.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud:** come app cloud è necessario selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 

     ![Accesso condizionale](./media/active-directory-conditional-access-mam/02.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
 
    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/03.png)

    b. Come **App client** selezionare **App per dispositivi mobili e client desktop**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/04.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

    - **Richiedi che i dispositivi siano contrassegnati come conformi**

    - **Richiedi app client approvata (anteprima)**

    - **Richiedi uno dei controlli selezionati**   
 
    ![Accesso condizionale](./media/active-directory-conditional-access-mam/11.png)



**Passaggio 2: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/61.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client**. 

    Come **App client* selezionare **Exchange Active Sync**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/08.png)

5. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)**.
 
    ![Accesso condizionale](./media/active-directory-conditional-access-mam/11.png)




**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**


![Accesso condizionale](./media/active-directory-conditional-access-mam/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Criteri di accesso basato su dispositivi conformi e basato su app per Exchange Online e SharePoint Online

Questo scenario è costituito dai criteri di accesso condizionale basato su dispositivi conformi e basato su app per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:
 
-   Configuri la posta elettronica con un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange
-   Riceva un messaggio di posta elettronica in cui è indicato che per accedere è necessario che il dispositivo sia registrato
-   Esegua il download del portale aziendale e acceda a tale portale
-   Controlli la posta elettronica e riceva un messaggio in cui viene chiesto di usare l'app Outlook
-   Esegua il download dell'app Outlook
-   Apra l'app Outlook e immetta le credenziali usate per la registrazione
-   Sia in grado di accedere alla posta elettronica

I criteri di protezione delle app di Intune vengono attivati al momento dell'accesso ai dati aziendali e possono richiedere all'utente di riavviare l'applicazione, usare un PIN aggiuntivo e altro ancora, se configurato per l'applicazione e la piattaforma.


### <a name="configuration"></a>Configurazione

**Passaggio 1: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online e SharePoint Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/62.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud:** come app cloud è necessario selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 

     ![Accesso condizionale](./media/active-directory-conditional-access-mam/02.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
 
    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/03.png)

    b. Come **App client** selezionare **App per dispositivi mobili e client desktop**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/04.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

    - **Richiedi che i dispositivi siano contrassegnati come conformi**

    - **Richiedi app client approvata (anteprima)**

    - **Richiedi uno dei controlli selezionati**   
 
    ![Accesso condizionale](./media/active-directory-conditional-access-mam/11.png)



**Passaggio 2: Configurare i criteri di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/active-directory-conditional-access-mam/61.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client**. 

    Come **App client** selezionare **Exchange Active Sync**.

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/08.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

    - **Richiedi che i dispositivi siano contrassegnati come conformi**

    - **Richiedi app client approvata (anteprima)**

    - **Richiedi tutti i controlli selezionati**   
 
    ![Accesso condizionale](./media/active-directory-conditional-access-mam/64.png)




**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**


![Accesso condizionale](./media/active-directory-conditional-access-mam/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).






## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
