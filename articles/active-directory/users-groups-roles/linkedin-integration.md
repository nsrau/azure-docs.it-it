---
title: Il consenso dell'amministratore per le connessioni dell'account LinkedIn - Azure Active Directory | Microsoft Docs
description: Viene illustrato come abilitare o disabilitare le connessioni account di integrazione di LinkedIn nelle app di Microsoft in Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368127"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Fornire il consenso per le connessioni dell'account LinkedIn per un'organizzazione di Azure Active Directory

È possibile consentire agli utenti dell'organizzazione per accedere alle loro connessioni di LinkedIn in alcune App Microsoft. Nessun dato viene condiviso fino a quando gli utenti il consenso per connettere i propri account. È possibile integrare l'organizzazione in Azure Active Directory (Azure AD) [interfaccia di amministrazione](https://aad.portal.azure.com).

> [!IMPORTANT]
> Le connessioni dell'account LinkedIn l'impostazione è in corso l'implementazione per organizzazioni di Azure AD. Si è implementato nell'organizzazione, viene abilitato per impostazione predefinita.
> 
> Eccezioni:
> * L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per il governo degli Stati Uniti, Microsoft Cloud per la Germania,oppure Azure e Office 365 gestito da 21Vianet in Cina.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Germania. L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per la Germania.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Francia.
>
> Dopo che sono abilitate le connessioni dell'account LinkedIn per l'organizzazione, le connessioni dell'account di lavoro dopo che gli utenti fornire il consenso alle App che accedono ai dati aziendali per loro conto. Per informazioni sull'impostazione del consenso dell'utente, vedere [come rimuovere l'accesso un utente a un'applicazione](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Usare il portale di Azure per abilitare le connessioni dell'account LinkedIn

È possibile abilitare le connessioni dell'account LinkedIn solo per gli utenti che si vuole accedere, dall'intera organizzazione solo agli utenti selezionati all'interno dell'organizzazione.

1. Accedi per il [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/) con un account che sia un amministratore globale per l'organizzazione di Azure AD.
2. Selezionare **Utenti**.
3. Nel pannello **Utenti** selezionare **Impostazioni utente**.
4. Sotto **le connessioni dell'account LinkedIn**, consentire agli utenti di connettere i propri account per accedere alle connessioni di LinkedIn in alcune App Microsoft. Nessun dato viene condiviso fino a quando gli utenti il consenso per connettere i propri account.

  * Selezionare **Sì** per fornire il consenso per il servizio per tutti gli utenti nell'organizzazione
  * Selezionare **Selected** fornire il consenso per solo determinati utenti nell'organizzazione
  * Selezionare **No** per ritirare il consenso per gli utenti dell'organizzazione

    ![Integrare le connessioni dell'account LinkedIn all'interno dell'organizzazione](./media/linkedin-integration/linkedin-integration.png)

5. Al termine, selezionare **salvare** per salvare le impostazioni.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Usare criteri di gruppo per abilitare le connessioni dell'account LinkedIn

1. Scaricare i [file dei modelli amministrativi di Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Estrarre i file **ADMX** e copiarli nel repository centrale.
3. Aprire Gestione Criteri di gruppo.
4. Creare un oggetto Criteri di gruppo con l'impostazione seguente: **Configurazione utente** > **Modelli amministrativi** > **Microsoft Office 2016** > **Varie** > **Mostra le funzionalità di LinkedIn nelle applicazioni di Office** .
5. Selezionare **Abilitato** o **Disabilitato**.
  
   Stato | Effetto
   ------ | ------
   **Enabled** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è abilitata nelle Opzioni di Office 2016. Gli utenti dell'organizzazione possono usare le funzionalità di LinkedIn nelle applicazioni di Office 2016.
   **Disabilitato** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è disabilitata nelle Opzioni di Office 2016 e gli utenti finali non possono modificare questa impostazione. Gli utenti dell'organizzazione non possono usare le funzionalità di LinkedIn nelle applicazioni di Office 2016.

Questi Criteri di gruppo influiscono solo sulle app di Office 2016 per il computer locale. Se gli utenti disabilitano LinkedIn nelle app di Office 2016, è comunque possibile visualizzare le funzionalità di LinkedIn in Office 365.

## <a name="next-steps"></a>Passaggi successivi

* [Il consenso dell'utente e condivisione di LinkedIn di dati](linkedin-user-consent.md)

* [About LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft)

* [Centro assistenza LinkedIn](https://www.linkedin.com/help/linkedin)

* [Visualizzare l'impostazione corrente dell'integrazione di LinkedIn nel portale di Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
