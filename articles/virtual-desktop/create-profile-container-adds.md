---
title: Creare il contenitore di profili FSLogix Azure Files Active Directory Domain Services - Azure
description: Questo articolo descrive come creare un contenitore di profili FSLogix con File di Azure e Servizi di dominio Azure Active Directory.This article describes how to create an FSLogix profile container with Azure Files and Azure Active Directory Domain Services.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265772"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Creare un contenitore di profili FSLogix con File di AzureCreate an FSLogix profile container with Azure Files

Questo articolo illustra come creare un contenitore di profili FSLogix con File di Azure e Servizi di dominio Azure Active Directory.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che tu abbia già configurato un'istanza di Servizi di dominio Active Directory di Azure.This article assumes you've already set up an Azure AD DS instance. Se non ne hai ancora uno, segui prima le istruzioni in Creare un dominio gestito di [base,](../active-directory-domain-services/tutorial-create-instance.md) quindi torna qui.

## <a name="add-azure-ad-ds-admins"></a>Aggiungere amministratori di Servizi di dominio Active Directory di AzureAdd Azure AD DS admins

Per aggiungere altri amministratori, è necessario creare un nuovo utente e concedergli le autorizzazioni.

Per aggiungere un amministratore:

1. Selezionare **Azure Active Directory** dalla barra laterale, quindi Tutti gli **utenti**e infine **Nuovo utente**.

2.  Immettere i dettagli utente nei campi.

3. Nel riquadro Azure Active Directory sul lato sinistro dello schermo selezionare **Gruppi**.

4. Selezionare il gruppo **AAD DC Administrators.**

5. Nel riquadro sinistro selezionare **Membri**, quindi **Aggiungi membri** nel riquadro principale. Verrà visualizzato un elenco di tutti gli utenti disponibili in Azure AD. Selezionare il nome del profilo utente appena creato.

## <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di AzureSet up an Azure Storage account

Ora è il momento di abilitare l'autenticazione di Azure AD DS su SMB (Server Message Block). 

Per abilitare l'autenticazione:

1. Se non è già stato fatto, configurare e distribuire un account di archiviazione di Azure versione 2 generico seguendo le istruzioni in Creare un account di archiviazione di Azure.If you haven't already, set up and deploy a general-purpose v2 Azure Storage account by following the instructions in [Create an Azure Storage account.](../storage/common/storage-account-create.md)

2. Al termine della configurazione dell'account, selezionare **Vai alla risorsa**.

3. Selezionare **Configurazione** dal riquadro sul lato sinistro dello schermo, quindi abilitare l'autenticazione di **Azure Active Directory per File** di Azure nel riquadro principale. Al termine, selezionare **Salva**.

4. Selezionare **Panoramica** nel riquadro sul lato sinistro dello schermo, quindi selezionare **File** nel riquadro principale.

5. Selezionare **Condivisione file** e immettere **il nome** e la **quota** nei campi visualizzati sul lato destro dello schermo.

## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità

Gli altri utenti dovranno disporre delle autorizzazioni di accesso per accedere alla condivisione file. A tale scopo, è necessario assegnare a ogni utente un ruolo con le autorizzazioni di accesso appropriate.

Per assegnare agli utenti le autorizzazioni di accesso:

1. Dal portale di Azure aprire la condivisione file creata in [Configurare un account di Archiviazione di Azure.](#set-up-an-azure-storage-account)

2. Selezionare **Controllo di accesso (IAM)**.

3. Selezionare **Aggiungi assegnazione ruolo**.

4. Nella scheda **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito appropriato dall'elenco dei ruoli. Per ottenere le autorizzazioni appropriate, è necessario selezionare almeno **Collaboratore condivisione SMB file** di archiviazione per l'account.

5. In **Assegna accesso a**selezionare Utente, gruppo o entità servizio di Azure Active **Directory.**

6. Selezionare un nome o un indirizzo di posta elettronica per l'identità di Azure Active Directory di destinazione.

7. Selezionare **Salva**.

## <a name="get-the-storage-account-access-key"></a>Ottenere la chiave di accesso dell'account di archiviazioneGet the Storage Account access key

Successivamente, è necessario ottenere la chiave di accesso per l'account di archiviazione.

Per ottenere la chiave di accesso dell'account di archiviazione:To get the Storage Account access key:

1. Dalla barra laterale del portale di Azure selezionare **Account di archiviazione**.

2. Nell'elenco degli account di archiviazione selezionare l'account per il quale è stato abilitato Servizi di dominio Active Directory di Azure e sono stati creati i ruoli personalizzati nei passaggi precedenti.

3. In **Impostazioni**selezionare **Chiavi di accesso** e copiare la chiave da **key1**.

4. Passare alla scheda **Macchine virtuali** e individuare qualsiasi macchina virtuale che diventerà parte del pool host.

5. Selezionare il nome della macchina virtuale (VM) in **Macchine virtuali (adVM)** e selezionare **Connetti**

    Verrà scaricato un file RDP che consentirà di accedere alla macchina virtuale con le proprie credenziali.

    ![Schermata della scheda RDP della finestra Connetti a macchina virtuale.](media/rdp-tab.png)

6. Dopo aver eseguito l'accesso alla macchina virtuale, eseguire un prompt dei comandi come amministratore.

7. Eseguire il comando seguente:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Sostituire `<desired-drive-letter>` con una lettera di unità `y:`di propria scelta (ad esempio, ).
    - Sostituire tutte `<storage-account-name>` le istanze di con il nome dell'account di archiviazione specificato in precedenza.
    - Sostituire `<share-name>` con il nome della condivisione creata in precedenza.
    - Sostituire `<storage-account-key>` con la chiave dell'account di archiviazione di Azure.Replace with the storage account key from Azure.

    Ad esempio:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Eseguire il comando seguente per concedere all'utente l'accesso completo alla condivisione File di Azure.Run the following command to grant the user full access to the Azure Files share.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Sostituire `<mounted-drive-letter>` con la lettera dell'unità che si desidera venga utilizzata dall'utente.
    - Sostituire `<user-email>` con l'UPN dell'utente che utilizzerà questo profilo per accedere alle macchine virtuali dell'host della sessione.

    Ad esempio:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Creare un contenitore di profili

Ora che i profili sono pronti per l'editor, è possibile creare un contenitore di profili FSLogix.Now that your profiles are ready to go, let's create a FSLogix profile container.

Per configurare un contenitore di profili FSLogix:To configure a FSLogix profile container:

1. Accedere alla macchina virtuale host della sessione configurata all'inizio di questo articolo, quindi [scaricare e installare l'agente FSLogix](/fslogix/install-ht/).

