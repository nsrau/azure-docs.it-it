---
title: Panoramica degli snapshot di condivisione di File di Azure (anteprima) | Microsoft Docs
description: "Uno snapshot di condivisione di File di Azure è una versione di sola lettura di una condivisione di File di Azure acquisita in un determinato momento. Una volta creato uno snapshot di condivisione, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot di condivisione consentono di eseguire il backup della condivisione così com'è in un determinato momento."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 70b7fec318b527c6c67bd281892c10814712bbc6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-files-share-snapshot-preview-overview"></a>Panoramica degli snapshot di condivisione di File di Azure (anteprima)
File di Azure consente di acquisire snapshot di condivisione delle condivisioni file. Gli snapshot di condivisione (anteprima) acquisiscono lo stato di condivisione nel momento specifico. In questo articolo vengono descritte le funzionalità offerte dagli snapshot di condivisione e in che modo sfruttarle in un caso d'uso personalizzato.


## <a name="when-to-use-share-snapshots"></a>Quando usare gli snapshot di condivisione

### <a name="protection-against-application-error-and-data-corruption"></a>Protezione dagli errori dell'applicazione e dal danneggiamento dei dati
-------------------------------------------------------------------------

Le applicazioni che usano condivisioni file di Azure eseguono diverse operazioni, tra cui scrittura, lettura, archiviazione, trasmissione o elaborazione. Un'applicazione può non essere configurata correttamente o è possibile che venga introdotto un bug accidentale che causa la sovrascrittura o il danneggiamento di alcuni blocchi. Per evitare questi scenari, è possibile creare uno snapshot di condivisione prima di distribuire il codice della nuova applicazione e, nei casi in cui viene introdotto un bug o un errore dell'applicazione con la nuova distribuzione, è possibile tornare a una versione precedente dei dati che si trovano nella condivisione file. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Protezione da eliminazioni accidentali o modifiche indesiderate
----------------------------------------------------------------------------------------------

Si supponga che un utente stia lavorando a un file di testo che si trova in una condivisione file. Quando il file di testo viene chiuso, l'utente non può più annullare le modifiche. In questi casi sarà necessario ripristinare una versione precedente del file. Gli snapshot di condivisione consentono di ripristinare versioni precedenti del file se questo viene accidentalmente rinominato o eliminato.

### <a name="general-backup-purposes"></a>Scopi generali di backup
---------------------------

Dopo aver creato una condivisione file di Azure, è possibile creare periodicamente uno snapshot di condivisione della condivisione file di Azure da usare per il backup dei dati. Se acquisito periodicamente, lo snapshot di condivisione aiuta a mantenere le versioni precedenti dei dati, che possono essere usate per i requisiti di controllo successivi o per il ripristino di emergenza.

## <a name="share-snapshot-capabilities"></a>Funzionalità degli snapshot di condivisione

Uno snapshot di condivisione è una copia di sola lettura temporizzata dei dati. È possibile creare, eliminare e gestire gli snapshot usando l'API REST. Le stesse funzionalità sono anche disponibili nella libreria client, nell'interfaccia della riga di comando e nel portale di Azure. Sarà presto disponibile anche l'integrazione di PowerShell per lo snapshot di condivisione. È possibile visualizzare gli snapshot di una condivisione sia con l'API REST sia con SMB. I clienti possono recuperare l'elenco delle versioni del file o della directory e possono montare una versione specifica direttamente come unità. Una volta creato uno snapshot di condivisione, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Non è possibile copiare un intero snapshot di condivisione in un altro account di archiviazione. A questo scopo, è necessario procedere file per file usando azcopy o altri meccanismi di copia.

La funzionalità degli snapshot di condivisione viene fornita a livello di condivisione file, mentre il recupero viene fornito a livello di singolo file per consentire il ripristino di singoli file. È possibile ripristinare una condivisione file completa usando SMB, l'API REST, il portale, la libreria client o strumenti di PowerShell o dell'interfaccia della riga di comando.

