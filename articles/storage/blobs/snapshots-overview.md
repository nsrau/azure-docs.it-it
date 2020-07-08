---
title: Snapshot BLOB
titleSuffix: Azure Storage
description: Informazioni su come creare uno snapshot di sola lettura di un BLOB per eseguire il backup dei dati BLOB in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cd8771afdc9c4462faaff758ffdd073382f69f53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550963"
---
# <a name="blob-snapshots"></a>Snapshot BLOB

Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento.

> [!NOTE]
> Il controllo delle versioni dei BLOB (anteprima) offre un modo alternativo per mantenere le copie cronologiche di un BLOB. Per ulteriori informazioni, vedere [controllo delle versioni dei BLOB (anteprima)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Informazioni sugli snapshot BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Uno snapshot di un BLOB è identico al relativo BLOB di base, ad eccezione del fatto che all'URI del BLOB viene aggiunto un valore **DateTime** per indicare data e ora di acquisizione dello snapshot. Ad esempio, se un URI del BLOB di pagine è `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l'URI dello snapshot è simile a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tutti gli snapshot condividono l'URI del BLOB di base. L'unica distinzione tra il BLOB di base e lo snapshot è il valore **DateTime** aggiunto.
>

Un BLOB può avere un numero qualsiasi di snapshot. Gli snapshot vengono mantenuti fino a quando non vengono eliminati in modo esplicito, ovvero uno snapshot non può sopravvivere al relativo BLOB di base. È possibile enumerare gli snapshot associati al BLOB di base per tenere traccia degli snapshot correnti.

Quando si crea uno snapshot di un BLOB, le proprietà di sistema del BLOB vengono copiate nello snapshot con gli stessi valori. Anche i metadati del BLOB di base vengono copiati nello snapshot, se non si specificano metadati separati per lo snapshot durante la creazione. Dopo aver creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo.

Eventuali lease associati al BLOB di base non vengono copiati nello snapshot. Non è possibile acquisire un lease in uno snapshot.

Un file di disco rigido virtuale viene usato per archiviare lo stato e le informazioni correnti per il disco della macchina virtuale. È possibile scollegare il disco dall'interno della macchina virtuale o arrestare la macchina virtuale e quindi creare uno snapshot del relativo file di disco rigido virtuale. È possibile usare il file di snapshot successivamente per recuperare il file di disco rigido virtuale in quel determinato momento e ricreare la macchina virtuale.

## <a name="understand-how-snapshots-accrue-charges"></a>Informazioni sull'incremento dei costi dovuto agli snapshot

La creazione di uno snapshot, una copia di sola lettura di un BLOB, può dar luogo a costi di archiviazione dei dati aggiuntivi sul conto. Nel progettare l'applicazione, è importante essere coscienti di come i costi possono aumentare, in modo da poterli ridurre al minimo.

### <a name="important-billing-considerations"></a>Considerazioni importanti sulla fatturazione

Nell'elenco seguente sono inclusi i punti principali da considerare quando si crea uno snapshot.

- Sono previsti costi per l'account di archiviazione per blocchi univoci o pagine, sia che si trovino nel BLOB che nello snapshot. Non sono previsti costi aggiuntivi per gli snapshot associati a un BLOB finché il BLOB su cui si basano non viene aggiornato. Dopo aver aggiornato il BLOB di base, questo differisce dai relativi snapshot. In questo caso, vengono addebitati costi per i blocchi univoci o le pagine in ogni BLOB o snapshot.
- Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Una volta che il blocco viene inviato nuovamente, differisce dalla controparte in ogni snapshot e all'utente verrà addebitato un costo per i relativi dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.
- La sostituzione di un BLOB in blocchi chiamando il metodo [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] o [UploadFromByteArray] [dotnet_UploadFromByteArray] sostituisce tutti i blocchi nel BLOB. Se è presente uno snapshot associato al BLOB, tutti i blocchi del BLOB di base e lo snapshot a questo punto differiranno e all'utente verranno addebitati i costi di tutti i blocchi in entrambi i BLOB. Questo vale persino se i dati nel BLOB di base e nello snapshot restano identici.
- Il servizio BLOB di Azure non dispone dei mezzi per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante considerare che se si aggiorna un BLOB contenente uno snapshot, si generano altri blocchi univoci e i costi aggiuntivi aumentano.

### <a name="minimize-cost-with-snapshot-management"></a>Ridurre al minimo i costi con la gestione degli snapshot

Si consiglia di gestire gli snapshot con attenzione per evitare costi supplementari. È possibile seguire queste procedure consigliate per ridurre al minimo i costi che sorgono con l'archiviazione degli snapshot:

- Eliminare e ricreare gli snapshot associati a un BLOB ogni volta che si aggiorna il BLOB, persino se l'aggiornamento viene eseguito con dati identici, a meno che la progettazione dell'applicazione non richieda di mantenerli. Se si eliminano e si ricreano gli snapshot del BLOB, ci si garantisce che il BLOB e gli snapshot non differiscano.
- Se si gestiscono snapshot per un BLOB, evitare di chiamare [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] o [UploadFromByteArray] [dotnet_UploadFromByteArray] per aggiornare il BLOB. Questi metodi sostituiscono tutti i blocchi nel BLOB, creando così delle differenze significative tra il BLOB di base e gli snapshot. Aggiornare invece il minor numero possibile di blocchi usando i metodi [PutBlock] [dotnet_PutBlock] e [PutBlockList] [dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scenari di fatturazione degli snapshot

Gli scenari seguenti dimostrano in che modo aumentano i costi per un BLOB in blocchi e i relativi snapshot.

#### <a name="scenario-1"></a>Scenario 1

Nello Scenario 1, il BLOB di base non è stato aggiornato da quanto è stato scattato lo snapshot, pertanto i costi sono relativi sono ai blocchi univoci 1, 2 e 3.

![Risorse di archiviazione di Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

Nello Scenario 2, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato aggiornato, e sebbene contenga gli stessi dati e lo stesso ID, non è lo stesso del blocco 3 nello snapshot. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

Nello Scenario 3, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma lo snapshot continua a riflettere il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Risorse di archiviazione di Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci. Questo scenario può verificarsi se si utilizza un metodo di aggiornamento, ad esempio [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] o [UploadFromByteArray] [dotnet_UploadFromByteArray], perché questi metodi sostituiscono tutto il contenuto di un BLOB.

![Risorse di archiviazione di Azure](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Passaggi successivi

- [Creare e gestire snapshot BLOB in .NET](snapshots-manage-dotnet.md)
- [Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali](../../virtual-machines/windows/incremental-snapshots.md)
