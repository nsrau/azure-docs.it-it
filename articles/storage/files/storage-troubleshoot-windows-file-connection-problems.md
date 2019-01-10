---
title: Risolvere i problemi di File di Azure in Windows | Microsoft Docs
description: Risoluzione dei problemi di File di Azure in Windows
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/30/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: caa078aa522e20a0e09d0b4d97461358c1698fc7
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744238"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Risolvere i problemi di File di Azure in Windows

Questo articolo elenca i problemi comuni correlati a File di Microsoft Azure quando si effettua la connessione da client Windows. L'articolo descrive anche le possibili cause e risoluzioni per tali problemi. Oltre ai passaggi di risoluzione dei problemi in questo articolo, si può anche usare [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) per verificare che l'ambiente client Windows sia conforme ai prerequisiti. AzFileDiagnostics automatizza il rilevamento della maggior parte dei sintomi indicati in questo articolo e consente di configurare l'ambiente in modo da ottenere prestazioni ottimali. È anche possibile trovare queste informazioni nell'articolo che illustra come [risolvere i problemi delle condivisioni File di Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) e indica i passaggi per risolvere problemi di connessione, mapping e montaggio delle condivisioni file di Azure.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Errore 5 durante il montaggio di una condivisione file di Azure

Quando si tenta di montare una condivisione file, è possibile che si riceva l'errore seguente:

- Errore di sistema 5. Accesso negato.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canale di comunicazione non crittografato

Per motivi di sicurezza, le connessioni alle condivisioni file di Azure vengono bloccate se il canale di comunicazione non è crittografato e il tentativo di connessione non viene effettuato dallo stesso data center in cui si trovano tali condivisioni. Le connessioni non crittografate all'interno dello stesso data center possono essere bloccate se l'impostazione [Trasferimento sicuro obbligatorio](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) è abilitata nell'account di archiviazione. Un canale di comunicazione crittografato è disponibile solo se il sistema operativo del client dell'utente supporta la crittografia SMB.

Windows 8, Windows Server 2012 e le versioni successive di ciascuno dei due sistemi operativi negoziano richieste comprendenti SMB 3.0, che supporta la crittografia.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

1. Eseguire la connessione da un client che supporta la crittografia SMB (Windows 8, Windows Server 2012 o versioni successive) o da una macchina virtuale nello stesso data center dell'account di archiviazione di Azure usato per la condivisione file di Azure.
2. Se il client non supporta la crittografia SMB, verificare che l'opzione [Trasferimento sicuro obbligatorio](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) sia disabilitata nell'account di archiviazione.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: nell'account di archiviazione sono abilitate regole di rete virtuale o del firewall. 

Se nell'account di archiviazione sono configurate regole di rete virtuale o del firewall, verrà negato l'accesso al traffico di rete a meno che all'indirizzo IP o alla rete virtuale client sia consentito l'accesso.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Errore 53, Errore 67 o Errore 87 quando si prova a montare o smontare una condivisione file di Azure

Quando si prova a montare una condivisione file da locale o da un data center diverso, potrebbero verificarsi gli errori seguenti:

- Errore di sistema 53. Impossibile trovare il percorso di rete.
- Errore di sistema 67. Impossibile trovare il nome della rete.
- Errore di sistema 87. Parametro non corretto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: la porta 445 è bloccata

L'errore di sistema 53 o 67 può verificarsi se la comunicazione in uscita dalla porta 445 verso un data center di File di Azure è bloccata. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

È possibile verificare se la porta 445 è bloccata dal firewall o dai vincoli dell'ISP con lo strumento [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) o il cmdlet `Test-NetConnection`. 

Per usare il cmdlet `Test-NetConnection` è necessario che sia installato il modulo AzureRM di PowerShell. Per altre informazioni, vedere l'articolo su come [installare il modulo di Azure PowerShell](/powershell/azure/install-azurerm-ps). Ricordarsi di sostituire `<your-storage-account-name>` e `<your-resoure-group-name>` con i nomi pertinenti per il proprio account di archiviazione.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName [System.Uri]::new($storageAccount.Context.FileEndPoint).Host -Port 445
  
    
Se la connessione ha avuto esito positivo, verrà visualizzato l'output seguente:
    
  
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Il comando precedente restituisce l'indirizzo IP corrente dell'account di archiviazione. Non è garantito che tale indirizzo IP rimanga invariato. Potrebbe cambiare in qualsiasi momento. Non includere l'indirizzo IP come hardcoded in script o nella configurazione di un firewall.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Collaborare con il reparto IT per aprire la porta 445 in uscita verso gli [intervalli IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: è abilitata la comunicazione NTLMv1

