---
title: Consenso dell'amministratore per le connessioni dell'account LinkedIn - Azure AD Documenti Microsoft
description: Spiega come abilitare o disabilitare le connessioni dell'account di integrazione LinkedIn nelle app Microsoft in Azure Active Directory
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
ms.openlocfilehash: 54e3821d269d11397ec4f9f5833e33ac6b555abc
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755112"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrare le connessioni dell'account LinkedIn in Azure Active Directory

È possibile consentire agli utenti dell'organizzazione di accedere alle connessioni LinkedIn all'interno di alcune app Microsoft.You can allow users in your organization to access their LinkedIn connections within some Microsoft apps. Nessun dato viene condiviso fino a quando gli utenti non acconsentono a connettere i propri account. È possibile integrare l'organizzazione nell'interfaccia di [amministrazione](https://aad.portal.azure.com)di Azure Active Directory (Azure AD).

> [!IMPORTANT]
> L'impostazione Connessioni account LinkedIn è attualmente in fase di implementazione nelle organizzazioni di Azure AD. Quando viene implementato nell'organizzazione, è abilitato per impostazione predefinita.
>
> Eccezioni:
>
> * L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per il US Gov, Microsoft Cloud per la Germania,oppure Azure e Office 365 gestito da 21Vianet in Cina.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Germania. L'impostazione non è disponibile per i clienti che usano Microsoft Cloud per la Germania.
> * L'impostazione è disattivata per impostazione predefinita per i tenant sottoposti a provisioning in Francia.
>
> Una volta abilitate le connessioni dell'account LinkedIn per l'organizzazione, le connessioni account funzionano dopo che gli utenti hanno il consenso alle app che accedono ai dati aziendali per loro conto. Per informazioni sull'impostazione di consenso dell'utente, vedere [Come rimuovere l'accesso di un utente a un'applicazione](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Abilitare le connessioni dell'account LinkedIn nel portale di AzureEnable LinkedIn account connections in the Azure portal

È possibile abilitare le connessioni dell'account LinkedIn solo per gli utenti a cui si desidera accedere, dall'intera organizzazione, agli utenti selezionati dell'organizzazione.

1. Accedere all'interfaccia di amministrazione di [Azure AD](https://aad.portal.azure.com/) con un account amministratore globale per l'organizzazione di Azure AD.
1. Selezionare **Utenti**.
1. Nella pagina **Utenti** selezionare **Impostazioni utente**.
1. In **Connessioni account LinkedIn**consente agli utenti di connettere i propri account per accedere alle connessioni LinkedIn all'interno di alcune app Microsoft. Nessun dato viene condiviso fino a quando gli utenti non acconsentono a connettere i propri account.

    * Selezionare **Sì** per abilitare il servizio per tutti gli utenti dell'organizzazione
    * Selezionare **Gruppo selezionato** per abilitare il servizio solo per un gruppo di utenti selezionati nell'organizzazione
    * Selezionare **No** per ritirare il consenso da tutti gli utenti dell'organizzazione

    ![Integrare le connessioni dell'account LinkedIn nell'organizzazione](./media/linkedin-integration/linkedin-integration.png)

1. Al termine, seleziona **Salva** per salvare le impostazioni.

> [!Important]
> L'integrazione di LinkedIn non è completamente abilitata per gli utenti fino a quando non acconsentono a connettere i propri account. Nessun dato viene condiviso quando si abilitano le connessioni account per gli utenti.

### <a name="assign-selected-users-with-a-group"></a>Assegnare utenti selezionati con un gruppoAssign selected users with a group

Abbiamo sostituito l'opzione 'Selezionato' che specifica un elenco di utenti con la possibilità di selezionare un gruppo di utenti in modo che è possibile abilitare la possibilità di collegare LinkedIn e account Microsoft per un singolo gruppo invece di molti singoli utenti. Se non hai attivato le connessioni all'account LinkedIn per singoli utenti selezionati, non devi fare nulla. Se in precedenza sono state abilitate le connessioni dell'account LinkedIn per singoli utenti selezionati, è necessario:

1. Ottenere l'elenco corrente dei singoli utentiGet the current list of individual users
1. Spostare i singoli utenti attualmente abilitati in un gruppo
1. Usare il gruppo del precedente come gruppo selezionato nell'impostazione Connessioni account LinkedIn nell'interfaccia di amministrazione di Azure AD.

> [!NOTE]
> Anche se non sposti i singoli utenti attualmente selezionati in un gruppo, questi possono comunque visualizzare le informazioni di LinkedIn nelle app Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Spostare gli utenti attualmente selezionati in un gruppo

1. Creare un file CSV degli utenti selezionati per le connessioni all'account LinkedIn.
1. Accedere a Microsoft 365 con l'account amministratore.
1. Avviare PowerShell.
1. Installare il modulo di Azure AD eseguendo`Install-Module AzureAD`
1. Eseguire lo script riportato di seguito:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Per usare il gruppo dal passaggio due come gruppo selezionato nell'impostazione Connessioni account LinkedIn nell'interfaccia di amministrazione di Azure AD, vedere Abilitare le [connessioni dell'account LinkedIn nel portale](#enable-linkedin-account-connections-in-the-azure-portal)di Azure.To use the group from step two as the selected group in the LinkedIn account connections setting in the Azure AD admin center, see Enable LinkedIn account connections in the Azure portal.

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilizzare Criteri di gruppo per abilitare le connessioni agli account LinkedIn

1. Scaricare i [file dei modelli amministrativi di Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Estrarre i file **ADMX** e copiarli nel repository centrale.
1. Aprire Gestione Criteri di gruppo.
1. Creare un oggetto Criteri di gruppo con la seguente impostazione:**Modelli** > **amministrativi** >  **Configurazione** > utente**Di Microsoft Office 2016** > Varie**Caratteristiche di Mostra LinkedIn nelle applicazioni**di Office .
1. Selezionare **Abilitato** o **Disabilitato**.
  
   State | Effetto
   ------ | ------
   **Attivato** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è abilitata nelle Opzioni di Office 2016. Gli utenti dell'organizzazione possono usare le caratteristiche di LinkedIn nelle applicazioni di Office 2016.
   **Disabled** | L'impostazione **Visualizza funzionalità di LinkedIn nelle applicazioni di Office** è disabilitata nelle Opzioni di Office 2016 e gli utenti finali non possono modificare questa impostazione. Gli utenti dell'organizzazione non possono usare le funzionalità di LinkedIn nelle applicazioni di Office 2016.

Questi Criteri di gruppo influiscono solo sulle app di Office 2016 per il computer locale. Se gli utenti disabilitano LinkedIn nelle app di Office 2016, possono comunque visualizzare le funzionalità di LinkedIn in Office 365.

## <a name="next-steps"></a>Passaggi successivi

* [Consenso dell'utente e condivisione dei dati per LinkedIn](linkedin-user-consent.md)

* [About LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft)

* [Centro assistenza LinkedIn](https://www.linkedin.com/help/linkedin)

* [Visualizzare l'impostazione corrente dell'integrazione di LinkedIn nel portale di Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