2. Decomprimere il file dell'agente FSLogix scaricato e passare a **X64** > **Releases**, quindi aprire **FSLogixAppsSetup.exe**.

3. Una volta avviato il programma di installazione, selezionare **Accetto i termini e le condizioni** di licenza. Se applicabile, fornire una nuova chiave.

4. Selezionare **Installa**.

5. Aprire **l'unità C**, quindi passare a **Programmi** > **FSLogix** > **Apps** per assicurarsi che l'agente FSLogix sia stato installato correttamente.

     >[!NOTE]
     > Se nel pool host sono presenti più macchine virtuali, è necessario ripetere i passaggi da 1 a 5 per ogni macchina virtuale.

6. Eseguire **l'Editor del Registro** di sistema (RegEdit) come amministratore.

7. Passare a **Computer** > **HKEY_LOCAL_MACHINE** > **software** > **FSLogix**, fare clic con il pulsante destro del mouse su **FSLogix**, scegliere **Nuovo**, quindi **Chiave**.

8. Creare una nuova chiave denominata **Profili**.

9.  Fare clic con il pulsante destro del mouse su **Profili**, selezionare **Nuovo**, quindi **Valore DWORD (32 bit).** Assegnare un nome al valore **Enabled** e impostare il valore **Data** su **1**.

    ![Cattura di schermata della chiave Profili. Il file REG_DWORD viene evidenziato e il relativo valore Data è impostato su 1.](media/dword-value.png)

10. Fare clic con il pulsante destro del mouse su **Profili**, selezionare **Nuovo**, quindi **Valore multistringa**. Assegnare al valore **VHDLocations** il valore immettere l'URI per la condivisione `\\fsprofile.file.core.windows.net\share` File di Azure come valore di dati.

    ![Schermata della chiave Profili che mostra il file VHDLocations. Il valore Data mostra l'URI per la condivisione File di Azure.Its Data value shows the URI for the Azure Files share.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Assegnare utenti a un host di sessioneAssign users to a session host

A questo punto è necessario assegnare gli utenti all'host della sessione.

Per assegnare gli utenti:

1. Eseguire Windows PowerShell come amministratore, quindi eseguire il cmdlet seguente per accedere a Windows Virtual Desktop con PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Quando vengono richieste le credenziali, immettere lo stesso utente a cui è stato concesso il ruolo TenantCreator, RDS Owner o RDS Contributor nel tenant di Windows Virtual Desktop.

2. Eseguire i cmdlet seguenti per assegnare l'utente al gruppo di desktop remoto:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Analogamente ai cmdlet precedenti, assicurarsi di sostituire `<your-wvd-tenant>`, `<wvd-pool>`e `<user-principal>` con i valori pertinenti.

    Ad esempio:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Assicurati che il tuo profilo funzioni

Ora tutto quello che dovete fare è assicurarsi che il profilo che avete creato esiste e funziona come previsto.

Per verificare il tuo profilo:

1. Aprire un browser e passare al client Web desktop [virtuale di Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

2. Accedere con l'account utente assegnato al gruppo Desktop remoto.

3. Dopo aver stabilito la sessione utente, aprire il portale di Azure e accedere con un account amministrativo.

4. Dalla barra laterale, seleziona **Account di archiviazione**.

5. Selezionare l'account di archiviazione configurato come condivisione file per il pool host della sessione e abilitato con Servizi di dominio Active Directory di Azure.

6. Seleziona l'icona **File,** quindi espandi la tua condivisione.

    Se tutto è impostato correttamente, si dovrebbe vedere una **directory** con `<user SID>-<username>`un nome formattato in questo modo: .

## <a name="next-steps"></a>Passaggi successivi

Se si cercano modi alternativi per creare contenitori di profili FSLogix, vedere gli articoli seguenti:If you're looking for alternate ways to create FSLogix profile containers, check out the following articles:

- [Creare un contenitore di profili per un pool host utilizzando una condivisione file.](create-host-pools-user-profile.md)
- [Creare un contenitore di profili FSLogix per un pool host usando I file di Rete di AzureCreate an FSLogix profile container for a host pool using Azure NetApp Files](create-fslogix-profile-container.md)

È possibile trovare informazioni più dettagliate sui concetti relativi ai contenitori FSlogix per i file di Azure nei [contenitori dei profili FSLogix e](fslogix-containers-azure-files.md)nei file di Azure.