Uno snapshot di condivisione di una condivisione file è identico alla condivisione file di base associata, eccetto che per il fatto che all'URI di condivisione è aggiunto un valore **DateTime** per indicare la data e l'ora di acquisizione dello snapshot. Ad esempio, se l'URI di una condivisione file è http://storagesample.core.file.windows.net/myshare, l'URI dello snapshot di condivisione è simile a
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z.
```

Gli snapshot di condivisione vengono mantenuti finché non vengono eliminati in modo esplicito. Uno snapshot di condivisione non può durare più a lungo della condivisione file di base associata. È possibile enumerare gli snapshot associati alla condivisione file di base per tenere traccia degli snapshot correnti. Quando si crea uno snapshot di condivisione di una condivisione file, i file che si trovano nelle proprietà di sistema della condivisione vengono copiati nello snapshot di condivisione con gli stessi valori. Anche i file di base e i metadati della condivisione file vengono copiati nello snapshot di condivisione, a meno che non si specifichino metadati separati per lo snapshot durante la sua creazione.

Non è possibile eliminare una condivisione per cui sono disponibili snapshot di condivisione senza eliminarne prima tutti gli snapshot.


## <a name="share-snapshot-space-usage"></a>Utilizzo dello spazio degli snapshot di condivisione 

Gli snapshot di condivisione sono incrementali per natura, ovvero vengono salvati solo i dati modificati dopo lo snapshot di condivisione più recente. Questo approccio riduce al minimo il tempo necessario per creare lo snapshot di condivisione e permette di risparmiare sui costi di archiviazione. Qualsiasi operazione di scrittura nell'oggetto o nella proprietà o qualsiasi operazione di aggiornamento dei metadati viene conteggiata come "contenuto modificato" e viene archiviata nello snapshot di condivisione. 

Per risparmiare spazio, è possibile eliminare lo snapshot di condivisione nel cui periodo si è verificata la variazione più elevata.

Anche se gli snapshot di condivisione vengono salvati in modo incrementale, il processo di eliminazione degli snapshot di condivisione è progettato in modo che sia necessario mantenere solo quello più recente per ripristinare la condivisione. Quando si elimina uno snapshot di condivisione, vengono rimossi solo i dati univoci per lo snapshot di condivisione stesso. Gli snapshot attivi contengono tutte le informazioni necessarie per individuare e ripristinare i dati (dal momento in cui è stato acquisito lo shapshot di condivisione) nel percorso originale o in uno alternativo. Il ripristino può essere eseguito a livello di elemento.

Gli snapshot non vengono conteggiati fino al limite di condivisione di 5 TB. Non è previsto alcun limite per la quantità totale di spazio occupata dallo snapshot di condivisione. Continuano ad applicarsi i limiti dell'account di archiviazione.

## <a name="limits"></a>Limiti

Il numero massimo di snapshot di condivisione attualmente consentito da File di Azure è 200. Dopo 200 snapshot di condivisione, quelli meno recenti dovranno essere eliminati per poterne creare di nuovi. Il numero di chiamate simultanee per la creazione di snapshot di condivisione è illimitato.
La quantità di spazio che può essere utilizzata dagli snapshot di condivisione di una condivisione file specifica è illimitata. 

## <a name="copy-data-back-to-a-share-from-share-snapshot"></a>Copiare i dati da uno snapshot di condivisione di nuovo a una condivisione

Le operazioni di copia che interessano file e snapshot di condivisione seguono queste regole:

È possibile copiare singoli file in uno snapshot di condivisione file nella condivisione di base o in qualsiasi altra posizione. È possibile ripristinare una versione precedente di un file o la condivisione file completa copiando i file uno per uno dallo snapshot di condivisione. Lo snapshot di condivisione non viene alzato di livello alla condivisione di base. Lo snapshot di condivisione resta intatto dopo la copia, ma la condivisione file di base viene sovrascritta con una copia dei dati disponibili nello snapshot di condivisione. Tutti i file ripristinati vengono conteggiati come "contenuto modificato".

È possibile copiare un file incluso in uno snapshot di condivisione in una destinazione con un nome diverso. Il file di destinazione risultante è un file scrivibile e non uno snapshot di condivisione.

Quando un file di destinazione viene sovrascritto con una copia, qualsiasi snapshot di condivisione associato al file di destinazione originale resta intatto.

## <a name="general-best-practices"></a>Procedure consigliate generali 

Quando si esegue l'infrastruttura in Azure, automatizzare i backup per il ripristino dei dati ogni volta che è possibile. Le azioni automatizzate sono più affidabili rispetto ai processi manuali e contribuiscono a migliorare la protezione e la recuperabilità dei dati. Per l'automazione, è possibile usare l'API REST, Client SDK o uno script.

Valutare con attenzione le impostazioni di frequenza e conservazione dello snapshot di condivisione prima di distribuire l'utilità di pianificazione dello snapshot di condivisione in modo da evitare costi superflui.

Gli snapshot di condivisione forniscono protezione solo a livello di file e le eliminazioni accidentali nella condivisione file o nell'account di archiviazione non sono protette dagli snapshot di condivisione. È possibile bloccare l'account di archiviazione o il gruppo di risorse per proteggere l'account di archiviazione da eliminazioni accidentali.

## <a name="next-steps"></a>Passaggi successivi
* [Work with Azure Files share snapshot](storage-how-to-use-files-snapshots.md) (Usare uno snapshot di condivisione di File di Azure)
* [Domande frequenti sugli snapshot di condivisione](storage-files-faq.md)

