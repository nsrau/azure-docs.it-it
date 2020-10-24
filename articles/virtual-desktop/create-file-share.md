---
title: Creare una condivisione file di File di Azure con un controller di dominio-Azure
description: Configurare un contenitore del profilo FSLogix in una condivisione file di Azure in un pool di host di desktop virtuali Windows esistente con il dominio Active Directory.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aad5ebaf7eef5b404f7849b79694facf1efd01b4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519440"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Creare un contenitore di profili con File di Azure e servizi di dominio Active Directory

In questo articolo si apprenderà come creare una condivisione file di Azure autenticata da un controller di dominio in un pool di host di desktop virtuali Windows esistente. È possibile usare questa condivisione file per archiviare i profili di archiviazione.

Questo processo usa Active Directory Domain Services (AD DS), che è un servizio directory locale. Per informazioni su come creare un contenitore di profili FSLogix con Azure AD DS, vedere [creare un contenitore di profili FSLogix con file di Azure](create-profile-container-adds.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che il controller di dominio sia sincronizzato con Azure e risolvibile dalla rete virtuale di Azure (VNET) a cui sono connessi gli host della sessione.

## <a name="set-up-a-storage-account"></a>Configurare un account di archiviazione

Prima di tutto, è necessario configurare un account di archiviazione File di Azure.

Per configurare un account di archiviazione:

1. Accedere al portale di Azure.

2. Cercare l' **account di archiviazione** nella barra di ricerca.

3. Selezionare **+Aggiungi**.

4. Immettere le informazioni seguenti nella pagina  **Crea account di archiviazione** :

    - Creare un nuovo gruppo di risorse.
    - Immettere un nome univoco per l'account di archiviazione.
    - Per **località**, è consigliabile scegliere la stessa località del pool host del desktop virtuale di Windows.
    - Per **Prestazioni** selezionare **Standard**. A seconda dei requisiti di IOPS. Per ulteriori informazioni, vedere [Opzioni di archiviazione per i contenitori del profilo FSLogix nel desktop virtuale di Windows](store-fslogix-profile.md).
    - Per **tipo di account**selezionare **archiviazione V2** o **filestorage** (disponibile solo se il livello di prestazioni è Premium).
    - Per la **replica**, selezionare **archiviazione con ridondanza locale (con ridondanza locale)**.

5. Al termine, selezionare **Verifica + crea**, quindi selezionare **Crea**.

Se sono necessarie istruzioni di configurazione più dettagliate, vedere [disponibilità a livello](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)di area.

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Successivamente, sarà necessario creare una condivisione file di Azure.

Per creare una condivisione file:

1. Selezionare **Vai alla risorsa**.

2. Nella pagina Panoramica selezionare **Condivisioni file**.

3. Selezionare **+ condivisioni file**, creare una nuova condivisione file denominata **profili**, quindi immettere una quota appropriata o lasciare il campo vuoto per nessuna quota.

4. Selezionare **Crea**.

## <a name="enable-active-directory-authentication"></a>Abilitare l'autenticazione Active Directory

Successivamente, è necessario abilitare l'autenticazione di Active Directory (AD). Per abilitare questo criterio, è necessario seguire le istruzioni di questa sezione in un computer già aggiunto al dominio. Per abilitare l'autenticazione, seguire queste istruzioni nella macchina virtuale che esegue il controller di dominio:

1. Remote Desktop Protocol nella macchina virtuale aggiunta al dominio.

2. Seguire le istruzioni in [abilitare l'autenticazione di Azure AD DS per le condivisioni file di Azure](../storage/files/storage-files-identity-ad-ds-enable.md) per installare il modulo AzFilesHybrid e abilitare l'autenticazione.

3.  Aprire il portale di Azure, aprire l'account di archiviazione, selezionare **configurazione**, quindi verificare che **Active Directory (ad)** sia impostato su **abilitato**.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della pagina di configurazione con Azure Active Directory (AD) abilitato.](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Assegnare le autorizzazioni RBAC di Azure agli utenti di desktop virtuali Windows

A tutti gli utenti che devono avere profili FSLogix archiviati nell'account di archiviazione deve essere assegnato il ruolo di collaboratore condivisione SMB per i dati dei file di archiviazione.

Gli utenti che accedono agli host della sessione Desktop virtuale Windows devono avere le autorizzazioni di accesso per accedere alla condivisione file. Per concedere l'accesso a una condivisione file di Azure è necessario configurare le autorizzazioni sia a livello di condivisione che a livello di NTFS, in modo analogo a una condivisione di Windows tradizionale.

Per configurare le autorizzazioni a livello di condivisione, assegnare a ogni utente un ruolo con le autorizzazioni di accesso appropriate. Le autorizzazioni possono essere assegnate a singoli utenti o a un gruppo di Azure AD. Per altre informazioni, vedere [assegnare le autorizzazioni di accesso a un'identità](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>Gli account o i gruppi a cui si assegnano le autorizzazioni devono essere stati creati nel dominio e sincronizzati con Azure AD. Gli account creati in Azure AD non funzioneranno.

Per assegnare le autorizzazioni di controllo degli accessi in base al ruolo di Azure (RBAC di Azure):

1. Aprire il Portale di Azure.

2. Aprire l'account di archiviazione creato in [configurare un account di archiviazione](#set-up-a-storage-account).

3. Selezionare **condivisioni file**, quindi selezionare il nome della condivisione file che si intende usare.

4. Selezionare **Controllo di accesso (IAM)** .

5. Selezionare **Aggiungi un'assegnazione di ruolo**.

6. Nella scheda **Aggiungi assegnazione ruolo** selezionare **archiviazione file di dati condivisione SMB con privilegi elevati** per l'account amministratore.

     Per assegnare autorizzazioni agli utenti per i profili FSLogix, seguire le stesse istruzioni. Tuttavia, quando si arriva al passaggio 5, selezionare **file di archiviazione dati condivisione SMB** .

7. Selezionare **Salva**.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Assegnare autorizzazioni agli utenti per la condivisione file di Azure

Dopo aver assegnato le autorizzazioni RBAC di Azure agli utenti, è necessario configurare le autorizzazioni NTFS.

Per iniziare, è necessario sapere due elementi dal portale di Azure:

- Percorso UNC.
- Chiave dell'account di archiviazione.

### <a name="get-the-unc-path"></a>Ottenere il percorso UNC

Ecco come ottenere il percorso UNC:

1. Aprire il Portale di Azure.

2. Aprire l'account di archiviazione creato in [configurare un account di archiviazione](#set-up-a-storage-account).

3. Selezionare **Impostazioni**, quindi selezionare **Proprietà**.

4. Copiare l'URI dell' **endpoint del servizio file primario** nell'editor di testo desiderato.

5. Dopo aver copiato l'URI, eseguire le operazioni seguenti per modificarlo nel percorso UNC:

    - Rimuovi `https://` e Sostituisci con `\\`
    - Sostituire la barra `/` con una barra rovesciata `\` .
    - Aggiungere il nome della condivisione file creata in [creare una condivisione file di Azure](#create-an-azure-file-share) alla fine dell'UNC.

        ad esempio `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Ottenere la chiave dell'account di archiviazione

Per ottenere la chiave dell'account di archiviazione:

1. Aprire il Portale di Azure.

2. Aprire l'account di archiviazione creato in [configurare un account di archiviazione](#set-up-a-storage-account).

3. Nella scheda **account di archiviazione** selezionare **chiavi di accesso**.

4. Copiare **Key1** o **Key2** in un file nel computer locale.

### <a name="configure-ntfs-permissions"></a>Configurare le autorizzazioni NTFS

Per configurare le autorizzazioni NTFS:

1. Aprire un prompt dei comandi in una macchina virtuale aggiunta a un dominio.

2. Eseguire il comando seguente per montare la condivisione file di Azure e assegnargli una lettera di unità:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Eseguire il comando seguente per esaminare le autorizzazioni di accesso per la condivisione file di Azure:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    Sostituire `<mounted-drive-letter>` con la lettera dell'unità a cui è stato eseguito il mapping.

    Per impostazione predefinita, *gli utenti di NT Authority\Authenticated Users* e *BUILTIN\Users* hanno determinate autorizzazioni. Queste autorizzazioni predefinite consentono a questi utenti di leggere i contenitori di profili di altri utenti. Tuttavia, le autorizzazioni descritte in [configurare le autorizzazioni di archiviazione per l'uso con](/fslogix/fslogix-storage-config-ht) i contenitori di profili e i contenitori di Office non consentono agli utenti di leggere i contenitori di profili di ogni altro.

4. Eseguire i comandi seguenti per consentire agli utenti di desktop virtuali Windows di creare il proprio contenitore di profili bloccando l'accesso ai contenitori di profili da altri utenti.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Sostituire <> della lettera di unità montata con la lettera dell'unità utilizzata per eseguire il mapping dell'unità.
     - Sostituire <> di posta elettronica dell'utente con l'UPN dell'utente o del gruppo di Active Directory che contiene gli utenti che dovranno accedere alla condivisione.

     Ad esempio:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>Configurare FSLogix nelle VM host sessione

Questa sezione illustra come configurare una macchina virtuale con FSLogix. È necessario seguire queste istruzioni ogni volta che si configura un host sessione. Prima di iniziare la configurazione, seguire le istruzioni riportate in [scaricare e installare FSLogix](/fslogix/install-ht). Per garantire che le chiavi del Registro di sistema siano impostate in tutti gli host sessione sono disponibili diverse opzioni. È possibile impostare queste opzioni in un'immagine oppure configurare criteri di gruppo.

Per configurare FSLogix nella macchina virtuale dell'host sessione:

1. Connettersi tramite RDP alla macchina virtuale host sessione del pool host del desktop virtuale di Windows.

2. [Scaricare e installare FSLogix](/fslogix/install-ht).

5. Seguire le istruzioni in [Configurare le impostazioni del registro contenitori del profilo](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Passare a **computer**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**.

    - Creare una chiave **profili** .

    - Create **Enabled, DWORD** con un valore 1.

    - Creazione **di VHDLocations, MULTI_SZ**.

    - Impostare il valore di **VHDLocations** sul percorso UNC generato in [ottenere il percorso UNC](#get-the-unc-path).

6. Riavviare la VM.

## <a name="testing"></a>Test

Dopo aver installato e configurato FSLogix, è possibile testare la distribuzione eseguendo l'accesso con un account utente a cui è stato assegnato un gruppo di app o un desktop nel pool host. Verificare che l'account utente con cui si esegue l'accesso disponga dell'autorizzazione per la condivisione file.

Se l'utente ha eseguito l'accesso in precedenza, avrà un profilo locale esistente che verrà usato durante la sessione. Per evitare di creare un profilo locale, creare un nuovo account utente da usare per i test o usare i metodi di configurazione descritti in [esercitazione: configurare il contenitore del profilo per reindirizzare i profili utente](/fslogix/configure-profile-container-tutorial/).

Per verificare le autorizzazioni per la sessione:

1. Avviare una sessione nel desktop virtuale di Windows.

2. Aprire il Portale di Azure.

3. Aprire l'account di archiviazione creato in [configurare un account di archiviazione](#set-up-a-storage-account).

4. Selezionare **Crea una condivisione** nella pagina Crea una condivisione file di Azure.

5. Verificare che nel file sia presente una cartella contenente il profilo utente.

Per ulteriori test, seguire le istruzioni riportate in [assicurarsi che il profilo funzioni](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi relativi a FSLogix, vedere [questa guida alla risoluzione dei problemi](/fslogix/fslogix-trouble-shooting-ht).
