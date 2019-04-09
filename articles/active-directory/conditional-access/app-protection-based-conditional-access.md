---
title: Come richiedere i criteri di protezione delle app per accedere all'app cloud con accesso condizionale in Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288502"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Procedura: Richiedono criteri di protezione delle app per accedere all'app cloud con l'accesso condizionale (anteprima)

I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. È importante assicurarsi che i dipendenti siano produttivi e al contempo evitare la perdita di dati. Con l'accesso condizionale di Azure Active Directory (Azure AD), è possibile proteggere i dati aziendali limitando l'accesso alle App cloud per le app client che hanno un criterio di protezione delle app prima di tutto.

Questo argomento illustra come configurare i criteri di accesso condizionale che richiedono criteri di protezione delle app prima dell'accesso ai dati.

## <a name="overview"></a>Panoramica

Con l'[accesso condizionale di Azure AD](overview.md) è possibile regolare la modalità di accesso alle risorse da parte degli utenti autorizzati. Si può, ad esempio, fare in modo che solo i dispositivi attendibili accedano alle app cloud.

È possibile usare i [criteri di protezione app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali. I criteri di protezione app di Intune non richiedono una soluzione di gestione dei dispositivi mobili (MDM), che consente di proteggere i dati aziendali con o senza la registrazione dei dispositivi in una soluzione di gestione di dispositivi.

Accesso condizionale di Azure Active Directory limita l'accesso alle App cloud alle applicazioni client che Intune ha segnalato ad Azure AD come la ricezione di un criterio di protezione delle app. Ad esempio, è possibile limitare l'accesso a Exchange Online per l'app Outlook con criteri di protezione app di Intune.

Nella terminologia di accesso condizionale, queste App client sono noti come criteri protetto con un **criteri di protezione delle app**.  

![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)

Per un elenco dei criteri di App client protetti, vedere [requisito dei criteri di protezione app](technical-reference.md#approved-client-app-requirement).

È possibile combinare criteri di accesso condizionale basato su app-protezione ad altri criteri, ad esempio [criteri di accesso condizionale basato su dispositivo](require-managed-devices.md) per offrire flessibilità nella protezione dei dati per i dispositivi personali e aziendali.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Vantaggi del requisito di accesso condizionale basato su protezione delle app

Simile a conformità viene segnalato da Intune per iOS e Android per dispositivo, Intune ora report gestiti ad Azure AD se viene applicato criteri di protezione delle app in modo che l'accesso condizionale può utilizzarlo come un controllo di accesso. Il nuovo criterio di accesso condizionale **criteri di protezione delle App** aumenta la protezione per la protezione da errori di amministrazione, ad esempio:

- utenti che non dispongono di una licenza di Intune
- utenti che non possono ricevere criteri di protezione app di Intune
- Intune app protection app da criteri che non sono state configurate per la ricezione di un criterio


## <a name="before-you-begin"></a>Prima di iniziare

Questo argomento presuppone che l'utente abbia familiarità con:

- Il [requisito dei criteri di protezione app](technical-reference.md#app-protection-policy-requirement) tecnica.

- La guida tecnica per il [requisito delle app client approvate](technical-reference.md#approved-client-app-requirement).

- I concetti di base relativi all'[accesso condizionale in Azure Active Directory](overview.md).

- La procedura per [configurare i criteri di accesso condizionale](app-based-mfa.md).


## <a name="prerequisites"></a>Prerequisiti

Per creare un criterio di accesso condizionale basato su protezione delle app, è necessario
- Dispone di Enterprise Mobility + Security o una sottoscrizione di Azure Active Directory premium e Intune
- Verificare che gli utenti vengono concessi in licenza per EMS o Azure AD e Intune
- Verificare che l'app client è stato configurato in Intune per ricevere criteri di protezione app
- Verificare che gli utenti sono configurati in Intune per ricevere criteri di protezione app di Intune

## <a name="app-protection-based-policy-for-exchange-online"></a>Criteri basati su protezione di App per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale basato su protezione app per l'accesso a Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Configuri la posta elettronica con un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange

- Riceva un messaggio di posta elettronica in cui è indicato che l'accesso è disponibile solo tramite l'app Outlook

- Esegua il download dell'applicazione con il collegamento

- Apra l'applicazione Outlook e acceda con le credenziali di Azure AD

- Riceva un messaggio in cui viene chiesto di installare Authenticator (iOS) o Portale aziendale (Android) per continuare

- Installi l'applicazione e possa tornare all'app Outlook per continuare

- Riceva un messaggio in cui viene chiesto di registrare un dispositivo

- È in grado di ricevere i criteri di protezione app di Intune

- Sia in grado di accedere alla posta elettronica

Eventuali criteri di protezione app di Intune devono essere relativo all'applicazione per accedere ai dati aziendali e possono richiedere all'utente di riavviare l'applicazione, usare un aggiuntive e così via PIN (se configurata per l'applicazione e piattaforma).

### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/01.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**:

    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Come **controlli di accesso**, è necessario avere **richiedono criteri di protezione delle app (anteprima)** selezionato.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)
 

**Passaggio 2: configurare un criterio di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.


3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client (anteprima)**. 

    a. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

    b. Come **controlli di accesso**, è necessario avere **richiedono criteri di protezione delle app (anteprima)** selezionato.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/05.png)


**Passaggio 3: configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Criteri di dispositivi conformi o basato su protezione delle App per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale di dispositivi conformi o basato su protezione app per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che:
 
- Un utente è già registrato (con o senza dispositivi aziendali)

- Gli utenti che non hanno registrato il dispositivo e non hanno eseguito la registrazione ad Azure AD usando un'applicazione protetta tramite app debbano registrare un dispositivo per accedere alle risorse

- Gli utenti che hanno eseguito la registrazione usando l'applicazione protetta tramite app non debbano registrare nuovamente il dispositivo

- Utente può ricevere criteri di protezione app di Intune se non è registrato

- Utente può accedere alla posta elettronica con un criterio di protezione app di Intune e Outlook se non è registrato

- Consente l'accesso tramite posta elettronica con Outlook se il dispositivo è registrato


### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/62.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

     ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
 
    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi uno dei controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/11.png)



