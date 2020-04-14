---
title: Risolvere i problemi di File di Azure in Windows | Microsoft Docs
description: Risoluzione dei problemi di File di Azure in Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 3237fe7d87ad058f255d1c77cb6d814bcd1c292e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262248"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Risolvere i problemi di File di Azure in Windows

Questo articolo elenca i problemi comuni correlati a File di Microsoft Azure quando si effettua la connessione da client Windows. L'articolo descrive anche le possibili cause e risoluzioni per tali problemi. Oltre alla procedura di risoluzione dei problemi descritta in questo articolo, è anche possibile usare [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) per assicurarsi che l'ambiente client Windows disponga dei prerequisiti corretti. AzFileDiagnostics automatizza il rilevamento della maggior parte dei sintomi indicati in questo articolo e consente di configurare l'ambiente in modo da ottenere prestazioni ottimali. Queste informazioni sono disponibili anche nello strumento di risoluzione dei problemi delle condivisioni File di Azure che illustra i passaggi per facilitare la connessione/mapping/montaggio delle condivisioni File di Azure.You can also find this information in the [Azure Files shares troubleshooter](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) that provides steps to assist you with problems connecting/mapping/mounting Azure Files shares.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Errore 5 durante il montaggio di una condivisione file di Azure

Quando si tenta di montare una condivisione file, è possibile che si riceva l'errore seguente:

- Errore di sistema 5. Accesso negato.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canale di comunicazione non crittografato

Per motivi di sicurezza, le connessioni alle condivisioni file di Azure vengono bloccate se il canale di comunicazione non è crittografato e il tentativo di connessione non viene effettuato dallo stesso data center in cui si trovano tali condivisioni. Le connessioni non crittografate all'interno dello stesso data center possono essere bloccate se l'impostazione [Trasferimento sicuro obbligatorio](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) è abilitata nell'account di archiviazione. Un canale di comunicazione crittografato è disponibile solo se il sistema operativo del client dell'utente supporta la crittografia SMB.

Windows 8, Windows Server 2012 e le versioni successive di ciascuno dei due sistemi operativi negoziano richieste comprendenti SMB 3.0, che supporta la crittografia.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

1. Eseguire la connessione da un client che supporta la crittografia SMB (Windows 8, Windows Server 2012 o versioni successive) o da una macchina virtuale nello stesso data center dell'account di archiviazione di Azure usato per la condivisione file di Azure.
2. Verificare che l'impostazione [Trasferimento sicuro necessario](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) sia disabilitata nell'account di archiviazione se il client non supporta la crittografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: Le regole della rete virtuale o del firewall sono abilitate nell'account di archiviazione 

Se nell'account di archiviazione sono configurate regole di rete virtuale o del firewall, verrà negato l'accesso al traffico di rete a meno che all'indirizzo IP o alla rete virtuale client sia consentito l'accesso.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Causa 3: Le autorizzazioni a livello di condivisione non sono corrette quando si utilizza l'autenticazione basata sull'identità

Se gli utenti accedono alla condivisione file di Azure usando l'autenticazione di Active Directory (AD) o Azure Active Directory Domain Services (Azure AD DS), l'accesso alla condivisione file avrà esito negativo con errore "Accesso negato" se le autorizzazioni a livello di condivisione non sono corrette. 

### <a name="solution-for-cause-3"></a>Soluzione per la causa 3

