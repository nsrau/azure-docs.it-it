---
title: Risolvere i problemi di Sincronizzazione File di Azure (anteprima) | Microsoft Docs
description: Risolvere i problemi comuni di Sincronizzazione File di Azure
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Risolvere i problemi di Sincronizzazione File di Azure (anteprima)
Sincronizzazione file di Azure (anteprima) consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Tutto questo avviene trasformando i sistemi Windows Server in una cache rapida della condivisione file di Azure. È possibile usare qualsiasi protocollo disponibile in Windows Server per accedere ai dati in locale (tra cui SMB, NFS e FTPS) ed è possibile scegliere tutte le cache necessarie in tutto il mondo.

L'obiettivo di questo articolo è aiutare l'utente a individuare e risolvere i problemi riscontrati con la distribuzione di Sincronizzazione file di Azure. Se i problemi non vengono risolti, la guida spiega come raccogliere i log importanti dal sistema per effettuare un'analisi più approfondita dei problemi. Se non viene visualizzata la risposta alla domanda, contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Forum di Archiviazione di Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Supporto tecnico Microsoft: per creare un nuovo caso di supporto, passare alla scheda "Guida e supporto tecnico" nel portale di Azure e fare clic su "Nuova richiesta di supporto".

## <a name="agent-installation-and-server-registration"></a>Installazione dell'agente e registrazione del server
<a id="agent-installation-failures"></a>**Come risolvere gli errori di installazione dell'agente**  
Se l'installazione dell'agente Sincronizzazione file di Azure non riesce, eseguire il comando seguente da un prompt dei comandi con privilegi elevati per abilitare la registrazione durante l'installazione dell'agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Quando l'installazione non riesce, esaminare il file installer.log per determinare la causa. 

> [!Note]  
> L'installazione dell'agente non riesce se si specifica di usare Microsoft Update e il servizio Windows Update non è in esecuzione.

<a id="server-registration-missing"></a>**Il server non è elencato in Server registrati nel portale di Azure**  
Se un server non è elencato tra i server registrati per un servizio di sincronizzazione archiviazione, eseguire i passaggi seguenti:
1. Accedere al server da registrare.
2. Aprire Esplora file e passare alla directory di installazione dell'agente di sincronizzazione archiviazione (il percorso predefinito è `C:\Program Files\Azure\StorageSyncAgent`). 
3. Eseguire ServerRegistration.exe e seguire la procedura guidata per registrare il server con un servizio di sincronizzazione archiviazione.

<a id="server-already-registered"></a>**Al termine dell'installazione dell'agente Sincronizzazione file di Azure, Registrazione server visualizza un messaggio per indicare che il server è già registrato**  
![Schermata della finestra di dialogo Registrazione server con messaggio di errore che indica che il server è già registrato](media/storage-sync-files-troubleshoot/server-registration-1.png)

