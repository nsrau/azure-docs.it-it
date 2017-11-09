---
title: Effettuare il provisioning dei pool di Azure Batch da immagini personalizzate | Microsoft Docs
description: "È possibile creare un pool di Batch da un'immagine personalizzata per effettuare il provisioning dei nodi di calcolo che contengono il software e i dati necessari per l'applicazione. Le immagini personalizzate sono uno strumento efficace per configurare i nodi di calcolo per l'esecuzione dei carichi di lavoro di Batch."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: v-dotren
ms.openlocfilehash: d62abd673f89fd51edba721119d1680762a60c76
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Usare un'immagine personalizzata gestita per creare un pool di macchine virtuali 

Quando si crea un pool in Azure Batch usando la configurazione della macchina virtuale, specificare l'immagine di macchina virtuale (VM) che fornisce la configurazione del sistema operativo per ogni nodo di calcolo nel pool. È possibile creare un pool di macchine virtuali con un'immagine di Azure Marketplace o con un'immagine personalizzata, vale a dire un'immagine di macchina virtuale creata e configurata manualmente. L'immagine personalizzata deve essere una risorsa di tipo *immagine gestita* nella stessa sottoscrizione di Azure e nella stessa area dell'account Batch.

## <a name="why-use-a-custom-image"></a>Vantaggi dell'uso di un'immagine personalizzata
Quando si specifica un'immagine personalizzata, si ha la possibilità di controllare la configurazione e il tipo del sistema operativo, nonché i dischi dati da usare. L'immagine personalizzata può includere applicazioni e dati di riferimento che diventano disponibili in tutti i nodi del pool di Azure Batch non appena viene effettuato il provisioning.

L'uso di un'immagine personalizzata permette di risparmiare tempo nel preparare i nodi di calcolo del pool per l'esecuzione del carico di lavoro di Batch. Benché sia possibile usare un'immagine di Azure Marketplace e installare il software in ogni nodo di calcolo dopo averne effettuato il provisioning, l'uso di un'immagine personalizzata può rivelarsi più efficiente.

L'uso di un'immagine personalizzata configurata per uno scenario specifico può essere caratterizzato da numerosi vantaggi:

- **Configurare il sistema operativo**. Le configurazioni speciali del sistema operativo possono essere eseguite sul disco del sistema operativo dell'immagine personalizzata. 
- **Pre-installare applicazioni**. La possibilità di creare un'immagine personalizzata con applicazioni pre-installate sul disco del sistema operativo risulta essere più efficiente e meno soggetta a errori rispetto all'installazione di applicazioni dopo l'esecuzione del provisioning dei nodi di calcolo mediante StartTask.
- **Risparmiare tempo di riavvio nelle macchine virtuali.** L'installazione delle applicazioni in genere prevede il riavvio della macchina virtuale e pertanto questa operazione richiede molto tempo. È possibile risparmiare tempo di riavvio mediante la pre-installazione delle applicazioni. 
- **Copiare grandi quantità di dati solo una volta.** È possibile includere i dati statici nell'immagine personalizzata gestita copiandoli nei dischi dati di un'immagine gestita. Questa operazione deve essere eseguita solo una volta e consente di rendere i dati disponibili per ogni nodo del pool.
- **Scelta di tipi di disco.** È possibile creare un'immagine personalizzata gestita da un disco rigido virtuale, da un disco gestito di una macchina virtuale di Azure, da uno snapshot di tali dischi o dall'installazione di Linux o Windows configurata. È possibile scegliere di usare l'archiviazione Premium per il disco del sistema operativo e per il disco dati.
- **Incrementare le dimensioni dei pool in base alle specifiche esigenze.** Quando per creare un pool si usa un'immagine personalizzata gestita, il pool può assumere le dimensioni desiderate. Non è necessario creare copie dei dischi rigidi virtuali del BLOB dell'immagine per adattare le dimensioni al numero di macchine virtuali. 


## <a name="prerequisites"></a>Prerequisiti

