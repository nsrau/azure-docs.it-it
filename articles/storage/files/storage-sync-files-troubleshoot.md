---
title: Risolvere i problemi di Sincronizzazione File di Azure (anteprima) | Microsoft Docs
description: Informazioni sulla risoluzione di problemi comuni di Sincronizzazione file di Azure.
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: f12ee39f900373fcab80e59bc20de59fa039f0ff
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Risolvere i problemi di Sincronizzazione File di Azure (anteprima)
È possibile usare Sincronizzazione file di Azure (anteprima) per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

L'obiettivo di questo articolo è aiutare l'utente a individuare e risolvere i problemi che si possono incontrare con la distribuzione di Sincronizzazione file di Azure. Viene inoltre spiegato come raccogliere i log importanti dal sistema per effettuare un'analisi più approfondita dei problemi. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Forum di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Supporto tecnico Microsoft. Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida**, selezionare **Guida e supporto** e quindi selezionare **Nuova richiesta di supporto**.

## <a name="agent-installation-and-server-registration"></a>Installazione dell'agente e registrazione del server
<a id="agent-installation-failures"></a>**Risolvere gli errori di installazione dell'agente**  
Se l'installazione dell'agente Sincronizzazione file di Azure ha esito negativo, eseguire il comando seguente per abilitare la registrazione durante l'installazione dell'agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Esaminare il file installer.log per determinare la causa dell'errore di installazione. 

> [!Note]  
> L'installazione dell'agente ha esito negativo se il computer è configurato per usare Microsoft Update e il servizio Microsoft Update non è in esecuzione.

<a id="server-registration-missing"></a>**Il server non è elencato in Server registrati nel portale di Azure**  
Se un server non è presente nell'elenco **Server registrati** per un servizio di sincronizzazione archiviazione, seguire questa procedura:
1. Accedere al server da registrare.
2. Aprire Esplora File e quindi passare alla directory di installazione dell'agente di sincronizzazione archiviazione (il percorso predefinito è C:\Program Files\Azure\StorageSyncAgent). 
3. Eseguire il file ServerRegistration.exe e completare la registrazione guidata del server con un servizio di sincronizzazione archiviazione.

<a id="server-already-registered"></a>**Durante l'installazione dell'agente Sincronizzazione file di Azure, Registrazione server visualizza il messaggio seguente: "Il server è già registrato"** 

![Schermata della finestra di dialogo Registrazione server con messaggio di errore che indica che il server è già registrato](media/storage-sync-files-troubleshoot/server-registration-1.png)

