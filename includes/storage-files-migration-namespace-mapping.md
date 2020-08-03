---
title: Eseguire il mapping di una struttura di cartelle a una topologia di Sincronizzazione file di Azure
description: Mapping di una struttura di file e cartelle esistente alle condivisioni file di Azure per l'uso con Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425150"
---
In questo passaggio si sta valutando il numero di condivisioni file di Azure necessarie. Una singola istanza di Windows Server (o cluster) può sincronizzare fino a 30 condivisioni file di Azure.

È possibile che siano presenti più cartelle nei volumi che attualmente si condividono localmente come condivisioni SMB per gli utenti e le app. Il modo più semplice consiste nel prevedere una condivisione locale che esegue il mapping di 1:1 a una condivisione file di Azure. Se si dispone di un numero sufficientemente piccolo, inferiore a 30 per una singola istanza di Windows Server, è consigliabile usare un mapping 1:1.

Se sono presenti più condivisioni di 30, spesso non è necessario eseguire il mapping di una condivisione locale 1:1 a una condivisione file di Azure. Prendere in considerazione le opzioni seguenti.

#### <a name="share-grouping"></a>Raggruppamento di condivisione

Se il reparto risorse umane (ad esempio) dispone di un totale di 15 condivisioni, è consigliabile archiviare tutti i dati HR in una singola condivisione file di Azure. L'archiviazione di più condivisioni locali in una condivisione file di Azure non impedisce di creare le 15 condivisioni SMB usuali nell'istanza locale di Windows Server. Significa solo che si organizzano le cartelle radice di queste 15 condivisioni come sottocartelle in una cartella comune. Si sincronizza quindi questa cartella comune con una condivisione file di Azure. In questo modo, per questo gruppo di condivisioni locali è necessaria solo una singola condivisione file di Azure nel cloud.

#### <a name="volume-sync"></a>Sincronizzazione del volume

Sincronizzazione file di Azure supporta la sincronizzazione della radice di un volume in una condivisione file di Azure. Se si sincronizza la cartella radice, tutte le sottocartelle e i file passeranno alla stessa condivisione file di Azure.

La sincronizzazione della radice del volume non è sempre la soluzione migliore. La sincronizzazione di più posizioni presenta vantaggi. In questo modo, ad esempio, è possibile ridurre il numero di elementi per ogni ambito di sincronizzazione. La configurazione di Sincronizzazione file di Azure con un numero inferiore di elementi non è solo vantaggioso per la sincronizzazione dei file. Un numero inferiore di elementi è utile anche per scenari simili ai seguenti:

