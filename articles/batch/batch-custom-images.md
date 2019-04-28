---
title: Effettuare il provisioning del pool di Azure Batch da un'immagine personalizzata | Microsoft Docs
description: Creare un pool di Batch da un'immagine personalizzata per effettuare il provisioning dei nodi di calcolo che contengono il software e i dati necessari per l'applicazione. Le immagini personalizzate sono uno strumento efficace per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 233b26b330fabe7da8664114ba1857f74feea4bc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764270"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Usare un'immagine personalizzata per creare un pool di macchine virtuali 

Quando si crea un pool in Azure Batch usando la configurazione della macchina virtuale, specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool di macchine virtuali con un'immagine di Azure Marketplace supportata o con un'immagine personalizzata, vale a dire un'immagine di macchina virtuale creata e configurata manualmente. L'immagine personalizzata deve essere una risorsa di tipo *immagine gestita* nella stessa sottoscrizione di Azure e nella stessa area dell'account Batch.

## <a name="why-use-a-custom-image"></a>Vantaggi dell'uso di un'immagine personalizzata

Quando si specifica un'immagine personalizzata, si ha la possibilità di controllare la configurazione e il tipo del sistema operativo, nonché i dischi dati da usare. L'immagine personalizzata può includere applicazioni e dati di riferimento che diventano disponibili in tutti i nodi del pool di Azure Batch non appena viene effettuato il provisioning.

L'uso di un'immagine personalizzata permette di risparmiare tempo nel preparare i nodi di calcolo del pool per l'esecuzione del carico di lavoro di Batch. Benché sia possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo averne effettuato il provisioning, l'uso di un'immagine personalizzata può rivelarsi più efficiente.

L'uso di un'immagine personalizzata configurata per uno scenario specifico può essere caratterizzato da numerosi vantaggi:

- **Configurare il sistema operativo**. È possibile personalizzare la configurazione del disco del sistema operativo dell'immagine. 
- **Pre-installare applicazioni**. La pre-installazione delle applicazioni sul disco del sistema operativo risulta essere più efficiente e meno soggetta a errori rispetto all'installazione di applicazioni dopo il provisioning dei nodi di calcolo tramite un'attività di avvio.
- **Risparmiare tempo di riavvio nelle macchine virtuali.** L'installazione delle applicazioni in genere prevede il riavvio della macchina virtuale e pertanto questa operazione richiede molto tempo. È possibile risparmiare tempo di riavvio mediante la pre-installazione delle applicazioni. 
- **Copiare grandi quantità di dati solo una volta.** Incorporare i dati statici nell'immagine personalizzata gestita copiandoli nei dischi dati di un'immagine gestita. Questa operazione deve essere eseguita solo una volta e consente di rendere i dati disponibili per ogni nodo del pool.
- **Scelta di tipi di disco.** È possibile scegliere di usare l'archiviazione Premium per il disco del sistema operativo e per il disco dati.
- **Aumentare le dimensioni dei pool.** Quando si usa un'immagine personalizzata gestita per creare un pool, il pool può aumentare senza che sia necessario creare copie dei dischi rigidi virtuali del BLOB immagine. 


## <a name="prerequisites"></a>Prerequisiti

- **Una risorsa immagine gestita**. Per creare un pool di macchine virtuali usando un'immagine personalizzata, è necessario possedere o creare una risorsa immagine gestita nella stessa sottoscrizione di Azure e nella stessa area dell'account Batch. È consigliabile creare l'immagine dagli snapshot del disco del sistema operativo della macchina virtuale e, facoltativamente, i relativi dischi dati collegati. Per altre informazioni e i passaggi per preparare un'immagine gestita, vedere la sezione seguente. 
  - Usare un'immagine personalizzata univoca per ogni pool che si crea.
  - Per creare un pool con l'immagine usando le API Batch, specificare l'**ID risorsa** dell'immagine, che si presenta nel formato `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Per usare il portale, usare il **nome** dell'immagine.  
  - La risorsa immagine gestita deve esistere per tutta la durata del pool per consentire il ridimensionamento orizzontale e può essere rimossa dopo che il pool è stato eliminato.

