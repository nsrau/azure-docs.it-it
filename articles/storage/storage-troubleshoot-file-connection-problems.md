---
title: Risoluzione dei problemi di archiviazione file di Azure | Microsoft Docs
description: Risoluzione dei problemi di archiviazione file di Azure
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: c62f8d077906ce8ad1b5501864a21ee369b2314a
ms.lasthandoff: 04/07/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>Risoluzione dei problemi di archiviazione file Azure
Questo articolo elenca i problemi comuni correlati all'archiviazione file di Microsoft Azure quando si effettua la connessione da client Windows e Linux. L'articolo descrive inoltre le possibili cause e risoluzioni per tali problemi.

**Problemi generali (comuni a client Windows e Linux)**

* [Errore di quota durante il tentativo di aprire un file](#quotaerror)
* [Rallentamento delle prestazioni quando si accede all'archiviazione file di Azure da Windows o Linux](#slowboth)
* [Come tracciare le operazioni di lettura e scrittura in archiviazione file di Azure](#traceop)

**Problemi relativi a client Windows**

* [Rallentamento delle prestazioni quando si accede all'archiviazione file di Azure da Windows 8.1 o Windows Server 2012 R2](#windowsslow)
* [Errore 53 durante il tentativo di montare una condivisione file di Azure](#error53)
* [Errore 87 indicante che il parametro non è corretto durante il tentativo di montare una condivisione file di Azure](#error87)
* [Il comando net use è stato eseguito, ma la condivisione file di Azure montata o la lettera dell'unità non è visibile nell'interfaccia utente di Esplora risorse](#netuse)
* [L'account di archiviazione contiene "/" e il comando net use non viene eseguito](#slashfails)
* [L'applicazione o il servizio non può accedere all'unità File di Azure montata](#accessfiledrive)
* [Consigli aggiuntivi per ottimizzare le prestazioni](#additional)
* [Errore "You are copying a file to a destination that does not support encryption" (La destinazione in cui si sta copiando il file non supporta la crittografia) quando si caricano o si copiano file in File di Azure](#encryption)

**Problemi relativi a client Linux**

* [Errore di I/O intermittente: "Host is down (Error 112)" (Host inattivo - Errore 112) su condivisioni file esistenti o blocco della shell quando si eseguono comandi list sul punto di montaggio](#error112)
* [Errore di montaggio 115 quando si tenta di montare File di Azure sulla macchina virtuale Linux](#error15)
* [Nella condivisione file di Azure montata nella macchina virtuale Linux viene riscontrato un rallentamento delle prestazioni](#delayproblem)
* [Errore di montaggio (11): risorsa temporaneamente non disponibile quando si esegue il montaggio sul kernel di Ubuntu 4.8 o versione successiva](#ubuntumounterror)

<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>Errore di quota durante il tentativo di aprire un file
In Windows si ricevono messaggi di errore simili ai seguenti:

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

In Linux si ricevono messaggi di errore simili ai seguenti:

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>Causa
Il problema si verifica perché si è raggiunto il limite superiore di handle aperti simultaneamente consentito per un file.

### <a name="solution"></a>Soluzione
Chiudere alcuni degli handle aperti simultaneamente per ridurne il numero e quindi riprovare. Per altre informazioni, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](storage-performance-checklist.md).

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Rallentamento delle prestazioni quando si accede all'archiviazione file di Azure da Windows o Linux
* In assenza di un requisito minimo specifico per la dimensione di I/O, è consigliabile usare 1 MB per assicurare prestazioni ottimali.
* Se si conosce la dimensione finale del file che si vuole estendere con operazioni di scrittura e il software non presenta problemi di compatibilità se la parte finale del file non ancora scritta contiene zeri, impostare la dimensione del file in fase preliminare anziché lasciare che ogni operazione di scrittura venga considerata un'estensione.
* Usare il metodo di copia corretto:
      * Usare AZCopy per i trasferimenti tra due condivisioni file. Per altri dettagli, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy).
      * Usare Robocopy per una condivisione file in un computer locale. Per altri dettagli, vedere [Multi-threaded robocopy for faster copies](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) (Robocopy multithread per le copie più rapide).
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Rallentamento delle prestazioni quando si accede all'archiviazione file di Azure da Windows 8.1 o Windows Server 2012 R2
Per i client che eseguono Windows 8.1 o Windows Server 2012 R2, assicurarsi che sia installata la hotfix [KB3114025](https://support.microsoft.com/kb/3114025). Questa hotfix consente di migliorare le prestazioni durante la creazione e la chiusura degli handle.

Per verificare se la hotfix è stata installata, è possibile eseguire lo script seguente:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se la hotfix è installata, viene visualizzato l'output seguente:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> Da dicembre 2015 la hotfix KB3114025 è installata per impostazione predefinita nelle immagini di Windows Server 2012 R2 presenti in Azure Marketplace.
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>Come tracciare le operazioni di lettura e scrittura in archiviazione file di Azure

[Analizzatore messaggi di Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=44226) è in grado di mostrare la richiesta di un client non crittografata ed esiste una correlazione abbastanza buona tra richieste di trasferimento e transazioni, presupponendo qui il protocollo SMB e non REST.  È tuttavia necessario eseguire questo strumento in ogni client con notevole dispendio di tempo in presenza di numerosi ruoli di lavoro di VM IaaS.

Se si usa l'analisi dei messaggi con ProcMon, è possibile avere un'idea piuttosto precisa del codice dell'applicazione responsabile delle transazioni.

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>Consigli aggiuntivi per ottimizzare le prestazioni
Non creare né aprire mai un file per l'I/O nella cache, che richiede l'accesso in scrittura ma non quello in lettura. In altre parole, quando si chiama **CreateFile()**, è necessario specificare sempre **GENERIC_READ | GENERIC_WRITE** e non soltanto **GENERIC_WRITE**. Un handle di sola scrittura non è in grado di memorizzare nella cache locale operazioni di scrittura di piccole dimensioni, nemmeno se si tratta dell'unico handle aperto per il file. Per questo motivo, in concomitanza con le operazioni di scrittura di piccole dimensioni si verifica una notevole riduzione delle prestazioni. Si noti che la modalità "a" di accesso a CRT **fopen()** consente di aprire un handle di sola scrittura.

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Errore 53" o "Errore 67" quando si prova a montare o smontare una condivisione file di Azure
Questo problema può essere causato dalle circostanze seguenti:

### <a name="cause-1"></a>Causa 1
"Errore di sistema 53. Accesso negato". Per motivi di sicurezza, le connessioni alle condivisioni file di Azure vengono bloccate se il canale di comunicazione non è crittografato e il tentativo di connessione non viene effettuato dalla stessa area di Azure in cui si trovano tali condivisioni. La crittografia del canale di comunicazione non è disponibile se il sistema operativo del client dell'utente non supporta la crittografia SMB. Questa condizione è indicata dal messaggio "Errore di sistema 53. Accesso negato", visualizzato quando un utente tenta di montare una condivisione file da un data center locale o diverso. Windows 8, Windows Server 2012 e le versioni successive di ciascuno dei due sistemi operativi negoziano una richiesta comprendente SMB 3.0, che supporta la crittografia.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1
Effettuare la connessione da un client che soddisfa i requisiti di Windows 8, Windows Server 2012 o versioni successive o che si connette da una macchina virtuale presente nella stessa area di Azure dell'account di archiviazione di Azure usato per la condivisione file di Azure.

### <a name="cause-2"></a>Causa 2
Durante il montaggio di una condivisione file di Azure può verificarsi un errore di sistema 53 o 67 se la comunicazione in uscita dalla porta 445 verso l'area di Azure di File di Azure è bloccata. Fare clic [qui](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

Comcast e alcune organizzazioni IT bloccano la porta. Per comprendere se l'errore di sistema 53 viene visualizzato per questo motivo, è possibile eseguire una query sull'endpoint TCP:445 mediante Portqry. Se l'endpoint TCP:445 risulta filtrato, la porta TCP è bloccata. Di seguito è fornito un esempio di query:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se l'endpoint TCP 445 è bloccato da una regola del percorso di rete, verrà visualizzato l'output seguente:

**TCP port 445 (microsoft-ds service): FILTERED**

Per altre informazioni sull'uso di Portqry, vedere [Descrizione dell'utilità della riga di comando Portqry.exe](https://support.microsoft.com/kb/310099).

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2
Collaborare con l'organizzazione IT per aprire la porta 445 verso [intervalli IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

<a id="error87"></a>
### <a name="cause-3"></a>Causa 3
L'errore di sistema 53 o 87 può essere visualizzato se sul client è abilitata la comunicazione NTLMv1. Con la comunicazione NTLMv1 abilitata, il client è meno sicuro. Di conseguenza, la comunicazione verrà bloccata per File di Azure. Per verificare se questa è la causa dell'errore, assicurarsi che la sottochiave seguente del Registro di sistema sia impostata su 3:

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

Per altre informazioni, vedere l'argomento [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) su TechNet.

### <a name="solution-for-cause-3"></a>Soluzione per la causa 3
Per risolvere questo problema, ripristinare il valore predefinito 3 per LmCompatibilityLevel nella chiave del Registro di sistema HKLM\SYSTEM\CurrentControlSet\Control\Lsa.

File di Azure supporta solo l'autenticazione NTLMv2. Assicurarsi che ai client venga applicato Criteri di gruppo per evitare la visualizzazione dell'errore. Questa operazione viene anche considerata una procedura consigliata ai fini della sicurezza. Per altre informazioni, vedere [How to configure clients to use NTLMv2 using Group Policy](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx) (Come configurare client per l'uso di NTLMv2 mediante Criteri di gruppo).

L'impostazione dei criteri consigliata è **Invia solo risposta NTLMv2**, corrispondente al valore del Registro di sistema 3. I client usano solo l'autenticazione NTLMv2 e la sicurezza di sessione NTLMv2, se supportata dal server. I controller di dominio accettano l'autenticazione LM, NTLM e NTLMv2.

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Il comando net use è stato eseguito, ma la condivisione file di Azure montata in Esplora risorse non è visibile
### <a name="cause"></a>Causa
Per impostazione predefinita, Esplora risorse non viene eseguito come amministratore. Se si esegue **net use** da un prompt dei comandi di amministratore, il mapping dell'unità di rete viene effettuato come amministratore. Poiché le unità mappate sono incentrate sull'utente, l'account utente usato per la connessione non ne consente la visualizzazione se tali unità sono state montate con un account utente diverso.

### <a name="solution"></a>Soluzione
Montare la condivisione da una riga di comando senza privilegi di amministratore. In alternativa, è possibile seguire la procedura descritta in [questo argomento di TechNet](https://technet.microsoft.com/library/ee844140.aspx) per configurare il valore del Registro di sistema **EnableLinkedConnections**.

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>L'account di archiviazione contiene "/" e il comando net use non viene eseguito
### <a name="cause"></a>Causa
Quando il comando **net use** viene eseguito al prompt dei comandi (cmd.exe), viene analizzato aggiungendo "/" come opzione della riga di comando. Tale aggiunta impedisce di eseguire il mapping delle unità.

### <a name="solution"></a>Soluzione
Per risolvere il problema, è possibile attenersi a una delle procedure seguenti:

•    Usare il comando di PowerShell seguente:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

Da un file batch l'operazione può essere eseguita nel modo seguente:

`Echo new-smbMapping ... | powershell -command –`

•    Racchiudere la chiave tra virgolette doppie, a meno che "/" non sia il primo carattere. Se è il primo carattere, usare la modalità interattiva e immettere la password separatamente oppure rigenerare le chiavi per ottenere una chiave che non inizi con il carattere barra (/).

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>L'applicazione o il servizio non può accedere all'unità File di Azure montata
### <a name="cause"></a>Causa
Le unità vengono montate per utente. Se l'applicazione o il servizio viene eseguito con un account utente diverso, l'unità non sarà visibile.

### <a name="solution"></a>Soluzione
Montare l'unità dallo stesso account utente con cui viene eseguita l'applicazione. Questa operazione può essere eseguita mediante strumenti quali psexec.

Un'altra opzione per **net use** consiste nel passare il nome e la chiave dell'account di archiviazione nei parametri di nome utente e password del comando **net use**.

Dopo aver seguito le istruzioni, è possibile che venga visualizzato il messaggio seguente: "Errore di sistema 1312. Una sessione di accesso specificata non esiste. Potrebbe essere già stata terminata"quando si esegue **net use** per l'account di sistema o del servizio di rete. In questo caso, assicurarsi che il nome utente passato a **net use** includa informazioni di dominio (ad esempio: "[nome account archiviazione].file.core.windows.net").

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Errore "You are copying a file to a destination that does not support encryption" (La destinazione in cui si sta copiando il file non supporta la crittografia)
### <a name="cause"></a>Causa
I file crittografati con BitLocker possono essere copiati in File di Azure. Tuttavia, il sistema di archiviazione file non supporta NTFS EFS. Di conseguenza, è probabile che in questo caso venga usato EFS. Se i file di cui si dispone sono crittografati tramite EFS, è possibile che non venga eseguita la copia nell'archiviazione file, a meno che il comando di copia non sia in grado di decrittografare un file copiato.

### <a name="workaround"></a>Soluzione alternativa
Per copiare un file nell'archiviazione file, è necessario prima decrittografarlo. A questo scopo, è possibile usare uno dei metodi seguenti:

•    Usare **copy /d**.

•    Impostare la chiave del Registro di sistema seguente:

* Percorso = HKLM\Software\Policies\Microsoft\Windows\System
* Tipo di valore = DWORD
* Nome = CopyFileAllowDecryptedRemoteDestination
* Valore = 1

Si noti, tuttavia, che l'impostazione della chiave del Registro di sistema influisce su tutte le operazioni di copia nelle condivisioni di rete.

<a id="error112"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>"Host is down (Error 112)" (Host inattivo - Errore 112) su condivisioni file esistenti o blocco della shell quando si eseguono comandi list sul punto di montaggio
### <a name="cause"></a>Causa
Questo errore si verifica quando il client Linux rimane inattivo per un periodo di tempo prolungato. Quando rimane a lungo inattivo, il client si disconnette provocando il timeout della connessione. 

La connessione può rimanere inattiva per vari motivi. È possibile ad esempio che si verifichino errori di comunicazione che impediscono di ristabilire una connessione TCP al server quando viene usata l'opzione di montaggio "soft", ossia l'impostazione predefinita.

Il problema può anche essere dovuto ad alcune correzioni per la riconnessione che non sono presenti nei kernel precedenti.

### <a name="solution"></a>Soluzione

Se si specifica un'opzione di montaggio "hard", il client viene forzato ad attendere fino a quando la connessione non è stabilita o interrotta in modo esplicito; può essere usata per evitare gli errori causati dai timeout di rete. Tuttavia, gli utenti devono essere consapevoli che ciò potrebbe provocare attese indefinite e devono pertanto poter gestire l'arresto di una connessione se necessario.

Questo problema di riconnessione nel kernel Linux è stato corretto come parte dei set di modifiche seguenti:

* [Fix reconnect to not defer smb3 session reconnect long after socket reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) (Correggere la funzionalità di riconnessione in modo da non rinviare la riconnessione della sessione smb3 a molto tempo dopo la riconnessione del socket)

* [Call echo service immediately after socket reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7) (Chiamare il servizio echo immediatamente dopo la riconnessione del socket)

* [CIFS: Fix a possible memory corruption during reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b) (CIFS: correggere un possibile danneggiamento della memoria dopo la riconnessione)

* [CIFS: Fix a possible double locking of mutex during reconnect - for kernels v4.9 and higher](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) (CIFS: Correggere un possibile doppio blocco del mutex durante la riconnessione, per i kernel 4.9 e versioni successive) 

È tuttavia possibile che queste modifiche non siano state ancora applicate a tutte le distribuzioni di Linux. Questo è l'elenco dei kernel Linux più diffusi a cui sono state applicate queste e altre correzioni di riconnessione: 4.4.40+ 4.8.16+ 4.9.1+.
È possibile passare alle versioni del kernel consigliate sopra per usare le correzioni più recenti.

### <a name="workaround"></a>Soluzione alternativa
Se non è possibile passare a tali versioni, una soluzione alternativa a questo problema consiste nel mantenere un file nella condivisione file di Azure in cui si eseguono operazioni di scrittura al massimo ogni 30 secondi. Deve trattarsi di un'operazione di scrittura, ad esempio la riscrittura della data di creazione o di modifica nel file. In caso contrario, i risultati verrebbero memorizzati nella cache e l'operazione potrebbe non attivare la riconnessione. 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>Errore di montaggio 115 quando si tenta di montare File di Azure sulla macchina virtuale Linux
### <a name="cause"></a>Causa
Le distribuzioni di Linux non supportano ancora la funzionalità di crittografia disponibile in SMB 3.0. Mancando questa funzionalità, è possibile che in alcune distribuzioni venga visualizzato un messaggio di errore 115 se si verifica il tentativo di montare File di Azure tramite SMB 3.0.

### <a name="solution"></a>Soluzione
Se il client SMB Linux usato non supporta la crittografia, procedere al montaggio di File di Azure tramite SMB 2.1 da una macchina virtuale Linux presente nella stessa area di Azure dell'account di archiviazione file.

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>Nella condivisione file di Azure montata nella macchina virtuale Linux viene riscontrato un rallentamento delle prestazioni

Un motivo del rallentamento delle prestazioni può essere la disattivazione del caching. Per verificare se il caching è abilitato, cercare "cache =".  *cache=none* indica che il caching è disabilitato. Rimontare la condivisione con il comando di montaggio predefinito o aggiungere in modo esplicito l'opzione **cache=strict** al comando di montaggio per verificare se è abilitato il caching predefinito o il caching in modalità "strict".

In alcuni scenari l'opzione di montaggio serverino può far sì che il comando ls esegua stat su ogni voce di directory, con un conseguente calo delle prestazioni quando la directory è di grandi dimensioni. È possibile controllare le opzioni di montaggio nella voce "/etc/fstab":

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

È inoltre possibile verificare se vengono usate le opzioni corrette eseguendo il comando **sudo mount | grep cifs** ed esaminando l'output:

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

Se le opzioni cache=strict o serverino non sono presenti, smontare e montare nuovamente i file di Azure eseguendo il comando mount, come indicato nella [documentazione](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share), e verificare nuovamente la presenza di opzioni corrette nella voce "/etc/fstab".

<a id="ubuntumounterror"></a>
## <a name="mount-error11-resource-temporarily-unavailable-when-mounting-to-ubuntu-48-kernel"></a>Errore di montaggio (11): risorsa temporaneamente non disponibile quando si esegue il montaggio sul kernel di Ubuntu 4.8 o versione successiva

### <a name="cause"></a>Causa
Problema noto nel kernel di Ubuntu 16.10 (v. 4.8) in cui il client dichiara di supportare la crittografia, ma in realtà non è così. 

### <a name="solution"></a>Soluzione
Fino a quando il problema non verrà risolto in Ubuntu 16.10, specificare l'opzione di montaggio "vers=2.1" oppure usare Ubuntu 16.04.
## <a name="learn-more"></a>Altre informazioni
* [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md)
* [Introduzione all'archiviazione file di Azure in Linux](storage-how-to-use-files-linux.md)