* Il ripristino sul lato cloud da uno snapshot di condivisione file di Azure può essere effettuato come backup.
* Il ripristino di emergenza di un server locale può velocizzare significativamente.
* Le modifiche apportate direttamente in una condivisione file di Azure (all'esterno della sincronizzazione) possono essere rilevate e sincronizzate più velocemente.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Un approccio strutturato a una mappa di distribuzione

Prima di distribuire l'archiviazione cloud in un passaggio successivo, è importante creare una mappa tra le cartelle locali e le condivisioni file di Azure. Questo mapping indicherà quindi il numero e i Sincronizzazione file di Azure risorse del *gruppo di sincronizzazione* di cui si eseguirà il provisioning. Un gruppo di sincronizzazione associa la condivisione file di Azure e la cartella del server e stabilisce una connessione di sincronizzazione.

Per prendere la decisione sul numero di condivisioni file di Azure necessarie, esaminare i limiti e le procedure consigliate seguenti. Questo consente di ottimizzare la mappa.

* Un server con l'agente di Sincronizzazione file di Azure installato può eseguire la sincronizzazione con un massimo di 30 condivisioni file di Azure.
* Una condivisione file di Azure viene distribuita in un account di archiviazione. In questo modo l'account di archiviazione è un obiettivo di scalabilità per i numeri delle prestazioni, ad esempio IOPS e velocità effettiva. 

  Due condivisioni file di Azure standard (non Premium) possono teoricamente saturare le prestazioni massime che un account di archiviazione può offrire. Se si prevede di alleghi solo Sincronizzazione file di Azure a queste condivisioni file, il raggruppamento di più condivisioni file di Azure nello stesso account di archiviazione non creerà un problema. Esaminare gli obiettivi delle prestazioni della condivisione file di Azure per ottenere informazioni più approfondite sulle metriche rilevanti da considerare. 

  Se si intende sollevare un'app in Azure che userà la condivisione file di Azure in modo nativo, potrebbero essere necessarie altre prestazioni dalla condivisione file di Azure. Se si tratta di una possibilità, anche in futuro, è consigliabile eseguire il mapping di una condivisione file di Azure al proprio account di archiviazione.
* È previsto un limite di 250 di account di archiviazione per ogni sottoscrizione in una singola area di Azure.

> [!TIP]
> Tenendo conto di queste informazioni, spesso è necessario raggruppare più cartelle di primo livello nei volumi in una directory radice comune e nuova. Si sincronizza quindi la nuova directory radice e tutte le cartelle raggruppate in una singola condivisione file di Azure. Questa tecnica consente di rimanere entro il limite di 30 sincronizzazioni di condivisione file di Azure per server.
>
> Questo raggruppamento in una radice comune non ha alcun effetto sull'accesso ai dati. Gli ACL resteranno invariati. È necessario modificare solo i percorsi di condivisione, ad esempio le condivisioni SMB o NFS, che potrebbero essere presenti nelle cartelle del server che sono state ora modificate in una radice comune. Non sono state apportate altre modifiche.

Un altro aspetto importante della Sincronizzazione file di Azure e delle prestazioni e dell'esperienza bilanciate consiste nel comprendere i fattori di scalabilità per le prestazioni di Sincronizzazione file di Azure. Ovviamente, quando i file vengono sincronizzati su Internet, i file più grandi impiegano più tempo e larghezza di banda per la sincronizzazione.

> [!IMPORTANT]
> Il vettore di scala più importante per Sincronizzazione file di Azure è il numero di elementi (file e cartelle) che devono essere sincronizzati.

Sincronizzazione file di Azure supporta la sincronizzazione di un massimo di 100 milioni elementi in una singola condivisione file di Azure. Questo limite può essere superato e Mostra solo i test eseguiti regolarmente dal team Sincronizzazione file di Azure.

È consigliabile evitare che il numero di elementi per ogni ambito di sincronizzazione sia basso. Questo è un fattore importante da considerare nel mapping delle cartelle alle condivisioni file di Azure.

In una situazione di questo genere è possibile che un set di cartelle possa essere sincronizzato logicamente con la stessa condivisione file di Azure (usando il nuovo approccio comune della cartella radice indicato in precedenza). Tuttavia, potrebbe essere comunque preferibile riraggruppare le cartelle in modo che vengano sincronizzate con due anziché una condivisione file di Azure. È possibile usare questo approccio per tenere bilanciato il numero di file e cartelle per ogni condivisione file nel server.

#### <a name="create-a-mapping-table"></a>Creare una tabella di mapping

:::row:::
    :::column:::
        [![Esempio di tabella di mapping. Scaricare il file seguente per sperimentare e usare il contenuto di questa immagine.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Usare una combinazione dei concetti precedenti per determinare il numero di condivisioni file di Azure necessarie e quali parti dei dati esistenti finiranno in quale condivisione file di Azure.
        
        Creare una tabella che registra i pensieri, in modo da potervi fare riferimento nel passaggio successivo. La permanenza organizzata è importante, perché può essere facile perdere i dettagli del piano di mapping quando si effettua il provisioning di molte risorse di Azure contemporaneamente. Per semplificare la creazione di un mapping completo, è possibile scaricare un file di Microsoft Excel come modello.

[//]: # (Il codice HTML viene visualizzato come unico modo per completare l'aggiunta di una tabella a due colonne nidificata con l'analisi dell'immagine funzionante e il testo/collegamento ipertestuale nella stessa riga.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Scaricare un modello di mapping dello spazio dei nomi.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
