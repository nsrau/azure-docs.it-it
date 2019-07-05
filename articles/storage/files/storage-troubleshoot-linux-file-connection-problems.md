---
title: Risolvere i problemi di File di Azure in Linux | Microsoft Docs
description: Risoluzione dei problemi di File di Azure in Linux
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 97f737c8d1228bd03baf59f2ebe830f715241299
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449852"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Risolvere i problemi di File di Azure in Linux

Questo articolo elenca i problemi comuni correlati a File di Azure quando si effettua la connessione da client Linux. L'articolo descrive anche le possibili cause e risoluzioni per tali problemi. 

Oltre alle procedure di risoluzione dei problemi descritte in questo articolo, si può usare [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) per verificare che il client Linux abbia i prerequisiti corretti. AzFileDiagnostics automatizza il rilevamento della maggior parte dei sintomi descritti in questo articolo. Aiuta a configurare l'ambiente per ottenere prestazioni ottimali. Queste informazioni sono disponibili anche in [Troubleshooter for Azure Files storage problems](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) (Strumento di risoluzione dei problemi di archiviazione di File di Azure). Lo strumento di risoluzione dei problemi fornisce le procedure da eseguire per risolvere i problemi di connessione, mapping e montaggio di condivisioni di File di Azure.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Non è possibile connettersi a o montare una condivisione file di Azure

### <a name="cause"></a>Causa

Le cause comuni di questo problema sono le seguenti:

- Si sta usando un client di distribuzione Linux incompatibile. Si consiglia di usare le distribuzioni Linux seguenti per connettersi a una condivisione file di Azure:

|   | SMB 2.1 <br>(Montaggio in macchine virtuali nella stessa area di Azure) | SMB 3.0 <br>(Montaggio in locale e tra più aree) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Le utilità CIFS (cfs-utils) non sono installate nel client.
- La versione SMB/CIFS minima, ossia la 2.1, non è installata nel client.
- La crittografia SMB 3.0 non è supportata nel client. Nella tabella precedente presenta un elenco delle distribuzioni di Linux il montaggio di tale supporto da on-premise e tra più aree usando la crittografia. Altre distribuzioni richiedono il kernel 4.11 e versioni successive.
- Si sta tentando di connettersi a un account di archiviazione tramite la porta TCP 445, che non è supportata.
- Si sta tentando di connettersi a una condivisione file di Azure da una macchina virtuale di Azure e la macchina virtuale non si trova nella stessa area dell'account di archiviazione.
- Se l'impostazione [Trasferimento sicuro obbligatorio]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) è abilitata nell'account di archiviazione, File di Azure consentirà solo connessioni crittografate con SMB 3.0.

### <a name="solution"></a>Soluzione

