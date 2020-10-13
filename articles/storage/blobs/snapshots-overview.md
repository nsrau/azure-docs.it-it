---
title: Snapshot BLOB
titleSuffix: Azure Storage
description: Informazioni sul funzionamento degli snapshot del BLOB e sulle modalità di fatturazione.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ab4c152f30ab96fe5e221a605a2339c773e32547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295402"
---
# <a name="blob-snapshots"></a>Snapshot BLOB

Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento.

> [!NOTE]
> Il controllo delle versioni dei BLOB offre un modo migliore per gestire le versioni precedenti di un BLOB. Per ulteriori informazioni, vedere [controllo delle versioni dei BLOB](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Informazioni sugli snapshot BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Uno snapshot di un BLOB è identico al relativo BLOB di base, ad eccezione del fatto che all'URI del BLOB viene aggiunto un valore **DateTime** per indicare data e ora di acquisizione dello snapshot. Ad esempio, se un URI del BLOB di pagine è `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l'URI dello snapshot è simile a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tutti gli snapshot condividono l'URI del BLOB di base. L'unica distinzione tra il BLOB di base e lo snapshot è il valore **DateTime** aggiunto.

Un BLOB può avere un numero qualsiasi di snapshot. Gli snapshot vengono mantenuti fino a quando non vengono eliminati in modo esplicito, in modo indipendente o come parte di un'operazione [Delete Blob](/rest/api/storageservices/delete-blob) per il BLOB di base. È possibile enumerare gli snapshot associati al BLOB di base per tenere traccia degli snapshot correnti.

Quando si crea uno snapshot di un BLOB, le proprietà di sistema del BLOB vengono copiate nello snapshot con gli stessi valori. Anche i metadati del BLOB di base vengono copiati nello snapshot, se non si specificano metadati separati per lo snapshot durante la creazione. Dopo aver creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo.

Eventuali lease associati al BLOB di base non vengono copiati nello snapshot. Non è possibile acquisire un lease in uno snapshot.

Un file di disco rigido virtuale viene usato per archiviare lo stato e le informazioni correnti per il disco della macchina virtuale. È possibile scollegare il disco dall'interno della macchina virtuale o arrestare la macchina virtuale e quindi creare uno snapshot del relativo file di disco rigido virtuale. È possibile usare il file di snapshot successivamente per recuperare il file di disco rigido virtuale in quel determinato momento e ricreare la macchina virtuale.

## <a name="understand-how-snapshots-accrue-charges"></a>Informazioni sull'incremento dei costi dovuto agli snapshot

### <a name="important-billing-considerations"></a>Considerazioni importanti sulla fatturazione

Nell'elenco seguente sono inclusi i punti principali da considerare quando si crea uno snapshot.

- Sono previsti costi per l'account di archiviazione per blocchi univoci o pagine, sia che si trovino nel BLOB che nello snapshot. Non sono previsti costi aggiuntivi per gli snapshot associati a un BLOB finché il BLOB su cui si basano non viene aggiornato. Dopo aver aggiornato il BLOB di base, questo differisce dai relativi snapshot. In questo caso, vengono addebitati costi per i blocchi univoci o le pagine in ogni BLOB o snapshot.
- Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Una volta che il blocco viene inviato nuovamente, differisce dalla controparte in ogni snapshot e all'utente verrà addebitato un costo per i relativi dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.
- L'aggiornamento di un BLOB in blocchi chiamando un metodo che sovrascrive l'intero contenuto del BLOB sostituirà tutti i blocchi nel BLOB. Se è presente uno snapshot associato al BLOB, tutti i blocchi del BLOB di base e lo snapshot a questo punto differiranno e all'utente verranno addebitati i costi di tutti i blocchi in entrambi i BLOB. Questo vale persino se i dati nel BLOB di base e nello snapshot restano identici.
- Il servizio BLOB di Azure non dispone dei mezzi per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante considerare che se si aggiorna un BLOB contenente uno snapshot, si generano altri blocchi univoci e i costi aggiuntivi aumentano.

### <a name="minimize-costs-with-snapshot-management"></a>Ridurre i costi con la gestione degli snapshot

Si consiglia di gestire gli snapshot con attenzione per evitare costi supplementari. È possibile seguire queste procedure consigliate per ridurre al minimo i costi che sorgono con l'archiviazione degli snapshot:

- Eliminare e ricreare gli snapshot associati a un BLOB ogni volta che si aggiorna il BLOB, persino se l'aggiornamento viene eseguito con dati identici, a meno che la progettazione dell'applicazione non richieda di mantenerli. Se si eliminano e si ricreano gli snapshot del BLOB, ci si garantisce che il BLOB e gli snapshot non differiscano.
- Se si gestiscono snapshot per un BLOB, evitare di chiamare metodi che sovrascrivono l'intero BLOB quando si aggiorna il BLOB. Aggiornare invece il minor numero possibile di blocchi per ridurre i costi.

### <a name="snapshot-billing-scenarios"></a>Scenari di fatturazione degli snapshot

Gli scenari seguenti dimostrano in che modo aumentano i costi per un BLOB in blocchi e i relativi snapshot.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

La creazione di uno snapshot, una copia di sola lettura di un BLOB, può dar luogo a costi di archiviazione dei dati aggiuntivi sul conto. Nel progettare l'applicazione, è importante essere coscienti di come i costi possono aumentare, in modo da poterli ridurre al minimo.

Gli snapshot BLOB, come le versioni BLOB, vengono fatturati alla stessa tariffa dei dati attivi. Il modo in cui vengono fatturati gli snapshot varia a seconda che il livello sia stato impostato in modo esplicito per il BLOB di base o per uno dei relativi snapshot (o versioni). Per altre informazioni sui livelli di BLOB, vedere [Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md).

Se non è stato modificato il livello di un BLOB o di uno snapshot, verranno addebitati i blocchi di dati univoci nel BLOB, i relativi snapshot e le eventuali versioni. Per ulteriori informazioni, vedere [fatturazione quando il livello BLOB non è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Se è stato modificato il livello di un BLOB o di uno snapshot, viene addebitato l'intero oggetto, indipendentemente dal fatto che il BLOB e lo snapshot si trovino di nuovo nello stesso livello. Per ulteriori informazioni, vedere [fatturazione quando il livello BLOB è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-been-explicitly-set).

Per ulteriori informazioni sui dettagli di fatturazione per le versioni BLOB, vedere controllo delle versioni dei [BLOB](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Fatturazione quando il livello BLOB non è stato impostato in modo esplicito

Se non è stato impostato in modo esplicito il livello BLOB per un BLOB di base o uno qualsiasi dei relativi snapshot, verranno addebitati solo i blocchi o le pagine univoche nel BLOB, i relativi snapshot e tutte le versioni che potrebbero avere. I dati condivisi tra un BLOB e i relativi snapshot vengono addebitati una sola volta. Quando un BLOB viene aggiornato, i dati in un BLOB di base divengono divergenti dai dati archiviati negli snapshot e i dati univoci vengono addebitati per blocco o pagina.

Quando si sostituisce un blocco all'interno di un BLOB in blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Dopo che il blocco è stato nuovamente sottoposta a commit, è diverso dalla relativa controparte nello snapshot e verranno addebitati i dati. Lo stesso vale per una pagina in un BLOB di pagine che viene aggiornata con dati identici.

L'archiviazione BLOB non dispone di un metodo per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante tenere presente che l'aggiornamento di un BLOB quando tale BLOB contiene snapshot o versioni comporta ulteriori blocchi univoci e costi aggiuntivi.

Quando un BLOB contiene snapshot, chiamare le operazioni di aggiornamento sui BLOB in blocchi in modo che aggiornino il minor numero possibile di blocchi. Le operazioni di scrittura che consentono un controllo granulare sui blocchi sono [Put Block](/rest/api/storageservices/put-block) e [Put Block List](/rest/api/storageservices/put-block-list). L'operazione [Put Blob](/rest/api/storageservices/put-blob) , d'altra parte, sostituisce l'intero contenuto di un BLOB e pertanto può causare addebiti aggiuntivi.

Negli scenari seguenti viene illustrato il modo in cui gli addebiti si accumulano per un BLOB in blocchi e i relativi snapshot quando il livello BLOB non è stato impostato in modo esplicito.

#### <a name="scenario-1"></a>Scenario 1

Nello Scenario 1, il BLOB di base non è stato aggiornato da quanto è stato scattato lo snapshot, pertanto i costi sono relativi sono ai blocchi univoci 1, 2 e 3.

![Diagramma 1 che mostra la fatturazione per blocchi univoci nel BLOB di base e nello snapshot.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

Nello Scenario 2, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato aggiornato, e sebbene contenga gli stessi dati e lo stesso ID, non è lo stesso del blocco 3 nello snapshot. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Diagramma 2 che mostra la fatturazione per blocchi univoci nel BLOB di base e nello snapshot.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

Nello Scenario 3, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma lo snapshot continua a riflettere il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Diagramma 3 che mostra la fatturazione per blocchi univoci nel BLOB di base e nello snapshot.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci.

![Diagramma 4 che mostra la fatturazione per blocchi univoci nel BLOB di base e nello snapshot.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Evitare di chiamare metodi che sovrascrivono l'intero BLOB e aggiornare invece singoli blocchi per ridurre i costi.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Fatturazione quando il livello BLOB è stato impostato in modo esplicito

Se il livello BLOB è stato impostato in modo esplicito per un BLOB o uno snapshot (o versione), viene addebitata la lunghezza totale del contenuto dell'oggetto nel nuovo livello, indipendentemente dal fatto che condivida blocchi con un oggetto nel livello originale. Viene addebitata anche la lunghezza totale del contenuto della versione meno recente nel livello originale. Qualsiasi versione o snapshot che rimane nel livello originale viene addebitato per i blocchi univoci che possono condividere, come descritto in [fatturazione quando il livello BLOB non è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Trasferimento di un BLOB in un nuovo livello

La tabella seguente descrive il comportamento di fatturazione per un BLOB o uno snapshot quando viene spostato in un nuovo livello.

| Quando il livello BLOB è impostato in modo esplicito su... | Viene addebitata la fatturazione... |
|-|-|
| Un BLOB di base con uno snapshot | Il BLOB di base nel nuovo livello e lo snapshot meno recente nel livello originale, oltre a eventuali blocchi univoci in altri snapshot. <sup>1</sup> |
| Un BLOB di base con una versione precedente e uno snapshot | Il BLOB di base nel nuovo livello, la versione meno recente nel livello originale e lo snapshot meno recente nel livello originale, più eventuali blocchi univoci in altre versioni o snapshot<sup>1</sup>. |
| Uno snapshot | Lo snapshot nel nuovo livello e il BLOB di base nel livello originale, più tutti i blocchi univoci in altri snapshot. <sup>1</sup> |

<sup>1</sup> Se sono presenti altre versioni o snapshot precedenti che non sono stati spostati dal livello originale, le versioni o gli snapshot vengono addebitati in base al numero di blocchi univoci che contengono, come descritto in [fatturazione quando il livello BLOB non è stato impostato in modo esplicito](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Il diagramma seguente illustra il modo in cui gli oggetti vengono fatturati quando un BLOB con snapshot viene spostato in un livello diverso.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagramma che illustra il modo in cui gli oggetti vengono fatturati quando un BLOB con snapshot è in modo esplicito a livelli.":::

L'impostazione esplicita del livello per un BLOB, una versione o uno snapshot non può essere annullata. Se si sposta un BLOB in un nuovo livello e quindi lo si sposta di nuovo al livello originale, viene addebitata la lunghezza totale del contenuto dell'oggetto anche se condivide blocchi con altri oggetti nel livello originale.

Le operazioni che impostano in modo esplicito il livello di un BLOB, una versione o uno snapshot includono:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Inserisci BLOB](/rest/api/storageservices/put-blob) con il livello specificato
- [Inserisci elenco di blocchi](/rest/api/storageservices/put-block-list) con il livello specificato
- [Copia BLOB](/rest/api/storageservices/copy-blob) con il livello specificato

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Eliminazione di un BLOB quando l'eliminazione temporanea è abilitata

Quando l'eliminazione temporanea BLOB è abilitata, se si elimina o sovrascrive un BLOB di base per cui è stato impostato in modo esplicito il livello, tutte le versioni precedenti o gli snapshot del BLOB eliminato temporaneamente vengono fatturati alla lunghezza del contenuto completo. Per altre informazioni sul modo in cui il controllo delle versioni dei BLOB e l'eliminazione temporanea interagiscono, vedere [controllo delle versioni dei BLOB e eliminazione](versioning-overview.md#blob-versioning-and-soft-delete)temporanea.

La tabella seguente descrive il comportamento di fatturazione per un BLOB eliminato temporaneamente, a seconda che il controllo delle versioni sia abilitato o disabilitato. Quando è abilitata la funzionalità di controllo delle versioni, viene creata una nuova versione quando un BLOB viene eliminato temporaneamente. Quando il controllo delle versioni è disabilitato, l'eliminazione temporanea di un BLOB crea uno snapshot di eliminazione temporanea.

| Quando si sovrascrive un BLOB di base con il livello impostato in modo esplicito... | Viene addebitata la fatturazione... |
|-|-|
| Se l'eliminazione e il controllo delle versioni del BLOB sono entrambi abilitati | Tutte le versioni esistenti alla lunghezza del contenuto completa indipendentemente dal livello. |
| Se l'eliminazione temporanea BLOB è abilitata ma il controllo delle versioni è disabilitato | Tutti gli snapshot di eliminazione temporanea esistenti alla lunghezza del contenuto completa indipendentemente dal livello. |

## <a name="next-steps"></a>Passaggi successivi

- [Controllo delle versioni dei BLOB](versioning-overview.md)
- [Creare e gestire snapshot BLOB in .NET](snapshots-manage-dotnet.md)
- [Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali](../../virtual-machines/windows/incremental-snapshots.md)
