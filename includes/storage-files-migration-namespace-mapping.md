---
title: Eseguire il mapping di una struttura di cartelle a una topologia di Sincronizzazione file di AzureMap a folder structure to an Azure File Sync topology
description: Mapping an existing file and folder structure to Azure file shares for use with Azure File Sync. Blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124712"
---
In questo passaggio si valuta il numero di condivisioni file di Azure necessarie. Un singolo Windows Server (o cluster) può sincronizzare fino a 30 condivisioni file di Azure.A single Windows Server (or cluster) can sync up to 30 Azure file shares.

È possibile che nei volumi siano presenti più cartelle attualmente condivise localmente, come condivisioni SMB per utenti e app. Il più semplice sarebbe immaginare per una condivisione locale per eseguire il mapping 1:1 a una condivisione file di Azure.The easiest would be vision for an on-premises share to map 1:1 to an Azure file share. Se si dispone di un numero sufficientemente piccolo, inferiore a 30 per un singolo Windows Server, è consigliabile un mapping 1:1.

Se si dispone di più condivisioni di 30, spesso non è necessario eseguire il mapping di una condivisione locale 1:1 a una condivisione file di Azure.If you have more shares than 30, it is often unnecessary to map an on-premises share 1:1 to an Azure file share.
Valutare le opzioni seguenti:

#### <a name="share-grouping"></a>Raggruppamento di condivisione

Ad esempio, se il reparto risorse umane ha un totale di 15 condivisioni, è possibile archiviare tutti i dati HR in una singola condivisione file di Azure.For instance, if your HR department has a total of 15 shares, then you could consider storing all of the HR data in a single Azure file share. L'archiviazione di più condivisioni locali in una condivisione file di Azure non impedisce di creare le 15 condivisioni SMB nel server Windows server locale. Significa solo che si organizzano le cartelle principali di queste 15 condivisioni come sottocartelle in una cartella comune. È quindi possibile sincronizzare questa cartella comune con una condivisione file di Azure.You then sync this common folder to an Azure file share. In questo modo, per questo gruppo di condivisioni locali è necessaria una sola condivisione file di Azure nel cloud.

#### <a name="volume-sync"></a>Sincronizzazione del volume

Sincronizzazione file di Azure supporta la sincronizzazione della radice di un volume in una condivisione file di Azure.Azure File Sync supports syncing the root of a volume to an Azure file share.
Se si sincronizza la cartella radice, tutte le sottocartelle e i file finiranno nella stessa condivisione file di Azure.If you sync the root folder, then all subfolders and files will end up in the same Azure file share.

Sincronizzare la radice del volume non sarà sempre la risposta migliore. La sincronizzazione di più posizioni offre vantaggi, ma consente di mantenere il numero di elementi inferiore per ambito di sincronizzazione. La configurazione di Sincronizzazione file di Azure con un numero inferiore di elementi non è solo vantaggiosa per la sincronizzazione dei file. Un numero inferiore di elementi beneficia anche di altri scenari come:

* ripristino sul lato cloud da uno snapshot di condivisione file di Azure eseguito come backup
* il ripristino di emergenza di un server locale può accelerare
* Le modifiche apportate direttamente in una condivisione file di Azure (al di fuori della sincronizzazione) possono essere rilevate e sincronizzate più rapidamente

#### <a name="a-structured-approach-to-a-deployment-map"></a>Approccio strutturato a una mappa di distribuzione

Prima di distribuire l'archiviazione cloud in un passaggio successivo, è importante creare una mappa tra le cartelle locali e le condivisioni file di Azure.Before deploying cloud storage in a subsequent step, it is important to create a map between on-premises folders and Azure file shares. Questo mapping indicherà quindi il numero e le risorse di "gruppo di sincronizzazione file" di Azure di cui eseguire il provisioning. Un gruppo di sincronizzazione collega la condivisione file di Azure e la cartella nel server e stabilisce una connessione di sincronizzazione.

Per prendere in mente la decisione sul numero di condivisioni file di Azure necessarie, esaminare i limiti e le procedure consigliate seguenti. In questo modo potrai ottimizzare la mappa:

