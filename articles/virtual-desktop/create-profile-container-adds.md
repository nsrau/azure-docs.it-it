---
title: Creare File di Azure del contenitore del profilo FSLogix Active Directory Domain Services-Azure
description: Questo articolo descrive come creare un contenitore di profili FSLogix con File di Azure e Azure Active Directory Domain Services.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70a56b7efc34ba2fd3c06521c6e4cac6ea28778f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302463"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Creare un contenitore di profili con File di Azure e Azure AD DS

In questo articolo viene illustrato come creare un contenitore di profili FSLogix con File di Azure e Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stata configurata un'istanza di Azure AD DS. Se non ne è ancora disponibile uno, seguire le istruzioni riportate in [creare un dominio gestito di base](../active-directory-domain-services/tutorial-create-instance.md) , quindi tornare qui.

## <a name="add-azure-ad-ds-admins"></a>Aggiungere Azure AD amministratori DS

Per aggiungere altri amministratori, creare un nuovo utente e concedere loro le autorizzazioni.

Per aggiungere un amministratore:

1. Selezionare **Azure Active Directory** dalla barra laterale, quindi selezionare **tutti gli utenti** e quindi selezionare **nuovo utente**.

2.  Immettere i dettagli dell'utente nei campi.

3. Nel riquadro Azure Active Directory sul lato sinistro della schermata selezionare **gruppi**.

4. Selezionare il gruppo **AAD DC Administrators** .

5. Nel riquadro sinistro selezionare **membri**, quindi selezionare **Aggiungi membri** nel riquadro principale. Verrà visualizzato un elenco di tutti gli utenti disponibili in Azure AD. Selezionare il nome del profilo utente appena creato.

## <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

A questo punto è possibile abilitare l'autenticazione di Azure AD DS su Server Message Block (SMB).

Per abilitare l'autenticazione:

1. Se non è già stato fatto, configurare e distribuire un account di archiviazione di Azure per utilizzo generico v2 seguendo le istruzioni riportate in [creare un account di archiviazione di Azure](../storage/common/storage-account-create.md).

2. Al termine della configurazione dell'account, selezionare **Vai alla risorsa**.

3. Selezionare **configurazione** dal riquadro sul lato sinistro della schermata e quindi abilitare **l'autenticazione Azure Active Directory per file di Azure** nel riquadro principale. Al termine, selezionare **Salva**.

4. Selezionare **Panoramica** nel riquadro sul lato sinistro della schermata, quindi selezionare **file** nel riquadro principale.

5. Selezionare **condivisione file** e immettere il **nome** e la **quota** nei campi visualizzati sul lato destro dello schermo.

## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità

Gli altri utenti dovranno disporre delle autorizzazioni di accesso per accedere alla condivisione file. A tale scopo, è necessario assegnare a ogni utente un ruolo con le autorizzazioni di accesso appropriate.

Per assegnare le autorizzazioni di accesso degli utenti:

1. Dalla portale di Azure aprire la condivisione file creata in [configurare un account di archiviazione di Azure](#set-up-an-azure-storage-account).

2. Selezionare **Controllo di accesso (IAM)** .

3. Selezionare **Aggiungi un'assegnazione di ruolo**.

4. Nella scheda **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito appropriato dall'elenco ruolo. Per ottenere le autorizzazioni appropriate, è necessario selezionare almeno il **collaboratore condivisione SMB dati file di archiviazione** per l'account.

5. Per **assegna accesso a** selezionare **Azure Active Directory utente, gruppo o entità servizio**.

6. Selezionare un nome o un indirizzo di posta elettronica per l'identità del Azure Active Directory di destinazione.

7. Selezionare **Salva**.

## <a name="get-the-storage-account-access-key"></a>Ottenere la chiave di accesso dell'account di archiviazione

Sarà quindi necessario ottenere la chiave di accesso per l'account di archiviazione.

Per ottenere la chiave di accesso dell'account di archiviazione:

1. Dalla barra laterale portale di Azure selezionare **account di archiviazione**.

2. Dall'elenco degli account di archiviazione selezionare l'account per cui è stato abilitato Azure AD DS e i ruoli personalizzati nei passaggi precedenti.

3. In **Impostazioni** selezionare **chiavi di accesso** e copiare la chiave da **Key1**.

4. Passare alla scheda **macchine virtuali** e individuare qualsiasi VM che diventerà parte del pool host.

5. Selezionare il nome della macchina virtuale (VM) in **macchine virtuali (adVM)** e selezionare **Connetti** .

    Verrà scaricato un file RDP che consente di accedere alla macchina virtuale con le proprie credenziali.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della scheda RDP della finestra Connetti a macchina virtuale.](media/rdp-tab.png)

6. Dopo aver effettuato l'accesso alla macchina virtuale, eseguire un prompt dei comandi come amministratore.

