---
title: Accesso condizionale con gestione delle app mobili in Azure Active Directory | Microsoft Docs
description: Informazioni su come funziona l'accesso condizionale con gestione delle app mobili in Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: it-it
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Accesso condizionale con gestione delle app mobili in Azure Active Directory  

L'accesso condizionale basato su app di Azure Active Directory (Azure AD) nel portale di Azure, combinato con i criteri di protezione delle app di Intune, consente di limitare l'accesso alle app cloud solo alle app per dispositivi mobili che supportano la protezione delle app di Intune, ad esempio limitare l'accesso a Exchange Online solo all'app Outlook. Questo supporto permette ai dispositivi non registrati per la gestione tramite Intune MDM di proteggere comunque i dati aziendali.   

L'accesso condizionale con gestione delle app mobili può essere combinato con altri criteri, ad esempio i criteri di accesso condizionale basati su dispositivo, per offrire flessibilità nella protezione dei dati per i dispositivi personali e aziendali. 

##<a name="before-you-begin"></a>Prima di iniziare

Questo argomento presuppone che l'utente abbia familiarità con:

- I concetti di base relativi all'[accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- La procedura per [configurare i criteri di accesso condizionale](active-directory-conditional-access-azure-portal-get-started.md).


Può inoltre essere opportuno esaminare le [procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md).  



## <a name="prerequisites"></a>Prerequisiti

1.  Prima di creare criteri di accesso condizionale basati su app, è necessario disporre di una sottoscrizione Premium per Enterprise Mobility + Security o Azure Active Directory e gli utenti devono avere una licenza per EMS o Azure AD. 
2.  Prima di creare nuovi criteri di accesso condizionale con gestione delle app mobili, è necessario esaminare gli scenari e le considerazioni sulla migrazione

## <a name="supported-platforms"></a>Piattaforme supportate

-   iOS

-   Android

## <a name="approved-client-applications"></a>Applicazioni client approvate 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Microsoft Teams

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Criteri per Exchange Online 

Questo scenario prevede la definizione di criteri di accesso condizionale con gestione delle app mobili per l'accesso a Exchange Online con app approvate.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Configuri la posta elettronica con un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange

- Riceva un messaggio di posta elettronica in cui è indicato che l'accesso è disponibile solo tramite l'app Outlook

- Esegua il download dell'applicazione con il collegamento

- Apra l'applicazione Outlook e acceda con le credenziali di Azure AD

- Riceva un messaggio in cui viene chiesto di installare Authenticor (iOS) o Portale aziendale (Android) per continuare

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


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Criteri con gestione di applicazioni mobili o dispositivi conformi per Exchange Online e SharePoint Online

Questo scenario prevede la definizione di criteri di accesso condizionale con gestione delle app per mobili o dispositivi conformi per l'accesso a Exchange Online con app approvate.


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





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Criteri con gestione di applicazioni mobili e dispositivi conformi per Exchange Online e SharePoint Online

Questo scenario prevede la definizione di criteri di accesso condizionale con gestione delle app mobili e dispositivi conformi per l'accesso a Exchange Online con app approvate.

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



## <a name="migration-considerations"></a>Considerazioni sulla migrazione

Se nel portale di Azure classico sono configurati dei criteri, è opportuno eseguirne la migrazione nel portale di Azure per i motivi seguenti:


- Un utente per cui sono configurati criteri nel portale di Azure classico e nel portale di Azure deve soddisfare i requisiti per entrambi i criteri 

- Se non si esegue la migrazione dei criteri esistenti, non si potranno implementare i criteri che concedono l'accesso


## <a name="migration-from-the-azure-classic-portal"></a>Migrazione dal portale di Azure classico

In questo scenario: 

- Nel [portale di Azure classico](https://manage.windowsazure.com) sono configurati:

    - SharePoint Online

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/14.png)

    - Criteri di accesso condizionale basato su dispositivo

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/15.png)

- Si vuole eseguire la configurazione di criteri di accesso condizionale con gestione di applicazioni mobili nel portale di Azure 
 

### <a name="configuration"></a>Configurazione 

- Esaminare i criteri di accesso condizionale basato su dispositivo

- Eseguirne la migrazione nel portale di Azure 

- Aggiungere criteri di accesso condizionale con gestione di applicazioni mobili


## <a name="migrating-from-intune"></a>Migrazione da Intune 

In questo scenario:

- In [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ) sono configurati criteri di accesso condizionale con gestione di applicazioni mobili per Exchange Online o SharePoint Online

    ![Accesso condizionale](./media/active-directory-conditional-access-mam/15.png)

- Si vuole eseguire la migrazione in modo da usare l'accesso condizionale con gestione di applicazioni mobili nel portale di Azure


### <a name="configuration"></a>Configurazione 
 
- Esaminare i criteri di accesso condizionale basato su dispositivo

- Eseguirne la migrazione nel portale di Azure 

- Esaminare i criteri di accesso condizionale con gestione di applicazioni mobili configurati per Exchange Online o SharePoint Online in Intune

- Aggiungere il controllo per **richiedere applicazioni approvate** oltre al controllo basato su dispositivo 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migrazione dal portale di Azure classico e Intune

In questo scenario:

- Sono configurati i criteri seguenti:

    - **Portale di Azure classico:** criteri di accesso condizionale basato su dispositivo 

    - **Intune:** criteri di accesso condizionale con gestione di applicazioni mobili 
    
- Si vuole eseguire la migrazione di entrambi i criteri in modo da usare l'accesso condizionale con gestione di applicazioni mobili nel portale di Azure


### <a name="configuration"></a>Configurazione

- Esaminare i criteri di accesso condizionale basato su dispositivo

- Eseguirne la migrazione nel portale di Azure 

- Esaminare i criteri di accesso condizionale con gestione di applicazioni mobili configurati per Exchange Online o SharePoint Online in Intune

- Aggiungere il controllo per **richiedere applicazioni approvate** oltre a quello basato su dispositivo 




## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
