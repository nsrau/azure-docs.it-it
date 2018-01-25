---
title: Panoramica degli snapshot di condivisione per File di Azure (anteprima) | Microsoft Docs
description: "Uno snapshot di condivisione è una versione di sola lettura di una condivisione di File di Azure, acquisita in un determinato momento per creare un backup della condivisione stessa."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: c309804f33fc0e5b2091e18dfe5fe3c9849a2709
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="overview-of-share-snapshots-for-azure-files-preview"></a>Panoramica degli snapshot di condivisione per File di Azure (anteprima)
File di Azure consente di acquisire snapshot di condivisione delle condivisioni file. Gli snapshot di condivisione (anteprima) acquisiscono lo stato di condivisione nel momento specifico. In questo articolo vengono descritte le funzionalità offerte dagli snapshot di condivisione e come trarne vantaggio in un caso d'uso personalizzato.


## <a name="when-to-use-share-snapshots"></a>Quando usare gli snapshot di condivisione

### <a name="protection-against-application-error-and-data-corruption"></a>Protezione dagli errori dell'applicazione e dal danneggiamento dei dati

Le applicazioni che usano le condivisioni di file eseguono diverse operazioni, tra cui scrittura, lettura, archiviazione, trasmissione ed elaborazione. Se un'applicazione non è configurata correttamente o viene introdotto inavvertitamente un bug può verificarsi la sovrascrittura o il danneggiamento di alcuni blocchi. Per contribuire a evitare questi scenari è possibile creare uno snapshot della condivisione prima di distribuire il nuovo codice dell'applicazione. Se con la nuova distribuzione viene introdotto un bug o un errore dell'applicazione è possibile tornare a una versione precedente dei dati nella condivisione. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Protezione da eliminazioni accidentali o modifiche indesiderate

Si supponga di lavorare su un file di testo in una condivisione di file. Quando il file di testo viene chiuso non è più possibile annullare le modifiche. In questi casi è necessario ripristinare una versione precedente del file. Gli snapshot di condivisione consentono di ripristinare versioni precedenti del file se questo viene accidentalmente rinominato o eliminato.

### <a name="general-backup-purposes"></a>Scopi generali di backup

Dopo la creazione di una condivisione file, è possibile creare periodicamente uno snapshot della condivisione file e usarlo per il backup dei dati. Se acquisito periodicamente, lo snapshot di condivisione aiuta a gestire versioni precedenti dei dati che possono essere usate per requisiti di controllo futuri o per il ripristino di emergenza.

## <a name="capabilities"></a>Capabilities

Uno snapshot di condivisione è una copia di sola lettura temporizzata dei dati. È possibile creare, eliminare e gestire gli snapshot usando l'API REST. Le stesse funzionalità sono anche disponibili nella libreria client, nell'interfaccia della riga di comando di Azure e nel portale di Azure. 

È possibile visualizzare gli snapshot di una condivisione sia con l'API REST sia con SMB. È possibile recuperare l'elenco delle versioni della directory o del file e montare direttamente una versione specifica come unità. 

Dopo aver creato uno snapshot di condivisione è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Non è possibile copiare un intero snapshot di condivisione in un altro account di archiviazione. È necessario effettuare l'operazione un file alla volta tramite AzCopy o altri meccanismi per la copia.

La funzionalità snapshot di condivisione è disponibile a livello di condivisione di file. Il recupero è disponibile a livello di singolo file, per consentire il ripristino di file singoli. È possibile ripristinare una condivisione file completa usando SMB, l'API REST, il portale, la libreria client o gli strumenti di PowerShell o dell'interfaccia della riga di comando.

Uno snapshot di una condivisione file è identico alla condivisione file di base. L'unica differenza sta nel fatto che all'URI della condivisione viene aggiunto un valore **DateTime** indicante l'ora di creazione dello snapshot di condivisione. Se ad esempio l'URI di una condivisione file è http://storagesample.core.file.windows.net/myshare, l'URI dello snapshot di condivisione è simile al seguente:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Gli snapshot di condivisione vengono mantenuti finché non vengono eliminati in modo esplicito. Uno snapshot di condivisione non può durare più a lungo della condivisione file di base associata. È possibile enumerare gli snapshot associati alla condivisione file di base per tenere traccia degli snapshot correnti. 

