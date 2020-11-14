---
title: Controllare le operazioni che un utente può eseguire a livello di file-condivisioni file di Azure
description: Informazioni su come configurare le autorizzazioni ACL di Windows per l'autenticazione di servizi di dominio Active Directory locale per le condivisioni file di Azure. Consentendo di sfruttare il controllo di accesso granulare.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 02b8d72ab88f9eca2e1fac4858c14826dae57dbe
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629173"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Parte 3: configurare le autorizzazioni a livello di directory e di file su SMB 

Prima di iniziare questo articolo, assicurarsi di aver completato l'articolo precedente, [assegnare le autorizzazioni a livello di condivisione a un'identità](storage-files-identity-ad-ds-assign-permissions.md) per assicurarsi che le autorizzazioni a livello di condivisione siano disponibili.

Dopo aver assegnato le autorizzazioni a livello di condivisione con il controllo degli accessi in base al ruolo di Azure, è necessario configurare gli elenchi di controllo di accesso di Windows appropriati a livello di radice, di directory o di file per sfruttare il controllo Si considerino le autorizzazioni a livello di condivisione RBAC di Azure come Gatekeeper di alto livello che determina se un utente può accedere alla condivisione. Gli ACL di Windows operano a un livello più granulare per determinare le operazioni che l'utente può eseguire a livello di directory o di file. Quando un utente tenta di accedere a un file o a una directory, vengono applicate sia le autorizzazioni a livello di condivisione che di file/directory. se, pertanto, si verifica una differenza tra di esse, verrà applicata solo quella più restrittiva. Ad esempio, se un utente dispone di accesso in lettura/scrittura a livello di file, ma solo di lettura a livello di condivisione, potrà solo leggere tale file. Lo stesso vale se è stato invertito e un utente dispone di accesso in lettura/scrittura a livello di condivisione, ma solo a livello di file può leggere il file.

## <a name="azure-rbac-permissions"></a>Autorizzazioni RBAC di Azure

La tabella seguente contiene le autorizzazioni RBAC di Azure correlate a questa configurazione:


| Ruolo predefinito  | Autorizzazione NTFS  | Accesso risultante  |
|---------|---------|---------|
|Ruolo con autorizzazioni di lettura per la condivisione SMB di dati per file di archiviazione | Controllo completo, modifica, lettura, scrittura, esecuzione | Lettura ed esecuzione  |
|     |   Lettura |     Lettura  |
|Collaboratore per la condivisione SMB di dati per file di archiviazione  |  Controllo completo    |  Modifica, lettura, scrittura, esecuzione |
|     |  Modifica         |  Modifica    |
|     |  Lettura ed esecuzione |  Lettura ed esecuzione |
|     |  Lettura           |  Lettura    |
|     |  Scrittura          |  Scrittura   |
|Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione | Controllo completo  |  Modifica, lettura, scrittura, modifica, esecuzione |
|     |  Modifica          |  Modifica |
|     |  Lettura ed esecuzione  |  Lettura ed esecuzione |
|     |  Lettura            |  Lettura   |
|     |  Scrittura           |  Scrittura  |



## <a name="supported-permissions"></a>Autorizzazioni supportate

File di Azure supporta il set completo di ACL Windows di base e avanzato. È possibile visualizzare e configurare gli elenchi di controllo di accesso di Windows in directory e file in una condivisione file di Azure montando la condivisione e quindi usando Esplora file di Windows, eseguendo il comando Windows [icacls](/windows-server/administration/windows-commands/icacls) o il comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Per configurare gli ACL con autorizzazioni superuser, è necessario montare la condivisione usando la chiave dell'account di archiviazione della macchina virtuale aggiunta al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare gli ACL di Windows.