**Passaggio 2: configurare un criterio di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client**. 

    Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi uno dei controlli selezionati**   
    ![Accesso condizionale](./media/app-protection-based-conditional-access/11.png)



**Passaggio 3: configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Criteri di dispositivi conformi e basato su protezione delle App per Exchange Online

Questo scenario è costituito da un criterio di accesso condizionale di dispositivi conformi e basato su app-protezione per l'accesso a Exchange Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:
 
-   Configuri la posta elettronica con un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange
-   Riceva un messaggio di posta elettronica in cui è indicato che per accedere è necessario che il dispositivo sia registrato
-   Esegua il download del portale aziendale e acceda a tale portale
-   Controlli la posta elettronica e riceva un messaggio in cui viene chiesto di usare l'app Outlook
-   Esegua il download dell'app Outlook
-   Apra l'app Outlook e immetta le credenziali usate per la registrazione
-   È in grado di ricevere i criteri di protezione app di Intune
-   È in grado di accedere alla posta elettronica con Outlook e criteri di protezione app di Intune

Eventuali criteri di protezione app di Intune vengono attivati prima di accedere ai dati aziendali e possono richiedere all'utente di riavviare l'applicazione, usare un aggiuntive e così via PIN (se configurato per la piattaforma e applicazione)


### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/01.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

     ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
 
    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi tutti i controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/13.png)



**Passaggio 2: configurare un criterio di accesso condizionale di Azure AD per Exchange Online con Active Sync (EAS)**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/06.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

    ![Accesso condizionale](./media/app-protection-based-conditional-access/07.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **App client (anteprima)**. 

    Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/92.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

   - **Richiedere che i dispositivi siano contrassegnati come conformi**

   - **Richiedi app client approvata (anteprima)**

   - **Richiedi tutti i controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/13.png)




**Passaggio 3: configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Criteri basato su app o la protezione delle App per Exchange Online e SharePoint Online

Questo scenario è costituito da un criterio di App approvate o basata su protezione app per l'accesso a Exchange Online e SharePoint Online.


### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Consente di configurare entrambe le applicazioni client che possono essere nell'elenco di App che supportano il requisito dei criteri di protezione app o il requisito di App approvate.  
- Le applicazioni client di utilizzi che soddisfano il requisito dei criteri di protezione app possono ricevere criteri di protezione app di Intune
- Utente Usa le applicazioni client che soddisfano il requisito dei criteri per le app approvate supporta criteri di protezione app di Intune
- Apre l'applicazione di accedere a posta elettronica o documenti
- Apra l'applicazione Outlook e acceda con le credenziali di Azure AD
- Viene richiesto di installare Authenticator (iOS) o portale aziendale (Android) per continuare (se non installato)
- Installi l'applicazione e possa tornare all'app Outlook per continuare
- Riceva un messaggio in cui viene chiesto di registrare un dispositivo
- È in grado di ricevere i criteri di protezione app di Intune
- È in grado di accedere alla posta elettronica con Outlook e criteri di protezione app di Intune
- È in grado di accedere ai siti o documenti a un'app non in al requisito dei criteri di protezione app ma elencate nel requisito app approvata.

Eventuali criteri di protezione app di Intune sono necessari per l'accesso ai dati aziendali e possono richiedere all'utente di riavviare l'applicazione, usare un aggiuntive e così via PIN (se configurato per la piattaforma e applicazione)

**Osservazioni**

- Se si desidera supportano entrambi i criteri di accesso condizionale basato su protezione e basato su app, è possibile utilizzare questo scenario.

- In questo *oppure* dei criteri, le app con **criteri di protezione delle app** requisito vengono valutati per l'accesso prima **App client approvate** requisito.

### <a name="configuration"></a>Configurazione

**Passaggio 1: configurare un criterio di accesso condizionale di Azure AD per Exchange Online**

Per i criteri di accesso condizionale in questo passaggio, è necessario configurare i componenti seguenti:

![Accesso condizionale](./media/app-protection-based-conditional-access/62.png)

1. **Nome**: specificare il nome dei criteri di accesso condizionale.

2. **Utenti e gruppi**: per i criteri di accesso condizionale deve essere selezionato almeno un utente o un gruppo.

3. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 

     ![Accesso condizionale](./media/app-protection-based-conditional-access/02.png)

4. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
 
    a. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/03.png)

    b. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.

    ![Accesso condizionale](./media/app-protection-based-conditional-access/91.png)

5. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:

   - **Richiedere app client approvata**

   - **Richiedi criteri di protezione delle app (anteprima)**

   - **Richiedi uno dei controlli selezionati**   
 
     ![Accesso condizionale](./media/app-protection-based-conditional-access/12.png)


**Passaggio 2: configurare criteri di protezione app di Intune per le applicazioni client per Android e iOS**


![Accesso condizionale](./media/app-protection-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).

Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 