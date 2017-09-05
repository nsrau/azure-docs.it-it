---
title: Note sulla versione di Microsoft Azure Storage Explorer (anteprima) | Microsoft Docs
description: Note sulla versione di Microsoft Azure Storage Explorer (anteprima)
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: cawa
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 63a24f6b153390533bba0888fd1051508c65bf6e
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="microsoft-azure-storage-explorer-preview-release-notes"></a>Note sulla versione di Microsoft Azure Storage Explorer (anteprima)

Questo articolo contiene le note sulla versione di anteprima di Azure Storage Explorer 0.8.16, nonché sulle versioni precedenti.

[Microsoft Azure Storage Explorer (anteprima)](./vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

## <a name="version-0816-preview"></a>Versione 0.8.16 (anteprima)
21/08/2017

### <a name="download-azure-storage-explorer-0816-preview"></a>Download di Azure Storage Explorer 0.8.16 (anteprima)
- [Azure Storage Explorer 0.8.16 (anteprima) per Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 0.8.16 (anteprima) per Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 0.8.16 (anteprima) per Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nuovo
* Quando si apre un BLOB, Storage Explorer richiederà di caricare il file scaricato se viene rilevata una modifica
* Esperienza di accesso ad Azure Stack migliorata
* Miglioramento delle prestazioni per il caricamento/download contemporanei di molti file di piccole dimensioni


### <a name="fixes"></a>Correzioni
* Per alcuni tipi di BLOB, la scelta della sostituzione in presenza di un conflitto di caricamento talvolta comporta il riavvio del caricamento. 
* Nella versione 0.8.15 i caricamenti rimangono talvolta bloccati al 99%.
* Durante il caricamento di file in una condivisione file, se si sceglie di caricare in una directory non ancora esistente, il caricamento ha esito negativo.
* Storage Explorer generava timestamp non corretti per le firme di accesso condiviso e le query di tabella.


Problemi noti
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

## <a name="version-0814-preview"></a>Versione 0.8.14 (anteprima)
22/06/2017

### <a name="download-azure-storage-explorer-0814-preview"></a>Download di Azure Storage Explorer 0.8.14 (anteprima)
* [Download di Azure Storage Explorer 0.8.14 (anteprima) per Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Download di Azure Storage Explorer 0.8.14 (anteprima) per Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Download di Azure Storage Explorer 0.8.14 (anteprima) per Linux](https://go.microsoft.com/fwlink/?LinkId=809308)

### <a name="new"></a>Nuovo

* Versione aggiornata di Electron a 1.7.2 per usufruire di numerosi aggiornamenti importanti sulla sicurezza
* È ora possibile accedere rapidamente alla guida online di risoluzione dei problemi dal menu Guida
* [Guida][2]alla risoluzione dei problemi di Storage Explorer
* [Istruzioni][3]per stabilire la connessione a una sottoscrizione di Azure Stack

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




## <a name="previous-releases"></a>Versioni precedenti

* [Versione 0.8.13](#version-0813)
* [Versione 0.8.12/0.8.11/0.8.10](#version-0812--0811--0810)
* [Versione 0.8.9/0.8.8](#version-089--088)
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


### <a name="version-0813"></a>Versione 0.8.13
12/05/2017

#### <a name="new"></a>Nuovo

* [Guida][2]alla risoluzione dei problemi di Storage Explorer
* [Istruzioni][3]per stabilire la connessione a una sottoscrizione di Azure Stack

#### <a name="fixes"></a>Correzioni

* Corretto: il caricamento del file aveva una probabilità elevata di causare un errore di memoria insufficiente
* Corretto: è ora possibile accedere con PIN/smart card
* Corretto: l'azione Apri nel portale ora funziona con Azure China, Azure Germany, Azure US Government e Azure Stack
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


### <a name="version-0812--0811--0810"></a>Versione 0.8.12/0.8.11/0.8.10
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


### <a name="version-089--088"></a>Versione 0.8.9/0.8.8
23/02/2017

<iframe width="560" height="315" src="https://www.youtube.com/embed/R6gonK3cYAc?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SrRPCm94mfE?ecver=1" frameborder="0" allowfullscreen></iframe>


#### <a name="new"></a>Nuovo

* Storage Explorer 0.8.9 scaricherà automaticamente la versione più recente degli aggiornamenti.
* Hotfix: l'uso di un URI di firma di accesso condiviso generato da portale per collegare un account di archiviazione comporta un errore.
* È ora possibile creare, gestire e alzare di livello degli snapshot di BLOB.
* È ora possibile accedere agli account di Azure China, Azure Germany e Azure US Government.
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

<iframe width="560" height="315" src="https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1" frameborder="0" allowfullscreen></iframe>

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
* Possibilità di aprire più editor in schede diverse. Clic singolo per aprire una scheda temporanea. Doppio clic per aprire una scheda permanente. È anche possibile fare clic sulla scheda temporanea per renderla permanente
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

<iframe width="560" height="315" src="https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1" frameborder="0" allowfullscreen></iframe>

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

<iframe width="560" height="315" src="https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1" frameborder="0" allowfullscreen></iframe>

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
* La ridenominazione di BLOB singoli o all'interno di un contenitore BLOB rinominato non mantiene gli snapshot. Tutte le altre proprietà e i metadati di BLOB, file ed entità vengono conservati durante un'operazione di ridenominazione
* La copia o la ridenominazione delle risorse non funziona all'interno di account associati alla firma di accesso condiviso

07/07/2016
### <a name="version-082"></a>Versione 0.8.2

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1" frameborder="0" allowfullscreen></iframe>

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

<iframe width="560" height="315" src="https://www.youtube.com/embed/ycfQhKztSIY?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1" frameborder="0" allowfullscreen></iframe>

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

<iframe width="560" height="315" src="https://www.youtube.com/embed/imbgBRHX65A?ecver=1" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1" frameborder="0" allowfullscreen></iframe>


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
* Aggiunta degli account di archiviazione per Azure China
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
