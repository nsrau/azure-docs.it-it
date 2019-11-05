---
title: Effettuare il provisioning di un pool personalizzato da un'immagine gestita-Azure Batch | Microsoft Docs
description: Creare un pool di batch da una risorsa immagine gestita per eseguire il provisioning dei nodi di calcolo con il software e i dati dell'applicazione.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: lahugh
ms.openlocfilehash: 82a5f79ca7b7a16cd8f7294ebd1f70816b40ad82
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "73519241"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Usare un'immagine gestita per creare un pool di macchine virtuali

Per creare un'immagine personalizzata per le macchine virtuali (VM) del pool di batch, è possibile usare la [raccolta di immagini condivise](batch-sig-images.md)o una risorsa *immagine gestita* .

> [!TIP]
> Nella maggior parte dei casi, è consigliabile creare immagini personalizzate usando la raccolta di immagini condivise. Usando la raccolta di immagini condivise, è possibile eseguire il provisioning dei pool più velocemente, ridimensionare quantità maggiori di macchine virtuali e migliorare l'affidabilità durante il provisioning delle macchine virtuali. Per altre informazioni, vedere [usare la raccolta di immagini condivise per creare un pool personalizzato](batch-sig-images.md).

## <a name="prerequisites"></a>Prerequisiti

- **Una risorsa immagine gestita**. Per creare un pool di macchine virtuali usando un'immagine personalizzata, è necessario possedere o creare una risorsa immagine gestita nella stessa sottoscrizione di Azure e nella stessa area dell'account Batch. È consigliabile creare l'immagine dagli snapshot del disco del sistema operativo della macchina virtuale e, facoltativamente, i relativi dischi dati collegati. Per altre informazioni e i passaggi per preparare un'immagine gestita, vedere la sezione seguente.
  - Usare un'immagine personalizzata univoca per ogni pool che si crea.
  - Per creare un pool con l'immagine usando le API Batch, specificare l'**ID risorsa** dell'immagine, che si presenta nel formato `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Per usare il portale, usare il **nome** dell'immagine.  
  - La risorsa immagine gestita deve esistere per tutta la durata del pool per consentire il ridimensionamento orizzontale e può essere rimossa dopo che il pool è stato eliminato.

- **Autenticazione di Azure Active Directory (AAD)** . L'API client di Batch deve usare l'autenticazione AAD. Il supporto di Azure Batch per l'autenticazione AAD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparare un'immagine personalizzata

In Azure è possibile preparare un'immagine gestita da:

- Snapshot del sistema operativo e dei dischi dati di una macchina virtuale di Azure
- Una macchina virtuale di Azure generalizzata con Managed Disks
- Un disco rigido virtuale generalizzato locale caricato nel cloud

Per ridimensionare i pool di Batch in modo affidabile con un'immagine personalizzata, si consiglia di creare un'immagine gestita usando *solo* il primo modo, ovvero usando gli snapshot dei dischi della macchina virtuale. Vedere i passaggi seguenti per preparare una macchina virtuale, acquisire uno snapshot e creare un'immagine dallo snapshot.

### <a name="prepare-a-vm"></a>Preparare una VM

