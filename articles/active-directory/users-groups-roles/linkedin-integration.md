---
title: Consenso dell'amministratore per le connessioni dell'account LinkedIn-Azure AD | Microsoft Docs
description: Illustra come abilitare o disabilitare le connessioni dell'account di integrazione LinkedIn nelle app Microsoft in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025403"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrare le connessioni dell'account LinkedIn in Azure Active Directory

È possibile consentire agli utenti dell'organizzazione di accedere alle proprie connessioni LinkedIn in alcune app Microsoft. I dati non vengono condivisi finché gli utenti non acconsentono a connettere i propri account. È possibile integrare l'organizzazione nell'interfaccia di [Amministrazione](https://aad.portal.azure.com)di Azure Active Directory (Azure ad).

> [!IMPORTANT]
> L'impostazione delle connessioni dell'account LinkedIn è attualmente in fase di implementazione per Azure AD organizzazioni. Quando viene implementato nell'organizzazione, è abilitato per impostazione predefinita.
> 
> Eccezioni:
> * L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per il governo degli Stati Uniti, Microsoft Cloud per la Germania,oppure Azure e Office 365 gestito da 21Vianet in Cina.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Germania. L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per la Germania.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Francia.
>
> Una volta abilitate le connessioni dell'account LinkedIn per l'organizzazione, le connessioni dell'account funzionano dopo che gli utenti hanno concesso il consenso alle app che accedono ai dati aziendali per loro conto. Per informazioni sull'impostazione di consenso dell'utente, vedere [come rimuovere l'accesso di un utente a un'applicazione](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Abilitare le connessioni dell'account LinkedIn nel portale di Azure

È possibile abilitare le connessioni dell'account LinkedIn solo per gli utenti a cui si vuole accedere, dall'intera organizzazione ai soli utenti selezionati nell'organizzazione.

1. Accedere all'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com/) con un account amministratore globale per l'organizzazione Azure ad.
1. Selezionare **Utenti**.
1. Nel pannello **Utenti** selezionare **Impostazioni utente**.
1. In **connessioni account LinkedIn**, consentire agli utenti di connettere i propri account per accedere alle proprie connessioni LinkedIn in alcune app Microsoft. I dati non vengono condivisi finché gli utenti non acconsentono a connettere i propri account.

    * Selezionare **Sì** per abilitare il servizio per tutti gli utenti dell'organizzazione
    * Selezionare il **gruppo selezionato** per abilitare il servizio solo per un gruppo di utenti selezionati nell'organizzazione
    * Selezionare **No** per prelevare il consenso da tutti gli utenti dell'organizzazione

    ![Integrare le connessioni dell'account LinkedIn nell'organizzazione](./media/linkedin-integration/linkedin-integration.png)

1. Al termine, selezionare **Salva** per salvare le impostazioni.

> [!Important]
> L'integrazione di LinkedIn non è completamente abilitata per gli utenti fino a quando non acconsente alla connessione dei propri account. Quando si abilitano le connessioni degli account per gli utenti, non vengono condivisi dati.

### <a name="assign-selected-users-with-a-group"></a>Assegnare gli utenti selezionati a un gruppo
È stata sostituita l'opzione ' selected ' che specifica un elenco di utenti con l'opzione per selezionare un gruppo di utenti in modo da consentire la possibilità di connettere gli account LinkedIn e Microsoft per un singolo gruppo anziché per molti utenti singoli. Se le connessioni dell'account LinkedIn non sono abilitate per utenti singoli selezionati, non è necessario eseguire alcuna operazione. Se in precedenza sono state abilitate le connessioni dell'account LinkedIn per utenti singoli selezionati, è necessario:

1. Ottenere l'elenco corrente di singoli utenti
1. Spostare i singoli utenti attualmente abilitati in un gruppo
1. Usare il gruppo del gruppo precedente come gruppo selezionato nell'impostazione delle connessioni dell'account LinkedIn nell'interfaccia di amministrazione di Azure AD.

> [!NOTE]
> Anche se non si spostano gli utenti individuali attualmente selezionati in un gruppo, possono comunque visualizzare le informazioni di LinkedIn nelle app Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Ottiene l'elenco corrente degli utenti selezionati

1. Accedere Microsoft 365 con l'account amministratore.
1. Passare a https://linkedinselectedusermigration.azurewebsites.net/. Verrà visualizzato un elenco di utenti selezionati per le connessioni dell'account LinkedIn.
1. Esportare l'elenco in un file CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Spostare i singoli utenti attualmente selezionati in un gruppo

1. Avviare PowerShell
1. Installare Azure AD modulo eseguendo `Install-Module AzureAD`
1. Eseguire lo script seguente:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Per usare il gruppo dal passaggio 2 come gruppo selezionato nell'impostazione delle connessioni dell'account LinkedIn nell'interfaccia di amministrazione di Azure AD, vedere [abilitare le connessioni dell'account LinkedIn nel portale di Azure](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Usare Criteri di gruppo per abilitare le connessioni dell'account LinkedIn

1. Scaricare i [file dei modelli amministrativi di Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Estrarre i file **ADMX** e copiarli nel repository centrale.
1. Aprire Gestione Criteri di gruppo.
1. Creare un oggetto Criteri di gruppo con le impostazioni seguenti: **Configurazione utente** > **Modelli amministrativi** > **Microsoft Office 2016** > **Varie** > **Show LinkedIn features in Office applications** (Mostra funzionalità di LinkedIn nelle applicazioni di Office).
1. Selezionare **Abilitato** o **Disabilitato**.
  
   Stato | Effetto
   ------ | ------
   **Enabled** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è abilitata nelle Opzioni di Office 2016. Gli utenti dell'organizzazione possono usare le funzionalità di LinkedIn nelle applicazioni Office 2016.
   **Disabilitata** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è disabilitata nelle Opzioni di Office 2016 e gli utenti finali non possono modificare questa impostazione. Gli utenti dell'organizzazione non possono usare le funzionalità di LinkedIn nelle applicazioni di Office 2016.

Questi Criteri di gruppo influiscono solo sulle app di Office 2016 per il computer locale. Se gli utenti disabilitano LinkedIn nelle app di Office 2016, possono comunque vedere le funzionalità di LinkedIn in Office 365.

## <a name="next-steps"></a>Passaggi successivi

* [Consenso dell'utente e condivisione dei dati per LinkedIn](linkedin-user-consent.md)

* [About LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft)

* [Centro assistenza LinkedIn](https://www.linkedin.com/help/linkedin)

* [Visualizzare l'impostazione corrente dell'integrazione di LinkedIn nel portale di Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