Quando si crea uno snapshot di una condivisione file, i file che si trovano nelle proprietà di sistema della condivisione vengono copiati nello snapshot di condivisione con gli stessi valori. Anche i file di base e i metadati della condivisione file vengono copiati nello snapshot di condivisione, a meno che non si specifichino metadati separati per lo snapshot durante la sua creazione.

Non è possibile eliminare una condivisione per la quale sono disponibili snapshot di condivisione senza eliminare prima tutti gli snapshot.


## <a name="space-usage"></a>Uso dello spazio 

Gli snapshot di condivisione sono di natura incrementale. Vengono salvati solo i dati modificati dopo il salvataggio più recente dello snapshot di condivisione. Questo approccio riduce al minimo il tempo necessario per creare lo snapshot di condivisione e permette di risparmiare sui costi di archiviazione. Qualsiasi operazione di scrittura nell'oggetto o nella proprietà o qualsiasi operazione di aggiornamento dei metadati viene conteggiata come "contenuto modificato" e archiviata nello snapshot di condivisione. 

Per risparmiare spazio è possibile eliminare lo snapshot di condivisione corrispondente al periodo con il massimo volume di variazioni.

Anche se gli snapshot di condivisione vengono salvati in modo incrementale, è sufficiente mantenere lo snapshot più recente per ripristinare la condivisione. Quando si elimina uno snapshot di condivisione, vengono rimossi solo i dati univoci per lo snapshot di condivisione stesso. Gli snapshot attivi contengono tutte le informazioni necessarie per trovare e ripristinare i dati (dal momento in cui è stato acquisito lo snapshot di condivisione) nel percorso originale o in uno alternativo. È possibile eseguire il ripristino a livello di elemento.

Gli snapshot non vengono inclusi nel conteggio per il limite di condivisione di 5 TB. Non è previsto alcun limite per la quantità di spazio totale occupata dagli snapshot di condivisione. Si applicano i limiti dell'account di archiviazione.

## <a name="limits"></a>Limiti

Il numero massimo di snapshot di condivisione attualmente supportato da File di Azure è 200. Dopo 200 snapshot di condivisione, per poter creare nuovi snapshot è necessario eliminare quelli meno recenti. 

Il numero di chiamate simultanee per la creazione di snapshot di condivisione è illimitato. La quantità di spazio che può essere usata dagli snapshot di condivisione di una condivisione file specifica è illimitata. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Copiare nuovamente i dati in una condivisione da uno snapshot di condivisione

Le operazioni di copia che interessano file e snapshot di condivisione osservano le regole seguenti:

È possibile copiare singoli file in uno snapshot di condivisione file nella condivisione di base o in qualsiasi altra posizione. È possibile ripristinare una versione precedente di un file o la condivisione file completa copiando i file uno alla volta dallo snapshot di condivisione. Lo snapshot di condivisione non viene alzato di livello a condivisione di base. 

Lo snapshot di condivisione resta intatto dopo la copia, ma la condivisione file di base viene sovrascritta con una copia dei dati disponibili nello snapshot di condivisione. Tutti i file ripristinati vengono conteggiati come "contenuto modificato".

È possibile copiare un file incluso in uno snapshot di condivisione in una destinazione con un nome diverso. Il file di destinazione risultante è un file scrivibile e non uno snapshot di condivisione.

Quando un file di destinazione viene sovrascritto con una copia, qualsiasi snapshot di condivisione associato al file di destinazione originale resta intatto.

## <a name="general-best-practices"></a>Procedure consigliate generali 

Quando si esegue l'infrastruttura in Azure, automatizzare i backup per il ripristino dei dati ogni volta che è possibile. Le azioni automatizzate sono più affidabili rispetto ai processi manuali e contribuiscono a migliorare la protezione e la recuperabilità dei dati. Per l'automazione è possibile usare l'API REST, l'SDK client o uno script.

Prima di distribuire l'utilità di pianificazione dello snapshot di condivisione valutare con cura le impostazioni di frequenza e conservazione dello snapshot di condivisione per evitare costi superflui.

Gli snapshot di condivisione offrono solo la protezione a livello di file. Gli snapshot di condivisione non impediscono le eliminazioni accidentali in una condivisione file o un account di archiviazione. Per proteggere l'account di archiviazione da eliminazioni accidentali è possibile bloccare l'account di archiviazione o il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi
* [Usare gli snapshot di condivisione](storage-how-to-use-files-snapshots.md)
* [Domande frequenti sugli snapshot di condivisione](storage-files-faq.md)