- **Autenticazione di Azure Active Directory (AAD)**. L'API client di Batch deve usare l'autenticazione AAD. Il supporto di Azure Batch per l'autenticazione AAD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparare un'immagine personalizzata

In Azure è possibile preparare un'immagine gestita dagli snapshot del sistema operativo e dei dischi dati di una macchina virtuale di Azure, da una VM di Azure generalizzata con dischi gestiti o da un disco rigido virtuale locale generalizzato da caricare. Per ridimensionare i pool di Batch in modo affidabile con un'immagine personalizzata, si consiglia di creare un'immagine gestita usando *solo* il primo modo, ovvero usando gli snapshot dei dischi della macchina virtuale. Vedere i passaggi seguenti per preparare una macchina virtuale, acquisire uno snapshot e creare un'immagine dallo snapshot. 

### <a name="prepare-a-vm"></a>Preparare una VM

Se si sta creando una nuova macchina virtuale per l'immagine, usare un'immagine di Azure Marketplace di terze parti prima supportata come immagine di base per l'immagine gestita da Batch. Immagini proprietarie possono essere utilizzate solo come un'immagine di base. Per ottenere un elenco completo dei riferimenti a immagini di Marketplace di Azure supportati da Azure Batch, vedere la [gli SKU agente nodo elenco](/rest/api/batchservice/account/listnodeagentskus) operazione.

> [!NOTE]
> È possibile usare un'immagine di terze parti che dispone di licenza aggiuntiva e di condizioni di acquisto come immagine di base. Per informazioni su queste immagini del Marketplace, vedere il materiale sussidiario per le macchine virtuali [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) o [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).


* Assicurarsi che la macchina virtuale sia creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.
* Non installare le estensioni di Azure, ad esempio l'estensione Script personalizzato, nella macchina virtuale. Se l'immagine contiene un'estensione preinstallata, Azure può incontrare alcuni problemi durante la distribuzione del pool di Batch.
* Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
* Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati.  