Per aggiornare le autorizzazioni a livello di condivisione, vedere Assegnare autorizzazioni di [accesso a un'identità.](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Errore 53, Errore 67 o Errore 87 quando si prova a montare o smontare una condivisione file di Azure

Quando si prova a montare una condivisione file da locale o da un data center diverso, potrebbero verificarsi gli errori seguenti:

- Errore di sistema 53. Impossibile trovare il percorso di rete.
- Errore di sistema 67. Impossibile trovare il nome della rete.
- Errore di sistema 87. Parametro non corretto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: La porta 445 è bloccata

L'errore di sistema 53 o 67 può verificarsi se la comunicazione in uscita dalla porta 445 verso un data center di File di Azure è bloccata. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

È possibile verificare se la porta 445 è bloccata dal firewall o dai vincoli dell'ISP con lo strumento [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) o il cmdlet `Test-NetConnection`. 

Per usare `Test-NetConnection` il cmdlet, è necessario installare il modulo di Azure PowerShell, vedere Installare il [modulo di Azure PowerShell](/powershell/azure/install-Az-ps) per altre informazioni. Ricordarsi di sostituire `<your-storage-account-name>` e `<your-resource-group-name>` con i nomi pertinenti per il proprio account di archiviazione.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Se la connessione ha avuto esito positivo, verrà visualizzato l'output seguente:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Il comando precedente restituisce l'indirizzo IP corrente dell'account di archiviazione. Non è garantito che tale indirizzo IP rimanga invariato. Potrebbe cambiare in qualsiasi momento. Non includere l'indirizzo IP come hardcoded in script o nella configurazione di un firewall.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

#### <a name="solution-1---use-azure-file-sync"></a>Soluzione 1: usare Sincronizzazione file di Azure
Sincronizzazione file di Azure può trasformare Windows Server locale in una cache rapida della condivisione file di Azure.Azure File Sync can transform your on-premises Windows Server into a quick cache of your Azure file share. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Sincronizzazione file di Azure funziona sulla porta 443 e può quindi essere usata come soluzione alternativa per accedere ai file di Azure dai client con la porta 445 bloccata. [Informazioni su come configurare Sincronizzazione file](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers)di Azure.

#### <a name="solution-2---use-vpn"></a>Soluzione 2: usare VPN
Configurando una VPN per il tuo account di archiviazione specifico, il traffico passerà attraverso un tunnel sicuro anziché su Internet. Seguire le [istruzioni per configurare VPN](storage-files-configure-p2s-vpn-windows.md) per accedere ai file di Azure da Windows.Follow the instructions to setup VPN to access Azure Files from Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Soluzione 3: sbloccare la porta 445 con l'aiuto dell'amministratore IT/ISP
Collaborare con il reparto IT o l'ISP per aprire la porta 445 in uscita verso [gli intervalli IP](https://www.microsoft.com/download/details.aspx?id=41653)di Azure.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Soluzione 4: usare strumenti basati sull'API REST come Storage Explorer/PowerShell
File di Azure supporta anche REST oltre a SMB. L'accesso REST funziona sulla porta 443 (tcp standard). Esistono vari strumenti che vengono scritti utilizzando l'API REST che consentono l'esperienza dell'interfaccia utente avanzata. [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) è uno di questi. [Scaricare e installare Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e connettersi alla condivisione file supportata da File di Azure.Download and Install Storage Explorer and connect to your file share backed by Azure Files. È anche possibile usare [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) che anche l'API REST dell'utente.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 è abilitato

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

Chiudere alcuni degli handle aperti simultaneamente per ridurne il numero e quindi riprovare. Per altre informazioni, vedere Elenco di controllo [per le prestazioni e la scalabilità di Archiviazione di Microsoft Azure.For](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)more information, see Microsoft Azure Storage performance and scalability checklist .

Per visualizzare gli handle aperti per una condivisione file, una directory o un file, utilizzare il cmdlet [PowerShell Get-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle)  

Per chiudere gli handle aperti per una condivisione file, una directory o un file, utilizzare il cmdlet di PowerShell [Close-AzStorageFileHandle.](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle)

> [!Note]  
> I cmdlet Get-AzStorageFileHandle e Close-AzStorageFileHandle sono inclusi nel modulo Az PowerShell versione 2.4 o successiva. Per installare il modulo Az PowerShell più recente, vedere [Installare il modulo di Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Errore "Nessun accesso" quando si tenta di accedere o eliminare una condivisione file di AzureError "No access" when you try to access or delete an Azure File Share  
Quando si tenta di accedere o eliminare una condivisione file di Azure nel portale, è che venga visualizzato il seguente errore:

Nessun accesso  
Codice di errore: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: le regole della rete virtuale o del firewall sono abilitate nell'account di archiviazione

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: l'account utente non ha accesso all'account di archiviazione

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Passare all'account di archiviazione in cui si trova la condivisione file di Azure, fare clic su **Controllo di accesso (IAM)** e verificare che l'account utente abbia accesso all'account di archiviazione. Per altre informazioni, vedere [Come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Impossibile eliminare un file o una directory in una condivisione file di Azure
Quando si tenta di eliminare un file, è che venga visualizzato il seguente errore:

La risorsa specificata è stata contrassegnata per l'eliminazione da un client SMB.

### <a name="cause"></a>Causa
Questo problema si verifica in genere se il file o la directory dispone di un handle aperto. 

### <a name="solution"></a>Soluzione

Se i client SMB hanno chiuso tutti gli handle aperti e il problema continua a verificarsi, eseguire le operazioni seguenti:

- Utilizzare il cmdlet [PowerShell Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) per visualizzare gli handle aperti.

- Utilizzare il cmdlet di PowerShell [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) per chiudere gli handle aperti. 

> [!Note]  
> I cmdlet Get-AzStorageFileHandle e Close-AzStorageFileHandle sono inclusi nel modulo Az PowerShell versione 2.4 o successiva. Per installare il modulo Az PowerShell più recente, vedere [Installare il modulo di Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Rallentamento della copia del file da e verso File di Azure in Windows

Si potrebbe verificare un rallentamento delle prestazioni quando si prova a trasferire file nel servizio File di Azure.

- In assenza di un requisito minimo specifico per la dimensione di I/O, è consigliabile usare 1 MiB per assicurare prestazioni ottimali.
-   Se si conosce la dimensione finale del file che si vuole estendere con operazioni di scrittura e il software non ha problemi di compatibilità se la parte finale del file non ancora scritta contiene zeri, impostare le dimensioni del file in fase preliminare anziché lasciare che ogni operazione di scrittura venga considerata un'estensione.
-   Usare il metodo di copia corretto:
    -   Usare [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per qualsiasi trasferimento tra due condivisioni file.
    -   Usare [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) tra condivisioni file in un computer locale.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerazioni per Windows 8.1 o Windows Server 2012 R2

Per i client che eseguono Windows 8.1 o Windows Server 2012 R2, assicurarsi che sia installata la hotfix [KB3114025](https://support.microsoft.com/help/3114025). Questa hotfix consente di migliorare le prestazioni durante la creazione e la chiusura degli handle.

Per verificare se la hotfix è stata installata, è possibile eseguire lo script seguente:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se la hotfix è installata, viene visualizzato l'output seguente:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Da dicembre 2015 la hotfix KB3114025 è installata per impostazione predefinita nelle immagini di Windows Server 2012 R2 presenti in Azure Marketplace.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nessuna cartella con una lettera di unità in "Risorse del computer" o "Questo PC"

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

- Eseguire il comando PowerShell seguente:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

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
- Usare il comando cmdkey per aggiungere le credenziali in Gestione credenziali. Eseguire questa operazione da una riga di comando nel contesto `runas`dell'account del servizio, tramite un account di accesso interattivo o utilizzando .
  
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
Per copiare un file tramite rete, è necessario prima decrittografarlo. Utilizzare una delle seguenti modalità:

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

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Errore AadDsTenantNotFound nell'abilitazione dell'autenticazione di Azure Active Directory Domain Service (AAD DS) per i file di Azure "Impossibile individuare i tenant attivi con ID aad-tenant-id tenant tenant"

### <a name="cause"></a>Causa

Errore AadDsTenantNotFound si verifica quando si tenta di abilitare l'autenticazione di Servizi di dominio [Azure Active Directory (Azure AD DS)](storage-files-identity-auth-active-directory-domain-service-enable.md) in File di Azure in un account di archiviazione in cui il servizio di [dominio AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) non viene creato nel tenant AAD della sottoscrizione associata.  

### <a name="solution"></a>Soluzione

Abilitare AAD DS nel tenant della sottoscrizione in cui è stato distribuito l'account di archiviazione. Sono necessari privilegi di amministratore del tenant di AAD per creare un dominio gestito. Se non si è l'amministratore del tenant di Azure AD, contattare l'amministratore e seguire le istruzioni dettagliate per [Abilitare Azure Active Directory Domain Services tramite il portale di Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Errore 'Errore di sistema 1359 si è verificato. Un errore interno ricevuto tramite accesso SMB alle condivisioni file con l'autenticazione di Azure Active Directory Domain Service (AAD DS) abilitata

### <a name="cause"></a>Causa

Errore 'Errore di sistema 1359 si è verificato. Un errore interno' si verifica quando si tenta di connettersi alla condivisione file con l'autenticazione di DS AAD abilitata su un DS AAD con nome DNS di dominio che inizia con un carattere numerico. Ad esempio, se il nome DNS del dominio AAD DS è "1domain", verrà visualizzato questo errore quando si tenta di montare la condivisione file utilizzando le credenziali AAD. 

### <a name="solution"></a>Soluzione

Attualmente, è possibile ridistribuire il dominio AAD utilizzando un nuovo nome DNS di dominio che si applica con le regole seguenti:
- I nomi non possono iniziare con un carattere numerico.
- I nomi devono avere una lunghezza compresa tra 3 e 63 caratteri.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