L'errore di sistema 53 o 87 può verificarsi se sul client è abilitata la comunicazione NTLMv1. File di Azure supporta solo l'autenticazione NTLMv2. Con la comunicazione NTLMv1 abilitata, il client è meno sicuro. Di conseguenza, la comunicazione viene bloccata per File di Azure. 

Per determinare se questa è la causa dell'errore, assicurarsi che la sottochiave seguente del Registro di sistema sia impostata su 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa &gt; LmCompatibilityLevel**

Per altre informazioni, vedere l'argomento [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) su TechNet.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Ripristinare **LmCompatibilityLevel** sul valore predefinito 3 nella sottochiave seguente del Registro di sistema:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Errore 1816 "Non c'è abbastanza disponibilità per elaborare il comando" quando si esegue la copia in una condivisione file di Azure

### <a name="cause"></a>Causa

L'errore 1816 si verifica quando si raggiunge il limite massimo di handle aperti simultanei consentito per un file nel computer in cui si sta montando la condivisione file.

### <a name="solution"></a>Soluzione

Chiudere alcuni degli handle aperti simultaneamente per ridurne il numero e quindi riprovare. Per altre informazioni, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>Errore "Accesso negato" durante l'esplorazione di una condivisione file di Azure nel portale

Quando si passa alla condivisione file di Azure nel portale, viene visualizzato il messaggio di errore seguente:

Accesso negato  
L'utente non è autorizzato ad accedere  
Non si è autorizzati ad accedere a questo contenuto. Per ottenere l'accesso, contattare il proprietario.  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 1: l'account utente non ha accesso all'account di archiviazione

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Passare all'account di archiviazione in cui si trova la condivisione file di Azure, fare clic su **Controllo di accesso (IAM)** e verificare che l'account utente abbia accesso all'account di archiviazione. Per altre informazioni, vedere [Come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: nell'account di archiviazione sono abilitate regole di rete virtuale o del firewall.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Rallentamento della copia del file da e verso File di Azure in Windows

Si potrebbe verificare un rallentamento delle prestazioni quando si prova a trasferire file nel servizio File di Azure.

