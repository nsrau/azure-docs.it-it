---
title: Microsoft Azure Storage Explorer 0.8.7 (anteprima) | Documentazione Microsoft
description: Note sulla versione di Microsoft Azure Storage Explorer 0.8.7 (anteprima)
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Microsoft Azure Storage Explorer 0.8.7 (anteprima)
## <a name="overview"></a>Panoramica
Questo articolo contiene le note sulla versione per la versione di anteprima di Azure Storage Explorer 0.8.7.

[Microsoft Azure Storage Explorer (anteprima)](./vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

## <a name="azure-storage-explorer-087-preview"></a>Azure Storage Explorer 0.8.7 (anteprima)
### <a name="download-azure-storage-explorer-087-preview"></a>Download di Azure Storage Explorer 0.8.7 (anteprima)
- [Azure Storage Explorer 0.8.7 Preview per Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 0.8.7 Preview per Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 0.8.7 Preview per Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>Nuovi aggiornamenti
* È possibile scegliere come risolvere i conflitti all'inizio di un aggiornamento, eseguire il download o copiare la sessione nella finestra **Activities** (Attività).
* È possibile passare il mouse su una scheda per visualizzare il percorso completo della risorsa di archiviazione.
* Quando si fa clic su una scheda, si sincronizza con il relativo percorso nel riquadro di spostamento a sinistra.

### <a name="fixes"></a>Correzioni
* Corretto: Storage Explorer è ora un'app attendibile in macOS.
* Corretto: Ubuntu 14.04 è supportato nuovamente.
* Corretto: a volte l'interfaccia utente Aggiungi account lampeggiava durante il caricamento delle sottoscrizioni.
* Corretto: a volte non tutte le risorse di archiviazione venivano elencate nel riquadro di spostamento a sinistra.
* Corretto: a volte il riquadro azioni visualizzava azioni vuote.
* Corretto: le dimensioni della finestra dell'ultima sessione chiusa vengono ora mantenute.
* Corretto: è possibile aprire più schede per la stessa risorsa usando il menu di scelta rapida.

### <a name="known-issues"></a>Problemi noti
* Accesso rapido funziona solo con gli elementi basati su sottoscrizione. Questa versione non supporta le risorse locali e le risorse collegate tramite chiave o token di firma di accesso condiviso.
* Accesso rapido può impiegare alcuni secondi per passare alla risorsa di destinazione, a seconda del numero di risorse presenti.
* Il caricamento simultaneo di più di tre gruppi di BLOB o di file può causare errori.
* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate o potrebbero essere generate eccezioni non gestite.
* La prima volta che si usa Storage Explorer in macOS potrebbero essere visualizzate diverse richieste di autorizzazione dell'utente per l'accesso a Keychain. È consigliabile selezionare **Always Allow** (Consenti sempre) per non visualizzare più la richiesta di conferma.

## <a name="previous-releases"></a>Versioni precedenti
### <a name="features"></a>Funzionalità
#### <a name="main-features"></a>Funzionalità principali
* Versioni per macOS, Linux e Windows
* Accesso per visualizzare gli account di archiviazione raggruppati per sottoscrizione:
    * Possibilità di usare l'account aziendale, l'account Microsoft, 2FA e così via
    * Configurazione e gestione delle impostazioni proxy
    * Rimozione di account tramite la disconnessione
* Connessione ad account di archiviazione mediante:
    * Nome e chiave dell'account
    * Endpoint personalizzati (tra cui Azure Cina)
    * URI di firma di accesso condiviso per account di archiviazione
* Supporto di Azure Resource Manager e Archiviazione (versione classica)
* Generazione di chiavi di firma di accesso condiviso per BLOB, contenitori BLOB, code, tabelle o condivisioni file
* Connessione a contenitori BLOB, code, tabelle o condivisioni file con la chiave di firma di accesso condiviso
* Gestione di criteri di accesso archiviati per contenitori BLOB, code, tabelle o condivisioni file
* Archivio di sviluppo locale con l'emulatore di archiviazione (solo Windows)
* Creazione ed eliminazione di contenitori BLOB, code o tabelle
* Visualizzazione di contenitori BLOB $logs e tabelle $metrics
* Ricerca di BLOB, code, tabelle o condivisioni file specifiche
* Collegamenti diretti ad account di archiviazione o contenitori, code, tabelle o condivisioni file per accedere facilmente alle risorse e condividerle
* Ridenominazione di contenitori BLOB, tabelle e condivisioni file
* Possibilità di gestire e configurare regole CORS
* Possibilità di copiare facilmente chiave primaria e secondaria per gli account di archiviazione
* Controlli di MD5 su caricamento e download per verificare l'integrità e la coerenza dei dati
* Ricerca di contenitori BLOB, tabelle, code, condivisioni file o account di archiviazione dalla casella di ricerca
* Possibilità di aggiungere ad Accesso rapido i servizi usati più di frequente per semplificare la navigazione
* Possibilità di aprire più editor in schede diverse. Clic singolo per aprire una scheda temporanea. Doppio clic per aprire una scheda permanente. È anche possibile fare clic sulla scheda temporanea per renderla permanente.
* Miglioramenti evidenti a prestazioni e stabilità per operazioni di caricamento e download, soprattutto per file di grandi dimensioni in computer veloci
* Reintroduzione della ricerca in ambito avanzata e aggiunta del concetto di ambito. È possibile immettere il percorso di un nodo tramite l'icona visualizzata al passaggio del mouse, fare clic con il pulsante destro del mouse e selezionare "Search From Here" (Cerca da qui) o immetterlo manualmente per definire l'ambito del nodo. Dopo aver definito l'ambito, è possibile aggiungere un termine di ricerca alla fine del percorso per eseguire una ricerca approfondita a partire da tale nodo.
* Sono stati aggiunti vari temi: Chiaro (impostazione predefinita), Scuro, Nero a contrasto elevato e Bianco a contrasto elevato.
* Scegliere Modifica -> Temi per modificare le preferenze dei temi.
* In Linux è ora necessario un sistema operativo a 64 bit
* È stato aggiornato il logo
#### <a name="blobs"></a>Blobs
* Visualizzazione di BLOB e spostamento tra directory
* Caricamento, download, eliminazione e copia di BLOB e cartelle
* Apertura e visualizzazione di BLOB di testo e immagine del contenuto
* Visualizzazione e modifica di metadati e proprietà di BLOB
* Ricerca di BLOB in base al prefisso
* Creazione e interruzione di lease per BLOB e contenitori BLOB
* Trascinamento della selezione di file da caricare
* Ridenominazione di BLOB e cartelle
* Possibilità di creare cartelle vuote "virtuali" in contenitori BLOB
* Possibilità di modificare le proprietà di BLOB e file
#### <a name="tables"></a>Tabelle
* Visualizzazione e query di entità con ODATA o uso del generatore di query per creare query complesse
* Aggiunta, modifica ed eliminazione di entità
* Importazione ed esportazione di contenuti di tabelle in formato CSV (inclusa l'esportazione dei risultati di query)
* Personalizzazione dell'ordine delle colonne
* Possibilità di salvare le query
#### <a name="queues"></a>Code
* Anteprima degli ultimi 32 messaggi
* Aggiunta, rimozione dalla coda e visualizzazione di messaggi
* Cancellazione della coda
* Possibilità di decidere se codificare/decodificare i messaggi in coda
#### <a name="file-shares"></a>Condivisioni file
* Visualizzazione di file e spostamento tra directory
* Caricamento, download, eliminazione e copia di file e directory
* Visualizzazione delle proprietà di file
* Ridenominazione di file e directory

### <a name="bug-fixes"></a>Correzioni di bug
* Corretto: problemi di blocco della schermata
* Corretto: sicurezza avanzata

### <a name="known-issues"></a>Problemi noti
* È possibile eseguire la ricerca in un massimo di 50.000 nodi circa. Se si supera tale limite, le prestazioni potrebbero risultare peggiorate. L'installazione di macOS può richiedere autorizzazioni elevate.
* Il pannello delle impostazioni dell'account potrebbe indicare che è necessario immettere nuovamente le credenziali per filtrare le sottoscrizioni.
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione.
* Azure Stack attualmente non supporta i file, quindi il tentativo di espandere il nodo **File** genera un errore.
* Per l'installazione di Linux 14.04 è necessaria la versione gcc aggiornata. Questi passaggi illustrano come eseguire l'aggiornamento:

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>Versioni precedenti
#### <a name="october-2016-release-version-085"></a>Versione di ottobre 2016 (versione 0.8.5)
* [Download per Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Download per Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Download per Linux](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->


