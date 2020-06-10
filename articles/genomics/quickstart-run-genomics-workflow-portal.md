---
title: 'Guida introduttiva: Eseguire un flusso di lavoro - Genomica di Microsoft'
description: La guida introduttiva mostra come caricare dati di input in un archivio BLOB di Azure ed eseguire un flusso di lavoro tramite il servizio Genomica di Microsoft.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.custom: tracking-python
ms.openlocfilehash: 167bcf4364b88529256b79574c6b8c03098fed02
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607126"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Guida introduttiva: Eseguire un flusso di lavoro tramite il servizio Genomica di Microsoft

In questo argomento di avvio rapido si caricano i dati di input in un account di archiviazione BLOB di Azure e si esegue un flusso di lavoro tramite il servizio Genomica di Microsoft usando il client di genomica Python. Genomica di Microsoft è un servizio scalabile e sicuro per l'analisi secondaria che consente di elaborare rapidamente un genoma, a partire dalle letture non elaborate e per la produzione di letture allineate e chiamate delle varianti. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12+](https://www.python.org/downloads/release/python-2714/) con `pip` installato e `python` nel percorso di sistema. Il client Genomica di Microsoft non è compatibile con Python 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Configurazione: creare un account di Genomica di Microsoft nel portale di Azure

Per creare un account di Genomica di Microsoft, passare a [Crea un account Genomica](https://portal.azure.com/#create/Microsoft.Genomics) nel portale di Azure. Se non si ha ancora una sottoscrizione di Azure, crearne una prima di creare un account di Genomica di Microsoft. 

![Genomica di Microsoft nel portale di Azure](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Genomica di Microsoft nel portale di Azure")

Configurare l'account di Genomica con le informazioni seguenti, come mostrato nell'immagine precedente. 

 |**Impostazione**          |  **Valore consigliato**  | **Descrizione campo** |
 |:-------------       |:-------------         |:----------            |
 |Subscription         | Nome della sottoscrizione utente|Unità di fatturazione per i servizi di Azure. Per informazioni dettagliate sulla sottoscrizione, vedere [Sottoscrizioni](https://account.azure.com/Subscriptions) |      
 |Resource group       | MyResourceGroup       |  I gruppi di risorse consentono di raggruppare più risorse di Azure (account di archiviazione, account di Genomica e così via) in un singolo gruppo per semplificare la gestione. Per altre informazioni, vedere [Gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Per informazioni sui nomi di gruppi di risorse validi, vedere [Convenzioni di denominazione](/azure/architecture/best-practices/resource-naming) |
 |Nome account         | MyGenomicsAccount     |Scegliere un identificatore dell'account univoco. Per informazioni sui nomi validi, vedere [Convenzioni di denominazione](/azure/architecture/best-practices/resource-naming) |
 |Location                   | Stati Uniti occidentali 2                    |    Il servizio è disponibile nelle aree Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale |

È possibile fare clic su **Notifiche** sulla barra dei menu per monitorare il processo di distribuzione.

![Notifications](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Notifiche")

Per altre informazioni su Genomica di Microsoft, vedere [Informazioni su Genomica di Microsoft](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Configurazione: installare il client Python per Genomica di Microsoft

È necessario installare sia Python e che il client Python per Genomica di Microsoft nell'ambiente locale. 

### <a name="install-python"></a>Installare Python

Il client Python per Genomica di Microsoft è compatibile con Python 2.7.12 o una versione 2.7.xx successiva. 2.7.14 è la versione consigliata. Il download è disponibile [qui](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x non è compatibile con Python 2.7.xx.  MSGen è un'applicazione Python 2.7. Quando si esegue MSGen, assicurarsi che l'ambiente Python attivo usi una versione 2.7.xx di Python. È possibile che si verifichino errori quando si tenta di usare MSGen con una versione 3.x di Python.

### <a name="install-the-microsoft-genomics-client"></a>Installare il client di Genomica di Microsoft

Usare `pip` di Python per installare il client `msgen` per Genomica di Microsoft. Le istruzioni seguenti presuppongono che Python sia già disponibile nel percorso di sistema. In caso di problemi dovuti al mancato riconoscimento dell'installazione tramite `pip`, è necessario aggiungere Python e la sottocartella degli script al percorso di sistema.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Se non si vuole installare `msgen` come file binario a livello di sistema e modificare i pacchetti Python a livello di sistema, usare il flag `–-user` con `pip`.
Se si usa l'installazione basata su pacchetti o il file setup.py, vengono installati tutti i pacchetti necessari. In caso contrario, i pacchetti necessari di base per `msgen` sono 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage). 
 * [Requests](https://pypi.python.org/pypi/requests). 

È possibile installare questi pacchetti usando `pip`, `easy_install` o tramite procedure `setup.py` standard. 

### <a name="test-the-microsoft-genomics-client"></a>Eseguire test del client di Genomica di Microsoft
Per testare il client di Genomica di Microsoft, scaricare il file di configurazione dall'account di Genomica. Nel portale di Azure passare all'account Genomica facendo clic su **Tutti i servizi** in alto a sinistra, quindi cercare e selezionare gli account Genomica.

![Trovare Genomica di Microsoft nel portale di Azure](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Trovare Genomica di Microsoft nel portale di Azure")

Selezionare l'account Genomica appena creato, passare a **Chiavi di accesso** e scaricare il file di configurazione.

![Scaricare il file di configurazione da Genomica di Microsoft](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Scaricare il file di configurazione da Genomica di Microsoft")

Verificare il funzionamento del client Python per Genomica di Microsoft con il comando seguente

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Creare un account di archiviazione di Microsoft Azure 
Il servizio Genomica di Microsoft prevede l'archiviazione di input come BLOB in blocchi in un account di archiviazione di Azure. I file di output vengono inoltre scritti come BLOB in blocchi in un contenitore specificato dall'utente in un account di archiviazione di Azure. I file di input e di output possono trovarsi in account di archiviazione diversi.
Se i dati sono già disponibili in un account di archiviazione di Azure, è necessario solo assicurarsi che si trovino nella stessa posizione dell'account di Genomica. In caso contrario, quando si esegue il servizio Genomica di Microsoft, verranno applicati addebiti per il traffico in uscita. Se non si ha un account di archiviazione di Azure, è necessario crearne uno e caricare i dati. Altre informazioni sugli account di archiviazione di Azure sono disponibili [qui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), incluse informazioni sull'account di archiviazione specifico e sui servizi forniti. Per creare un account di archiviazione di Azure, passare a [Crea account di archiviazione](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) nel portale di Azure.  

![Pagina per la creazione dell'account di archiviazione](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Pagina per la creazione dell'account di archiviazione")

Configurare l'account di archiviazione con le informazioni seguenti, come mostrato nell'immagine precedente. Usare la maggior parte delle opzioni standard per un account di archiviazione, specificando solo che l'account è un account di archiviazione BLOB, non per utilizzo generico. Un archivio BLOB può risultare da 2 a 5 volte più veloce per download e caricamenti.  La scelta consigliata è il modello di distribuzione predefinito, Azure Resource Manager.  

 |**Impostazione**          |  **Valore consigliato**  | **Descrizione campo** |
 |:-------------------------       |:-------------         |:----------            |
 |Subscription         | Sottoscrizione di Azure |Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.azure.com/Subscriptions) |      
 |Resource group       | MyResourceGroup       |  È possibile selezionare lo stesso gruppo di risorse dell'account Genomica. Per informazioni sui nomi di gruppi di risorse validi, vedere [Regole di denominazione](/azure/architecture/best-practices/resource-naming) |
 |Nome dell'account di archiviazione         | MyStorageAccount     |Scegliere un identificatore dell'account univoco. Per informazioni sui nomi validi, vedere [Regole di denominazione](/azure/architecture/best-practices/resource-naming) |
 |Location                  | Stati Uniti occidentali 2                  | Usare la stessa località specificata per l'account Genomica per ridurre gli addebiti relativi al traffico in uscita e per ridurre la latenza.  | 
 |Prestazioni                  | Standard                   | Il livello predefinito è Standard. Per informazioni dettagliate sugli account di archiviazione Standard e Premium, vedere [Introduzione ad Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Tipo di account       | BlobStorage       |  Un archivio BLOB può risultare da 2 a 5 volte più veloce rispetto all'utilizzo generico per download e caricamenti. |
 |Replica                  | Archiviazione con ridondanza locale                  | L'archiviazione con ridondanza locale replica i dati entro il data center nell'area in cui è stato creato l'account di archiviazione. Per altre informazioni, vedere [Replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Livello di accesso                  | Accesso frequente                   | Il livello di accesso frequente indica un accesso più frequente agli oggetti nell'account di archiviazione.    |

Selezionare quindi **Rivedi e crea** per creare l'account di archiviazione. Analogamente alla creazione dell'account Genomica, è possibile selezionare **Notifiche** sulla barra dei menu superiore per monitorare il processo di distribuzione. 

## <a name="upload-input-data-to-your-storage-account"></a>Caricare i dati di input nell'account di archiviazione

Il servizio Genomica di Microsoft prevede letture di estremità abbinate (file fastq o bam) come file di input. È possibile scegliere di caricare dati personalizzati o esplorare l'uso di dati di esempio disponibili pubblicamente. Se si vogliono usare i dati di esempio disponibili pubblicamente, tali dati sono ospitati qui:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

Nell'account di archiviazione è necessario creare un contenitore BLOB per i dati di input e un secondo contenitore BLOB per i dati di output.  Caricare i dati di input nel contenitore BLOB di input. È possibile usare diversi strumenti per questa procedura, tra cui [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter) o [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Eseguire un flusso di lavoro tramite il servizio Genomica di Microsoft con il client Python 

Per eseguire un flusso di lavoro tramite il servizio Genomica di Microsoft, modificare il file *config.txt* per specificare il contenitore di archiviazione di input e di output per i dati.
Aprire il file *config.txt* scaricato dall'account Genomica. Le sezioni da specificare riguardano la chiave di sottoscrizione e i sei elementi nella parte inferiore, il nome dell'account di archiviazione, la chiave e il nome del contenitore per input e output. Queste informazioni sono disponibili nella sezione **Chiavi di accesso** del portale di Azure per l'account di archiviazione o direttamente da Azure Storage Explorer.  

![Configurazione di Genomica](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Configurazione di Genomica")

Se si vuole eseguire GATK4, impostare il parametro `process_name` su `gatk4`.

Per impostazione predefinita, il servizio Genomica genera file VCF. Se si vuole ottenere un file gVCF anziché VCF come output (equivalente a `-emitRefConfidence` in GATK 3.x e `emit-ref-confidence` in GATK 4.x), aggiungere il parametro `emit_ref_confidence` al file *config.txt* e impostarlo su `gvcf`, come illustrato nella figura qui sopra.  Per tornare all'output VCF, rimuoverlo dal file *config.txt* o impostare il parametro `emit_ref_confidence` su `none`. 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Inviare il flusso di lavoro al servizio Genomica di Microsoft tramite il client di Genomica di Microsoft

Usare il client Python di Genomica di Microsoft per inviare il flusso di lavoro con il comando seguente:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

È possibile visualizzare lo stato dei flussi di lavoro usando il comando seguente: 
```python
msgen list -f c:\temp\config.txt 
```

Al termine del flusso di lavoro, è possibile visualizzare i file di output nell'account di archiviazione di Azure nel contenitore di output configurato. 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati caricati dati di input di esempio in Archiviazione di Azure ed è stato inviato un flusso di lavoro al servizio Genomica di Microsoft tramite il client `msgen` Python. Per altre informazioni su altri tipi di file di input che possono essere usati con il servizio Genomica di Microsoft, vedere le pagine seguenti: [paired FASTQ](quickstart-input-pair-FASTQ.md) (FASTQ abbinato)  | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ or BAM](quickstart-input-multiple.md) (Più FASTQ o BAM) È anche possibile esplorare questa esercitazione usando l'[esercitazione sui notebook di Azure](https://aka.ms/genomicsnotebook).