- In assenza di un requisito minimo specifico per la dimensione di I/O, è consigliabile usare 1 MiB per assicurare prestazioni ottimali.
-   Se si conosce la dimensione finale del file che si vuole estendere con operazioni di scrittura e il software non ha problemi di compatibilità se la parte finale del file non ancora scritta contiene zeri, impostare le dimensioni del file in fase preliminare anziché lasciare che ogni operazione di scrittura venga considerata un'estensione.
-   Usare il metodo di copia corretto:
    -   Usare [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per i trasferimenti tra due condivisioni file.
    -   Usare [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) tra condivisioni file in un computer locale.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerazioni per Windows 8.1 o Windows Server 2012 R2

Per i client che eseguono Windows 8.1 o Windows Server 2012 R2, assicurarsi che sia installata la hotfix [KB3114025](https://support.microsoft.com/help/3114025). Questa hotfix consente di migliorare le prestazioni durante la creazione e la chiusura degli handle.

Per verificare se la hotfix è stata installata, è possibile eseguire lo script seguente:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se la hotfix è installata, viene visualizzato l'output seguente:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Da dicembre 2015 la hotfix KB3114025 è installata per impostazione predefinita nelle immagini di Windows Server 2012 R2 presenti in Azure Marketplace.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Nessuna cartella con una lettera di unità in **Computer locale**

Se si esegue il mapping di una condivisione di file di Azure come amministratore tramite il comando net use, la condivisione risulta mancante.

### <a name="cause"></a>Causa

Per impostazione predefinita, Esplora file di Windows non viene eseguito come amministratore. Se si esegue net use da un prompt dei comandi amministrativo, il mapping dell'unità di rete viene eseguito come amministratore. Poiché le unità mappate sono incentrate sull'utente, l'account utente usato per la connessione non ne consente la visualizzazione se tali unità sono state montate con un account utente diverso.

### <a name="solution"></a>Soluzione
Montare la condivisione da una riga di comando senza privilegi di amministratore. In alternativa, è possibile seguire la procedura descritta in [questo argomento di TechNet](https://technet.microsoft.com/library/ee844140.aspx) per configurare il valore del Registro di sistema **EnableLinkedConnections**.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Il comando net use non viene eseguito se l'account di archiviazione contiene una barra

### <a name="cause"></a>Causa

Il comando net use interpreta una barra (/) come un'opzione della riga di comando. Se il nome dell'account utente inizia con una barra, il mapping dell'unità ha esito negativo.

### <a name="solution"></a>Soluzione

Per risolvere il problema, è possibile attenersi a una delle procedure seguenti:

- Eseguire il seguente comando PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Da un file batch, si può eseguire il comando in questo modo:

  `Echo new-smbMapping ... | powershell -command –`

- Racchiudere la chiave tra virgolette doppie, a meno che la barra non sia il primo carattere. Se è il primo carattere, usare la modalità interattiva e immettere la password separatamente oppure rigenerare le chiavi per ottenere una chiave che non inizi con una barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>L'applicazione o il servizio non è in grado di accedere a un'unità di File di Azure montata

### <a name="cause"></a>Causa

Le unità vengono montate per utente. Se l'applicazione o il servizio viene eseguito con un account utente diverso da quello che ha montato l'unità, l'unità non sarà visibile.

### <a name="solution"></a>Soluzione

Usare una delle soluzioni seguenti:

-   Montare l'unità dallo stesso account utente che contiene l'applicazione. Si può usare uno strumento come PsExec.
- Passare il nome e la chiave dell'account di archiviazione nei parametri di nome utente e password del comando net use.
- Usare il comando cmdkey per aggiungere le credenziali in Gestione credenziali. Eseguire il comando da una riga di comando nel contesto dell'account di servizio, tramite un accesso interattivo o mediante RunAs.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mappare direttamente la condivisione, senza usare una lettera di unità mappata. Alcune applicazioni potrebbero non riconnettersi alla lettera di unità in modo corretto, pertanto l'uso del percorso UNC completo è più affidabile. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Dopo aver seguito le istruzioni, è possibile che venga visualizzato il messaggio di errore seguente quando si esegue net use per l'account del servizio di sistema/rete: "Errore di sistema 1312. Una sessione di accesso specificata non esiste. Potrebbe essere già stata terminata." In questo caso, assicurarsi che il nome utente passato a net use includa informazioni di dominio (ad esempio: "[nome account archiviazione].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Errore "You are copying a file to a destination that does not support encryption" (La destinazione in cui si sta copiando il file non supporta la crittografia)

Quando un file viene copiato tramite la rete, viene decrittografato nel computer di origine, trasmesso in testo non crittografato e crittografato nuovamente nella destinazione. Tuttavia, quando si prova a copiare un file crittografato potrebbe essere visualizzato l'errore seguente: "La destinazione in cui si sta copiando il file non supporta la crittografia."

### <a name="cause"></a>Causa
Questo problema può verificarsi se si usa EFS (Encrypting File System). I file crittografati con BitLocker possono essere copiati in File di Azure. Tuttavia, File di Azure non supporta NTFS EFS.

### <a name="workaround"></a>Soluzione alternativa
Per copiare un file tramite rete, è necessario prima decrittografarlo. Usare uno dei metodi seguenti:

- Usare il comando **copy /d**. Questo consente di salvare i file crittografati come file decrittografati nella destinazione.
- Impostare la chiave del Registro di sistema seguente:
  - Percorso = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo valore = DWORD
  - Nome = CopyFileAllowDecryptedRemoteDestination
  - Valore = 1

Si noti che l'impostazione della chiave del Registro di sistema influisce su tutte le operazioni di copia eseguite nelle condivisioni di rete.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumerazione lenta di file e cartelle

### <a name="cause"></a>Causa

Questo problema può verificarsi se nel computer client non è disponibile una quantità di cache sufficiente per le directory di grandi dimensioni.

### <a name="solution"></a>Soluzione

Per risolvere questo problema, modificare il valore del Registro di sistema **DirectoryCacheEntrySizeMax** per consentire la memorizzazione nella cache di elenchi di directory di maggiori dimensioni nel computer client:

- Percorso: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Nome valore: DirectoryCacheEntrySizeMax 
- Tipo di valore: DWORD
 
 
È ad esempio possibile impostare questo valore su 0x100000 e verificare se si riscontra un miglioramento delle prestazioni.


## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