Per risolvere il problema, usare lo [strumento di risoluzione dei problemi per gli errori di montaggio di File di Azure in Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Questo strumento:

* Facilita la convalida dell'ambiente di esecuzione del client.
* Rileva la configurazione client incompatibile che causerebbe errori di accesso per File di Azure.
* Fornisce indicazioni specifiche per risolvere i problemi autonomamente.
* Raccoglie le tracce di diagnostica.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Errore di montaggio (13): Accesso negato" quando si monta una condivisione file di Azure

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: canale di comunicazione non crittografato

Per motivi di sicurezza, le connessioni alle condivisioni file di Azure vengono bloccate se il canale di comunicazione non è crittografato e il tentativo di connessione non viene effettuato dallo stesso data center in cui si trovano tali condivisioni. Le connessioni non crittografate all'interno dello stesso data center possono essere bloccate se l'impostazione [Trasferimento sicuro obbligatorio](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) è abilitata nell'account di archiviazione. Un canale di comunicazione crittografato è disponibile solo se il sistema operativo del client dell'utente supporta la crittografia SMB.

Per altre informazioni, vedere [Prerequisiti per il montaggio di una condivisione file di Azure con Linux e il pacchetto cifs-utils](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux#prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package). 

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

1. Eseguire la connessione da un client che supporta la crittografia SMB o da una macchina virtuale nello stesso data center dell'account di archiviazione di Azure usato per la condivisione file di Azure.
2. Se il client non supporta la crittografia SMB, verificare che l'opzione [Trasferimento sicuro obbligatorio](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) sia disabilitata nell'account di archiviazione.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: nell'account di archiviazione sono abilitate regole di rete virtuale o del firewall. 

Se nell'account di archiviazione sono configurate regole di rete virtuale o del firewall, verrà negato l'accesso al traffico di rete a meno che all'indirizzo IP o alla rete virtuale client sia consentito l'accesso.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[accesso negato] Quota disco superata" quando si tenta di aprire un file

In Linux si riceve un messaggio di errore simile al seguente:

**\<nome file > quota disco [autorizzazione negata] ha superato**

### <a name="cause"></a>Causa

È stato raggiunto il limite massimo di handle aperti simultaneamente consentito per un file.

È prevista una quota pari a 2.000 handle aperti su un singolo file. In presenza di 2.000 handle aperti viene visualizzato un messaggio di errore che segnala il raggiungimento della quota.

### <a name="solution"></a>Soluzione

Ridurre il numero di handle aperti simultaneamente chiudendone alcuni e quindi riprovare.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Rallentamento della copia del file da e verso File di Azure in Linux

- In assenza di un requisito minimo specifico per la dimensione di I/O, è consigliabile usare 1 MiB per assicurare prestazioni ottimali.
- Se si conoscono le dimensioni finali del file che si vuole estendere con operazioni di scrittura e il software non presenta problemi di compatibilità se la parte finale del file non ancora scritta contiene zeri, impostare le dimensioni del file in fase preliminare anziché lasciare che ogni operazione di scrittura venga considerata un'estensione.
- Usare il metodo di copia corretto:
    - Usare [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) per i trasferimenti tra due condivisioni file.
    - Con cp parallelo potrebbe migliorare la velocità di copia, il numero di thread dipende il caso d'uso e del carico di lavoro. Questo esempio Usa sei: `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - Strumenti open source terze parti, ad esempio:
        - [Parallelo GNU](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) : Ordina i file e le inserisce in partizioni.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) -Usa Fpart e uno strumento di copia per generare più istanze per migrare i dati da src_dir a dst_url.
        - [Con più](https://github.com/pkolano/mutil) -cp multithread e md5sum basato su GNU coreutils.

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Errore di montaggio (115): L'operazione è in corso" quando si esegue il montaggio di File di Azure usando SMB 3.0

### <a name="cause"></a>Causa

Alcune distribuzioni di Linux non supportano ancora le funzionalità di crittografia disponibili in SMB 3.0. Mancando questa funzionalità, potrebbe essere visualizzato un messaggio di errore 115 se l'utente tenta di montare File di Azure tramite SMB 3.0. SMB 3.0 con crittografia completa è supportato solo quando si usa Ubuntu 16.04 o versioni successive.

### <a name="solution"></a>Soluzione

La funzionalità di crittografia per SMB 3.0 per Linux è stata introdotta nel kernel 4.11. Questa funzionalità consente di montare una condivisione file di Azure da un ambiente locale o da un'area di Azure diversa. Questa funzionalità è inclusa nelle distribuzioni Linux elencate in [minime consigliate le versioni con funzionalità di montaggio corrispondenti (SMB versione 2.1 ed SMB versione 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30). Altre distribuzioni richiedono il kernel 4.11 e versioni successive.

Se il client Linux SMB non supporta la crittografia, montare File di Azure usando SMB 2.1 da una macchina virtuale Linux di Azure presente nello stesso data center della condivisione file. Verificare che l'opzione [Trasferimento sicuro obbligatorio]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) sia disabilitata nell'account di archiviazione. 

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>Errore "Errore di autorizzazione" durante l'esplorazione di una condivisione file di Azure nel portale

Quando si passa alla condivisione file di Azure nel portale, viene visualizzato il messaggio di errore seguente:

Errore di autorizzazione  
L'utente non è autorizzato ad accedere

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 1: l'account utente non ha accesso all'account di archiviazione

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Passare all'account di archiviazione in cui si trova la condivisione file di Azure, fare clic su **Controllo di accesso (IAM)** e verificare che l'account utente abbia accesso all'account di archiviazione. Per altre informazioni, vedere [Come proteggere l'account di archiviazione con il controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac).

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: nell'account di archiviazione sono abilitate regole di rete virtuale o del firewall.

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che le regole di rete virtuale e di firewall siano configurate correttamente nell'account di archiviazione. Per verificare se le regole di rete virtuale o del firewall sono la causa del problema, modificare temporaneamente le impostazioni dell'account di archiviazione per **consentire l'accesso da tutte le reti**. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Rallentamento delle prestazioni in una condivisione file di Azure montata in una VM Linux

### <a name="cause-1-caching"></a>Causa 1: Memorizzazione nella cache

Una possibile causa del rallentamento delle prestazioni è la disattivazione della memorizzazione nella cache. La memorizzazione nella cache può essere utile che se si accede più volte un file, in caso contrario, può essere un sovraccarico. Controllare se si usa la cache prima di disabilitarlo.

### <a name="solution-for-cause-1"></a>Soluzione per la causa 1

Per controllare se la memorizzazione nella cache è disattivata, cercare la voce **cache =** .

**cache=none** indica che la memorizzazione nella cache è disattivata. Eseguire nuovamente il montaggio della condivisione usando il comando di montaggio predefinito o aggiungendo esplicitamente l'opzione **cache=strict** al comando di montaggio per assicurarsi che la modalità di memorizzazione nella cache predefinita o "strict" sia attivata.

In alcuni scenari, l'opzione di montaggio **serverino** può far sì che il comando **ls** esegua stat rispetto a ogni voce di directory. Questo comportamento determina una riduzione delle prestazioni quando si elenca una directory di grandi dimensioni. È possibile controllare le opzioni di montaggio nella voce **/etc/fstab**:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

È anche possibile controllare se vengono usate le opzioni corrette eseguendo il comando **sudo mount | grep cifs** e controllandone l'output. Di seguito è riportato un output di esempio:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Se l'opzione **cache=strict** o **serverino** non è presente, smontare e montare nuovamente File di Azure eseguendo il comando di montaggio dalla [documentazione](../storage-how-to-use-files-linux.md). Verificare quindi di nuovo che la voce **/etc/fstab** disponga delle opzioni corrette.

### <a name="cause-2-throttling"></a>Causa 2: Limitazione

È possibile che si sono verificate la limitazione delle richieste e le richieste vengono inviate a una coda. È possibile verificarlo sfruttando [metriche di archiviazione di Azure in Monitoraggio di Azure](../common/storage-metrics-in-azure-monitor.md).

### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Verificare che l'app si trova all'interno di [file di Azure Ridimensiona destinazioni](storage-files-scale-targets.md#azure-files-scale-targets).

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>I timestamp sono andati persi durante la copia dei file da Windows a Linux

Nelle piattaforme Linux/Unix il comando **cp -p** non riesce se file 1 e file 2 sono di proprietà di utenti diversi.

### <a name="cause"></a>Causa

Il flag force **f** in COPYFILE determina l'esecuzione di **cp -p -f** in Unix. Questo comando non riesce anche a mantenere il timestamp del file di cui non si è proprietari.

### <a name="workaround"></a>Soluzione alternativa

Usare l'account utente di archiviazione per copiare i file:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Non è possibile connettersi a o montare una condivisione file di Azure

### <a name="cause"></a>Causa

Le cause comuni di questo problema sono le seguenti:

- Si sta usando un client di distribuzione Linux incompatibile. Si consiglia di usare le distribuzioni Linux seguenti per connettersi a una condivisione file di Azure:

    |   | SMB 2.1 <br>(Montaggio in macchine virtuali nella stessa area di Azure) | SMB 3.0 <br>(Montaggio in locale e tra più aree) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- Le utilità CIFS (cifs-utils) non sono installate nel client.
- La versione SMB/CIFS minima, ossia la 2.1, non è installata nel client.
- La crittografia SMB 3.0 non è supportata nel client. La crittografia SMB 3.0 è disponibile in Ubuntu 16.4 e versioni successive, insieme a SUSE 12.3 e versioni successive. Altre distribuzioni richiedono il kernel 4.11 e versioni successive.
- Si sta tentando di connettersi a un account di archiviazione tramite la porta TCP 445, che non è supportata.
- Si sta tentando di connettersi a una condivisione file di Azure da una macchina virtuale di Azure e la macchina virtuale non si trova nella stessa area dell'account di archiviazione.
- Se l'impostazione [Trasferimento sicuro obbligatorio]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) è abilitata nell'account di archiviazione, File di Azure consentirà solo connessioni crittografate con SMB 3.0.

### <a name="solution"></a>Soluzione

Per risolvere il problema, usare lo [strumento di risoluzione dei problemi per gli errori di montaggio di File di Azure in Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089). Questo strumento:

* Facilita la convalida dell'ambiente di esecuzione del client.
* Rileva la configurazione client incompatibile che causerebbe errori di accesso per File di Azure.
* Fornisce indicazioni specifiche per risolvere i problemi autonomamente.
* Raccoglie le tracce di diagnostica.


## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: cannot access '&lt;path&gt;': Input/output error (IS: non è possibile accedere a 'percorso': errore di input/output)

Quando si prova a elencare i file in una condivisione file di Azure tramite il comando ls, questo si blocca durante la generazione dell'elenco. Viene visualizzato l'errore seguente:

**ls: cannot access'&lt;path&gt;': Input/output error** (IS: non è possibile accedere a 'percorso': errore di input/output)


### <a name="solution"></a>Soluzione
Aggiornare il kernel di Linux a una delle versioni seguenti che includono una correzione per questo problema:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Tutte le versioni successive o corrispondenti alla 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Cannot create symbolic links - ln: failed to create symbolic link 't': Operation not supported (Impossibile creare collegamenti simbolici - ln: impossibile creare il collegamento simbolico 't': operazione non supportata)

### <a name="cause"></a>Causa
Per impostazione predefinita, il montaggio di condivisioni file di Azure in Linux tramite CIFS non abilita il supporto per i collegamenti simbolici. Viene visualizzato un errore simile al seguente:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Soluzione
Il client Linux CIFS non supporta la creazione di collegamenti simbolici in stile Windows tramite il protocollo SMB 2 o 3. Il client Linux supporta attualmente un altro stile di collegamenti simbolici, detto [Minshall+French](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks), per le operazioni create e follow. I clienti che necessitano di collegamenti simbolici possono usare l'opzione di montaggio "mfsymlinks". L'uso di "mfsymlinks" è consigliato perché è anche il formato usato dai Mac.

Per usare i collegamenti simbolici, aggiungere quanto segue alla fine del comando di montaggio CIFS:

```
,mfsymlinks
```

Il comando è quindi simile al seguente:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

È quindi possibile creare i collegamenti simbolici come suggerito nel [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Errore di montaggio (112): Host inattivo" perché la riconnessione è scaduta

Un errore di montaggio "112" si verifica nel client Linux quando il client è stato inattivo per lungo tempo. Dopo un lungo tempo di inattività, il client si disconnette e si verifica il timeout della connessione.  

### <a name="cause"></a>Causa

La connessione può rimanere inattiva per i motivi seguenti:

-   Errori di comunicazione della rete che impediscono di ristabilire una connessione TCP al server quando viene usata l'opzione di montaggio predefinita "soft".
-   Correzioni di riconnessione recenti che non sono presenti in kernel precedenti

### <a name="solution"></a>Soluzione

Questo problema di riconnessione nel kernel Linux è stato corretto nell'ambito delle modifiche seguenti:

- [Fix reconnect to not defer smb3 session reconnect long after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93) (Correggere la funzionalità di riconnessione in modo da non rinviare la riconnessione della sessione smb3 a molto tempo dopo la riconnessione del socket)
- [Call echo service immediately after socket reconnect](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7) (Chiamare il servizio echo immediatamente dopo la riconnessione del socket)
- [CIFS: Fix a possible memory corruption during reconnect](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b) (CIFS: correggere un possibile danneggiamento della memoria dopo la riconnessione)
- [CIFS: Fix a possible double locking of mutex during reconnect - for kernels v4.9 and later](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) (CIFS: Correggere un possibile doppio blocco del mutex durante la riconnessione, per i kernel 4.9 e versioni successive)

È tuttavia possibile che queste modifiche non siano state ancora trasferite a tutte le distribuzioni di Linux. Questa correzione e altre correzioni di riconnessione sono reperibile nella [minime consigliate le versioni con funzionalità di montaggio corrispondenti (SMB versione 2.1 ed SMB versione 3.0)](storage-how-to-use-files-linux.md#minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30) sezione del [Usa file di Azure con Linux](storage-how-to-use-files-linux.md)articolo. Per ottenere questa correzione, eseguire l'aggiornamento a una di queste versioni del kernel consigliate.

### <a name="workaround"></a>Soluzione alternativa

È possibile ovviare a questo problema specificando un hard mount. Un hard mount forza il client ad attendere che una connessione venga stabilita oppure interrotta in modo esplicito. È possibile usarlo per prevenire errori causati da timeout di rete. Questa soluzione può tuttavia causare attese interminabili. Occorre quindi essere pronti a interrompere la connessione se necessario.

Se non è possibile eseguire l'aggiornamento alle versioni del kernel più recenti, si può ovviare a questo problema conservando un file nella condivisione file di Azure in cui scrivere ogni 30 secondi o meno. Deve trattarsi di un'operazione di scrittura, ad esempio la riscrittura della data di creazione o di modifica del file. In caso contrario, i risultati verrebbero memorizzati nella cache e l'operazione potrebbe non attivare la riconnessione.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