Questo messaggio viene visualizzato se il server è stato registrato in precedenza con un servizio di sincronizzazione archiviazione. Per annullare la registrazione del server con il servizio di sincronizzazione archiviazione corrente e registrare il server con un nuovo servizio di sincronizzazione archiviazione, seguire la procedura per [annullare la registrazione di un server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se il server non è elencato in Server registrati in Servizio di sincronizzazione archiviazione, eseguire i comandi di PowerShell seguenti nel server di cui si vuole annullare la registrazione:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se il server fa parte di un cluster, è disponibile un parametro `Reset-StorageSyncServer -CleanClusterRegistration` facoltativo che rimuove anche la registrazione del cluster.

<a id="web-site-not-trusted"></a>**Perché quando si registra un server si ricevono numerose risposte indicanti che il sito Web non è attendibile?**  
Questo errore si verifica perché durante la registrazione del server è stato abilitato il criterio di **sicurezza avanzata di Internet Explorer**. Per altre informazioni su come disabilitare correttamente il criterio di **sicurezza avanzata di Internet Explorer**, vedere [Preparare Windows Server per l'uso con Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) e [Come distribuire Sincronizzazione file di Azure (anteprima)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestione dei gruppi di sincronizzazione
<a id="cloud-endpoint-using-share"></a>**La creazione dell'endpoint cloud non riesce e un messaggio di errore indica che la condivisione file di Azure specificata è già in uso da parte di un endpoint cloud diverso**  
Questo errore si verifica se la condivisione file di Azure è già in uso da parte di un altro endpoint cloud. 

Se si riceve questo errore e la condivisione file di Azure non è attualmente in uso da parte di un endpoint cloud, eseguire i passaggi di seguito per cancellare i metadati di sincronizzazione file di Azure nella condivisione file di Azure:

> [!Warning]  
> L'eliminazione dei metadati in una condivisione file di Azure che è attualmente in uso da parte di un endpoint cloud fa sì che le operazioni di sincronizzazione file di Azure non riescano. 

1. Nel portale di Azure passare alla condivisione file di Azure.  
2. Fare clic con il pulsante destro del mouse sulla condivisione file di Azure e scegliere **Modifica metadati**
3. Fare clic con il pulsante destro del mouse sul servizio di sincronizzazione e scegliere **Elimina**.

<a id="cloud-endpoint-authfailed"></a>**La creazione dell'endpoint cloud non riesce e viene restituito l'errore AuthorizationFailed**  
Questo problema si verifica se l'account utente non dispone di diritti sufficienti per creare un endpoint cloud. 

Per creare un endpoint cloud l'account utente deve avere le seguenti autorizzazioni Microsoft:  
* Lettura: Ottieni definizione ruolo
* Scrittura: Crea o aggiorna la definizione del ruolo personalizzata
* Lettura: Ottieni assegnazione ruolo
* Scrittura: Crea assegnazione ruolo

I seguenti ruoli predefiniti dispongono delle autorizzazioni Microsoft appropriate:  
* Proprietario
* Amministratore accessi utente

Per determinare se il ruolo dell'account utente dispone delle autorizzazioni appropriate, eseguire le operazioni seguenti:  
* Nel portale di Azure fare clic su **Gruppi di risorse**.
* Selezionare il gruppo di risorse in cui si trova l'account di archiviazione e fare clic su **Controllo di accesso (IAM)**.
* Fare clic sul **ruolo** (ad esempio Proprietario, Collaboratore) del proprio account utente.
* Nell'elenco **Provider di risorse** selezionare **Autorizzazione Microsoft**. 
* **Assegnazione ruolo** deve avere le autorizzazioni **Lettura** e **Scrittura**.
* **Definizione ruolo** deve avere le autorizzazioni **Lettura** e **Scrittura**.

<a id="cloud-endpoint-deleteinternalerror"></a>**L'eliminazione di un endpoint non riesce e viene restituito l'errore MgmtInternalError**  
Questo problema può verificarsi se l'account di condivisione file o l'account di archiviazione di Azure è stato eliminato prima di eliminare l'endpoint cloud. Questo problema verrà risolto in un aggiornamento futuro. È comunque possibile eliminare l'endpoint cloud.

Per impedire che si verifichi questo problema, eliminare l'endpoint cloud prima di eliminare l'account di condivisione file o l'account di archiviazione di Azure.

## <a name="sync"></a>Sincronizzazione
<a id="afs-change-detection"></a>**È stato creato un file direttamente nella condivisione file di Azure su SMB o tramite il portale. Quanto tempo richiede la sincronizzazione del file con i server nel gruppo di sincronizzazione?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Come risolvere i problemi di sincronizzazione in un server**  
Se la sincronizzazione non riesce in un server, eseguire queste operazioni:
- Verificare che esista un endpoint server nel portale di Azure per la directory che si vuole sincronizzare con una condivisione file di Azure:
    
    ![Schermata di un gruppo di sincronizzazione con un endpoint server e un endpoint cloud nel portale di Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Esaminare i log eventi operativi e di diagnostica nel Visualizzatore eventi, sotto `Applications and Services\Microsoft\FileSync\Agent`.
- Verificare la connettività Internet nel server.
- Verificare che il servizio Sincronizzazione file di Azure sia in esecuzione nel server aprendo lo snap-in Servizi di MMC e che l'agente di sincronizzazione archiviazione (FileSyncSvc) sia in esecuzione.

<a id="replica-not-ready"></a>**La sincronizzazione non riesce e viene restituito l'errore: 0x80c8300f - La replica non è pronta per eseguire l'operazione richiesta**  
Questo errore è previsto se si crea un endpoint cloud e si usa una condivisione file di Azure contenente dati. Dopo aver completato il rilevamento delle modifiche nella condivisione file di Azure (che può richiedere fino a 24 ore) la sincronizzazione funzionerà correttamente.

<a id="broken-sync-files"></a>**Come risolvere i problemi di sincronizzazione dei singoli file**  
Se non è possibile sincronizzare singoli file, eseguire queste operazioni:
- Esaminare i log eventi operativi e di diagnostica sotto `Applications and Services\Microsoft\FileSync\Agent` nel Visualizzatore eventi
- Verificare che non esistano handle aperti per il file
    - Nota: Sincronizzazione file di Azure crea periodicamente snapshot Servizio Copia Shadow per sincronizzare i file con handle aperti

## <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud 
<a id="files-fail-tiering"></a>**Come risolvere i problemi dei file che non è possibile archiviare a livelli**  
Se non è possibile archiviare i file a livelli in File di Azure, procedere come segue:

- Verificare che i file esistano nella condivisione di file di Azure
    - Nota: un file deve essere sincronizzato con una condivisione di file di Azure per poter essere archiviato a livelli
- Esaminare i log eventi operativi e di diagnostica situati sotto `Applications and Services\Microsoft\FileSync\Agent` nel Visualizzatore eventi
- Verificare la connettività Internet nel server 
- Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione
    - Aprire un prompt dei comandi con privilegi elevati, eseguire `fltmc` e verificare che siano elencati i driver di filtro StorageSync.sys e StorageSyncGuard.sys del file system

<a id="files-fail-recall"></a>**Come risolvere i problemi dei file che non è possibile richiamare**  
Se non è possibile richiamare i file, procedere come segue:
- Esaminare i log eventi operativi e di diagnostica situati sotto `Applications and Services\Microsoft\FileSync\Agent` nel Visualizzatore eventi
- Verificare che i file esistano nella condivisione di file di Azure
- Verificare la connettività Internet nel server 
- Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione
    - Aprire un prompt dei comandi con privilegi elevati, eseguire `fltmc` e verificare che siano elencati i driver di filtro StorageSync.sys e StorageSyncGuard.sys del file system

<a id="files-unexpectedly-recalled"></a>**Come risolvere i problemi dei file richiamati in modo imprevisto su un server**  
Antivirus, backup e altre applicazioni che leggono quantità elevate di file causano richiami indesiderati a meno che rispettino l'attributo offline e ignorino il contenuto dei file. Ignorare i file offline per i prodotti che supportano questa opzione consente di evitare richiami indesiderati quando si eseguono operazioni come scansioni antivirus o processi di backup.

Rivolgersi al fornitore di software per sapere come configurare la soluzione in modo che non legga i file offline.

I richiami indesiderati possono verificarsi anche in altri scenari, ad esempio l'esplorazione di file in Esplora File. Aprire una cartella che contiene file archiviati a livelli nel cloud in Esplora File nel server può causare richiami indesiderati, soprattutto se nel server è abilitato un antivirus.

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali
Se si verificano problemi con Sincronizzazione file di Azure in un server, per prima cosa eseguire queste operazioni:
- Esaminare i log eventi operativi e di diagnostica nel Visualizzatore eventi
    - I problemi di sincronizzazione, archiviazione a livelli e richiamo vengono registrati nei log eventi operativi e di diagnostica in `Applications and Services\Microsoft\FileSync\Agent`
    - I problemi di gestione di un server (ad esempio, le impostazioni di configurazione) vengono registrati nei log eventi operativi e di diagnostica in `Applications and Services\Microsoft\FileSync\Management`
- Verificare che il servizio Sincronizzazione file di Azure sia in esecuzione nel server
    - Aprire lo snap-in Servizi di MMC e verificare se l'agente di sincronizzazione archiviazione (FileSyncSvc) è in esecuzione
- Verificare che i driver di filtro di Sincronizzazione file di Azure (StorageSync.sys e StorageSyncGuard.sys) siano in esecuzione
    - Aprire un prompt dei comandi con privilegi elevati, eseguire fltmc e verificare che siano elencati i driver di filtro StorageSync.sys e StorageSyncGuard.sys del file system

Se il problema non viene risolto usando i passaggi precedenti, attenersi a questa procedura per eseguire lo strumento AFSDiag:
1. Creare una directory che verrà usata per salvare l'output di AFSDiag, ad esempio, c:\output.
2. Aprire una finestra di PowerShell con privilegi elevati ed eseguire i comandi seguenti (premere INVIO dopo ogni comando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Per il livello di traccia nella modalità kernel di Sincronizzazione file di Azure, immettere 1 (a meno che non sia richiesta in modo specifico la creazione di tracce più dettagliate) e premere INVIO.
4. Per il livello di traccia nella modalità utente di Sincronizzazione file di Azure, immettere 1 (a meno che non sia richiesta in modo specifico la creazione di tracce più dettagliate) e premere INVIO.
5. Riprodurre il problema e al termine premere D.
6. Un file ZIP contenente i file di traccia e log sarà presente nella directory di output specificata.

## <a name="see-also"></a>Vedere anche
- [Domande frequenti su File di Azure](storage-files-faq.md)
- [Risolvere i problemi di File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Risolvere i problemi di File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