7. Eseguire il comando seguente:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Sostituire `<desired-drive-letter>` con una lettera di unità di propria scelta (ad esempio, `y:` ).
    - Sostituire tutte le istanze di `<storage-account-name>` con il nome dell'account di archiviazione specificato in precedenza.
    - Sostituire `<share-name>` con il nome della condivisione creata in precedenza.
    - Sostituire `<storage-account-key>` con la chiave dell'account di archiviazione di Azure.

    Esempio:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Eseguire i comandi seguenti per consentire agli utenti di desktop virtuali Windows di creare il proprio contenitore di profili bloccando l'accesso ai contenitori di profili da altri utenti.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Sostituire `<mounted-drive-letter>` con la lettera dell'unità utilizzata per eseguire il mapping dell'unità.
    - Sostituire `<user-email>` con l'UPN dell'utente o del gruppo di Active Directory contenente gli utenti che dovranno accedere alla condivisione.

    Esempio:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Creare un contenitore di profili

Ora che i profili sono pronti per l'uso, è possibile creare un contenitore del profilo FSLogix.

Per configurare un contenitore del profilo FSLogix:

1. Accedere alla VM host sessione configurata all'inizio di questo articolo, quindi [scaricare e installare l'agente FSLogix](/fslogix/install-ht/).

2. Decomprimere il file dell'agente FSLogix scaricato e **x64** passare a  >  **versioni** x64, quindi aprire **FSLogixAppsSetup.exe**.

3. Una volta avviato il programma di installazione, selezionare Accetto **i termini e le condizioni di licenza.** Se applicabile, specificare una nuova chiave.

4. Selezionare **Installa**.

5. Aprire l' **unità C**, quindi passare a **programmi**  >  **FSLogix**  >  **app** per assicurarsi che l'agente FSLogix sia stato installato correttamente.

     >[!NOTE]
     > Se sono presenti più macchine virtuali nel pool host, sarà necessario ripetere i passaggi da 1 a 5 per ogni macchina virtuale.

6. Eseguire l' **Editor del registro di sistema** (regedit) come amministratore.

7. Passare a **computer**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**, fare clic con il pulsante destro del mouse su **FSLogix**, scegliere **nuovo**, quindi selezionare **chiave**.

8. Creare una nuova chiave denominata **profiles**.

9.  Fare clic con il pulsante destro del mouse su **profili**, scegliere **nuovo**, quindi selezionare **valore DWORD (32-bit).** Denominare il valore **abilitato** e impostare il valore **dei dati** su **1**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della chiave dei profili. Il file di REG_DWORD è evidenziato e il relativo valore di dati è impostato su 1.](media/dword-value.png)

10. Fare clic con il pulsante destro del mouse su **profili**, scegliere **nuovo**, quindi selezionare **valore multistringa**. Denominare il valore **VHDLocations** e impostare immettere l'URI per la condivisione file di Azure `\\fsprofile.file.core.windows.net\share` come valore di dati.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della chiave dei profili che mostra il file VHDLocations. Il valore dei dati Mostra l'URI per la condivisione File di Azure.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Assegnazione di utenti a un host sessione

A questo punto è necessario assegnare gli utenti all'host sessione.

Per assegnare gli utenti:

1. Eseguire Windows PowerShell come amministratore, quindi eseguire il cmdlet seguente per accedere a desktop virtuale Windows con PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Quando vengono richieste le credenziali, immettere lo stesso utente a cui è stato concesso il ruolo TenantCreator, proprietario Servizi Desktop remoto o collaboratore Servizi Desktop remoto sul tenant del desktop virtuale di Windows.

2. Eseguire i cmdlet seguenti per assegnare l'utente al gruppo desktop remoto:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Analogamente ai cmdlet precedenti, assicurarsi di sostituire `<your-wvd-tenant>` , `<wvd-pool>` e `<user-principal>` con i valori pertinenti.

    Esempio:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Verificare che il profilo funzioni

A questo punto è sufficiente assicurarsi che il profilo creato esista e funzioni come previsto.

Per verificare il profilo:

1. Aprire un browser e passare al [client Web desktop virtuale di Windows](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Accedere con l'account utente assegnato al gruppo di Desktop remoto.

3. Una volta stabilita la sessione utente, aprire il portale di Azure e accedere con un account amministrativo.

4. Dalla barra laterale selezionare **account di archiviazione**.

5. Selezionare l'account di archiviazione configurato come condivisione file per il pool host della sessione e abilitato con Azure AD DS.

6. Selezionare l'icona **file** , quindi espandere la condivisione.

    Se tutti gli elementi sono configurati correttamente, verrà visualizzata una **directory** con un nome formattato come segue: `<user SID>-<username>` .

## <a name="next-steps"></a>Passaggi successivi

Se si stanno cercando modi alternativi per creare contenitori di profili FSLogix, vedere gli articoli seguenti:

- [Creare un contenitore di profili per un pool host usando una condivisione file](create-host-pools-user-profile.md).
- [Creare un contenitore di profili FSLogix per un pool host usando Azure NetApp Files](create-fslogix-profile-container.md)

Per informazioni più dettagliate sui concetti relativi ai contenitori FSlogix per file di Azure, vedere [contenitori di profili FSlogix e file di Azure](fslogix-containers-azure-files.md).
