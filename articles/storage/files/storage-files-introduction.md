---
title: Introduzione ad Archiviazione file di Azure | Microsoft Docs
description: Introduzione ad Archiviazione file di Azure, che offre condivisioni file di rete in Microsoft Cloud
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Introduzione ad Archiviazione file di Azure

Archiviazione file di Azure offre condivisioni file di rete nel cloud usando standard di settore come il [protocollo Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e [Common Internet File System (CIFS)](https://technet.microsoft.com/library/cc939973.aspx). Le condivisioni file di Azure possono essere montate contemporaneamente da Macchine virtuali di Azure e distribuzioni locali che eseguono Windows, macOS o Linux. Un account di archiviazione per utilizzo generico consente di accedere ad Archiviazione file di Azure, Archiviazione BLOB di Azure e Archiviazione code di Azure.

## <a name="videos"></a>Video
| Introduzione ad Archiviazione file di Azure (27 minuti) | Esercitazione su Archiviazione file di Azure (5 minuti)  |
|-|-|
| [![Screencast del video di introduzione ad Archiviazione file di Azure (fare clic per riprodurre)](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Screencast dell'esercitazione su Archiviazione file di Azure (fare clic per riprodurre)](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Vantaggi offerti da Archiviazione file di Azure

Archiviazione file di Azure consente di sostituire i file server basati su Windows Server, Linux o NAS ospitati in locale o nel cloud con una condivisione file su cloud indipendente dal sistema operativo. Archiviazione file di Azure offre i vantaggi seguenti:

* **Accesso condiviso**. Le condivisioni file di Azure supportano il protocollo SMB standard di settore. Di conseguenza, è possibile sostituire facilmente le condivisioni file locali con condivisioni file di Azure senza preoccuparsi della compatibilità delle applicazioni. La possibilità di accedere a una condivisione file da più computer e applicazioni/istanze è un vantaggio importante di Archiviazione file di Azure.

* **Soluzione completamente gestita**. È possibile creare condivisioni file di Azure senza dover gestire l'hardware o un sistema operativo. Di conseguenza, non è necessario applicare patch del sistema operativo server con aggiornamenti critici della sicurezza o sostituire dischi rigidi difettosi.

* **Script e strumenti**. È possibile usare i cmdlet di PowerShell e l'interfaccia della riga di comando di Azure per creare, montare e gestire le condivisioni file di Azure nell'ambito dell'amministrazione delle applicazioni di Azure. Le condivisioni file di Azure possono essere create e gestite con il [portale di Azure](https://portal.azure.com) e [Azure Storage Explorer](https://storageexplorer.com). 

* **Resilienza**. Archiviazione file di Azure è stato progettato per garantire disponibilità costante. Sostituendo le condivisioni file locali con Archiviazione file di Azure, non sarà più necessario occuparsi di problemi di rete o interruzioni dell'alimentazione a livello locale. 

* **Programmabilità nota**. Le applicazioni eseguite in Azure possono accedere ai dati nella condivisione tramite le [API di I/O del file system](https://msdn.microsoft.com/library/system.io.file.aspx). Gli sviluppatori possono quindi riutilizzare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. Oltre alle API di I/O di sistema, è possibile usare qualsiasi libreria client di archiviazione di Azure, ad esempio quella per [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), o l'[API REST di Archiviazione di Azure](/rest/api/storageservices/file-service-rest-api).

Le condivisioni file di Azure possono essere usate per gli scopi seguenti:

* **Sostituire file server locali**. È possibile usare Archiviazione file di Azure per sostituire completamente le condivisioni file in dispositivi NAS o file server locali. I sistemi operativi più diffusi, come Windows, macOS e Linux, possono montare facilmente una condivisione file di Azure ovunque nel mondo.

* **Trasferire in modalità lift-and-shift le applicazioni**

    Archiviazione file di Azure facilita il trasferimento in modalità lift-and-shift nel cloud delle applicazioni che usano condivisioni file locali per condividere i dati tra le parti dell'applicazione. A tale scopo, ogni VM si connette alla condivisione file e può quindi leggere e scrivere i file così come in una condivisione file locale.

* **Semplificare lo sviluppo per il cloud**
    
    È possibile usare Archiviazione file di Azure in diversi modi per semplificare i nuovi progetti di sviluppo per il cloud.
    
    * **Impostazioni delle applicazioni condivise**
    
        Un modello comune per le applicazioni distribuite consiste nell'inserire i file di configurazione in una posizione centralizzata in cui siano accessibili da numerose VM diverse. Tali file di configurazione possono ora essere archiviati in una condivisione file di Azure ed essere letti da tutte le istanze delle applicazioni. Le impostazioni possono essere gestite anche tramite l'interfaccia REST, che consente l'accesso ai file di configurazione in tutto il mondo.

    * **Condivisione di diagnostica**
    
        Una condivisione file di Azure può essere usata anche per salvare file di diagnostica come log, metriche e dump di arresto anomalo del sistema. La disponibilità delle condivisioni file tramite SMB e l'interfaccia REST consente alle applicazioni di creare o sfruttare diversi strumenti di analisi per l'elaborazione e l'analisi dei dati di diagnostica.

    * **Sviluppo/test/debug**

        Quando gli sviluppatori o gli amministratori usano VM nel cloud, spesso necessitano di un set di strumenti o di utilità. L'installazione e la distribuzione di queste utilità in ogni macchina virtuale in cui sono necessarie possono richiedere molto tempo. Con Archiviazione file di Azure, uno sviluppatore o un amministratore può archiviare gli strumenti preferiti in una condivisione file a cui sarà possibile connettersi facilmente da qualsiasi macchina virtuale.
        
## <a name="how-does-it-work"></a>Come funziona?

Gestire le condivisioni file di Azure è molto più semplice che gestire le condivisioni file in locale. Il diagramma seguente illustra i costrutti di gestione di Archiviazione file di Azure:

![Struttura di Archiviazione file](./media/storage-files-introduction/files-concepts.png)

* **Account di archiviazione**: tutti gli accessi ad Archiviazione di Azure vengono eseguiti tramite un account di archiviazione. Per informazioni dettagliate sulla capacità degli account di archiviazione, vedere gli [obiettivi di scalabilità e prestazioni](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Condivisione**: una condivisione di Archiviazione file è una condivisione file SMB in Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino a una capacità di 5 TB di condivisione del file.

* **Directory**: una gerarchia di directory facoltativa.

* **File**: un file nella condivisione. Le dimensioni massime di un file possono estendersi fino a 1 TB.

* **Formato dell'URL**: è possibile fare riferimento ai file usando il formato di URL seguente:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Creare una condivisione file di Azure](storage-how-to-create-file-share.md)
* [Eseguire la connessione e il montaggio in Windows](storage-how-to-use-files-windows.md)
* [Eseguire la connessione e il montaggio in Linux](storage-how-to-use-files-linux.md)
* [Eseguire la connessione e il montaggio in macOS](storage-how-to-use-files-mac.md)
* [Domande frequenti](../storage-files-faq.md)
* [Risoluzione dei problemi in Windows](storage-troubleshoot-windows-file-connection-problems.md)   
* [Risoluzione dei problemi in Linux](storage-troubleshoot-linux-file-connection-problems.md)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>Articoli concettuali e video
* [Azure File storage: a frictionless cloud SMB file system for Windows and Linux (Archiviazione file di Azure: un file system SMB nel cloud senza conflitti per Windows e Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Supporto degli strumenti per Archiviazione file di Azure
* [Come usare AzCopy con Archiviazione di Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>Post di BLOG
* [Archiviazione file di Azure è attualmente disponibile a livello generale](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage (Analisi di Archiviazione file di Azure)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File (Migrazione dei dati in File di Azure)](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Riferimento
* [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)