Questo messaggio viene visualizzato se il server è stato registrato in precedenza con un servizio di sincronizzazione archiviazione. Per annullare la registrazione del server con il servizio di sincronizzazione archiviazione corrente e registrarlo con un nuovo servizio di sincronizzazione archiviazione, seguire la procedura descritta in [Annullare la registrazione del server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se il server non è presente nell'elenco **Server registrati** del servizio di sincronizzazione archiviazione, nel server di cui si vuole annullare la registrazione eseguire i comandi PowerShell seguenti:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se il server fa parte di un cluster, è possibile usare il parametro facoltativo *Reset-StorageSyncServer -CleanClusterRegistration* per rimuovere anche la registrazione del cluster.

<a id="web-site-not-trusted"></a>**Perché quando si registra un server si ricevono numerose risposte indicanti che il sito Web non è attendibile. Perché?**  
Questo errore si verifica perché durante la registrazione del server sono abilitati i criteri **Sicurezza avanzata di Internet Explorer**. Per altre informazioni su come disabilitare correttamente i criteri **Sicurezza avanzata di Internet Explorer**, vedere [Preparare Windows Server per l'uso con Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestione dei gruppi di sincronizzazione
<a id="cloud-endpoint-using-share"></a>**La creazione di endpoint cloud ha esito negativo e restituisce un messaggio di errore che indica che la condivisione file di Azure specificata è già usata da un endpoint cloud diverso**  
Questo errore si verifica se la condivisione file di Azure è già in uso da parte di un altro endpoint cloud. 

Se viene visualizzato questo messaggio e la condivisione file di Azure non è attualmente in uso da alcun endpoint cloud, attenersi ai passaggi seguenti per cancellare i metadati di Sincronizzazione file di Azure nella condivisone file di Azure:

> [!Warning]  
> L'eliminazione dei metadati nella condivisione file di Azure attualmente in uso da un endpoint cloud impedisce il corretto funzionamento di Sincronizzazione file di Azure. 

1. Nel portale di Azure passare alla condivisione file di Azure.  
2. Fare clic con il pulsante destro del mouse sulla condivisione file di Azure e scegliere **Modifica metadati**.
3. Fare clic con il pulsante destro del mouse su **SyncService** e quindi fare clic su **Elimina**.

<a id="cloud-endpoint-authfailed"></a>**La creazione dell'endpoint cloud ha esito negativo e viene restituito il messaggio di errore "AuthorizationFailed"**  
Questo problema si verifica se l'account utente non ha diritti sufficienti per creare un endpoint cloud. 

Per creare un endpoint cloud, l'account utente deve avere le autorizzazioni Microsoft seguenti:  
* Lettura: Ottieni definizione ruolo
* Scrittura: Crea o aggiorna la definizione del ruolo personalizzata
* Lettura: Ottieni assegnazione ruolo
* Scrittura: Crea assegnazione ruolo

I seguenti ruoli predefiniti dispongono delle necessarie autorizzazioni Microsoft:  
* Proprietario
* Amministratore accessi utente

Per determinare se il ruolo dell'account utente in uso dispone delle autorizzazioni necessarie:  
1. Nel portale di Azure fare clic su **Gruppi di risorse**.
2. Selezionare il gruppo di risorse in cui si trova l'account di archiviazione e quindi selezionare **Controllo di accesso (IAM)**.
3. Selezionare il **ruolo** per l'account utente, ad esempio proprietario o collaboratore.
4. Nell'elenco **Provider di risorse** selezionare **Autorizzazione Microsoft**. 
    * In **Assegnazione ruolo** devono essere impostate le autorizzazioni **Lettura** e **Scrittura**.
    * In **Definizione ruolo** devono essere impostate le autorizzazioni **Lettura** e **Scrittura**.

<a id="cloud-endpoint-deleteinternalerror"></a>**L'eliminazione di un endpoint ha esito negativo e viene restituito l'errore "MgmtInternalError"**  
Questo problema può verificarsi se l'account di condivisione file o l'account di archiviazione di Azure viene eliminato prima dell'endpoint cloud. Questo problema verrà risolto in un aggiornamento futuro. Sarà a quel punto possibile eliminare un endpoint cloud dopo aver eliminato l'account di archiviazione o di condivisione file di Azure.

Nel frattempo, per impedire che si verifichi questo problema, eliminare l'endpoint cloud prima dell'account di condivisione file o dell'account di archiviazione di Azure.

## <a name="sync"></a>Sincronizzazione
<a id="afs-change-detection"></a>**Se è stato creato un file direttamente nella condivisione file di Azure su SMB o nel portale, quanto tempo richiede la sincronizzazione del file nei server nel gruppo di sincronizzazione?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**La sincronizzazione ha esito negativo in un server**  
Se la sincronizzazione ha esito negativo in un server:
1. Verificare che esista un endpoint server nel portale di Azure per la directory che si vuole sincronizzare con una condivisione file di Azure:
    
    ![Screenshot di un gruppo di sincronizzazione con un endpoint server e un endpoint cloud nel portale di Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. Nel Visualizzatore eventi esaminare i registri eventi operativi e diagnostici, che si trovano in Applications e Services\Microsoft\FileSync\Agent.
    1. Verificare che il server disponga della connettività Internet.
    2. Verificare che il servizio Sincronizzazione file di Azure sia in esecuzione nel server. Aprire a tal fine lo snap-in di MMC di Servizi e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione.

<a id="replica-not-ready"></a>**La sincronizzazione ha esito negativo e viene restituito il messaggio di errore: "0x80c8300f - La replica non è pronta per eseguire l'operazione richiesta"**  
Questo errore è previsto se si crea un endpoint cloud e si usa una condivisione file di Azure contenente dati. Al termine del processo di rilevamento modifiche in esecuzione in una condivisione file di Azure (potrebbe richiedere fino a 24 ore), la sincronizzazione dovrebbe iniziare a funzionare correttamente.

<a id="broken-sync-files"></a>**Risolvere i problemi di mancata sincronizzazione di file singoli**  
Se la sincronizzazione di file singoli ha esito negativo:
1. Nel Visualizzatore eventi esaminare i registri eventi operativi e diagnostici, che si trovano in Applications e Services\Microsoft\FileSync\Agent.
2. Verificare che nel file non siano presenti handle aperti.

    > [!NOTE]
    > Sincronizzazione file di Azure crea periodicamente snapshot VSS per sincronizzare i file con handle aperti.

## <a name="cloud-tiering"></a>Archiviazione a livelli nel cloud 
Nell'archiviazione a livelli nel cloud possono prendere forma due percorsi di errore:

- È possibile che i file non possano essere archiviati a livelli, ovvero che Sincronizzazione file di Azure non riesca ad archiviare a livelli i file in File di Azure.
- È possibile che i file non possano essere richiamati, ovvero che il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) non riesca a scaricare i dati quando un utente tenta di accedere a un file archiviato a livelli.

Dai questi due percorsi di errore possono essere generate due principali classi di errore:

- Errori di archiviazione cloud
    - *Problemi di disponibilità del servizio di archiviazione temporanea*. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Condivisione file di Azure non accessibile*. Questo errore si verifica in genere quando si elimina una condivisione file di Azure che si trova ancora in un endpoint cloud di un gruppo di sincronizzazione.
    - *Account di archiviazione inaccessibile*. Questo errore si verifica in genere quando si elimina un account di archiviazione che ha ancora una condivisione file di Azure costituita da un endpoint cloud di un gruppo di sincronizzazione. 
- Errori del server 
    - *Il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys) non è caricato*. Per rispondere alle richieste di archiviazione a livelli/richiamo, è necessario che il filtro del file system di Sincronizzazione file di Azure sia caricato. È possibile che un filtro non risulti caricato per vari motivi, ma la causa più comune è che un amministratore lo abbia scaricato manualmente. Il filtro del file system di Sincronizzazione file di Azure deve risultare sempre caricato per garantire il corretto funzionamento di Sincronizzazione file di Azure.
    - *Reparse point mancante, danneggiato o non funzionante*. Un reparse point è una speciale struttura di dati in un file costituita da due parti:
        1. Un tag di reparse, che indica al sistema operativo che per eseguire alcune operazioni di I/O sul file è possibile che sia necessario il filtro del file system di Sincronizzazione file di Azure (StorageSync.sys). 
        2. I dati di reparse, che indicano al filtro del file system l'URI del file nell'endpoint cloud associato (la condivisione file di Azure). 
        
        Nella maggior parte dei casi, un reparse point viene danneggiato quando un amministratore tenta di modificarne il tag o i dati. 
    - *Problemi di connettività di rete*. Per archiviare a livelli o richiamare un file, il server deve avere la connettività Internet.

Le sezioni seguenti indicano come risolvere problemi di archiviazione a livelli nel cloud e determinare se si tratta di un problema di archiviazione cloud o di un problema del server.

<a id="files-fail-tiering"></a>**Risolvere i problemi dei file che non è possibile archiviare a livelli**  
Se non è possibile archiviare a livelli i file in File di Azure:

1. Verificare che i file siano presenti nella condivisione file di Azure.

    > [!NOTE]
    > È necessario che un file venga sincronizzato con una condivisione di file di Azure per poter essere archiviato a livelli.
2. Nel Visualizzatore eventi esaminare i registri eventi operativi e diagnostici, che si trovano in Applications e Services\Microsoft\FileSync\Agent.
    1. Verificare che il server disponga della connettività Internet. 
    2. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
        - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

<a id="files-fail-recall"></a>**Risolvere i problemi di file che non è possibile richiamare**  
Se il richiamo di file ha esito negativo:
1. Nel Visualizzatore eventi esaminare i registri eventi operativi e diagnostici, che si trovano in Applications e Services\Microsoft\FileSync\Agent.
    1. Verificare che i file siano presenti nella condivisione file di Azure.
    2. Verificare che il server disponga della connettività Internet. 
    3. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
        - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

<a id="files-unexpectedly-recalled"></a>**Risolvere i problemi dei file richiamati in modo imprevisto in un server**  
Antivirus, backup e altre applicazioni che leggono quantità elevate di file causano richiami indesiderati a meno che non rispettino l'attributo per ignorare i file offline e ignorare la lettura del contenuto di tali file. Se si ignorano i file offline per i prodotti che supportano questa opzione, è possibile evitare richiami imprevisti durante operazioni come le analisi antivirus o i processi di backup.

Per informazioni su come configurare la soluzione in modo che non legga i file offline, rivolgersi al produttore del software.

I richiami imprevisti possono verificarsi anche in altri scenari, ad esempio quando si visualizzano file in Esplora File. Se si apre una cartella contenente file archiviati a livelli nel cloud in Esplora File nel server, è possibile che si verifichino richiami non previsti. Questa situazione si verifica con ancora maggiore probabilità se nel server è abilitata una soluzione antivirus.

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali
Se si verificano problemi con Sincronizzazione file di Azure in un server, per prima cosa eseguire questi passaggi:
1. Nel Visualizzatore eventi esaminare i registri eventi operativi e diagnostici.
    - I problemi di sincronizzazione, archiviazione a livelli e richiamo vengono registrati nei registri eventi operativi e diagnostici in Applications e Services\Microsoft\FileSync\Agent.
    - I problemi correlati alla gestione di un server (ad esempio le impostazioni di configurazione) vengono registrati nei registri eventi operativi e diagnostici in Applications e Services\Microsoft\FileSync\Management.
2. Verificare che il servizio Sincronizzazione file di Azure sia in esecuzione nel server:
    - Aprire lo snap-in di MMC di Servizi e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione.
3. Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione:
    - Al prompt dei comandi con privilegi elevati. eseguire `fltmc`. Verificare che i driver di filtro del file system StorageSync.sys e StorageSyncGuard.sys siano presenti nell'elenco.

Se il problema persiste, eseguire lo strumento AFSDiag:
1. Creare una directory in cui verranno salvati i risultati di AFSDiag, ad esempio C:\Output.
2. Aprire una finestra di PowerShell con privilegi elevati ed eseguire i comandi seguenti, premendo INVIO dopo ogni comando:

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Per il livello di traccia nella modalità kernel di Sincronizzazione file di Azure, immettere **1** (salvo diversamente specificato, per creare tracce più dettagliate) e quindi premere INVIO.
4. Per il livello di traccia nella modalità utente di Sincronizzazione file di Azure, immettere **1** (salvo diversamente specificato, per creare tracce più dettagliate) e quindi premere INVIO.
5. Riprodurre il problema. Al termine immettere **D**.
6. Nella directory di output specificata verrà salvato un file con estensione zip contenente i registri e i file di traccia.

## <a name="see-also"></a>Vedere anche
- [Domande frequenti su File di Azure](storage-files-faq.md)
- [Risolvere i problemi di File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Risolvere i problemi di File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
