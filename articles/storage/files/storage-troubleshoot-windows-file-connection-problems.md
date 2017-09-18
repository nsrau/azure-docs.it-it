---
title: Risolvere i problemi di archiviazione file di Azure in Windows | Microsoft Docs
description: Risoluzione dei problemi di archiviazione file di Azure in Windows
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 0e3bbf5ad2ae9cda72876af6bdf880e3aa4f63ac
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---
# <a name="troubleshoot-azure-file-storage-problems-in-windows"></a>Risolvere i problemi di archiviazione file di Azure in Windows

Questo articolo elenca i problemi comuni correlati all'archiviazione file di Microsoft Azure quando si effettua la connessione da client Windows. L'articolo descrive anche le possibili cause e risoluzioni per tali problemi. Oltre ai passaggi di risoluzione dei problemi in questo articolo, si può anche usare [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) per verificare che l'ambiente client Windows abbia prerequisiti corretti. AzFileDiagnostics automatizza il rilevamento della maggior parte dei sintomi indicati in questo articolo e consente di configurare l'ambiente in modo da ottenere prestazioni ottimali. È anche possibile trovare queste informazioni nell'articolo che illustra come [risolvere i problemi delle condivisioni File di Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) e indica i passaggi per risolvere problemi di connessione, mapping e montaggio delle condivisioni file di Azure.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Errore 53, Errore 67 o Errore 87 quando si prova a montare o smontare una condivisione file di Azure

Quando si prova a montare una condivisione file da locale o da un data center diverso, potrebbero verificarsi gli errori seguenti:

- Errore di sistema 53. Impossibile trovare il percorso di rete.
- Errore di sistema 67. Impossibile trovare il nome della rete.
- Errore di sistema 87. Parametro non corretto.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canale di comunicazione non crittografato

Per motivi di sicurezza, le connessioni alle condivisioni file di Azure vengono bloccate se il canale di comunicazione non è crittografato e il tentativo di connessione non viene effettuato dallo stesso data center in cui si trovano tali condivisioni. La crittografia del canale di comunicazione è disponibile solo se il sistema operativo del client dell'utente supporta la crittografia SMB.

Windows 8, Windows Server 2012 e le versioni successive di ciascuno dei due sistemi operativi negoziano richieste comprendenti SMB 3.0, che supporta la crittografia.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Connettersi da un client che soddisfa uno dei requisiti seguenti:

- Soddisfa i requisiti di Windows 8 e Windows Server 2012 o versioni successive
- Effettua la connessione da una macchina virtuale nello stesso data center dell'account di archiviazione di Azure usato per la condivisione file di Azure

### <a name="cause-2-port-445-is-blocked"></a>Causa 2: la porta 445 è bloccata

L'errore di sistema 53 o 67 può verificarsi se la comunicazione in uscita dalla porta 445 verso un data center di archiviazione file di Azure è bloccata. Passare a [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

Per comprendere se l'errore di sistema 53 viene visualizzato per questo motivo, è possibile eseguire una query sull'endpoint TCP:445 mediante Portqry. Se l'endpoint TCP:445 risulta filtrato, la porta TCP è bloccata. Di seguito è fornito un esempio di query:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se la porta TCP 445 è bloccata da una regola del percorso di rete, verrà visualizzato l'output seguente:

  `TCP port 445 (microsoft-ds service): FILTERED`

Per altre informazioni sull'uso di Portqry, vedere [Descrizione dell'utilità della riga di comando Portqry.exe](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Collaborare con il reparto IT per aprire la porta 445 in uscita verso gli [intervalli IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>Causa 3: è abilitata la comunicazione NTLMv1

L'errore di sistema 53 o 87 può verificarsi se sul client è abilitata la comunicazione NTLMv1. L'archiviazione file di Azure supporta solo l'autenticazione NTLMv2. Con la comunicazione NTLMv1 abilitata, il client è meno sicuro. Di conseguenza, la comunicazione viene bloccata per l'archiviazione file di Azure. 

Per determinare se questa è la causa dell'errore, assicurarsi che la sottochiave seguente del Registro di sistema sia impostata su 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Per altre informazioni, vedere l'argomento [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) su TechNet.

### <a name="solution-for-cause-3"></a>Soluzione per la causa 3

Ripristinare **LmCompatibilityLevel** sul valore predefinito 3 nella sottochiave seguente del Registro di sistema:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Errore 1816 "Non c'è abbastanza disponibilità per elaborare il comando" quando si esegue la copia in una condivisione file di Azure

### <a name="cause"></a>Causa

L'errore 1816 si verifica quando si raggiunge il limite massimo di handle aperti simultanei consentito per un file nel computer in cui si sta montando la condivisione file.

### <a name="solution"></a>Soluzione

Chiudere alcuni degli handle aperti simultaneamente per ridurne il numero e quindi riprovare. Per altre informazioni, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-file-storage-in-windows"></a>Rallentamento della copia del file da e verso l'archiviazione file di Azure in Windows

Si potrebbe verificare un rallentamento delle prestazioni quando si prova a trasferire file nel servizio File di Azure.

- In assenza di un requisito minimo specifico per la dimensione di I/O, è consigliabile usare 1 MB per assicurare prestazioni ottimali.
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
## <a name="application-or-service-cannot-access-a-mounted-azure-file-storage-drive"></a>L'applicazione o il servizio non può accedere a un'unità di archiviazione file di Azure montata

### <a name="cause"></a>Causa

Le unità vengono montate per utente. Se l'applicazione o il servizio viene eseguito con un account utente diverso da quello che ha montato l'unità, l'unità non sarà visibile.

### <a name="solution"></a>Soluzione

Usare una delle soluzioni seguenti:

-   Montare l'unità dallo stesso account utente che contiene l'applicazione. Si può usare uno strumento come PsExec.
- Passare il nome e la chiave dell'account di archiviazione nei parametri di nome utente e password del comando net use.

Dopo aver seguito le istruzioni, è possibile che venga visualizzato il messaggio di errore seguente quando si esegue net use per l'account del servizio di sistema/rete: "Errore di sistema 1312. Una sessione di accesso specificata non esiste. Potrebbe essere già stata terminata." In questo caso, assicurarsi che il nome utente passato a net use includa informazioni di dominio (ad esempio: "[nome account archiviazione].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Errore "You are copying a file to a destination that does not support encryption" (La destinazione in cui si sta copiando il file non supporta la crittografia)

Quando un file viene copiato tramite la rete, viene decrittografato nel computer di origine, trasmesso in testo non crittografato e crittografato nuovamente nella destinazione. Tuttavia, quando si prova a copiare un file crittografato potrebbe essere visualizzato l'errore seguente: "La destinazione in cui si sta copiando il file non supporta la crittografia."

### <a name="cause"></a>Causa
Questo problema può verificarsi se si usa EFS (Encrypting File System). I file crittografati con BitLocker possono essere copiati nell'archiviazione file di Azure. Tuttavia, l'archiviazione file di Azure non supporta NTFS EFS.

### <a name="workaround"></a>Soluzione alternativa
Per copiare un file tramite rete, è necessario prima decrittografarlo. Usare uno dei metodi seguenti:

- Usare il comando **copy /d**. Questo consente di salvare i file crittografati come file decrittografati nella destinazione.
- Impostare la chiave del Registro di sistema seguente:
  - Percorso = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo valore = DWORD
  - Nome = CopyFileAllowDecryptedRemoteDestination
  - Valore = 1

Si noti che l'impostazione della chiave del Registro di sistema influisce su tutte le operazioni di copia eseguite nelle condivisioni di rete.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

