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
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920248"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrare le connessioni dell'account LinkedIn in Azure Active Directory

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

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Abilitare le connessioni dell'account LinkedIn nel portale di Azure

È possibile abilitare le connessioni dell'account LinkedIn solo per gli utenti che si vuole accedere, dall'intera organizzazione solo agli utenti selezionati all'interno dell'organizzazione.

1. Accedi per il [interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com/) con un account che sia un amministratore globale per l'organizzazione di Azure AD.
1. Selezionare **Utenti**.
1. Nel pannello **Utenti** selezionare **Impostazioni utente**.
1. Sotto **le connessioni dell'account LinkedIn**, consentire agli utenti di connettere i propri account per accedere alle connessioni di LinkedIn in alcune App Microsoft. Nessun dato viene condiviso fino a quando gli utenti il consenso per connettere i propri account.

    * Selezionare **Sì** per abilitare il servizio per tutti gli utenti nell'organizzazione
    * Selezionare **Selected** per consentire solo un gruppo di utenti selezionati all'interno dell'organizzazione
    * Selezionare **No** per ritirare il consenso da tutti gli utenti nell'organizzazione

    ![Integrare le connessioni dell'account LinkedIn all'interno dell'organizzazione](./media/linkedin-integration/linkedin-integration.png)

1. Al termine, selezionare **salvare** per salvare le impostazioni.

> [!Important]
> L'integrazione di LinkedIn non è completamente abilitato per gli utenti fino a quando non fornisce il consenso per connettere i propri account. Nessun dato viene condiviso quando si attivano le connessioni dell'account per gli utenti.

### <a name="assign-selected-users-with-a-group"></a>Assegnare gli utenti selezionati con un gruppo
Sono stati sostituiti l'opzione 'Selezionati' che specifica un elenco di utenti con l'opzione per selezionare un gruppo di utenti in modo che è possibile abilitare la possibilità di connettere un account LinkedIn e Microsoft per un singolo gruppo anziché molti utenti singoli. Se non si dispone di connessioni dell'account LinkedIn abilitate per i singoli utenti selezionati, non devi eseguire alcuna operazione. Se è stata precedentemente abilitata connessioni dell'account LinkedIn per utenti singoli selezionati, è necessario:

1. Ottenere l'elenco corrente dei singoli utenti
1. Spostare i singoli utenti attualmente abilitati in un gruppo
1. Usare il gruppo dal precedente come il gruppo selezionato nelle connessioni all'account LinkedIn, impostazione nell'interfaccia di amministrazione di Azure AD.

> [!NOTE]
> Anche se i singoli utenti attualmente selezionati non viene spostata a un gruppo, è comunque possibile visualizzare le informazioni di LinkedIn nelle app di Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Ottiene l'elenco corrente degli utenti selezionati

1. Accedere a Microsoft 365 con l'account amministratore.
1. Passare a https://linkedinselectedusermigration.azurewebsites.net/. Verrà visualizzato l'elenco di utenti che sono selezionati per le connessioni dell'account LinkedIn.
1. Esportare l'elenco in un file CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Spostare i singoli utenti attualmente selezionati in un gruppo

1. Avviare PowerShell
1. Installare il modulo di Azure AD tramite l'esecuzione `Install-Module AzureAD`
1. Eseguire lo script seguente:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Per usare il gruppo del passaggio due come il gruppo selezionato nelle connessioni all'account LinkedIn, impostazione nell'interfaccia di amministrazione di Azure AD, vedere [le connessioni dell'account LinkedIn Abilita nel portale di Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Usare criteri di gruppo per abilitare le connessioni dell'account LinkedIn

1. Scaricare i [file dei modelli amministrativi di Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Estrarre i file **ADMX** e copiarli nel repository centrale.
1. Aprire Gestione Criteri di gruppo.
1. Creare un oggetto Criteri di gruppo con l'impostazione seguente: **Configurazione utente** > **Modelli amministrativi** > **Microsoft Office 2016** > **Varie** > **Mostra le funzionalità di LinkedIn nelle applicazioni di Office** .
1. Selezionare **Abilitato** o **Disabilitato**.
  
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
