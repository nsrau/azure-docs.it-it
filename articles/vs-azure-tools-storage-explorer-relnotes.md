---
title: Note sulla versione di Microsoft Azure Storage Explorer
description: Note sulla versione di Microsoft Azure Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 0b2ffc00b6c96f2c31a4b711f618e7b87b6f69e0
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482127"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Note sulla versione di Microsoft Azure Storage Explorer

Questo articolo contiene le note sulla versione più recenti per Azure Storage Explorer, nonché le note sulla versione per le versioni precedenti. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

Per scaricare le versioni precedenti di Storage Explorer, è possibile visitare la pagina relativa alle [versioni](https://github.com/microsoft/AzureStorageExplorer/releases) del repository GitHub.

## <a name="version-1110"></a>Versione 1.11.0
11/4/2019

### <a name="new"></a>Nuovo
* Le operazioni per i BLOB, ADLS Gen2 e Managed Disks usano il AzCopy integrato. In particolare, le operazioni seguenti vengono eseguite con AzCopy:
   * Blobs
      * Apri per la modifica e il caricamento
      * Caricare, incluso il trascinamento & drop
      * Scaricare
      * Copia & incollare #1249
      * Elimina
   * BLOB ADLS Gen2
      * Caricare, incluso il trascinamento & drop
      * Scaricare
      * Copia & incolla
      * Elimina, inclusa la cartella Delete
   * Managed Disks
      * Upload
      * Scaricare
      * Copia & incolla

   Sono state inoltre aggiunte diverse funzionalità richieste di frequente all'esperienza AzCopy integrata:
   * Risoluzione dei conflitti: verrà richiesto durante i trasferimenti per risolvere i conflitti. #1455
   * Carica come BLOB di pagine: è possibile scegliere se AzCopy carica i file con estensione vhd e VHDX come BLOB di pagine. #1164 e #1601
   * Parametri configurabili di AzCopy: sono state aggiunte diverse impostazioni per ottimizzare le prestazioni e l'utilizzo delle risorse di AzCopy. Per altri dettagli, vedere di seguito.

* Per abilitare l'accesso a più protocolli ADLS Gen2 e BLOB e migliorare ulteriormente ADLS Gen2 esperienze, sono state aggiunte le funzionalità seguenti per gli account di ADLS Gen2:
   * Eseguire la ricerca usando nomi descrittivi per impostare le autorizzazioni ACL
   * Visualizzare i contenitori nascosti, ad esempio $logs e $web
   * Acquisisci e Interrompi lease del contenitore
   * Acquisire e interrompere il lease di BLOB #848
   * Gestire i criteri di accesso ai contenitori
   * Configurare i livelli di accesso BLOB
   * Copiare & incollare i BLOB

* In questa versione sono disponibili in anteprima 17 lingue aggiuntive. È possibile passare alla lingua desiderata nella pagina impostazioni in "applicazione" → "impostazioni internazionali" → "lingua (anteprima)". Stiamo ancora lavorando duramente per tradurre stringhe aggiuntive e migliorare la qualità della traduzione. Per ricevere commenti e suggerimenti relativi a una traduzione o se si nota una stringa che non è ancora stata tradotta, [aprire un problema in GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* In ogni versione, si tenta di caricare alcune impostazioni per abilitare la Storage Explorer. In questa versione sono state aggiunte le impostazioni per configurare ulteriormente AzCopy e per nascondere i nodi del servizio:
   * Limite della larghezza di banda AzCopy: consente di controllare la quantità di AzCopy utilizzata dalla rete. È possibile trovare questa impostazione in "trasferimenti" → "AzCopy" → "velocità massima di trasferimento". #1099
   * AzCopy MD5 check: consente di configurare se e in che modo rigorosamente AzCopy controlla la presenza di hash MD5 durante il download. È possibile trovare questa impostazione in "trasferimenti" → "AzCopy" → "verifica MD5".
   * AzCopy della concorrenza e della dimensione del buffer di memoria. per impostazione predefinita, AzCopy analizzerà il computer per determinare i valori predefiniti ragionevoli per queste impostazioni. Tuttavia, se si verificano problemi di prestazioni, è possibile usare queste impostazioni avanzate per personalizzare ulteriormente il modo in cui AzCopy viene eseguito nel computer. È possibile trovare queste impostazioni in "trasferimenti" → "AzCopy". #994
   * Visualizzare e nascondere i nodi del servizio: queste impostazioni offrono le opzioni per visualizzare o nascondere i servizi di Azure supportati da Storage Explorer. È possibile trovare queste impostazioni nella sezione "servizi". #1877

* Quando si crea uno snapshot di un disco gestito, viene ora fornito un nome predefinito. #1847
* Quando si esegue la connessione con Azure AD, se si associa un contenitore BLOB ADLS Gen2, verrà visualizzato "(ADLS Gen2)" accanto al nodo. #1861

### <a name="fixes"></a>Correzioni
* Durante la copia, il caricamento o il download di dischi di grandi dimensioni, Storage Explorer talvolta non riescono a revocare l'accesso ai dischi che interessano l'operazione. Questo problema è stato risolto. #2048
* Statistiche della tabella non riuscite durante la visualizzazione di una query della chiave di partizione. Questo problema è stato risolto. #1886

### <a name="known-issues"></a>Problemi noti
* Storage Explorer 1.11.0 richiede ora un endpoint DFS, ad esempio "myaccount.dfs.core.windows.net", per la connessione a contenitori di ADLS Gen2. Nelle versioni precedenti di Storage Explorer è stato consentito l'utilizzo di un endpoint BLOB. Questi allegati potrebbero non funzionare più dopo l'aggiornamento a 1.11.0. Se si verifica questo problema, riconnettersi utilizzando l'endpoint DFS.
* Le impostazioni numeriche non vengono controllate se si trovano in un intervallo valido. #2140
* La copia di contenitori BLOB da un account di archiviazione a un altro nella visualizzazione albero potrebbe non riuscire. Stiamo esaminando il problema. #2124
* L'impostazione di aggiornamento automatico non ha ancora effetto su tutte le operazioni in Esplora BLOB.
* Le funzionalità del disco gestito non sono supportate in Azure Stack.
* Se il caricamento o l'Incolla del disco non riesce e un nuovo disco è stato creato prima dell'errore, Storage Explorer non eliminerà il disco.
* A seconda del momento in cui si annulla un caricamento o un incolla su disco, è possibile lasciare il nuovo disco in uno stato danneggiato. In tal caso, è necessario eliminare il nuovo disco o chiamare manualmente le API del disco per sostituire il contenuto del disco in modo che non sia più danneggiato.
* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
   * ADLS Gen2
   * Managed Disks
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per l'esecuzione di Storage Explorer in Linux è necessario installare prima alcune dipendenze. Per ulteriori informazioni, consultare la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer.

## <a name="previous-releases"></a>Versioni precedenti

* [Versione 1.10.1](#version-1101)
* [Versione 1.10.0](#version-1100)
* [Versione 1.9.0](#version-190)
* [Versione 1.8.1](#version-181)
* [Versione 1.8.0](#version-180)
* [Versione 1.7.0](#version-170)
* [Versione 1.6.2](#version-162)
* [Versione 1.6.1](#version-161)
* [Versione 1.6.0](#version-160)
* [Versione 1.5.0](#version-150)
* [Versione 1.4.4](#version-144)
* [Versione 1.4.3](#version-143)
* [Versione 1.4.2](#version-142)
* [Versione 1.4.1](#version-141)
* [Versione 1.3.0](#version-130)
* [Versione 1.2.0](#version-120)
* [Versione 1.1.0](#version-110)
* [Versione 1.0.0](#version-100)
* [Versione 0.9.6](#version-096)
* [Versione 0.9.5](#version-095)
* [Versione 0.9.4 e 0.9.3](#version-094-and-093)
* [Versione 0.9.2](#version-092)
* [Versione 0.9.1 e 0.9.0](#version-091-and-090)
* [ 0.8.16](#version-0816)
* [Versione 0.8.14](#version-0814)
* [Versione 0.8.13](#version-0813)
* [Versione 0.8.12, 0.8.11 e 0.8.10](#version-0812-and-0811-and-0810)
* [Versione 0.8.9 e 0.8.8](#version-089-and-088)
* [Versione 0.8.7](#version-087)
* [Versione 0.8.6](#version-086)
* [Versione 0.8.5](#version-085)
* [Versione 0.8.4](#version-084)
* [Versione 0.8.3](#version-083)
* [Versione 0.8.2](#version-082)
* [Versione 0.8.0](#version-080)
* [Versione 0.7.20160509.0](#version-07201605090)
* [Versione 0.7.20160325.0](#version-07201603250)
* [Versione 0.7.20160129.1](#version-07201601291)
* [Versione 0.7.20160105.0](#version-07201601050)
* [Versione 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Versione 1.10.1
9/19/2019

### <a name="hotfix"></a>Hotfix
* Si è verificato un errore in 1.10.0 durante il tentativo di visualizzare i dati negli account ADLS di generazione 1. Questo errore ha impedito il rendering corretto del pannello di Explorer. Questo problema è stato risolto. #1853 #1865

### <a name="new"></a>Nuovo
* Storage Explorer dispone ora di un'interfaccia utente di impostazioni dedicata. È possibile accedervi da modifica → impostazioni oppure facendo clic sull'icona delle impostazioni (l'ingranaggio) nella barra degli strumenti verticale a sinistra. Questa funzionalità è il primo passaggio per fornire un'ampia gamma di [impostazioni richieste dall'utente](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). A partire da questa versione sono supportate le impostazioni seguenti:
  * Tema
  * Proxy
  * Disconnetti all'uscita #6
  * Abilitare l'accesso al flusso del codice del dispositivo
  * Aggiornamento automatico #1526
  * Abilita AzCopy
  * AzCopy SAS Duration se sono presenti altri setttings da aggiungere, [aprire un problema in GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) che descrive l'impostazione che si vuole visualizzare.
* Storage Explorer supporta ora Managed Disks. È possibile:
  * Caricare un disco rigido virtuale locale in un nuovo disco
  * Scaricare un disco
  * Copiare e incollare dischi tra gruppi di risorse e aree
  * Elimina dischi
  * Creazione di uno snapshot di un disco il caricamento, il download e la copia tra aree di dischi sono basati su AzCopy V10.
* È ora possibile installare Storage Explorer tramite l'archivio di snap in Linux. Quando si installa tramite lo snap Store, tutte le dipendenze vengono installate per l'utente, incluso .NET Core. Attualmente è stato verificato che Storage Explorer viene eseguito correttamente in Ubuntu e CentOS. Se si verificano problemi di installazione dall'archivio snap in altre distribuzioni Linux, [aprire un problema in GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Per altre informazioni sull'installazione dall'archivio snap, vedere la [Guida introduttiva](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux). #68
* Sono state apportate due modifiche principali per la connessione con Azure Active Directory (Azure AD), destinate a rendere la funzionalità più utile per gli utenti di ADLS Gen2:
  * È ora possibile selezionare il tenant in cui si trova la risorsa a cui si sta connettendo. Ciò significa che non è più necessario avere accesso RBAC alla sottoscrizione della risorsa.
  * Se si connette un contenitore BLOB di ADLS Gen2, è ora possibile connettersi a un percorso specifico nel contenitore.
* Quando si gestiscono ACL per ADLS Gen2 file e cartelle, Storage Explorer ora visualizzerà i nomi descrittivi per le entità nell'ACL. #957
* Quando si aggiunge tramite OID a un ADLS Gen2 ACL, Storage Explorer ora convaliderà che l'OID appartiene a un'entità valida nel tenant. #1603
* Le scelte rapide da tastiera per lo spostamento tra le schede ora utilizzano combinazioni di tasti più standard. #1018
* Quando si fa clic su una scheda, quest'operazione verrà chiusa. #1348
* Se un trasferimento AzCopy contiene ignorati e nessun errore, Storage Explorer ora visualizzerà un'icona di avviso per evidenziare che si sono verificati i salti. #1490
* Il AzCopy integrato è stato aggiornato alla versione 10.2.1. Inoltre, è ora possibile visualizzare la versione di AzCopy installata nella finestra di dialogo informazioni su. #1343

### <a name="fixes"></a>Correzioni
* Molti utenti hanno eseguito in vari modi "non è possibile leggere la versione di errori non definiti" o "non è possibile leggere la connessione degli errori non definiti" quando si utilizzano account di archiviazione collegati. Sebbene si continui a esaminare la causa principale di questo problema, in 1.10.0 è stata migliorata la gestione degli errori relativi al caricamento degli account di archiviazione collegati. #1626, #985 e #1532
* È possibile che l'albero di Explorer (lato sinistro) si trovi in uno stato in cui lo stato attivo passa ripetutamente al nodo superiore. Questo problema è stato risolto. #1596
* Quando si gestiscono gli snapshot di un BLOB, utilità non legge il timestamp associato allo snapshot. Questo problema è stato risolto. #1202
* L'impostazione del proxy in macOS non è stata impostata in tempo per l'uso da parte del processo di autenticazione. Questo problema è stato risolto. #1567
* Se un account di archiviazione in un cloud sovrano è stato associato usando il nome e la chiave, AzCopy non funzionerà. Questo problema è stato risolto. #1544
* Quando si esegue la connessione tramite una stringa di connessione, Storage Explorer ora rimuoverà gli spazi finali. #1387

### <a name="known-issues"></a>Problemi noti
* L'impostazione di aggiornamento automatico non ha ancora effetto su tutte le operazioni in Esplora BLOB.
* Le funzionalità del disco gestito non sono supportate in Azure Stack.
* Se il caricamento o l'Incolla del disco non riesce e un nuovo disco è stato creato prima dell'errore, Storage Explorer non eliminerà il disco.
* A seconda del momento in cui si annulla un caricamento o un incolla su disco, è possibile lasciare il nuovo disco in uno stato danneggiato. In tal caso, è necessario eliminare il nuovo disco o chiamare manualmente le API del disco per sostituire il contenuto del disco in modo che non sia più danneggiato.
* A seconda del momento in cui si annulla un caricamento o un incolla su disco, è possibile lasciare il nuovo disco in uno stato danneggiato. In tal caso, è necessario eliminare il nuovo disco o chiamare manualmente le API del disco per sostituire il contenuto del disco in modo che non sia più danneggiato.
* Quando si esegue un download di BLOB non AzCopy, l'MD5 per i file di grandi dimensioni non viene verificato. Ciò è dovuto a un bug in Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
   * ADLS Gen2
   * Managed Disks
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per l'esecuzione di Storage Explorer in Linux è necessario installare prima alcune dipendenze. Per ulteriori informazioni, consultare la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer.


## <a name="version-1100"></a>Versione 1.10.0
9/12/2019

### <a name="new"></a>Nuovo

* Storage Explorer dispone ora di un'interfaccia utente di impostazioni dedicata. È possibile accedervi da modifica → impostazioni oppure facendo clic sull'icona delle impostazioni (l'ingranaggio) nella barra degli strumenti verticale a sinistra. Questa funzionalità è il primo passaggio per fornire un'ampia gamma di [impostazioni richieste dall'utente](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). A partire da questa versione sono supportate le impostazioni seguenti:
    * Tema
    * Proxy
    * Disconnetti all'uscita [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Abilitare l'accesso al flusso del codice del dispositivo
    * Aggiornamento automatico [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Abilita AzCopy
    * Durata SAS AzCopy

    Se sono presenti altri setttings che si vuole aggiungere, [aprire un problema in GitHub che descrive l'impostazione che si vuole visualizzare](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage Explorer supporta ora Managed Disks. È possibile:
    * Caricare un disco rigido virtuale locale in un nuovo disco
    * Scaricare un disco
    * Copiare e incollare dischi tra gruppi di risorse e aree
    * Elimina dischi
    * Creare uno snapshot di un disco

    Il caricamento, il download e la copia tra aree di dischi sono basati su AzCopy V10.
* È ora possibile installare Storage Explorer tramite l'archivio di snap in Linux. Quando si installa tramite lo snap Store, tutte le dipendenze vengono installate per l'utente, incluso .NET Core. Attualmente è stato verificato che Storage Explorer viene eseguito correttamente in Ubuntu e CentOS. Se si verificano problemi di installazione dall'archivio snap in altre distribuzioni Linux, [aprire un problema in GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Per altre informazioni sull'installazione dall'archivio snap, vedere la [Guida introduttiva](https://aka.ms/storageexplorer/snapinformation). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Sono state apportate due modifiche principali per la connessione con Azure Active Directory (Azure AD), destinate a rendere la funzionalità più utile per gli utenti ADLS Gen2: * è ora possibile selezionare il tenant in cui si trova la risorsa che si sta associando. Ciò significa che non è più necessario avere accesso RBAC alla sottoscrizione della risorsa.
        * Se si connette un contenitore BLOB di ADLS Gen2, è ora possibile connettersi a un percorso specifico nel contenitore.
* Quando si gestiscono ACL per ADLS Gen2 file e cartelle, Storage Explorer ora visualizzerà i nomi descrittivi per le entità nell'ACL. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Quando si aggiunge tramite OID a un ADLS Gen2 ACL, Storage Explorer ora convaliderà che l'OID appartiene a un'entità valida nel tenant. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Le scelte rapide da tastiera per lo spostamento tra le schede ora utilizzano combinazioni di tasti più standard. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Quando si fa clic su una scheda, quest'operazione verrà chiusa. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Se un trasferimento AzCopy contiene ignorati e nessun errore, Storage Explorer ora visualizzerà un'icona di avviso per evidenziare che si sono verificati i salti. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Il AzCopy integrato è stato aggiornato alla versione 10.2.1. Inoltre, è ora possibile visualizzare la versione di AzCopy installata nella finestra di dialogo informazioni su. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Correzioni

* Molti utenti hanno eseguito in vari modi "non è possibile leggere la versione di errori non definiti" o "non è possibile leggere la connessione degli errori non definiti" quando si utilizzano account di archiviazione collegati. Sebbene si continui a esaminare la causa principale di questo problema, in 1.10.0 è stata migliorata la gestione degli errori relativi al caricamento degli account di archiviazione collegati. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)e [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* È possibile che l'albero di Explorer (lato sinistro) si trovi in uno stato in cui lo stato attivo passa ripetutamente al nodo superiore. Questo problema è stato risolto. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Quando si gestiscono gli snapshot di un BLOB, utilità non legge il timestamp associato allo snapshot. Questo problema è stato risolto. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* L'impostazione del proxy in macOS non è stata impostata in tempo per l'uso da parte del processo di autenticazione. Questo problema è stato risolto. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Se un account di archiviazione in un cloud sovrano è stato associato usando il nome e la chiave, AzCopy non funzionerà. Questo problema è stato risolto. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Quando si esegue la connessione tramite una stringa di connessione, Storage Explorer ora rimuoverà gli spazi finali. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Problemi noti

* L'impostazione di aggiornamento automatico non ha ancora effetto su tutte le operazioni in Esplora BLOB.
* Le funzionalità del disco gestito non sono supportate in Azure Stack.
* Se il caricamento o l'Incolla del disco non riesce e un nuovo disco è stato creato prima dell'errore, Storage Explorer non eliminerà il disco.
* A seconda del momento in cui si annulla un caricamento o un incolla su disco, è possibile lasciare il nuovo disco in uno stato danneggiato. In tal caso, è necessario eliminare il nuovo disco o chiamare manualmente le API del disco per sostituire il contenuto del disco in modo che non sia più danneggiato.
* Quando si esegue un download di BLOB non AzCopy, l'MD5 per i file di grandi dimensioni non viene verificato. Ciò è dovuto a un bug in Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
   * ADLS Gen2
   * Managed Disks
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per l'esecuzione di Storage Explorer in Linux è necessario installare prima alcune dipendenze. Per ulteriori informazioni, consultare la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer.

## <a name="version-190"></a>Versione 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Scaricare Azure Storage Explorer 1.9.0
- [Azure Storage Explorer 1.9.0 per Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 per Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 per Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nuovo

* È ora possibile allegare i contenitori BLOB tramite Azure AD (autorizzazioni RBAC o ACL). Questa funzionalità è destinata a consentire agli utenti che hanno accesso ai contenitori, ma non agli account di archiviazione in cui si trovano i contenitori. Per ulteriori informazioni su questa funzionalità, vedere la guida Introduzione.
* Acquisisci e Interrompi lease ora funzionano con RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* La gestione dei criteri di accesso e l'impostazione del livello di accesso pubblico ora funzionano con RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* L'eliminazione delle cartelle BLOB ora funziona con RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* La modifica del livello di accesso BLOB ora funziona con RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* È ora possibile reimpostare rapidamente l'accesso rapido tramite "Guida" → "Reimposta". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Funzionalità di anteprima

* L'accesso al flusso del codice del dispositivo è ora disponibile per l'anteprima. Per abilitarla, passare a "anteprima" → "usare l'accesso al flusso del codice del dispositivo". Si consiglia agli utenti che hanno riscontrato problemi con le finestre di accesso vuote di provare questa funzionalità, perché potrebbe rivelarsi un tipo di accesso più affidabile.
* Storage Explorer integrato con AzCopy è attualmente disponibile per l'anteprima. Per abilitarlo, passare a "Preview" → "usare AzCopy per il caricamento e il download di BLOB migliorati". I trasferimenti di BLOB completati con AzCopy dovrebbero essere più veloci e più efficienti.

### <a name="fixes"></a>Correzioni

* Correzione non riuscita a caricare più di 50 sottoscrizioni per un account. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Correzione del pulsante "Accedi" non funzionante sulla barra informazioni visualizzata quando un collegamento diretto non riesce. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Correzione del mancato caricamento dei file con estensione app in macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Correzione di "Riprova tutto" non funzionante per una ridenominazione BLOB non riuscita. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Correzione dell'annullamento non funzionante durante l'apertura di un BLOB. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Correzione di più problemi di ortografia e descrizione comando all'interno del prodotto. Molti ringraziano tutti gli utenti che hanno segnalato questi problemi. [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Problemi noti

* Quando si esegue un download di BLOB non AzCopy, l'MD5 per i file di grandi dimensioni non viene verificato. Ciò è dovuto a un bug in Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Il tentativo di accedere ai BLOB di ADLS Gen2 quando si è protetti da un proxy potrebbe non riuscire.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
   * ADLS Gen2
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per l'esecuzione di Storage Explorer in Linux è necessario installare prima alcune dipendenze. Per ulteriori informazioni, consultare la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer.

## <a name="version-181"></a>Versione 1.8.1
5/13/2019

### <a name="hotfixes"></a>Hotfix
* In alcuni casi, se si fa clic su "carica altro" a livello di risorsa, non verrà restituita la pagina successiva di risorse. Questo problema è stato risolto. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* In Windows, i download di AzCopy hanno esito negativo se è stato scaricato un singolo file o una cartella e il nome del file o della cartella ha un carattere non valido per un percorso di Windows. Questo problema è stato risolto. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* In casi estremamente rari, durante l'esecuzione di una ridenominazione di una condivisione file o di una ridenominazione in una condivisione file, se le copie per la ridenominazione non sono riuscite o se l'esplorazione dell'archiviazione non è riuscita a confermare la riuscita delle copie con Azure, è possibile che Storage Explorer elimini o RIGINALI i file prima del completamento della copia. Questo problema è stato risolto.

### <a name="new"></a>Nuovo

* La versione AzCopy integrata è stata aggiornata alla versione 10.1.0.
* È ora possibile usare Ctrl/Cmd + R per aggiornare l'editor attualmente attivo. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* La versione dell'API di archiviazione Azure Stack è stata modificata in 2017-04-17.
* La finestra di dialogo Gestisci accesso per ADLS Gen2 ora manterrà la maschera sincronizzata in modo analogo ad altri strumenti di autorizzazioni POSIX. Verrà inoltre visualizzato un avviso se viene apportata una modifica che determina il superamento dei limiti della maschera da parte di un utente o di un gruppo. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Per i caricamenti AzCopy, il flag per calcolare e impostare l'hash MD5 è ora abilitato. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funzionalità di anteprima

* L'accesso al flusso del codice del dispositivo è ora disponibile per l'anteprima. Per abilitarla, passare a "anteprima" → "usare l'accesso al flusso del codice del dispositivo". Si consiglia agli utenti che hanno riscontrato problemi con le finestre di accesso vuote di provare questa funzionalità, perché potrebbe rivelarsi un tipo di accesso più affidabile.
* Storage Explorer integrato con AzCopy è attualmente disponibile per l'anteprima. Per abilitarlo, passare a "Preview" → "usare AzCopy per il caricamento e il download di BLOB migliorati". I trasferimenti di BLOB completati con AzCopy dovrebbero essere più veloci e più efficienti.

### <a name="fixes"></a>Correzioni

* La finestra di dialogo criteri di accesso non configurerà più una data di scadenza per i criteri di accesso alle archiviazione che non hanno una scadenza. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Sono state apportate alcune modifiche alla finestra di dialogo Genera SAS per assicurarsi che i criteri di accesso archiviati vengano usati correttamente quando si genera una firma di accesso condiviso. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Quando si tenta di caricare un file allineato a un byte non 512 in un BLOB di pagine, Storage Explorer esporrà ora un errore più rilevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* La copia di un contenitore BLOB che utilizzava un nome visualizzato avrebbe avuto esito negativo. A questo punto, viene usato il nome effettivo del contenitore BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Il tentativo di eseguire determinate azioni su una cartella ADLS Gen2 con caratteri Unicode nel nome potrebbe non riuscire. Tutte le azioni dovrebbero ora funzionare. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemi noti

* Quando si esegue un download di BLOB non AzCopy, l'MD5 per i file di grandi dimensioni non viene verificato. Ciò è dovuto a un bug in Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Il tentativo di accedere ai BLOB di ADLS Gen2 quando si è protetti da un proxy potrebbe non riuscire.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
   * ADLS Gen2
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per l'esecuzione di Storage Explorer in Linux è necessario installare prima alcune dipendenze. Per ulteriori informazioni, consultare la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer.

## <a name="version-180"></a>Versione 1.8.0
5/1/2019

### <a name="new"></a>Nuovo

* La versione AzCopy integrata è stata aggiornata alla versione 10.1.0.
* È ora possibile usare Ctrl/Cmd + R per aggiornare l'editor attualmente attivo. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* La versione dell'API di archiviazione Azure Stack è stata modificata in 2017-04-17.
* La finestra di dialogo Gestisci accesso per ADLS Gen2 ora manterrà la maschera sincronizzata in modo analogo ad altri strumenti di autorizzazioni POSIX. Verrà inoltre visualizzato un avviso se viene apportata una modifica che determina il superamento dei limiti della maschera da parte di un utente o di un gruppo. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Per i caricamenti AzCopy, il flag per calcolare e impostare l'hash MD5 è ora abilitato. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Funzionalità di anteprima

* L'accesso al flusso del codice del dispositivo è ora disponibile per l'anteprima. Per abilitarla, passare a "anteprima" → "usare l'accesso al flusso del codice del dispositivo". Si consiglia agli utenti che hanno riscontrato problemi con le finestre di accesso vuote di provare questa funzionalità, perché potrebbe rivelarsi un tipo di accesso più affidabile.
* Storage Explorer integrato con AzCopy è attualmente disponibile per l'anteprima. Per abilitarlo, passare a "Preview" → "usare AzCopy per il caricamento e il download di BLOB migliorati". I trasferimenti di BLOB completati con AzCopy dovrebbero essere più veloci e più efficienti.

### <a name="fixes"></a>Correzioni

* La finestra di dialogo criteri di accesso non configurerà più una data di scadenza per i criteri di accesso alle archiviazione che non hanno una scadenza. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Sono state apportate alcune modifiche alla finestra di dialogo Genera SAS per assicurarsi che i criteri di accesso archiviati vengano usati correttamente quando si genera una firma di accesso condiviso. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Quando si tenta di caricare un file allineato a un byte non 512 in un BLOB di pagine, Storage Explorer esporrà ora un errore più rilevante. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* La copia di un contenitore BLOB che utilizzava un nome visualizzato avrebbe avuto esito negativo. A questo punto, viene usato il nome effettivo del contenitore BLOB. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Il tentativo di eseguire determinate azioni su una cartella ADLS Gen2 con caratteri Unicode nel nome potrebbe non riuscire. Tutte le azioni dovrebbero ora funzionare. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Problemi noti

* Quando si esegue un download di BLOB non AzCopy, l'MD5 per i file di grandi dimensioni non viene verificato. Ciò è dovuto a un bug in Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Il tentativo di accedere ai BLOB di ADLS Gen2 quando si è protetti da un proxy potrebbe non riuscire.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
   * ADLS Gen2
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per l'esecuzione di Storage Explorer in Linux è necessario installare prima alcune dipendenze. Per ulteriori informazioni, consultare la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) Storage Explorer.

## <a name="version-170"></a>Versione 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Scaricare Azure Storage Explorer 1.7.0
- [Azure Storage Explorer 1.7.0 per Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 per Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 per Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nuovo

* È ora possibile modificare il proprietario e il gruppo proprietario quando si gestisce l'accesso per un contenitore ADLS Gen2, un file o una cartella.
* In Windows, l'aggiornamento Storage Explorer dall'interno del prodotto è ora un'installazione incrementale. Questa operazione dovrebbe comportare un'esperienza di aggiornamento più rapida. Se si preferisce un'installazione pulita, è possibile scaricare il [programma di installazione](https://azure.microsoft.com/features/storage-explorer/) manualmente, quindi installarlo manualmente. #1089

### <a name="preview-features"></a>Funzionalità di anteprima

* L'accesso al flusso del codice del dispositivo è ora disponibile per l'anteprima. Per abilitarla, passare a "anteprima" → "usare l'accesso al flusso del codice del dispositivo". Si consiglia agli utenti che hanno riscontrato problemi con le finestre di accesso vuote di provare questa funzionalità, perché potrebbe rivelarsi un tipo di accesso più affidabile. #938
* Storage Explorer integrato con AzCopy è attualmente disponibile per l'anteprima. Per abilitarlo, passare a "Preview" → "usare AzCopy per il caricamento e il download di BLOB migliorati". I trasferimenti di BLOB completati con AzCopy dovrebbero essere più veloci e più efficienti.

### <a name="fixes"></a>Correzioni

* È ora possibile scegliere il tipo di BLOB che si vuole caricare come quando AzCopy è abilitato. #1111
* In precedenza, se sono stati abilitati i siti web statici per un account di archiviazione ADLS Gen2 e il nome e la chiave sono stati collegati, Storage Explorer non avrebbe rilevato che lo spazio dei nomi gerarchico è stato abilitato. Questo problema è stato risolto. #1081
* Nell'editor BLOB, l'ordinamento in base ai giorni di conservazione rimanenti o lo stato è stato danneggiato. Questo problema è stato risolto. #1106
* Dopo la versione 1.5.0, Storage Explorer non è più in attesa del completamento delle copie lato server prima di segnalare l'esito positivo durante un'operazione di ridenominazione o copia & incolla. Questo problema è stato risolto. #976
* Quando si usa la funzionalità sperimentale AzCopy, il comando copiato dopo aver fatto clic su "Copia comando negli Appunti" non è sempre eseguibile in modo autonomo. A questo punto, verranno copiati tutti i comandi necessari per eseguire il trasferimento manualmente. #1079
* In precedenza, i BLOB di ADLS Gen2 non erano accessibili se si fosse protetti da un proxy. Ciò è dovuto a un bug in una nuova libreria di rete usata da Storage SDK. In 1.7.0 è stato effettuato un tentativo di attenuare questo problema, ma alcuni utenti potrebbero continuare a vedere i problemi. Una correzione completa verrà rilasciata in un aggiornamento futuro. #1090
* In 1.7.0 la finestra di dialogo Salva file ora memorizza correttamente l'ultima posizione in cui è stato salvato il file. #16
* Nel pannello Proprietà è stato visualizzato il livello SKU di un account di archiviazione come tipo di account. Questo problema è stato risolto. #654
* In alcuni casi, è Impossibile interrompere il lease di un BLOB, anche se il nome del BLOB è stato immesso correttamente. Questo problema è stato risolto. #1070

### <a name="known-issues"></a>Problemi noti

* Quando si usa il controllo degli accessi in base al ruolo, Storage Explorer richiede alcune autorizzazioni del livello di gestione per accedere alle risorse di archiviazione Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) .
* Il tentativo di accedere ai BLOB di ADLS Gen2 quando si è protetti da un proxy potrebbe non riuscire.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere il numero 537.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere #537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Versione 1.6.2
1/9/2019

### <a name="hotfixes"></a>Hotfix
* In 1.6.1, le entità aggiunte a Azure Data Lake Storage Gen2 ACL da ObjectId che non sono utenti vengono aggiunte sempre come gruppi. A questo punto, solo i gruppi vengono aggiunti come gruppi; le entità quali applicazioni aziendali e le entità servizio, vengono aggiunte come utenti. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Se un account di archiviazione di Azure Data Lake Storage Gen2 non possiede alcun contenitore ed è stato collegato con nome e chiave, Storage Explorer potrebbe non rilevare che l'Account di archiviazione sia Azure Data Lake Storage Gen2. Questo problema è stato risolto. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* Nella versione 1.6.0, conflitti durante l'operazione di copia e incolla non richiedono una soluzione. Al contrario, la copia in conflitto avrà semplicemente esito negativo. A questo punto verrà richiesto come risolvere il primo conflitto. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* A causa di limitazioni dell'API, nella finestra di dialogo Gestire l'accesso sono state disabilitate tutte le convalide di ObjectIds. È consentita la convalida solo dei nomi delle entità utente. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Nella finestra di dialogo Gestire l'accesso di Azure Data Lake Storage Gen2, non era possibile modificare le autorizzazioni per un gruppo. Questo problema è stato risolto. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* È stato aggiunto il supporto per il caricamento tramite trascinamento della selezione all'editor Azure Data Lake Storage Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Nella proprietà URL disponibile nella finestra di dialogo delle proprietà dei file e delle cartelle di Azure Data Lake Storage Gen2 mancava talvolta una '/'. Questo problema è stato risolto. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se non è possibile ottenere le autorizzazioni correnti per un contenitore, un file o una cartella di Azure Data Lake Storage Gen2, l'errore non viene correttamente visualizzato nel log delle attività. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Il percorso temporaneo creato per l'apertura dei file è stato abbreviato per ridurre le probabilità di creare un percorso che non rispetta il parametro MAX_PATH in Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* La finestra di dialogo Connetti viene ora correttamente visualizzata quando non è connesso alcun utente e non è collegata alcuna risorsa. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Nella versione 1.6.0, il salvataggio delle proprietà per BLOB e file non appartenenti al servizio dello spazio dei nomi gerarchico codificava il valore di ogni proprietà. Sebbene non fosse necessario, quindi, venivano codificati anche valori che contenevano solo caratteri ASCII. Vengono codificati ora solo i valori che contengono caratteri non ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Il caricamento di una cartella in un contenitore BLOB non appartenente al servizio dello spazio dei nomi gerarchico aveva esito negativo se veniva usata una firma di accesso condiviso o se alla firma di accesso condiviso non erano state concesse autorizzazioni di lettura. Questo problema è stato risolto. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* L'annullamento di un trasferimento di AzCopy non funzionava. Questo problema è stato risolto. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy generava un errore se si tentava di scaricare da un contenitore BLOB di Azure Data Lake Storage Gen2 una cartella contente spazi nel nome. Questo problema è stato risolto. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* L'editor CosmosDB non funzionava nella versione 1.6.0. Questo problema è stato corretto. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nuovo

* È ora possibile usare Storage Explorer per accedere ai dati del BLOB tramite il [controllo degli accessi in base al ruolo](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se si è connessi e Storage Explorer non riesce a recuperare le chiavi dell'account di archiviazione, verrà usato un token OAuth per l'autenticazione quando si interagisce con i dati.
* Storage Explorer supporta ora gli account di archiviazione di Azure Data Lake Storage Gen2. Se Storage Explorer rileva che per un account di archiviazione è abilitato lo spazio dei nomi gerarchico, accanto al nome dell'account di archiviazione sarà visibile la voce "(ADLS Gen2 Preview)". Storage Explorer è in grado di rilevare se lo spazio dei nomi gerarchico è abilitato solo se si è connessi o se l'account di archiviazione è stato collegato con il nome e la chiave. Con gli account di archiviazione di Azure Data Lake Storage Gen2, è possibile usare Storage Explorer per:
  * Creare ed eliminare contenitori
  * Gestire proprietà e autorizzazioni (lato sinistro) dei contenitori
  * Visualizzare ed esplorare i dati all'interno dei contenitori
  * Creare nuove cartelle
  * Caricare, scaricare, rinominare ed eliminare file e cartelle
  * Gestire proprietà e autorizzazioni (lato destro) di file e cartelle.
    
    Altre funzionalità tipiche dei BLOB, come l'eliminazione temporanea e gli snapshot, non sono attualmente disponibili. La gestione delle autorizzazioni è disponibile solo se si è connessi. Se si usa un account di archiviazione di Azure Data Lake Storage Gen2, inoltre, Storage Explorer si avvarrà di AzCopy per tutti i caricamenti e i download e, per impostazione predefinita, userà le credenziali di nome e chiave per tutte le operazioni (se disponibili).
* In seguito ai numerosi commenti e suggerimenti inviati dagli utenti, è nuovamente possibile usare l'opzione di interruzione dei lease per interrompere i lease su più BLOB contemporaneamente.

### <a name="known-issues"></a>Problemi noti

* Durante il download da un account di archiviazione di Azure Data Lake Storage Gen2, se uno dei file trasferiti esiste già, è possibile che AzCopy si arresti in modo anomalo. Questo problema verrà risolto in un hotfix futuro.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere #537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Versione 1.6.1
18/12/2018

### <a name="hotfixes"></a>Hotfix
* A causa di limitazioni dell'API, nella finestra di dialogo Gestire l'accesso sono state disabilitate tutte le convalide di ObjectIds. È consentita la convalida solo dei nomi delle entità utente. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Nella finestra di dialogo Gestire l'accesso di Azure Data Lake Storage Gen2, non era possibile modificare le autorizzazioni per un gruppo. Questo problema è stato risolto. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* È stato aggiunto il supporto per il caricamento tramite trascinamento della selezione all'editor Azure Data Lake Storage Gen2. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* Nella proprietà URL disponibile nella finestra di dialogo delle proprietà dei file e delle cartelle di Azure Data Lake Storage Gen2 mancava talvolta una '/'. Questo problema è stato risolto. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Se non è possibile ottenere le autorizzazioni correnti per un contenitore, un file o una cartella di Azure Data Lake Storage Gen2, l'errore non viene correttamente visualizzato nel log delle attività. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Il percorso temporaneo creato per l'apertura dei file è stato abbreviato per ridurre le probabilità di creare un percorso che non rispetta il parametro MAX_PATH in Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* La finestra di dialogo Connetti viene ora correttamente visualizzata quando non è connesso alcun utente e non è collegata alcuna risorsa. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Nella versione 1.6.0, il salvataggio delle proprietà per BLOB e file non appartenenti al servizio dello spazio dei nomi gerarchico codificava il valore di ogni proprietà. Sebbene non fosse necessario, quindi, venivano codificati anche valori che contenevano solo caratteri ASCII. Vengono codificati ora solo i valori che contengono caratteri non ASCII. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Il caricamento di una cartella in un contenitore BLOB non appartenente al servizio dello spazio dei nomi gerarchico aveva esito negativo se veniva usata una firma di accesso condiviso o se alla firma di accesso condiviso non erano state concesse autorizzazioni di lettura. Questo problema è stato risolto. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* L'annullamento di un trasferimento di AzCopy non funzionava. Questo problema è stato risolto. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy generava un errore se si tentava di scaricare da un contenitore BLOB di Azure Data Lake Storage Gen2 una cartella contente spazi nel nome. Questo problema è stato risolto. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* L'editor CosmosDB non funzionava nella versione 1.6.0. Questo problema è stato corretto. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nuovo

* È ora possibile usare Storage Explorer per accedere ai dati del BLOB tramite il [controllo degli accessi in base al ruolo](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se si è connessi e Storage Explorer non riesce a recuperare le chiavi dell'account di archiviazione, verrà usato un token OAuth per l'autenticazione quando si interagisce con i dati.
* Storage Explorer supporta ora gli account di archiviazione di Azure Data Lake Storage Gen2. Se Storage Explorer rileva che per un account di archiviazione è abilitato lo spazio dei nomi gerarchico, accanto al nome dell'account di archiviazione sarà visibile la voce "(ADLS Gen2 Preview)". Storage Explorer è in grado di rilevare se lo spazio dei nomi gerarchico è abilitato solo se si è connessi o se l'account di archiviazione è stato collegato con il nome e la chiave. Con gli account di archiviazione di Azure Data Lake Storage Gen2, è possibile usare Storage Explorer per:
  * Creare ed eliminare contenitori
  * Gestire proprietà e autorizzazioni (lato sinistro) dei contenitori
  * Visualizzare ed esplorare i dati all'interno dei contenitori
  * Creare nuove cartelle
  * Caricare, scaricare, rinominare ed eliminare file e cartelle
  * Gestire proprietà e autorizzazioni (lato destro) di file e cartelle.
    
    Altre funzionalità tipiche dei BLOB, come l'eliminazione temporanea e gli snapshot, non sono attualmente disponibili. La gestione delle autorizzazioni è disponibile solo se si è connessi. Se si usa un account di archiviazione di Azure Data Lake Storage Gen2, inoltre, Storage Explorer si avvarrà di AzCopy per tutti i caricamenti e i download e, per impostazione predefinita, userà le credenziali di nome e chiave per tutte le operazioni (se disponibili).
* In seguito ai numerosi commenti e suggerimenti inviati dagli utenti, è nuovamente possibile usare l'opzione di interruzione dei lease per interrompere i lease su più BLOB contemporaneamente.

### <a name="known-issues"></a>Problemi noti

* Durante il download da un account di archiviazione di Azure Data Lake Storage Gen2, se uno dei file trasferiti esiste già, è possibile che AzCopy si arresti in modo anomalo. Questo problema verrà risolto in un hotfix futuro.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere #537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Versione 1.6.0
05/12/2018

### <a name="new"></a>Nuovo

* È ora possibile usare Storage Explorer per accedere ai dati del BLOB tramite il [controllo degli accessi in base al ruolo](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Se si è connessi e Storage Explorer non riesce a recuperare le chiavi dell'account di archiviazione, verrà usato un token OAuth per l'autenticazione quando si interagisce con i dati.
* Storage Explorer supporta ora gli account di archiviazione di Azure Data Lake Storage Gen2. Se Storage Explorer rileva che per un account di archiviazione è abilitato lo spazio dei nomi gerarchico, accanto al nome dell'account di archiviazione sarà visibile la voce "(ADLS Gen2 Preview)". Storage Explorer è in grado di rilevare se lo spazio dei nomi gerarchico è abilitato solo se si è connessi o se l'account di archiviazione è stato collegato con il nome e la chiave. Con gli account di archiviazione di Azure Data Lake Storage Gen2, è possibile usare Storage Explorer per:
  * Creare ed eliminare contenitori
  * Gestire proprietà e autorizzazioni (lato sinistro) dei contenitori
  * Visualizzare ed esplorare i dati all'interno dei contenitori
  * Creare nuove cartelle
  * Caricare, scaricare, rinominare ed eliminare file e cartelle
  * Gestire proprietà e autorizzazioni (lato destro) di file e cartelle.
    
    Altre funzionalità tipiche dei BLOB, come l'eliminazione temporanea e gli snapshot, non sono attualmente disponibili. La gestione delle autorizzazioni è disponibile solo se si è connessi. Se si usa un account di archiviazione di Azure Data Lake Storage Gen2, inoltre, Storage Explorer si avvarrà di AzCopy per tutti i caricamenti e i download e, per impostazione predefinita, userà le credenziali di nome e chiave per tutte le operazioni (se disponibili).
* In seguito ai numerosi commenti e suggerimenti inviati dagli utenti, è nuovamente possibile usare l'opzione di interruzione dei lease per interrompere i lease su più BLOB contemporaneamente.

### <a name="known-issues"></a>Problemi noti

* Durante il download da un account di archiviazione di Azure Data Lake Storage Gen2, se uno dei file trasferiti esiste già, è possibile che AzCopy si arresti in modo anomalo. Questo problema verrà risolto in un hotfix futuro.
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere #537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versione 1.5.0
29/10/2018

### <a name="new"></a>Nuovo

* È ora possibile usare [AzCopy v10 (anteprima)](https://github.com/Azure/azure-storage-azcopy) per il caricamento e il download di BLOB. Per abilitare questa funzionalità, passare al menu "Sperimentale" e quindi fare clic su "Use AzCopy for Improved Blob Upload and Download" (Usa AzCopy per ottimizzare caricamento e download di BLOB). Quando è abilitata, AzCopy verrà usato negli scenari seguenti:
   * Caricamento di cartelle e file in contenitori BLOB, tramite la barra degli strumenti o il metodo di trascinamento della selezione.
   * Download di cartelle e file, tramite la barra degli strumenti o il menu di scelta rapida.

* Quando inoltre si usa AzCopy:
   * È possibile copiare il comando di AzCopy usato per eseguire il trasferimento negli Appunti. È sufficiente fare clic su "Copy AzCopy Command to Clipboard" (Copia comando AzCopy negli Appunti) nel log attività.
   * Dopo il caricamento sarà necessario aggiornare manualmente l'editor BLOB.
   * Il caricamento di file in BLOB di accodamento non è supportato. I file con estensione vhd verranno caricati come BLOB di pagine e tutti gli altri file verranno caricati come BLOB in blocchi.
   * Gli errori e i conflitti che si verificano durante il caricamento o il download verranno rilevati solo dopo al termine di un caricamento o di un download.

Il supporto per l'uso di AzCopy con condivisioni file sarà disponibile in futuro.
* Storage Explorer usa ora Electron versione 2.0.11.
* L'interruzione del lease può ora essere eseguita solo su un BLOB alla volta. È inoltre necessario immettere il nome del BLOB di cui si vuole interrompere il lease. Questa modifica è stata apportata per ridurre la probabilità di interruzione accidentale di un lease, soprattutto nelle macchine virtuali. 394
* Se si verificano problemi di accesso, è ora possibile provare a reimpostare l'autenticazione. Passare al menu "Guida" e fare clic su "Reimposta" per accedere a questa funzionalità. 419

### <a name="fix"></a>Correzione

* In seguito ai numerosi commenti e suggerimenti inviati dagli utenti, il nodo dell'emulatore predefinito è stato nuovamente abilitato. È comunque possibile aggiungere altre connessioni all'emulatore tramite la finestra di dialogo Connect (Connetti), ma se l'emulatore è configurato in modo da usare le porte predefinite, è anche possibile usare il nodo "Emulator * Default Ports" (Emulatore * Porte predefinite) in "Local & Attached/Storage Accounts" (Locali e collegati/Account di archiviazione). 669
* Storage Explorer non consente più di impostare i valori dei metadati del BLOB che contengono spazi iniziali o finali. 760
* Il pulsante "Sign In" (Accedi) era sempre abilitato nelle stesse pagine della finestra di dialogo Connect (Connetti). Ora è disabilitato nei casi appropriati. 761
* Accesso rapido non genererà più un errore nella console quando non sono stati aggiunti elementi di Accesso rapido.

### <a name="known-issues"></a>Problemi noti

* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere #537.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su questo problema.
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron. Per ovviare a questo problema quando si esegue il caricamento o il download da un contenitore BLOB, è possibile usare la funzionalità sperimentale AzCopy.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; se si prova a usarle mentre si lavora in Azure Stack, potrebbero verificarsi errori imprevisti.
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Versione 1.4.4
15/10/2018

### <a name="hotfixes"></a>Hotfix
* È stato eseguito il rollback della versione dell'API di Gestione delle risorse di Azure per sbloccare gli utenti di Azure per enti pubblici statunitensi. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Le caselle di selezione di caricamento ora usano animazioni CSS per ridurre la quantità di GPU usata da Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nuovo
* Gli allegati delle risorse esterne, ad esempio per le connessioni di firma di accesso condiviso e gli emulatori, sono stati notevolmente migliorati. A questo punto è possibile:
   * Personalizzare il nome della risorsa visualizzato che si collega. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Collegarsi a più emulatori locali usando porte diverse. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Aggiungere le risorse collegate ad Accesso rapido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer supporta ora l'eliminazione temporanea. È possibile:
   * Configurare un criterio per l'eliminazione temporanea facendo clic sul nodo dei contenitori Blob dell'account di archiviazione.
   * Visualizzare i blob eliminati temporaneamente nell'editor di Blob, selezionare "Blob attivati ed eliminati" nell'elenco a discesa accanto alla barra di spostamento.
   * Annullare l'eliminazione dei blob eliminati temporaneamente.

### <a name="fixes"></a>Correzioni
* L'azione "Configura impostazioni CORS" non è più disponibile negli account di Archiviazione Premium poiché gli account di Archiviazione Premium non supportano CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* È ora disponibile una proprietà di firma di accesso condiviso per i servizi collegati di firma di accesso condiviso. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* L'azione "Imposta livello di accesso predefinito" è ora disponibili per gli account di archiviazione BLOB e GPV2 aggiunti ad Accesso rapido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* In alcuni casi, Storage Explorer non riuscirebbe a visualizzare gli account di archiviazione classici. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemi noti
* Quando si usano gli emulatori, ad esempio Emulatore di archiviazione di Azure o Azurite, è necessario che rimangano in attesa di connessioni sulle porte predefinite. In caso contrario, Storage Explorer non potrà eseguire la connessione degli emulatori.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Versione 1.4.3
11/10/2018

### <a name="hotfixes"></a>Hotfix
* È stato eseguito il rollback della versione dell'API di Gestione delle risorse di Azure per sbloccare gli utenti di Azure per enti pubblici statunitensi. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Le caselle di selezione di caricamento ora usano animazioni CSS per ridurre la quantità di GPU usata da Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nuovo
* Gli allegati delle risorse esterne, ad esempio per le connessioni di firma di accesso condiviso e gli emulatori, sono stati notevolmente migliorati. A questo punto è possibile:
   * Personalizzare il nome della risorsa visualizzato che si collega. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Collegarsi a più emulatori locali usando porte diverse. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Aggiungere le risorse collegate ad Accesso rapido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer supporta ora l'eliminazione temporanea. È possibile:
   * Configurare un criterio per l'eliminazione temporanea facendo clic sul nodo dei contenitori Blob dell'account di archiviazione.
   * Visualizzare i blob eliminati temporaneamente nell'editor di Blob, selezionare "Blob attivati ed eliminati" nell'elenco a discesa accanto alla barra di spostamento.
   * Annullare l'eliminazione dei blob eliminati temporaneamente.

### <a name="fixes"></a>Correzioni
* L'azione "Configura impostazioni CORS" non è più disponibile negli account di Archiviazione Premium poiché gli account di Archiviazione Premium non supportano CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* È ora disponibile una proprietà di firma di accesso condiviso per i servizi collegati di firma di accesso condiviso. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* L'azione "Imposta livello di accesso predefinito" è ora disponibili per gli account di archiviazione BLOB e GPV2 aggiunti ad Accesso rapido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* In alcuni casi, Storage Explorer non riuscirebbe a visualizzare gli account di archiviazione classici. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemi noti
* Quando si usano gli emulatori, ad esempio Emulatore di archiviazione di Azure o Azurite, è necessario che rimangano in attesa di connessioni sulle porte predefinite. In caso contrario, Storage Explorer non potrà eseguire la connessione degli emulatori.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versione 1.4.2
24/09/2018

### <a name="hotfixes"></a>Hotfix
* Aggiornare la versione dell'API di Gestione delle risorse di Azure al 01/07/2018 per aggiungere il supporto di nuovi tipi di account di Archiviazione di Azure. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nuovo
* Gli allegati delle risorse esterne, ad esempio per le connessioni di firma di accesso condiviso e gli emulatori, sono stati notevolmente migliorati. A questo punto è possibile:
   * Personalizzare il nome della risorsa visualizzato che si collega. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Collegarsi a più emulatori locali usando porte diverse. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Aggiungere le risorse collegate ad Accesso rapido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer supporta ora l'eliminazione temporanea. È possibile:
   * Configurare un criterio per l'eliminazione temporanea facendo clic sul nodo dei contenitori Blob dell'account di archiviazione.
   * Visualizzare i blob eliminati temporaneamente nell'editor di Blob, selezionare "Blob attivati ed eliminati" nell'elenco a discesa accanto alla barra di spostamento.
   * Annullare l'eliminazione dei blob eliminati temporaneamente.

### <a name="fixes"></a>Correzioni
* L'azione "Configura impostazioni CORS" non è più disponibile negli account di Archiviazione Premium poiché gli account di Archiviazione Premium non supportano CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* È ora disponibile una proprietà di firma di accesso condiviso per i servizi collegati di firma di accesso condiviso. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* L'azione "Imposta livello di accesso predefinito" è ora disponibili per gli account di archiviazione BLOB e GPV2 aggiunti ad Accesso rapido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* In alcuni casi, Storage Explorer non riuscirebbe a visualizzare gli account di archiviazione classici. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemi noti
* Quando si usano gli emulatori, ad esempio Emulatore di archiviazione di Azure o Azurite, è necessario che rimangano in attesa di connessioni sulle porte predefinite. In caso contrario, Storage Explorer non potrà eseguire la connessione degli emulatori.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Versione 1.4.1
28/08/2018

### <a name="hotfixes"></a>Hotfix
* Al primo avvio, Storage Explorer non è riuscito a generare la chiave usata per crittografare dati sensibili. Ciò potrebbe causare problemi quando si usa Accesso rapido e si collegano risorse. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Se l'account non richiede l'autenticazione a più fattori per il relativo tenant principale, ma è necessario per altri tenant, Storage Explorer potrebbe non elencare le sottoscrizioni. A questo punto, dopo l'accesso con un account di questo tipo, Storage Explorer richiederà di immettere nuovamente le credenziali ed eseguire l'autenticazione a più fattori. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer non è riuscito a collegare le risorse di Azure Germania e Azure US Government. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Se l'utente è connesso a due account con lo stesso indirizzo di posta elettronica, Storage Explorer talvolta non riuscirà a mostrare le risorse nella visualizzazione struttura ad albero. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Nei computer Windows più lenti, la schermata iniziale potrebbe talvolta richiedere molto tempo prima di essere visualizzata. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* La finestra di dialogo di connessione viene visualizzata anche se sono presenti account o servizi collegati. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nuovo
* Gli allegati delle risorse esterne, ad esempio per le connessioni di firma di accesso condiviso e gli emulatori, sono stati notevolmente migliorati. A questo punto è possibile:
   * Personalizzare il nome della risorsa visualizzato che si collega. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Collegarsi a più emulatori locali usando porte diverse. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Aggiungere le risorse collegate ad Accesso rapido. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer supporta ora l'eliminazione temporanea. È possibile:
   * Configurare un criterio per l'eliminazione temporanea facendo clic sul nodo dei contenitori Blob dell'account di archiviazione.
   * Visualizzare i blob eliminati temporaneamente nell'editor di Blob, selezionare "Blob attivati ed eliminati" nell'elenco a discesa accanto alla barra di spostamento.
   * Annullare l'eliminazione dei blob eliminati temporaneamente.

### <a name="fixes"></a>Correzioni
* L'azione "Configura impostazioni CORS" non è più disponibile negli account di Archiviazione Premium poiché gli account di Archiviazione Premium non supportano CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* È ora disponibile una proprietà di firma di accesso condiviso per i servizi collegati di firma di accesso condiviso. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* L'azione "Imposta livello di accesso predefinito" è ora disponibili per gli account di archiviazione BLOB e GPV2 aggiunti ad Accesso rapido. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* In alcuni casi, Storage Explorer non riuscirebbe a visualizzare gli account di archiviazione classici. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Problemi noti
* Quando si usano gli emulatori, ad esempio Emulatore di archiviazione di Azure o Azurite, è necessario che rimangano in attesa di connessioni sulle porte predefinite. In caso contrario, Storage Explorer non potrà eseguire la connessione degli emulatori.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Versione 1.3.0
09/07/2018

### <a name="new"></a>Nuovo
* L'accesso ai contenitori $web usati da siti Web statici è ora supportato. In questo modo è possibile caricare e gestire facilmente i file e le cartelle in uso nel sito Web. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* La barra delle applicazioni in macOS è stata riorganizzata. Le modifiche includono un menu File, alcune modifiche ai tasti di scelta rapida e diversi nuovi comandi nel menu dell'app. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* L'endpoint dell'autorità per l'accesso ad Azure Governo degli Stati Uniti è stato modificato in https://login.microsoftonline.us/
* Accessibilità: quando è attiva un'utilità per la lettura dello schermo, la navigazione da tastiera ora funziona con le tabelle usate per visualizzare gli elementi sul lato destro. È possibile usare i tasti di direzione per spostarsi tra righe e colonne, INVIO per richiamare le azioni predefinite, il tasto del menu di scelta rapida per aprire il menu relativo a un elemento e MAIUSC o CTRL per la selezione multipla. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Correzioni
*  In alcuni computer i processi figlio impiegavano troppo tempo ad avviarsi. Quando si verifica questa situazione, un messaggio di errore indica che il processo figlio non è stato avviato in modo tempestivo. Il tempo a disposizione per l'avvio di un processo figlio è stato aumentato da 20 a 90 secondi. Se si verifica ancora questo problema, aggiungere un commento sul problema GitHub collegato. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Usando una firma di accesso condiviso priva di autorizzazioni di lettura, in precedenza non era possibile caricare un BLOB di grandi dimensioni. La logica per il caricamento è stata modificata in modo da funzionare in questo scenario. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* L'impostazione del livello di accesso pubblico per un contenitore rimuove tutti i criteri di accesso e viceversa. Ora il livello di accesso pubblico e i criteri di accesso vengono mantenuti quando si imposta uno dei due elementi. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" era troncato nella finestra di dialogo Proprietà. Questo problema è stato risolto. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Il prefisso "Microsoft Azure Storage Explorer -" mancava nella finestra di dialogo Crea nuova Directory. Questo problema è stato risolto. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Accessibilità: è difficile spostarsi nella finestra di dialogo Aggiungi entità quando si usa VoiceOver. Sono stati apportati miglioramenti. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Accessibilità: il colore di sfondo del pulsante che comprime ed espande il riquadro Azioni e proprietà non era coerente con controlli simili dell'interfaccia utente nel tema Nero a contrasto elevato. Il colore è stato modificato. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Accessibilità: nel tema Nero a contrasto elevato lo stile stato attivo per il pulsante "X" della finestra di dialogo Proprietà non era visibile. Questo problema è stato risolto. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Accessibilità: nelle schede Azioni e Proprietà mancavano diversi valori ARIA e quindi l'esperienza di lettura dello schermo risultava scadente. I valori ARIA mancanti sono stati aggiunti. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Accessibilità: ai nodi dell'albero compressi sul lato sinistro non veniva assegnato false come valore aria-expanded. Questo problema è stato risolto. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemi noti
* Lo scollegamento da una risorsa collegata tramite URI SAS, ad esempio un contenitore BLOB, può causare un errore che impedisce ad altri allegati di essere visualizzati correttamente. Per risolvere questo problema, aggiornare semplicemente il nodo del gruppo. Per altre informazioni, vedere [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/537).
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack non supporta le funzionalità seguenti; provando a usarle durante l'utilizzo di Azure Stack potrebbero verificarsi errori imprevisti:
   * Condivisioni file
   * Livelli di accesso
   * Eliminazione temporanea
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Versione 1.2.0
06/12/2018

### <a name="new"></a>Nuovo
* Se Storage Explorer non riesce a caricare le sottoscrizioni da un solo subset dei tenant, eventuali sottoscrizioni caricate correttamente verranno visualizzate insieme a un messaggio di errore specifico per i tenant che hanno dato esito negativo. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* In Windows, quando un aggiornamento è disponibile, è ora possibile eseguire l'aggiornamento alla chiusura. Quando si seleziona questa opzione, il programma di installazione per l'aggiornamento verrà eseguito dopo la chiusura di Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* È stato aggiunto il ripristino dello snapshot al menu di scelta rapida dell'editor di condivisione file durante la visualizzazione di uno snapshot di condivisione file.[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Il pulsante Cancella coda ora è sempre abilitato.[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* È stato riabilitato il supporto per l'accesso ad AD FS di Azure Stack. Azure Stack 1804 o versione successiva è obbligatorio. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correzioni
* Se si visualizzavano gli snapshot di una condivisione file il cui nome era un prefisso di un'altra condivisione file nello stesso account di archiviazione, venivano elencati anche gli snapshot di altre condivisioni file. Il problema è stato risolto. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Quando si eseguiva il collegamento tramite firma di accesso condiviso, il ripristino di un file da uno snapshot di condivisione file restituiva un errore. Il problema è stato risolto. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Quando si visualizzavano gli snapshot per un BLOB, l'azione di alzare di livello lo snapshot veniva abilitata quando erano selezionati il BLOB di base e un singolo snapshot. L'azione viene ora abilitata solo se è selezionato un singolo snapshot. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Se si avviava un singolo processo (ad esempio il download di un BLOB) che aveva esito negativo, l'operazione non veniva riavviata automaticamente prima dell'avvio di un altro processo dello stesso tipo. Tutti i processi devono ora riavviarsi automaticamente, indipendentemente dal numero di processi in coda.
* Gli editor aperti per i nuovi contenitori BLOB creati in GPV2 e gli account di archiviazione BLOB non disponevano di una colonna di livello di accesso. Il problema è stato risolto. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Una colonna di livello di accesso talvolta non veniva visualizzata quando un account di archiviazione o un contenitore BLOB veniva collegato tramite firma di accesso condiviso. La colonna ora viene sempre visualizzata, ma con un valore vuoto se non è impostato un livello di accesso. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* L'impostazione del livello di accesso di un nuovo BLOB in blocchi caricato era disabilitata. Il problema è stato risolto. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Se il pulsante "Mantieni scheda aperta" veniva richiamato tramite tastiera, si perdeva lo stato attivo della tastiera. Lo stato attivo si sposta ora sulla scheda che è stata lasciata aperta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Per una query eseguita in Generatore di query, VoiceOver non forniva una descrizione utile dell'operatore corrente. La descrizione è ora più completa. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* I collegamenti di paginazione per i vari editor non erano descrittivi. Ora contengono maggiori descrizioni. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Nella finestra di dialogo Aggiungi entità, VoiceOver non indicava la colonna di cui faceva parte un elemento di input. Il nome della colonna corrente è ora incluso nella descrizione dell'elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* I pulsanti di opzione e le caselle di controllo non avevano un bordo visibile quando erano attivati. Il problema è stato risolto. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemi noti
* Quando si usano gli emulatori, ad esempio Emulatore di archiviazione di Azure o Azurite, è necessario che rimangano in attesa di connessioni sulle porte predefinite. In caso contrario, Storage Explorer non potrà eseguire la connessione degli emulatori.
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versione 1.1.0
05/09/2018

### <a name="new"></a>Nuovo
* Storage Explorer supporta ora l'uso di Azurite. Nota: la connessione ad Azurite è hardcoded per gli endpoint di sviluppo predefiniti.
* Storage Explorer supporta ora i livelli di accesso solo per BLOB e gli account di archiviazione per utilizzo generico v2. Per altre informazioni sui livelli di accesso, vedere [qui](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* L'ora di inizio non è più necessaria per la generazione di una firma di accesso condiviso.

### <a name="fixes"></a>Correzioni
* Il recupero delle sottoscrizioni per gli account US Government viene interrotto. Il problema è stato risolto. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* La scadenza per i criteri di accesso non viene salvata correttamente. Il problema è stato risolto. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Quando si genera un URL di firma di accesso condiviso per un elemento in un contenitore, il nome dell'elemento non viene aggiunto all'URL. Il problema è stato risolto. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Quando si crea una firma di accesso condiviso, viene usato come valore predefinito una scadenza già trascorsa. Questo problema era dovuto al fatto che Storage Explorer usava come valori predefiniti l'ultima ora di inizio e l'ultima scadenza usate. Ogni volta che si apre la finestra di dialogo di firma di accesso condiviso, viene generato ora un nuovo set di valori predefiniti. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Quando si eseguono copie tra gli account di archiviazione, viene generata una firma di accesso condiviso di 24 ore. Se dura più di 24 ore la copia non riuscirà. La durata della firma di accesso condiviso è stata aumentata a una settimana per ridurre le probabilità che una copia non riesca a causa della scadenza di una firma di accesso condiviso. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Per alcune attività non sempre funziona fare clic su "Annulla". Il problema è stato risolto. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Per alcune attività la velocità di trasferimento è errata. Il problema è stato risolto. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* L'ortografia di "Indietro" nel menu Visualizza è errata. L'ortografia è stata ora corretta. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Nella pagina finale di Windows Installer è presente il pulsante "Avanti". È stato modificato con il pulsante "Fine". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Lo stato attivo delle schede non è visibile per i pulsanti nelle finestre di dialogo quando si usa il tema Nero a contrasto elevato. È ora visibile.[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* L'utilizzo di maiuscole e minuscole di "Risoluzione automatica" per le azioni nel log attività non è corretto. È ora corretto. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Quando si elimina un'entità da una tabella, nella finestra di dialogo in cui viene richiesto di confermare l'operazione viene visualizzata un'icona di errore. Nella finestra di dialogo viene visualizzata ora un'icona di avviso. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemi noti
* Se si usa Visual Studio per Mac e non è mai stata creata una configurazione personalizzata di AAD, potrebbe non essere possibile effettuare l'accesso. Per risolvere il problema, eliminare il contenuto di ~/.IdentityService/AadConfigurations. Se con tale operazione non si viene sbloccati, aggiungere un commento su [questo problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite non ha ancora implementato completamente tutte le API di archiviazione. Per questo motivo, potrebbe esserci errori imprevisti o un comportamento imprevisto quando si usa Azurite per l'archivio di sviluppo.
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Il caricamento dalla cartella OneDrive non funziona a causa di un bug in NodeJS. Il bug è stato risolto ma non è ancora stato integrato in Electron.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versione 1.0.0
04/16/2018

### <a name="new"></a>Nuovo
* Autenticazione avanzata che consente a Storage Explorer di utilizzare lo stesso archivio account di Visual Studio 2017. Per utilizzare questa funzionalità, sarà necessario accedere nuovamente agli account e impostare nuovamente le sottoscrizioni filtrate.
* Per gli account Azure Stack supportati da AAD, Storage Explorer recupererà ora le sottoscrizioni Azure Stack quando "Stack Azure di destinazione" è abilitato. Non è necessario creare un ambiente di accesso personalizzato.
* Alcuni tasti di scelta rapida sono stati aggiunti per consentire una navigazione più veloce. Sono inclusi diversi pannelli di attivazione/disattivazione e lo spostamento tra gli editor. Vedere il menu Visualizza per altri dettagli.
* I feedback di Storage Explorer ora si basano su GitHub. È possibile raggiungere la pagina dei problemi facendo clic sul pulsante di feedback nella parte inferiore sinistra o andando su [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). È possibile fornire suggerimenti, segnalare un problema, porre domande o lasciare qualsiasi altra forma di feedback.
* Se si sono verificati problemi relativi ai certificati SSL ed è impossibile trovare il certificato che causa l'errore, è possibile avviare Storage Explorer dalla riga di comando con il flag `--ignore-certificate-errors`. Quando viene avviato con questo flag, Storage Explorer ignorerà gli errori del certificato SSL.
* È ora disponibile l'opzione "Scarica" nel menu di scelta rapida per gli elementi di BLOB e di file.
* Accessibilità e supporto di lettura dello schermo migliorati. Se ci si basa sulle funzionalità di accessibilità, vedere la nostra [documentazione di accessibilità](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) per ulteriori informazioni.
* Storage Explorer usa ora Electron 1.8.3

### <a name="breaking-changes"></a>Modifiche di rilievo
* Storage Explorer è diventato una nuova libreria di autenticazione. A seguito del passaggio alla libreria, sarà necessario accedere nuovamente agli account e impostare nuovamente le sottoscrizioni filtrate
* Il metodo utilizzato per crittografare i dati sensibili è stato modificato. Questo può comportare che alcuni elementi di Accesso rapido devono essere nuovamente aggiunti e/o alcune delle risorse associate devono essere ricollegate.

### <a name="fixes"></a>Correzioni
* Alcuni utenti dietro i proxy hanno raggruppato caricamenti o download di BLOB a interrotti da un messaggio di errore "Impossibile risolvere". Il problema è stato risolto.
* Se durante l'uso di un collegamento diretto era necessario eseguire l'accesso, facendo clic sul prompt di accesso veniva visualizzata una finestra di dialogo vuota. Il problema è stato risolto.
* In Linux, se Storage Explorer non è in grado di avviarsi a causa di un arresto anomalo del processo GPU, si riceveranno informazioni sull'arresto anomalo, verrà indicato come utilizzare lo switch "-disable-gpu"e Storage Explorer verrà riavviato automaticamente con l'opzione abilitata.
* Era difficile l’identificazione di criteri di accesso non validi nella finestra di dialogo Criteri di accesso. Gli iD dei criteri di accesso non validi sono ora indicati in rosso per una maggiore visibilità.
* Il registro attività aveva talvolta vaste porzioni di spazi vuoti tra le diverse parti di un'attività. Il problema è stato risolto.
* Nell'editor di query di tabella, se vi fosse stata una clausola timestamp in uno stato non valido e quindi si fosse tentato di modificare un'altra clausola, l'editor si sarebbe bloccato. L'editor ripristinerà la clausola timestamp all’ultimo stato valido quando viene rilevata una modifica in un'altra clausola.
* Se ci si fosse arrestati durante la digitazione nella query di ricerca nella visualizzazione struttura ad albero, la ricerca sarebbe iniziata e lo stato attivo sarebbe stato rubato dalla casella di testo. A questo punto, avviare esplicitamente la ricerca, premendo "invio" o facendo clic sul pulsante per avviare la ricerca.
* Il comando "Ottieni firma di accesso condiviso" talvolta era disattivato quando si faceva clic con il tasto destro del mouse su un file in una condivisione file. Il problema è stato risolto.
* Se il nodo della struttura albero delle risorse con lo stato attivo fosse stato filtrato durante la ricerca, sarebbe stato impossibile aggiungere una tabulazione nell’albero delle risorse e usare i tasti di direzione per esplorare l'albero delle risorse. A questo punto, se il nodo dell'albero delle risorse con lo stato attivo è nascosto, il primo nodo nell'albero delle risorse verrà automaticamente attivato.
* Un separatore aggiuntivo era visibile in alcuni casi nella barra degli strumenti dell'editor. Il problema è stato risolto.
* Talvolta, la casella di testo di navigazione era in eccesso. Il problema è stato risolto.
* Gli editor BLOB e di condivisione file erano talvolta aggiornati costantemente durante il caricamento di molti file in una sola volta. Il problema è stato risolto.
* La funzionalità "Statistiche cartella" non aveva alcun ruolo nella visualizzazione di gestione snapshot di condivisione file. Ora è stata disabilitata.
* In Linux, il menu File non è stato visualizzato. Il problema è stato risolto.
* Durante il caricamento di una cartella in una condivisione file, per impostazione predefinita, venivano caricati solo i contenuti della cartella. A questo punto, il comportamento predefinito è caricare il contenuto della cartella in una cartella corrispondente nella condivisione file.
* L'ordinamento dei pulsanti in alcune finestre di dialogo era stato invertito. Il problema è stato risolto.
* Varie correzioni relative alla sicurezza.

### <a name="known-issues"></a>Problemi noti
* In rari casi, lo stato attivo dell'albero può rimanere bloccato in Accesso rapido. Per sbloccare lo stato attivo, è possibile eseguire l’operazione Aggiorna tutto.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti Linux, è necessario installare [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versione 0.9.6
28/02/2018

### <a name="fixes"></a>Correzioni
* Un problema impediva che i BLOB/file previsti fossero elencati nell'editor. Il problema è stato risolto.
* Un problema causava il passaggio da una visualizzazione snapshot a un'altra per visualizzare gli elementi in modo non corretto. Il problema è stato risolto.

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta [qui](https://github.com/Azure/azure-storage-node/issues/317).
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Version 0.9.5
02/06/2018

### <a name="new"></a>Nuovo

* Supporto per gli snapshot delle condivisioni file:
    * Creare e gestire snapshot per le condivisioni file.
    * Cambiare le viste degli snapshot delle condivisioni file durante l'esplorazione.
    * Ripristinare le versioni precedenti dei file.
* Supporto dell'anteprima per Archivio Azure Data Lake:
    * Connettersi alle risorse di Archivio Azure Data Lake tra più account.
    * Connettersi e condividere le risorse di Archivio Azure Data Lake tramite gli URI ADL.
    * Eseguire operazioni di base su file/cartella in modo ricorsivo.
    * Aggiungere singole cartelle all'Accesso rapido.
    * Visualizzare le statistiche della cartella.

### <a name="fixes"></a>Correzioni
* Miglioramenti delle prestazioni di avvio.
* Varie correzioni di bug.

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versione 0.9.4 e 0.9.3
21/01/2018

### <a name="new"></a>Nuovo
* La finestra Storage Explorer esistente verrà nuovamente usata nei casi seguenti:
    * Apertura di collegamenti diretti generati in Storage Explorer.
    * Apertura di Storage Explorer dal portale.
    * Apertura di Storage Explorer dall'estensione Azure Storage VS Code (disponibile a breve).
* Funzionalità aggiuntiva per aprire una nuova finestra di Storage Explorer da Storage Explorer.
    * Per Windows, è disponibile l'opzione "Nuova finestra" nel menu File e nel menu di scelta rapida della barra delle applicazioni.
    * Per Mac, è disponibile l'opzione "Nuova finestra" nel Menu app.

### <a name="fixes"></a>Correzioni
* Correzione di un problema di sicurezza. Passare alla versione 0.9.4 il prima possibile.
* La pulizia delle attività precedenti non è stata eseguita in modo appropriato. Ciò ha avuto un impatto sulle prestazioni dei processi con esecuzione prolungata. La pulizia è stata ora eseguita correttamente.
* Le azioni che interessano un numero elevato di file e directory potrebbero occasionalmente causare il blocco di Storage Explorer. Le richieste in Azure per le condivisioni file sono ora limitate per ridurre l'uso delle risorse di sistema.

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* I tasti di scelta rapida per "Visualizza Explorer" e "Visualizza Gestione account" sono CTRL/CMD+ MAIUSC+E e CTRL/CMD+MAIUSC+A rispettivamente.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versione 0.9.2
11/01/2017

### <a name="hotfixes"></a>Hotfix
* Erano possibili modifiche impreviste dei dati durante la modifica dei valori Edm.DateTime per le entità di tabella in base al fuso orario locale. Ora l'editor usa una casella di testo normale, fornendo un controllo preciso e coerente sui valori Edm.DateTime.
* Il caricamento o il download di un gruppo di BLOB collegato con un nome e una chiave non veniva avviato. Il problema è stato risolto.
* In precedenza Storage Explorer richiedeva di ripetere l'autenticazione di un account non aggiornato solo se venivano selezionate una o più sottoscrizioni dell'account. Ora Storage Explorer visualizza la richiesta anche se l'account è completamente filtrato.
* Il dominio degli endpoint per Azure per enti pubblici statunitensi era errato. Questo problema è stato risolto.
* Talvolta era difficile fare clic sul pulsante Applica nel pannello Gestisci account. Il problema non dovrebbe più verificarsi.

### <a name="new"></a>Nuovo
* Anteprima del supporto per Azure Cosmos DB:
    * [Documentazione online](./cosmos-db/storage-explorer.md)
    * Creare database e raccolte
    * Manipolare i dati
    * Eseguire query, creare ed eliminare documenti
    * Aggiornare stored procedure, funzioni definite dall'utente o trigger
    * Usare le stringhe di connessione per connettersi e gestire i database
* Miglioramento delle prestazioni del caricamento/download di molti BLOB di piccole dimensioni.
* Aggiunta di un'azione "Riprova tutto" se sono presenti errori in un gruppo di BLOB da caricare o scaricare.
* Azure Storage Explorer ora sospenderà l'iterazione durante il caricamento o download di BLOB se rileva che la connessione di rete si è interrotta. Quando la connessione di rete viene ristabilita, è possibile riprendere l'iterazione.
* Aggiunta delle funzionalità "Chiudi tutto", "Chiudi altre" e "Chiudi" per le schede tramite i menu di scelta rapida.
* Azure Storage Explorer ora usa le finestre di dialogo native e i menu di contesto nativi.
* Azure Storage Explorer ora è più accessibile. I miglioramenti includono:
    * Supporto migliorato per i lettori di schermo, per NVDA in Windows e VoiceOver su Mac
    * Miglioramento dei temi a contrasto elevato
    * Correzione di errori di spostamento con il tasto Tab e di spostamento dello stato attivo con la tastiera

### <a name="fixes"></a>Correzioni
* Se si tenta di aprire o scaricare un BLOB con un nome di file non valido per Windows, l'operazione non riesce. Azure Storage Explorer ora rileva se un nome di BLOB non è valido e chiede se si desidera codificarlo o ignorare il BLOB. Azure Storage Explorer rileva anche se un nome di file sembra essere codificato e chiede se si vuole decodificarlo prima del caricamento.
* Durante il caricamento di BLOB, l'editor per il contenitore di BLOB di destinazione potrebbe talvolta non aggiornarsi correttamente. Il problema è stato risolto.
* Il supporto per varie forme di stringhe di connessione e URI SAS è regredito. Sono stati risolti tutti i problemi noti, ma si prega di inviare commenti se si riscontrano ulteriori problemi.
* Le notifiche degli aggiornamenti non venivano mostrate ad alcuni utenti della versione 0.9.0. Questo problema è stato risolto e gli utenti interessati dal bug possono scaricare manualmente la versione più recente di Azure Storage Explorer [qui](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* I tasti di scelta rapida per "Visualizza Explorer" e "Visualizza Gestione account" sono CTRL/CMD+ MAIUSC+E e CTRL/CMD+MAIUSC+A rispettivamente.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versione 0.9.1 e 0.9.0
10/20/2017
### <a name="new"></a>Nuovo
* Anteprima del supporto per Azure Cosmos DB:
    * [Documentazione online](./cosmos-db/storage-explorer.md)
    * Creare database e raccolte
    * Manipolare i dati
    * Eseguire query, creare ed eliminare documenti
    * Aggiornare stored procedure, funzioni definite dall'utente o trigger
    * Usare le stringhe di connessione per connettersi e gestire i database
* Miglioramento delle prestazioni del caricamento/download di molti BLOB di piccole dimensioni.
* Aggiunta di un'azione "Riprova tutto" se sono presenti errori in un gruppo di BLOB da caricare o scaricare.
* Azure Storage Explorer ora sospenderà l'iterazione durante il caricamento o download di BLOB se rileva che la connessione di rete si è interrotta. Quando la connessione di rete viene ristabilita, è possibile riprendere l'iterazione.
* Aggiunta delle funzionalità "Chiudi tutto", "Chiudi altre" e "Chiudi" per le schede tramite i menu di scelta rapida.
* Azure Storage Explorer ora usa le finestre di dialogo native e i menu di contesto nativi.
* Azure Storage Explorer ora è più accessibile. I miglioramenti includono:
    * Supporto migliorato per i lettori di schermo, per NVDA in Windows e VoiceOver su Mac
    * Miglioramento dei temi a contrasto elevato
    * Correzione di errori di spostamento con il tasto Tab e di spostamento dello stato attivo con la tastiera

### <a name="fixes"></a>Correzioni
* Se si tenta di aprire o scaricare un BLOB con un nome di file non valido per Windows, l'operazione non riesce. Azure Storage Explorer ora rileva se un nome di BLOB non è valido e chiede se si desidera codificarlo o ignorare il BLOB. Azure Storage Explorer rileva anche se un nome di file sembra essere codificato e chiede se si vuole decodificarlo prima del caricamento.
* Durante il caricamento di BLOB, l'editor per il contenitore di BLOB di destinazione potrebbe talvolta non aggiornarsi correttamente. Il problema è stato risolto.
* Il supporto per varie forme di stringhe di connessione e URI SAS è regredito. Sono stati risolti tutti i problemi noti, ma si prega di inviare commenti se si riscontrano ulteriori problemi.
* Le notifiche degli aggiornamenti non venivano mostrate ad alcuni utenti della versione 0.9.0. Questo problema è stato risolto e gli utenti interessati dal bug possono scaricare manualmente la versione più recente di Azure Storage Explorer [qui](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Problemi noti
* Azure Storage Explorer non supporta gli account ADFS.
* I tasti di scelta rapida per "Visualizza Explorer" e "Visualizza Gestione account" sono CTRL/CMD+ MAIUSC+E e CTRL/CMD+MAIUSC+A rispettivamente.
* Quando la destinazione è Azure Stack, il caricamento di alcuni file come BLOB di accodamento può non riuscire.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Questo avviene perché si sta usando la soluzione alternativa di annullamento del filtro descritta qui.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* La shell Electron utilizzata da Azure Storage Explorer ha problemi con l'accelerazione hardware di alcune GPU (graphics processing unit, unità di elaborazione grafica). Se Azure Storage Explorer mostra una finestra principale vuota, è possibile provare ad avviare Azure Storage Explorer dalla riga di comando e disattivare l'accelerazione GPU aggiungendo il parametro `--disable-gpu`:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versione 0.8.16
21/08/2017

### <a name="new"></a>Nuovo
* Quando si apre un BLOB, Storage Explorer richiederà di caricare il file scaricato se viene rilevata una modifica
* Esperienza di accesso ad Azure Stack migliorata
* Miglioramento delle prestazioni per il caricamento/download contemporanei di molti file di piccole dimensioni


### <a name="fixes"></a>Correzioni
* Per alcuni tipi di BLOB, la scelta della sostituzione in presenza di un conflitto di caricamento talvolta comporta il riavvio del caricamento.
* Nella versione 0.8.15 i caricamenti rimangono talvolta bloccati al 99%.
* Durante il caricamento di file in una condivisione file, se si sceglie di caricare in una directory non ancora esistente, il caricamento ha esito negativo.
* Storage Explorer generava timestamp non corretti per le firme di accesso condiviso e le query di tabella.


### <a name="known-issues"></a>Problemi noti
* L'uso di una stringa di connessione con nome e chiave attualmente non funziona. Questo problema verrà risolto nella prossima versione. Fino ad allora è possibile collegarsi con nome e chiave.
* Se si tenta di aprire un file con un nome di file di Windows non valido, il download genera un errore di file non trovato.
* L'azione "Annulla" per un'attività potrebbe impiegare qualche istante per diventare effettiva. Si tratta di un limite della libreria di nodi di Archiviazione di Azure.
* Dopo aver completato il caricamento di un blob, viene aggiornata la scheda che ha avviato il caricamento. Questa è una modifica rispetto al comportamento precedente. Verrà inoltre visualizzata nuovamente la radice del contenitore in cui ci si trova.
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale decisione.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Per gli utenti di Ubuntu 14.04, è necessario assicurarsi che GCC sia aggiornato. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Per gli utenti di Ubuntu 17.04, è necessario installare GConf. Questa operazione può essere eseguita tramite i comandi seguenti e riavviando successivamente il computer:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versione 0.8.14
22/06/2017

### <a name="new"></a>Nuovo

* Versione aggiornata di Electron a 1.7.2 per usufruire di numerosi aggiornamenti importanti sulla sicurezza
* È ora possibile accedere rapidamente alla guida online di risoluzione dei problemi dal menu Guida
* [Guida][2] alla risoluzione dei problemi di Storage Explorer
* [Istruzioni][3] per la connessione a una sottoscrizione di Azure stack

### <a name="known-issues"></a>Problemi noti

* Su Linux, i pulsanti nella finestra di conferma Elimina cartella non vengono registrati con i clic del mouse. Una soluzione alternativa consiste nell'usare il tasto Invio
* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale selezione
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Ubuntu 14.04 richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versione 0.8.13
12/05/2017

#### <a name="new"></a>Nuovo

* [Guida][2] alla risoluzione dei problemi di Storage Explorer
* [Istruzioni][3] per la connessione a una sottoscrizione di Azure stack

#### <a name="fixes"></a>Correzioni

* Corretto: il caricamento del file aveva una probabilità elevata di causare un errore di memoria insufficiente
* Corretto: è ora possibile accedere con PIN/smart card
* Corretto: l'apertura nel portale ora funziona con Azure China 21Vianet, Azure Germania, Azure US Government e Azure Stack
* Corretto: durante il caricamento di una cartella in un contenitore BLOB, talvolta si verificava un errore "Operazione non valida"
* Corretto: selezionare tutto ciò che è stato disabilitato durante la gestione di snapshot
* Corretto: i metadati del BLOB di base avrebbero potuto venire sovrascritti dopo aver visualizzato le proprietà dei relativi snapshot

#### <a name="known-issues"></a>Problemi noti

* Se si sceglie il certificato PIN/smart card non corretto, è necessario il riavvio per fare in modo che Storage Explorer dimentichi tale selezione
* Mentre viene eseguito lo zoom avanti o indietro, il livello di zoom può momentaneamente ripristinare il livello predefinito
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Ubuntu 14.04 richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versione 0.8.12, 0.8.11 e 0.8.10
07/04/2017

#### <a name="new"></a>Nuovo

* Storage Explorer si chiude automaticamente quando si installa un aggiornamento dalla notifica di aggiornamento
* L'accesso rapido sul posto fornisce un'esperienza ottimizzata per l'uso con le risorse a cui si accede di frequente
* Nell'editor del contenitore BLOB, è ora possibile visualizzare a quale macchina virtuale appartiene un BLOB dedicato
* È ora possibile comprimere il pannello di sinistra
* L'azione Individuazione ora viene eseguito contemporaneamente al download
* Usare Statistiche negli editor Contenitore BLOB, Condivisione File e Tabelle per visualizzare le dimensioni della risorsa o selezione
* È ora possibile accedere agli account di Azure Stack basati su Azure Active Directory (AAD).
* È ora possibile caricare file di archivio superiori ai 32 MB per gli account di archiviazione Premium
* Supporto di accessibilità migliorato
* È ora possibile aggiungere certificati SSL attendibili X.509 codificati Base-64 andando su Modifica -&gt; Certificati SSL -&gt; Importa certificati

#### <a name="fixes"></a>Correzioni

* Corretto: dopo l'aggiornamento delle credenziali dell'account, la visualizzazione albero talvolta non veniva aggiornata automaticamente
* Corretto: la generazione di una firma di accesso condiviso per le code e le tabelle dell'emulatore dà origine a un URL non valido
* Corretto: gli account di archiviazione premium possono ora essere espansi mentre è abilitato un proxy
* Corretto: il pulsante Applica nella pagina di gestione degli account non funziona se si aveva selezionato 1 o 0 account
* Corretto: il caricamento di BLOB che richiedono le risoluzioni di conflitti potrebbe non andare a buon termine - corretto in 0.8.11
* Corretto: l'invio di commenti e suggerimenti veniva interrotto nella versione 0.8.11 - corretto in 0.8.12

#### <a name="known-issues"></a>Problemi noti

* Dopo l'aggiornamento a 0.8.10, è necessario aggiornare tutte le credenziali.
* Mentre viene eseguito lo zoom avanti o indietro, il livello di zoom può momentaneamente ripristinare il livello predefinito.
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Sebbene Azure Stack attualmente non supporta le condivisioni file, viene comunque visualizzato un nodo delle condivisioni di file in un account di archiviazione di Azure Stack associato.
* Ubuntu 14.04 richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versione 0.8.9 e 0.8.8
23/02/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nuovo

* Storage Explorer 0.8.9 scaricherà automaticamente la versione più recente degli aggiornamenti.
* Hotfix: l'uso di un URI di firma di accesso condiviso generato da portale per collegare un account di archiviazione comporta un errore.
* È ora possibile creare, gestire e alzare di livello degli snapshot di BLOB.
* È ora possibile accedere agli account di Azure Cina 21Vianet, Azure Germania e Azure per enti pubblici statunitensi.
* È ora possibile modificare il livello di zoom. Usare le opzioni nel menu Visualizza per Zoom avanti, Zoom indietro e Reimposta zoom.
* I caratteri Unicode sono ora supportati nei metadati dell'utente per file e BLOB.
* Miglioramenti all'accessibilità.
* Le note sulla versione successiva possono essere visualizzate dalla notifica di aggiornamento. È inoltre possibile visualizzare le note sulla versione attuale dal menu Guida.

#### <a name="fixes"></a>Correzioni

* Corretto: il numero di versione viene ora visualizzato correttamente nel Pannello di controllo in Windows
* Corretto: la ricerca non è più limitata a 50.000 nodi
* Corretto: caricamento predefinito in una condivisione di file se non esiste già una directory di destinazione
* Corretto: maggiore stabilità per processi di caricamento e download prolungati

#### <a name="known-issues"></a>Problemi noti

* Mentre viene eseguito lo zoom avanti o indietro, il livello di zoom può momentaneamente ripristinare il livello predefinito.
* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso.
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti.
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori.

16/12/2016
### <a name="version-087"></a>Versione 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nuovo

* È possibile scegliere come risolvere i conflitti all'inizio di una sessione di aggiornamento, download o copia nella finestra Attività
* È possibile passare il mouse su una scheda per visualizzare il percorso completo della risorsa di archiviazione
* Quando si fa clic su una scheda, si sincronizza con il relativo percorso nel riquadro di spostamento a sinistra

#### <a name="fixes"></a>Correzioni

* Corretto: Storage Explorer è ora un'app attendibile su Mac
* Corretto: Ubuntu 14.04 è supportato nuovamente
* Corretto: a volte l'interfaccia utente Aggiungi account lampeggiava durante il caricamento delle sottoscrizioni
* Corretto: a volte non tutte le risorse di archiviazione venivano elencate nel riquadro di spostamento a sinistra
* Corretto: a volte il riquadro azioni visualizzava azioni vuote
* Corretto: le dimensioni della finestra dell'ultima sessione chiusa vengono ora mantenute
* Corretto: è possibile aprire più schede per la stessa risorsa usando il menu di scelta rapida

#### <a name="known-issues"></a>Problemi noti

* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate o potrebbero essere generate eccezioni non gestite
* La prima volta che si usa Storage Explorer in macOS potrebbero essere visualizzate diverse richieste di autorizzazione dell'utente per l'accesso a Keychain. È consigliabile selezionare Consenti sempre per non visualizzare più la richiesta di conferma

18/11/2016
### <a name="version-086"></a>Versione 0.8.6

#### <a name="new"></a>Nuovo

* Possibilità di aggiungere ad Accesso rapido i servizi usati più di frequente per semplificare la navigazione
* Possibilità di aprire più editor in schede diverse. Clic singolo per aprire una scheda temporanea; fare doppio clic per aprire una scheda permanente. È anche possibile fare clic sulla scheda temporanea per renderla una scheda permanente
* Sono stati apportati miglioramenti evidenti a prestazioni e stabilità per operazioni di caricamento e download, soprattutto per file di grandi dimensioni in computer veloci
* Possibilità di creare cartelle vuote "virtuali" in contenitori BLOB
* È stato reintrodotto l'ambito di ricerca con la nuova ricerca avanzata con sottostringa, pertanto ora vi sono due opzioni di ricerca:
    * Ricerca globale: è sufficiente immettere solo un termine di ricerca nella casella di testo di ricerca
    * Ricerca con ambito: fare clic sull'icona della lente di ingrandimento accanto a un nodo, quindi aggiungere un termine di ricerca alla fine del percorso o fare clic con il pulsante destro del mouse e selezionare "Cerca da qui"
* Sono stati aggiunti vari temi: Chiaro (impostazione predefinita), Scuro, Nero a contrasto elevato e Bianco a contrasto elevato. Scegliere Modifica -&gt; Temi per modificare le preferenze dei temi
* È possibile modificare le proprietà di BLOB e file
* Sono ora supportati i messaggi in coda codificati (base64) e non codificati
* In Linux è ora necessario un sistema operativo a 64 bit. Per questa versione è supportato solo Ubuntu 16.04.1 LTS a 64 bit
* È stato aggiornato il logo

#### <a name="fixes"></a>Correzioni

* Corretto: problemi di blocco della schermata
* Corretto: sicurezza avanzata
* Corretto: a volte venivano visualizzati account collegati doppi
* Corretto: un blob con un tipo di contenuto non definito poteva generare un'eccezione
* Corretto: non era possibile aprire il pannello Query in una tabella vuota
* Corretto: bug delle variabili in Cerca
* Corretto: il numero delle risorse che è possibile caricare tramite "Carica altro" è stato aumentato da 50 a 100
* Corretto: alla prima esecuzione, se si è effettuato l'accesso a un account, vengono ora selezionate tutte le sottoscrizioni per tale account per impostazione predefinita

#### <a name="known-issues"></a>Problemi noti

* Questa versione di Storage Explorer non può essere eseguita in Ubuntu 14.04
* Per aprire più schede per la stessa risorsa, non fare clic in modo continuo sulla stessa risorsa. Fare clic su un'altra risorsa, tornare indietro e quindi fare clic sulla risorsa originale per aprirla nuovamente in un'altra scheda
* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti
* Il caricamento simultaneo di più di 3 gruppi di BLOB o di file può causare errori
* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate o potrebbero essere generate eccezioni non gestite

03/10/2016
### <a name="version-085"></a>Versione 0.8.5

#### <a name="new"></a>Nuovo

* È ora possibile usare le chiavi di firma di accesso condiviso generate dal portale per collegarsi all'account di archiviazione e alle risorse

#### <a name="fixes"></a>Correzioni

* Corretto: a volte la race condition durante la ricerca faceva sì che i nodi diventassero non espandibili
* Corretto: "Usa HTTP" non funziona quando ci si connette agli account di archiviazione con il nome account e la chiave
* Corretto: le chiavi di firma di accesso condiviso (specialmente quelle generati da portale) restituiscono un errore "barra finale"
* Corretto: problemi di importazione della tabella
    * In alcuni casi la chiave di partizione e la chiave di riga venivano annullate
    * Impossibile leggere le chiavi di partizione "null"

#### <a name="known-issues"></a>Problemi noti

* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate
* Azure Stack attualmente non supporta i file, quindi il tentativo di espandere File genera un errore

12/09/2016
### <a name="version-084"></a>Versione 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nuovo

* Generazione di collegamenti diretti ad account di archiviazione, contenitori, code, tabelle o condivisioni file per accedere facilmente alle risorse e condividerle - Supporto Windows e Mac OS
* Ricerca di contenitori BLOB, tabelle, code, condivisioni file o account di archiviazione dalla casella di ricerca
* È ora possibile raggruppare le clausole nel generatore di query della tabella
* Rinominare e copiare/incollare contenitori BLOB, condivisioni file, tabelle, BLOB, cartelle di BLOB, file e directory all'interno di contenitori e account collegati alla firma di accesso condiviso
* Quando vengono ridenominati e copiati contenitori BLOB e condivisioni file, proprietà e metadati vengono mantenuti

#### <a name="fixes"></a>Correzioni

* Corretto: impossibile modificare le voci della tabella se contengono le proprietà di tipo booleano o binario

#### <a name="known-issues"></a>Problemi noti

* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate

03/08/2016
### <a name="version-083"></a>Versione 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nuovo

* Ridenominazione di contenitori, tabelle e condivisioni file
* Migliore esperienza con il Generatore di query
* Possibilità di salvare e caricare le query
* Collegamenti diretti ad account di archiviazione o contenitori, code, tabelle o condivisioni file per accedere facilmente alle risorse e condividerle (solo su Windows, supporto per macOS presto disponibile)
* Possibilità di gestire e configurare regole CORS

#### <a name="fixes"></a>Correzioni

* Corretto: gli account di Microsoft richiedono la riautenticazione ogni 8-12 ore

#### <a name="known-issues"></a>Problemi noti

* In alcuni casi l'interfaccia utente potrebbe sembrare bloccata. Per risolvere il problema, ingrandire la finestra
* L'installazione di macOS potrebbe richiedere autorizzazioni elevate
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni
* Quando si ridenominano condivisioni file, contenitori BLOB e tabelle non vengono conservati i metadati o altre proprietà nel contenitore, ad esempio la quota di condivisione file, il livello di accesso pubblico o i criteri di accesso
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* La copia o la ridenominazione delle risorse non funziona all'interno di account associati alla firma di accesso condiviso

07/07/2016
### <a name="version-082"></a>Versione 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nuovo

* Gli account di archiviazione vengono raggruppati in sottoscrizioni. L'archiviazione e le risorse di sviluppo collegate tramite chiave o firma di accesso condiviso vengono visualizzate nel nodo (locale e collegato)
* Disconnettersi dagli account nel pannello "Impostazioni account Azure"
* Configurare le impostazioni proxy per abilitare e gestire l'accesso
* Creare e interrompere i lease di blob
* Aprire contenitori BLOB, code, tabelle e i file con un unico clic

#### <a name="fixes"></a>Correzioni

* Corretto: i messaggi in coda con le librerie .NET o Java non vengono decodificati correttamente da base64
* Corretto: le tabelle $metrics non vengono visualizzate per gli account di archiviazione Blob
* Corretto: il nodo delle tabelle non funziona per l'archiviazione locale (sviluppo)

#### <a name="known-issues"></a>Problemi noti

* L'installazione di macOS potrebbe richiedere autorizzazioni elevate

15/06/2016
### <a name="version-080"></a>Versione 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nuovo

* Supporto per condivisione file: visualizzazione, caricamento, download, copia di file e directory, URI della firma di accesso condiviso (creazione e la connessione)
* Esperienza utente migliorata in termini di connessione a Storage con chiavi di account o URI della firma di accesso condiviso
* Risultati della query relativa alla tabella di esportazione
* Riordinamento e personalizzazione delle colonne della tabella
* Visualizzazione dei contenitori BLOB $logs e delle tabelle $metrics per gli account di archiviazione con le metriche abilitate
* Comportamento di esportazione e importazione migliorato, include ora il tipo di valore della proprietà

#### <a name="fixes"></a>Correzioni

* Corretto: il caricamento o il download di BLOB di grandi dimensioni può essere incompleto
* Corretto: la modifica, l'aggiunta o l'importazione di un'entità con un valore numerico ("1") ne comporta il raddoppiamento
* Corretto: impossibile espandere il nodo della tabella nell'ambiente di sviluppo locale

#### <a name="known-issues"></a>Problemi noti

* L tabelle $metrics non sono visibili per gli account di archiviazione Blob
* I messaggi in coda aggiunti a livello di programmazione potrebbe non essere visualizzati correttamente se i messaggi vengono codificati tramite la codifica Base64

17/05/2016
### <a name="version-07201605090"></a>Versione 0.7.20160509.0

#### <a name="new"></a>Nuovo

* Migliore gestione degli errori per i crash dell'app

#### <a name="fixes"></a>Correzioni

* Correzione del bug per cui i messaggi sulla barra delle informazioni talvolta non vengono visualizzati quando sono necessarie credenziali di accesso

#### <a name="known-issues"></a>Problemi noti

* Tabelle: in fase di aggiunta, modifica o importazione di un'entità che dispone di una proprietà con un valore numerico ambiguo, ad esempio "1" o "1.0", quando l'utente tenta di inviare il valore come un `Edm.String`, questo ritornerà indietro attraverso l'API del client come Edm.Double

31/03/2016

### <a name="version-07201603250"></a>Versione 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nuovo

* Supporto tabella: visualizzazione, esecuzione di query, esportazione, importazione e operazioni CRUD per le entità
* Supporto coda: visualizzazione, aggiunta, rimozione dei messaggi dalla coda
* Generazione di URI della firma di accesso condiviso per gli account di archiviazione
* La connessione agli account di archiviazione con l'URI della firma di accesso condiviso
* Notifiche di aggiornamento per gli aggiornamenti futuri di Storage Explorer
* Aspetto aggiornato

#### <a name="fixes"></a>Correzioni

* Miglioramenti dell'affidabilità e delle prestazioni

### <a name="known-issues-amp-mitigations"></a>Problemi noti &amp; Prevenzione

* Il download di file BLOB di grandi dimensioni non funziona correttamente. È consigliabile usare AzCopy fino alla risoluzione del problema
* Le credenziali dell'account non verranno recuperate o memorizzate nella cache se non è possibile trovare la cartella home o questa non può essere scritta
* In fase di aggiunta, modifica o importazione di un'entità che dispone di una proprietà con un valore numerico ambiguo, ad esempio "1" o "1.0", quando l'utente tenta di inviare il valore come un `Edm.String`, questo ritornerà indietro attraverso l'API del client come Edm.Double
* Quando si importano file CSV con i record su più righe, i dati possono venire tagliati o mescolati

03/02/2016

### <a name="version-07201601291"></a>Versione 0.7.20160129.1

#### <a name="fixes"></a>Correzioni

* Miglioramento delle prestazioni complessive durante il caricamento, il download e la copia di BLOB

14/01/2016

### <a name="version-07201601050"></a>Versione 0.7.20160105.0

#### <a name="new"></a>Nuovo

* Supporto Linux (funzionalità analoghe a OSX)
* Aggiunta di contenitori BLOB con la chiave di firma di accesso condiviso (SAS)
* Aggiunta degli account di archiviazione per Azure China 21Vianet
* Aggiunta di account di archiviazione con endpoint personalizzati
* Apertura e visualizzazione di BLOB di testo e immagine del contenuto
* Visualizzazione e modifica di metadati e proprietà di BLOB

#### <a name="fixes"></a>Correzioni

* Corretto: a volte il caricamento o il download di un numero elevato di BLOB (500+) può risultare in una schermata bianca dell'app
* Corretto: quando si imposta il livello di accesso pubblico del contenitore BLOB, il nuovo valore non viene aggiornato finché non si imposta nuovamente lo stato attivo sul contenitore. Inoltre, per impostazione predefinita la finestra di dialogo è sempre impostata su "Nessun accesso pubblico" e non sul valore effettivo corrente.
* Miglioramenti generali della tastiera/accessibilità e del supporto interfaccia utente
* Wrapping di testo della cronologia di navigazione quando è lungo con spazio
* La finestra di dialogo della firma di accesso condiviso supporta la convalida dell'input
* L'archiviazione locale continua a essere disponibile anche se le credenziali utente sono scadute
* Quando viene eliminato un contenitore BLOB aperto, viene chiuso Esplora risorse BLOB sul lato destro

#### <a name="known-issues"></a>Problemi noti

* Linux richiede l'aggiornamento della versione gcc. La procedura per effettuare l'aggiornamento è riportata qui sotto:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

18/11/2015
### <a name="version-07201511160"></a>Versione 0.7.20151116.0

#### <a name="new"></a>Nuovo

* Versioni per macOS e Windows
* Accesso per visualizzare gli account di archiviazione: possibilità di usare l'account aziendale, l'account Microsoft, 2FA e così via.
* Archivio di sviluppo locale (usare l'emulatore di archiviazione, solo Windows)
* Supporto di Azure Resource Manager e delle risorse Classic
* Creazione ed eliminazione di BLOB, code o tabelle
* Ricerca di BLOB, code o tabelle specifiche
* Esplorazione del contenuto dei contenitori BLOB
* Visualizzazione e spostamento tra directory
* Caricamento, download ed eliminazione di BLOB e cartelle
* Visualizzazione e modifica di metadati e proprietà di BLOB
* Generazione di chiavi di firma di accesso condiviso
* Gestione e creazione di SAP (Stored Access Policies)
* Ricerca di BLOB in base al prefisso
* Trascinamento della selezione di file da caricare o scaricare

#### <a name="known-issues"></a>Problemi noti

* Quando si imposta il livello di accesso pubblico del contenitore BLOB, il nuovo valore non viene aggiornato finché non si imposta nuovamente lo stato attivo sul contenitore
* Quando si apre la finestra di dialogo per impostare il livello di accesso pubblico, viene sempre visualizzato "Nessun accesso pubblico" come valore predefinito e non il valore corrente effettivo
* Impossibile rinominare i BLOB scaricati
* Le voci del log attività a volte rimangono "bloccate" in stato in corso quando si verifica un errore, e l'errore non viene visualizzato
* A volte si verifica un crash o diventa completamente bianco quando si cerca di caricare o scaricare un numero elevato di BLOB
* In alcuni casi l'annullamento di un'operazione di copia non funziona
* Durante la creazione di un contenitore (BLOB/coda/tabella), se si immette un nome non valido di input e si procede alla creazione di un altro nome in un altro tipo di contenitore non è possibile impostare lo stato attivo sul nuovo tipo
* Impossibile creare una nuova cartella o rinominare una cartella




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
