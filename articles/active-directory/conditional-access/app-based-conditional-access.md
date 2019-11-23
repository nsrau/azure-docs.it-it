---
title: Approved client apps with Conditional Access - Azure Active Directory
description: Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
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
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381113"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How To: Require approved client apps for cloud app access with Conditional Access 

I dipendenti usano dispositivi mobili sia per le attività personali che per quelle aziendali. È importante assicurarsi che i dipendenti siano produttivi e al contempo evitare la perdita di dati. With Azure Active Directory (Azure AD) Conditional Access, you can restrict access to your cloud apps to approved client apps that can protect your corporate data.  

Questo argomento illustra come configurare criteri di accesso che richiedono app client approvate.

## <a name="overview"></a>Panoramica

With [Azure AD Conditional Access](overview.md), you can fine-tune how authorized users can access your resources. Si può, ad esempio, fare in modo che solo i dispositivi attendibili accedano alle app cloud.

È possibile usare i [criteri di protezione app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali. I criteri di protezione app di Intune non richiedono una soluzione di gestione dei dispositivi mobili (MDM), che consente di proteggere i dati aziendali con o senza la registrazione dei dispositivi in una soluzione di gestione di dispositivi.

Azure Active Directory Conditional Access enables you to limit access to your cloud apps to client apps that support Intune app protection policies. È possibile, ad esempio, limitare l'accesso a Exchange Online all'app Outlook.

In the Conditional Access terminology, these client apps are known as **approved client apps**.  

![Accesso condizionale](./media/app-based-conditional-access/05.png)

Per un elenco di app client approvate, vedere [Requisito per le app client approvate](technical-reference.md#approved-client-app-requirement).

You can combine app-based Conditional Access policies with other policies such as [device-based Conditional Access policies](require-managed-devices.md) to provide flexibility in how to protect data for both personal and corporate devices.

## <a name="before-you-begin"></a>Prima di iniziare

Questo argomento presuppone che l'utente abbia familiarità con:

- La guida tecnica per il [requisito delle app client approvate](technical-reference.md#approved-client-app-requirement).
- The basic concepts of [Conditional Access in Azure Active Directory](overview.md).
- How to [configure a Conditional Access policy](app-based-mfa.md).
- The [migration of Conditional Access policies](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Prerequisiti

To create an app-based Conditional Access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 

## <a name="exchange-online-policy"></a>Criteri per Exchange Online 

This scenario consists of an app-based Conditional Access policy for access to Exchange Online.

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

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform).

### <a name="configuration"></a>Configurazione 

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**.
1. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**:
   1. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.
1. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)** .

   ![Accesso condizionale](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**.
1. **Condizioni**: come **Condizioni** è necessario configurare **App client (anteprima)** . 
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.
   1. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)** .

      ![Accesso condizionale](./media/app-based-conditional-access/05.png)

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="exchange-online-and-sharepoint-online-policy"></a>Criteri per Exchange Online e SharePoint Online

This scenario consists of a Conditional Access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:

- Provi a usare l'app SharePoint per connettersi a siti aziendali e visualizzarli
- Attempts to sign in with the same credentials as the Outlook app credentials
- Non debba ripetere la registrazione e riesca a ottenere l'accesso alle risorse

### <a name="configuration"></a>Configurazione

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud:** come app cloud è necessario selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 
1. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**:
   1. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.
1. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)** .

   ![Accesso condizionale](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. Online 
1. **Condizioni**: come **Condizioni** è necessario configurare **App client**:
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.
   1. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)** .

      ![Accesso condizionale](./media/app-based-conditional-access/05.png)

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Criteri di accesso basato su dispositivi conformi o basato su app per Exchange Online e SharePoint Online

This scenario consists of an app-based or compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che:
 
- Some users are already enrolled (with or without corporate devices)
- Gli utenti che non hanno registrato il dispositivo e non hanno eseguito la registrazione ad Azure AD usando un'applicazione protetta tramite app debbano registrare un dispositivo per accedere alle risorse
- Gli utenti che hanno eseguito la registrazione usando l'applicazione protetta tramite app non debbano registrare nuovamente il dispositivo

### <a name="configuration"></a>Configurazione

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud:** come app cloud è necessario selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 
1. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
   1. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.
1. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi app client approvata (anteprima)**
   - **Richiedi uno dei controlli selezionati**   
 
      ![Accesso condizionale](./media/app-based-conditional-access/11.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 
1. **Condizioni**: come **Condizioni** è necessario configurare **App client**. 
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.
1. Come **Controlli di accesso** è necessario che sia selezionata l'opzione **Richiedi app client approvata (anteprima)** .
 
   ![Accesso condizionale](./media/app-based-conditional-access/11.png)

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Criteri di accesso basato su dispositivi conformi e basato su app per Exchange Online e SharePoint Online

This scenario consists of an app-based and compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>Istruzioni dello scenario

Questo scenario presuppone che un utente:
 
- Configuri la posta elettronica con un'applicazione di posta elettronica nativa in iOS o Android per la connessione a Exchange
- Riceva un messaggio di posta elettronica in cui è indicato che per accedere è necessario che il dispositivo sia registrato
- Esegua il download del portale aziendale e acceda a tale portale
- Controlli la posta elettronica e riceva un messaggio in cui viene chiesto di usare l'app Outlook
- Esegua il download dell'app Outlook
- Apra l'app Outlook e immetta le credenziali usate per la registrazione
- Sia in grado di accedere alla posta elettronica

I criteri di protezione delle app di Intune vengono attivati al momento dell'accesso ai dati aziendali e possono richiedere all'utente di riavviare l'applicazione, usare un PIN aggiuntivo e altro ancora, se configurato per l'applicazione e la piattaforma.

### <a name="configuration"></a>Configurazione

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud:** come app cloud è necessario selezionare **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 
1. **Condizioni**: come **Condizioni** è necessario configurare **Piattaforme del dispositivo** e **App client**. 
   1. Come **Piattaforme del dispositivo** selezionare **Android** e **iOS**.
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client di autenticazione moderna**.
1. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi app client approvata (anteprima)**
   - **Richiedi tutti i controlli selezionati**   
 
      ![Accesso condizionale](./media/app-based-conditional-access/13.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **App cloud**: come app cloud è necessario selezionare **Office 365 Exchange Online**. 
1. **Condizioni**: come **Condizioni** è necessario configurare **App client (anteprima)** . 
   1. Come **App client (anteprima)** selezionare **App per dispositivi mobili e client desktop** e **Client Exchange ActiveSync**.

   ![Accesso condizionale](./media/app-based-conditional-access/92.png)

1. Come **Controlli di accesso** è necessario che siano selezionate le opzioni seguenti:
   - **Richiedi che i dispositivi siano contrassegnati come conformi**
   - **Richiedi app client approvata (anteprima)**
   - **Richiedi tutti i controlli selezionati**   

**Passaggio 3: Configurare i criteri di protezione delle app di Intune per le applicazioni client iOS e Android**

![Accesso condizionale](./media/app-based-conditional-access/09.png)

Per altre informazioni, vedere [Proteggere app e dati con Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).

Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory. 