### <a name="create-a-vm-snapshot"></a>Creare uno snapshot della macchina virtuale

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Per creare uno snapshot del sistema operativo o dei dischi dati di una macchina virtuale, è possibile usare il portale di Azure o gli strumenti da riga di comando. Per i passaggi e le opzioni per creare uno snapshot, vedere il materiale sussidiario per le VM [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) o [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Creare un'immagine da uno o più snapshot

Per creare un'immagine gestita da uno snapshot, usare gli strumenti da riga di comando di Azure, ad esempio il comando [az image create](/cli/azure/image). È possibile creare un'immagine specificando uno snapshot del disco del sistema operativo e, facoltativamente, uno o più snapshot dei dischi dati.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Per creare un pool da un'immagine personalizzata nel portale

Dopo avere salvato l'immagine personalizzata e conoscendone l'ID risorsa o il nome, è possibile creare un pool di Batch da tale immagine. La procedura seguente illustra come creare un pool dal portale di Azure.

> [!NOTE]
> Se si sta creando il pool usando una delle API di Batch, assicurarsi che l'identità usata per l'autenticazione AAD disponga delle autorizzazioni per la risorsa immagine. Vedere [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).
>
> La risorsa per l'immagine gestita deve esistere per la durata del pool. Se la risorsa sottostante viene eliminata, non può essere ridimensionato il pool. 

1. Passare all'account Batch nel portale di Azure. Questo account deve trovarsi nella stessa area e nella stessa sottoscrizione del gruppo di risorse contenente l'immagine personalizzata. 
2. Nella finestra **Impostazioni** a sinistra scegliere la voce di menu **Pool**.
3. Nella finestra **Pool** selezionare il comando **Aggiungi**.
4. Nella finestra **Aggiungi pool** selezionare **Immagine personalizzata (Linux/Windows)** nell'elenco a discesa **Tipo di immagine**. Nell'elenco a discesa **Immagine di macchina virtuale personalizzata** selezionare il nome dell'immagine (forma breve dell'ID risorsa).
5. Selezionare il valore corretto nei campi **Server di pubblicazione/Offerta/SKU** per l'immagine personalizzata.
6. Specificare le rimanenti impostazioni obbligatorie, incluse **Dimensioni nodo**, **Nodi dedicati target** e **Nodi per priorità bassa**, oltre alle impostazioni facoltative desiderate.

    Per un'immagine personalizzata di Microsoft Windows Server Datacenter 2016, ad esempio, la finestra **Aggiungi pool** viene visualizzata come illustrato di seguito:

    ![Aggiungere un pool da un'immagine di Windows personalizzata](media/batch-custom-images/add-pool-custom-image.png)
  
Per verificare se un pool esistente è basato su un'immagine personalizzata, vedere la proprietà **Sistema operativo** nella sezione di riepilogo delle risorse della finestra **Pool**. Se il pool è stato creato da un'immagine personalizzata, viene impostato su **Immagine di macchina virtuale personalizzata**.

Tutte le immagini personalizzate associate a un pool vengono visualizzate nella finestra **Proprietà** del pool.

## <a name="considerations-for-large-pools"></a>Considerazioni per i pool di grandi dimensioni

Se si prevede di creare un pool con centinaia di macchine virtuali e oltre usando un'immagine personalizzata, è importante seguire le indicazioni precedenti per usare un'immagine creata dallo snapshot di una macchina virtuale.

Si noti inoltre quanto segue:

- **Limiti dimensioni**: Batch limita le dimensioni del pool a 2500 nodi di calcolo dedicati o a 1000 nodi con priorità bassa, quando si usa un'immagine personalizzata.

  Se si usa la stessa immagine (o più immagini basate sullo stesso snapshot) per creare più pool, il numero totale di nodi di calcolo nel pool non può superare i limiti sopra indicati. Non è consigliabile usare un'immagine o il relativo snapshot sottostante per più di un pool.

  I limiti possono essere anche inferiori se si configura il pool con i [pool NAT in ingresso](pool-endpoint-configuration.md).

- **Timeout ridimensionamento**: se il pool contiene un numero fisso di nodi (senza scalabilità automatica), aumentare il valore della proprietà resizeTimeout del pool a un valore come 20-30 minuti. Se il pool non raggiunge la dimensione di destinazione entro il periodo di timeout, eseguire un'altra [operazione di ridimensionamento](/rest/api/batchservice/pool/resize).

  Se si prevede di creare un pool con oltre 300 nodi di calcolo, può essere necessario ridimensionare il pool più volte per raggiungere le dimensioni di destinazione.

## <a name="considerations-for-using-packer"></a>Considerazioni sull'uso di Packer

Creazione di una risorsa immagine gestita direttamente con Packer può essere eseguita solo con gli account Batch in modalità di sottoscrizione utente. Per gli account in modalità servizio Batch, è necessario creare prima un disco rigido virtuale, quindi importare il disco rigido virtuale a una risorsa immagine gestita. I passaggi per creare una risorsa immagine gestita variano a seconda della modalità di allocazione pool (sottoscrizione utente o il servizio Batch).

Assicurarsi che la risorsa usata per creare l'immagine gestita esiste per la durata di qualsiasi pool facendo riferimento all'immagine personalizzata. In caso contrario, può comportare errori di allocazione pool e/o ridimensionare gli errori. 

Se l'immagine o la risorsa sottostante viene rimosso, si potrebbe verificarsi un errore simile a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. In questo caso, assicurarsi che la risorsa sottostante non è stata rimossa.

Per altre informazioni sull'uso di Packer per creare una macchina virtuale, vedere [creare un'immagine Linux con Packer](../virtual-machines/linux/build-image-with-packer.md) oppure [compilare un'immagine di Windows con Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