Nella directory radice di una condivisione file sono incluse le autorizzazioni seguenti:

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|Utenti|Definizione|
|---|---|
|BUILTIN\Administrators|Tutti gli utenti che sono amministratori di dominio dell'ambiente di servizi di dominio Active Directory locale.
|BUILTIN\Users|Gruppo di sicurezza predefinito in AD. Include gli utenti NT Authority\authenticated Users per impostazione predefinita. Per un file server tradizionale, è possibile configurare la definizione di appartenenza per server. Per File di Azure, non esiste un server di hosting, di conseguenza BUILTIN\Users include lo stesso set di utenti degli utenti NT Authority\authenticated Users.|
|NT AUTHORITY\SYSTEM|Account del servizio del sistema operativo del file server. Tale account del servizio non si applica nel contesto File di Azure. È incluso nella directory radice in modo che sia coerente con l'esperienza server dei file di Windows per scenari ibridi.|
|Utenti NT Authority\authenticated Users|Tutti gli utenti in Active Directory che possono ottenere un token Kerberos valido.|
|CREATOR OWNER|Ogni oggetto directory o file dispone di un proprietario per l'oggetto. Se nell'oggetto sono assegnati ACL a "CREATOR OWNER", l'utente proprietario di questo oggetto dispone delle autorizzazioni per l'oggetto definito dall'ACL.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il `net use` comando di Windows per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio seguente con valori personalizzati. Per altre informazioni sul montaggio di condivisioni file, vedere [usare una condivisione file di Azure con Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se si verificano problemi durante la connessione a File di Azure, vedere [lo strumento per la risoluzione dei problemi pubblicato per file di Azure errori di montaggio in Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vengono inoltre fornite [indicazioni](./storage-files-faq.md#on-premises-access) per aggirare gli scenari in cui la porta 445 è bloccata. 

## <a name="configure-windows-acls"></a>Configurare gli ACL di Windows

Una volta montata la condivisione file con la chiave dell'account di archiviazione, è necessario configurare gli ACL di Windows (noti anche come autorizzazioni NTFS). È possibile configurare gli ACL di Windows con Esplora file di Windows o con icacls.

Se si dispone di directory o file in file server locali con elenchi DACL di Windows configurati in base alle identità di servizi di dominio Active Directory, è possibile copiarli in File di Azure di salvare in modo permanente gli ACL con strumenti tradizionali per la copia dei file, ad esempio Robocopy o [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Se le directory e i file sono suddivisi in livelli File di Azure tramite Sincronizzazione file di Azure, gli ACL vengono trasferiti e salvati in modo permanente nel formato nativo.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Configurare gli ACL di Windows con Esplora file di Windows

Utilizzare Esplora file di Windows per concedere l'autorizzazione completa a tutte le directory e i file nella condivisione file, inclusa la directory radice.

1. Aprire Esplora file di Windows e fare clic con il pulsante destro del mouse su file/directory e selezionare **Proprietà**.
1. Selezionare la scheda **Sicurezza**.
1. Selezionare **modifica.** per modificare le autorizzazioni.
1. È possibile modificare le autorizzazioni degli utenti esistenti o selezionare **Aggiungi...** per concedere le autorizzazioni ai nuovi utenti.
1. Nella finestra del prompt dei comandi per l'aggiunta di nuovi utenti, immettere il nome utente di destinazione a cui si vogliono concedere le autorizzazioni nella casella **immettere i nomi degli oggetti da selezionare** e selezionare **Controlla nomi** per trovare il nome UPN completo dell'utente di destinazione.
1.    Selezionare **OK**.
1.    Nella scheda **sicurezza** selezionare tutte le autorizzazioni che si desidera concedere al nuovo utente.
1.    Selezionare **Applica**.

### <a name="configure-windows-acls-with-icacls"></a>Configurare gli ACL di Windows con icacls

Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto nell'esempio con i valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per ulteriori informazioni su come utilizzare icacls per impostare gli ACL di Windows e sui diversi tipi di autorizzazioni supportate, vedere [la Guida di riferimento alla riga di comando per icacls](/windows-server/administration/windows-commands/icacls).

## <a name="next-steps"></a>Passaggi successivi

Ora che la funzionalità è abilitata e configurata, passare all'articolo successivo, in cui è possibile montare la condivisione file di Azure da una macchina virtuale aggiunta a un dominio.

[Parte 4: montare una condivisione file da una macchina virtuale aggiunta a un dominio](storage-files-identity-ad-ds-mount-file-share.md)