Se si sta creando una nuova macchina virtuale per l'immagine, usare un'immagine di Azure Marketplace di prima entità supportata da batch come immagine di base per l'immagine gestita. Solo le immagini di primo entità possono essere utilizzate come immagine di base. Per ottenere un elenco completo dei riferimenti alle immagini di Azure Marketplace supportati da Azure Batch, vedere l'operazione [List node Agent SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> È possibile usare un'immagine di terze parti che dispone di licenza aggiuntiva e di condizioni di acquisto come immagine di base. Per informazioni su queste immagini del Marketplace, vedere il materiale sussidiario per le macchine virtuali [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).

- Assicurarsi che la macchina virtuale venga creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.
- Non installare le estensioni di Azure, ad esempio l'estensione Script personalizzato, nella macchina virtuale. Se l'immagine contiene un'estensione preinstallata, Azure può incontrare alcuni problemi durante la distribuzione del pool di Batch.
- Quando si usano dischi dati collegati, è necessario montare e formattare i dischi all'interno di una macchina virtuale per usarli.
- Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
- Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati.  

### <a name="create-a-vm-snapshot"></a>Creare uno snapshot della macchina virtuale

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Per creare uno snapshot del sistema operativo o dei dischi dati di una macchina virtuale, è possibile usare il portale di Azure o gli strumenti da riga di comando. Per i passaggi e le opzioni per creare uno snapshot, vedere il materiale sussidiario per le VM [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) o [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creare un'immagine da uno o più snapshot

Per creare un'immagine gestita da uno snapshot, usare gli strumenti da riga di comando di Azure, ad esempio il comando [az image create](/cli/azure/image). È possibile creare un'immagine specificando uno snapshot del disco del sistema operativo e, facoltativamente, uno o più snapshot dei dischi dati.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Per creare un pool da un'immagine personalizzata nel portale

Dopo avere salvato l'immagine personalizzata e conoscendone l'ID risorsa o il nome, è possibile creare un pool di Batch da tale immagine. La procedura seguente illustra come creare un pool dal portale di Azure.

> [!NOTE]
> Se si sta creando il pool usando una delle API di Batch, assicurarsi che l'identità usata per l'autenticazione AAD disponga delle autorizzazioni per la risorsa immagine. Vedere [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).
>
> La risorsa per l'immagine gestita deve esistere per la durata del pool. Se la risorsa sottostante viene eliminata, non è possibile ridimensionare il pool.

1. Passare all'account Batch nel portale di Azure. Questo account deve trovarsi nella stessa area e nella stessa sottoscrizione del gruppo di risorse contenente l'immagine personalizzata.
2. Nella finestra **Impostazioni** a sinistra scegliere la voce di menu **Pool**.
3. Nella finestra **Pool** selezionare il comando **Aggiungi**.
4. Nella finestra **Aggiungi pool** selezionare **Immagine personalizzata (Linux/Windows)** nell'elenco a discesa **Tipo di immagine**. Nell'elenco a discesa **Immagine di macchina virtuale personalizzata** selezionare il nome dell'immagine (forma breve dell'ID risorsa).
5. Selezionare il valore corretto nei campi **Server di pubblicazione/Offerta/SKU** per l'immagine personalizzata.
6. Specificare le impostazioni obbligatorie rimanenti, tra cui le **dimensioni del nodo**, i **nodi dedicati di destinazione**e i nodi con **priorità bassa**, nonché le impostazioni facoltative desiderate.

    Per un'immagine personalizzata di Microsoft Windows Server Datacenter 2016, ad esempio, la finestra **Aggiungi pool** viene visualizzata come illustrato di seguito:

    ![Aggiungere un pool da un'immagine di Windows personalizzata](media/batch-custom-images/add-pool-custom-image.png)
  
Per verificare se un pool esistente è basato su un'immagine personalizzata, vedere la proprietà **Sistema operativo** nella sezione di riepilogo delle risorse della finestra **Pool**. Se il pool è stato creato da un'immagine personalizzata, viene impostato su **Immagine di macchina virtuale personalizzata**.

Tutte le immagini personalizzate associate a un pool vengono visualizzate nella finestra **Proprietà** del pool.

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia di macchine virtuali e oltre usando un'immagine personalizzata, è importante seguire le indicazioni precedenti per usare un'immagine creata dallo snapshot di una macchina virtuale.

Tenere presente anche le considerazioni seguenti:

- **Limiti dimensioni**: Batch limita le dimensioni del pool a 2500 nodi di calcolo dedicati o a 1000 nodi con priorità bassa, quando si usa un'immagine personalizzata.

  Se si usa la stessa immagine (o più immagini basate sullo stesso snapshot) per creare più pool, il numero totale di nodi di calcolo nel pool non può superare i limiti sopra indicati. Non è consigliabile usare un'immagine o il relativo snapshot sottostante per più di un pool.

  I limiti possono essere anche inferiori se si configura il pool con i [pool NAT in ingresso](pool-endpoint-configuration.md).

- **Timeout ridimensionamento**: se il pool contiene un numero fisso di nodi (senza scalabilità automatica), aumentare il valore della proprietà resizeTimeout del pool a un valore come 20-30 minuti. Se il pool non raggiunge la dimensione di destinazione entro il periodo di timeout, eseguire un'altra [operazione di ridimensionamento](/rest/api/batchservice/pool/resize).

  Se si prevede di creare un pool con oltre 300 nodi di calcolo, può essere necessario ridimensionare il pool più volte per raggiungere le dimensioni di destinazione.
  
Usando la [raccolta di immagini condivise](batch-sig-images.md), è possibile creare pool di dimensioni maggiori con le immagini personalizzate insieme a più repliche di immagini condivise. Utilizzando immagini condivise, il tempo necessario affinché il pool raggiunga lo stato stabile è più veloce del 25% e la latenza di inattività della macchina virtuale è fino al 30% più breve.

## <a name="considerations-for-using-packer"></a>Considerazioni sull'uso di Packer

La creazione di una risorsa immagine gestita direttamente con Packer può essere eseguita solo con account batch in modalità sottoscrizione utente. Per gli account in modalità servizio batch, è prima necessario creare un disco rigido virtuale, quindi importare il disco rigido virtuale in una risorsa immagine gestita. A seconda della modalità di allocazione del pool (sottoscrizione utente o servizio batch), i passaggi per creare una risorsa immagine gestita variano.

Assicurarsi che la risorsa usata per creare l'immagine gestita esista per la durata di qualsiasi pool che fa riferimento all'immagine personalizzata. In caso contrario, possono verificarsi errori di allocazione del pool e/o errori di ridimensionamento.

Se l'immagine o la risorsa sottostante viene rimossa, è possibile che venga ricevuto un errore simile a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Se viene ricevuto questo errore, assicurarsi che la risorsa sottostante non sia stata rimossa.

Per altre informazioni sull'uso di Packer per creare una macchina virtuale, vedere creare [un'immagine Linux con Packer](../virtual-machines/linux/build-image-with-packer.md) o [creare un'immagine Windows con Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
