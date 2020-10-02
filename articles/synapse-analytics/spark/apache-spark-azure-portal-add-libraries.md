---
title: Gestire le librerie per Apache Spark in Azure sinapsi Analytics
description: Informazioni su come aggiungere e gestire le librerie usate da Apache Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: euang
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: ad3231652056244fdfc814251e6caad025db77e5
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649899"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gestire le librerie per Apache Spark in Azure sinapsi Analytics

Le librerie forniscono codice riutilizzabile che può essere utile includere nei programmi o nei progetti. Per rendere disponibili le applicazioni di terze parti o codice compilato localmente, è possibile installare una libreria in uno dei pool Spark (anteprima). Quando una libreria è installata per un pool Spark, è disponibile per tutte le sessioni che usano lo stesso pool. 

## <a name="default-installation"></a>Installazione predefinita
Apache Spark in Azure sinapsi Analytics ha un'installazione completa di Anaconda, oltre a librerie aggiuntive. L'elenco completo delle librerie è disponibile nel [Apache Spark supporto della versione](apache-spark-version-support.md). 

Quando viene avviata un'istanza di Spark, queste librerie verranno incluse automaticamente. È possibile aggiungere altri pacchetti Python e personalizzati al livello del pool di Spark (anteprima).


## <a name="manage-python-packages"></a>Gestire pacchetti Python
Una volta identificate le librerie che si vuole usare per l'applicazione Spark, è possibile installarle in un pool Spark (anteprima). 

 È possibile utilizzare un file di *requirements.txt* (output del `pip freeze` comando) per aggiornare l'ambiente virtuale. I pacchetti elencati in questo file per l'installazione o l'aggiornamento vengono scaricati da PyPi al momento dell'avvio del pool. Questo file dei requisiti viene usato ogni volta che viene creata un'istanza Spark dal pool Spark.

> [!IMPORTANT]
> - Se il pacchetto che si sta installando è di grandi dimensioni o richiede molto tempo per l'installazione, questo influirà sul tempo di avvio dell'istanza di Spark.
> - I pacchetti che richiedono il supporto del compilatore in fase di installazione, ad esempio GCC, non sono supportati.
> - Non è possibile effettuare il downgrade dei pacchetti, solo aggiunti o aggiornati.

### <a name="requirements-format"></a>Formato requisiti

Il frammento di codice seguente mostra il formato per il file dei requisiti. Il nome del pacchetto PyPi è elencato insieme a una versione esatta. Questo file segue il formato descritto nella documentazione di riferimento per il [blocco PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . In questo esempio viene pin una versione specifica. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Installare i pacchetti Python
Durante lo sviluppo dell'applicazione Spark, potrebbe essere necessario aggiornare le librerie esistenti o installarne di nuove. È possibile aggiornare le librerie durante o dopo la creazione del pool.

#### <a name="install-packages-during-pool-creation"></a>Installare i pacchetti durante la creazione del pool
Per installare le librerie in un pool Spark (anteprima) durante la creazione del pool:
   
1. Passare all'area di lavoro di Azure sinapsi Analytics dal portale di Azure.
   
2. Selezionare **Crea pool di Apache Spark** , quindi selezionare la scheda **Impostazioni aggiuntive** . 
   
3. Caricare il file di configurazione dell'ambiente utilizzando il selettore file nella sezione **pacchetti** della pagina. 
   
![Aggiungere librerie Python durante la creazione del pool](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Aggiungere librerie Python")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Installare i pacchetti dall'area di lavoro sinapsi
Per aggiornare o aggiungere altre librerie a un pool Spark (anteprima) dal portale di analisi delle sinapsi di Azure:

1.  Passare all'area di lavoro di Azure sinapsi Analytics dal portale di Azure.
   
2.  Avviare l'area di lavoro di Azure sinapsi Analytics dalla portale di Azure.

3.  Selezionare **Gestisci** dal pannello di navigazione principale, quindi selezionare **Apache Spark pool**.
   
4. Selezionare un singolo pool Spark e caricare il file di configurazione dell'ambiente usando il selettore file nella sezione  **pacchetti** della pagina.

![Aggiungere librerie Python in sinapsi](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png "Aggiungere librerie Python")
   
#### <a name="install-packages-from-the-azure-portal"></a>Installare i pacchetti dal portale di Azure
Per installare una libreria in un pool Spark (anteprima) direttamente dal portale di Azure:
   
 1. Passare all'area di lavoro di Azure sinapsi Analytics dal portale di Azure.
   
 2. Nella sezione **risorse sinapsi** selezionare la scheda **pool di Apache Spark** e selezionare un pool Spark nell'elenco.
   
 3. Selezionare **pacchetti** dalla sezione **Impostazioni** del pool Spark. 

 4. Caricare il file di configurazione dell'ambiente utilizzando il selettore file.

![Schermata che evidenzia il pulsante Carica file di configurazione dell'ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Aggiungere librerie Python")

### <a name="verify-installed-libraries"></a>Verificare le librerie installate

Per verificare se sono installate le versioni corrette delle librerie corrette, eseguire il codice seguente

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>Aggiornare i pacchetti Python
I pacchetti possono essere aggiunti o modificati in qualsiasi momento tra le sessioni. Quando viene caricato un nuovo file di configurazione del pacchetto, i pacchetti e le versioni esistenti vengono sovrascritti.  

Per aggiornare o disinstallare una libreria:
1. Passare all'area di lavoro di Azure sinapsi Analytics. 

2. Usando l'area di lavoro portale di Azure o la sinapsi di Azure, selezionare il **pool di Apache Spark** che si vuole aggiornare.

3. Passare alla sezione **pacchetti** e caricare un nuovo file di configurazione dell'ambiente
   
4. Dopo aver salvato le modifiche, sarà necessario terminare le sessioni attive e consentire il riavvio del pool. Facoltativamente, è possibile forzare la chiusura delle sessioni attive selezionando la casella di controllo per **forzare nuove impostazioni**.

![Aggiungere librerie Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Aggiungere librerie Python")
   

> [!IMPORTANT]
> Selezionando l'opzione per **forzare nuove impostazioni**, si terminano tutte le sessioni correnti per il pool Spark selezionato. Una volta terminate le sessioni, sarà necessario attendere il riavvio del pool. 
>
> Se questa impostazione è deselezionata, sarà necessario attendere la fine della sessione Spark corrente o arrestarla manualmente. Al termine della sessione, sarà necessario consentire il riavvio del pool. 


## <a name="manage-a-python-wheel"></a>Gestire una rotellina Python

### <a name="install-a-custom-wheel-file"></a>Installare un file della rotellina personalizzato
I pacchetti di ruote compilati personalizzati possono essere installati nel pool di Apache Spark caricando tutti i file della rotellina nell'account Azure Data Lake Storage (Gen2) collegato con l'area di lavoro sinapsi. 

I file devono essere caricati nel percorso seguente nel contenitore predefinito dell'account di archiviazione: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>sparkpools/<pool_name>libraries/python/
```

>[!IMPORTANT]
>È possibile aggiungere o modificare pacchetti personalizzati tra le sessioni. Tuttavia, sarà necessario attendere il riavvio del pool e della sessione per visualizzare il pacchetto aggiornato.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: [supporto della versione Apache Spark](apache-spark-version-support.md)