- **Una risorsa immagine gestita**. Per creare un pool di macchine virtuali usando un'immagine personalizzata, è necessario creare una risorsa immagine gestita nella stessa sottoscrizione di Azure e nella stessa area dell'account di Batch. Per le opzioni disponibili per la creazione di un'immagine gestita, vedere la sezione seguente.
- **Autenticazione di Azure Active Directory (AAD)**. L'API client di Batch deve usare l'autenticazione AAD. Il supporto di Azure Batch per l'autenticazione AAD è documentato in [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Preparare un'immagine personalizzata
È possibile preparare un'immagine gestita da un disco rigido virtuale, da una macchina virtuale di Azure con dischi gestiti o dallo snapshot di una macchina virtuale. 

Durante la preparazione dell'immagine è necessario tenere presente quanto segue:

* Assicurarsi che l'immagine del sistema operativo di base usata per il provisioning dei pool di Batch non includa alcuna estensione di Azure preinstallata, ad esempio l'estensione Script personalizzato. Se l'immagine contiene un'estensione preinstallata, è possibile che Azure rilevi problemi durante la distribuzione della VM.
* Verificare che l'immagine del sistema operativo di base usi l'unità temporanea predefinita. L'agente del nodo Batch attualmente prevede l'uso dell'unità temporanea predefinita.
* Non è possibile eliminare la risorsa immagine gestita a cui fa riferimento un pool di Batch per la durata del pool. Se la risorsa immagine gestita viene eliminata, le dimensioni del pool non potranno aumentare ulteriormente. 

### <a name="to-create-a-managed-image"></a>Per creare un'immagine gestita
È possibile usare qualsiasi disco preparato esistente del sistema operativo Windows o Linux per creare un'immagine gestita. Per usare, ad esempio, un'immagine locale, caricare il disco locale in un account Archiviazione di Azure nella stessa sottoscrizione e nella stessa area dell'account Batch usando AzCopy o un altro strumento di caricamento. Per istruzioni dettagliate sul caricamento di un disco rigido virtuale e creare un'immagine gestita, vedere le linee guida relative alle macchine virtuali di [Windows](../virtual-machines/windows/upload-generalized-managed.md) o [Linux](../virtual-machines/linux/upload-vhd.md).

È anche possibile preparare un'immagine gestita da una macchina virtuale di Azure nuova o esistente oppure da uno snapshot della macchina virtuale. 

* Se si crea una nuova macchina virtuale, è possibile usare un'immagine di Azure Marketplace come immagine di base per l'immagine gestita e quindi personalizzarla. 

* Se si prevede di acquisire l'immagine usando il portale, assicurarsi che la macchina virtuale venga creata con un disco gestito. Questa è l'impostazione di archiviazione predefinita quando si crea una macchina virtuale.

* Quando la VM è in esecuzione, connetterla tramite RDP (per Windows) o SSH (per Linux). Installare il software necessario o copiare i dati desiderati e quindi generalizzare la VM.  

Per istruzioni su come generalizzare una macchina virtuale di Azure e creare un'immagine gestita, vedere le linee guida relative alle macchine virtuali di [Windows](../virtual-machines/windows/capture-image-resource.md) o [Linux](../virtual-machines/linux/capture-image.md).

A seconda di come si decide di creare un pool di Batch con l'immagine, per l'immagine è necessario disporre dell'identificatore seguente:

* Se si prevede di creare un pool con l'immagine usando le API di Batch, l'**ID risorsa** dell'immagine, il cui formato è `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Se si prevede di usare il portale, il **nome** dell'immagine. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Per creare un pool da un'immagine personalizzata nel portale

Dopo avere salvato l'immagine personalizzata e conoscendone l'ID risorsa o il nome, è possibile creare un pool di Batch da tale immagine. La procedura seguente illustra come creare un pool dal portale di Azure.

> [!NOTE]
> Se si sta creando il pool usando una delle API di Batch, assicurarsi che l'identità usata per l'autenticazione AAD disponga delle autorizzazioni per la risorsa immagine. Vedere [Autenticare le soluzioni del servizio Batch con Active Directory](batch-aad-auth.md).
>

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
 
## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dettagliata di Batch, vedere [Sviluppare soluzioni di calcolo parallele su larga scala con Batch](batch-api-basics.md).