* Un server in cui è installato l'agente di Sincronizzazione file di Azure può essere sincronizzato con un massimo di 30 condivisioni file di Azure.A server with the Azure File Sync agent installed, can sync with up to 30 Azure file shares.
* Una condivisione file di Azure viene distribuita all'interno di un account di archiviazione. In questo modo l'account di archiviazione è una destinazione di scalabilità per i numeri di prestazioni, ad esempio operazioni di I/O al secondo e velocità effettiva. Due condivisioni file di Azure standard (non premium) potrebbero teoricamente approfondire le prestazioni massime che un account di archiviazione può offrire. Se si prevede di collegare Azure File Sync solo a queste condivisioni file, il raggruppamento di più condivisioni file di Azure nello stesso account di archiviazione non crea un problema. Esaminare gli obiettivi di prestazioni della condivisione file di Azure per informazioni più approfondite sulle metriche rilevanti da considerare. Se si prevede di eseguire il revocamento di un'app in Azure che utilizzerà la condivisione file di Azure in modo nativo, potrebbero essere necessarie ulteriori prestazioni dalla condivisione file di Azure.If you plan to lifting an app to Azure that will use the Azure file share natively, then you might need more performance from your Azure file share. Se questa è una possibilità, anche in futuro, è consigliabile eseguire il mapping di una condivisione file di Azure al proprio account di archiviazione.
* Esiste un limite di 250 account di archiviazione per sottoscrizione in una singola area di Azure.There is a limit of 250 storage accounts per subscription in a single Azure region.

> [!TIP]
> Tenendo presenti queste informazioni, spesso diventa necessario raggruppare più cartelle di primo livello nei volumi in una nuova directory radice comune. La nuova directory radice e tutte le cartelle raggruppate verranno quindi sincronizzate in una singola condivisione file di Azure.You will then sync this new root directory and all the folders you grouped into it, to a single Azure file share.                                                    

Questa tecnica consente di rimanere entro il limite di sincronizzazione di 30 condivisioni file di Azure per server.
Questo raggruppamento in una radice comune non ha alcun impatto sull'accesso ai dati. I tuoi ACL rimangono così com'è, devi solo modificare tutti i percorsi di condivisione (come le condivisioni SMB o NFS) che potresti avere sulle cartelle del server che ora hai cambiato in una radice comune. Nient'altro cambia.

Un altro aspetto importante di Sincronizzazione file di Azure e prestazioni ed esperienza bilanciate è la comprensione dei fattori di scala per le prestazioni di Sincronizzazione file di Azure.Another important aspect of Azure File Sync and a balanced performance and experience is an understanding of the scale factors for Azure File Sync performance. Ovviamente, quando i file vengono sincronizzati su Internet, i file più grandi richiedono più tempo e larghezza di banda per la sincronizzazione.

> [!IMPORTANT]
> Il vettore di scala più importante per Sincronizzazione file di Azure è il numero di elementi (file e cartelle) che devono essere sincronizzati.

Sincronizzazione file di Azure supporta la sincronizzazione di un massimo di 100.000 elementi in una singola condivisione file di Azure.Azure File Sync supports syncing up to 100,000 items to a single Azure file share. Questo limite può essere superato e illustra solo ciò che il team di Sincronizzazione file di Azure testa regolarmente.

È consigliabile mantenere basso il numero di elementi per ambito di sincronizzazione. Questo aspetto è un fattore importante da considerare nel mapping delle cartelle alle condivisioni file di Azure.That aspect is an important factor to be considered in your mapping of folders to Azure file shares.

Anche se nella situazione un set di cartelle può sincronizzare logicamente con la stessa condivisione file di Azure (usando il nuovo approccio comune alle cartelle radice dall'alto) potrebbe comunque essere preferibile raggruppare di nuovo le cartelle in modo che vengano sincronizzate con due anziché con una condivisione file di Azure.Even if in your situation a set of folders can logically sync to the same Azure file share (using the new, common root folder approach above) it might still be better to regroup folders such that they sync to two instead of one Azure file share. Questo approccio può essere utilizzato per mantenere bilanciato il numero di file e cartelle per condivisione file sul server.

#### <a name="create-a-mapping-table"></a>Creare una tabella di mapping

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Usare una combinazione dei concetti precedenti per determinare il numero di condivisioni file di Azure necessarie e le parti dei dati esistenti in cui si troveranno in quale condivisione file di Azure.
        
        Creare una tabella che registri i tuoi pensieri, in modo che tu possa farvi riferimento nel passaggio successivo. Rimanere organizzati è importante in quanto può essere facile perdere i dettagli del piano di mapping quando si esegue il provisioning di molte risorse di Azure contemporaneamente. Per facilitare la creazione di un mapping completo, è possibile scaricare un file di Microsoft Excel come modello.

[//]: # (HTML viene visualizzato come l'unico modo per ottenere l'aggiunta di una tabella nidificata a due colonne con l'analisi delle immagini di lavoro e il testo/collegamento ipertestuale sulla stessa riga.)

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
