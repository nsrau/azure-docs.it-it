---
title: Aggiungere e gestire le librerie per Apache Spark in Azure sinapsi Analytics
description: Informazioni su come aggiungere e gestire le librerie usate da Apache Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 85635ff3a52236d0c53b72c68eda0d71debe1f61
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145909"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Aggiungere e gestire le librerie per Apache Spark in Azure sinapsi Analytics

Apache Spark dipende da numerose librerie per offrire funzionalità. Queste librerie possono essere ampliate o sostituite con librerie aggiuntive o versioni aggiornate di quelle meno recenti.

I pacchetti Python possono essere aggiunti al livello del pool Spark (anteprima) e i pacchetti basati su file con estensione jar possono essere aggiunti a livello di definizione del processo Spark.

## <a name="adding-or-updating-python-libraries"></a>Aggiunta o aggiornamento di librerie Python

Apache Spark in Azure sinapsi Analytics ha un'installazione completa di Anaconda, oltre a librerie aggiuntive. L'elenco completo delle librerie è disponibile nel [Apache Spark supporto della versione](apache-spark-version-support.md).

Quando viene avviata un'istanza di Spark, viene creato un nuovo ambiente virtuale utilizzando questa installazione come base. Inoltre, è possibile utilizzare un file di *requirements.txt* (output del `pip freeze` comando) per aggiornare l'ambiente virtuale. I pacchetti elencati in questo file per l'installazione o l'aggiornamento vengono scaricati da PyPi al momento dell'avvio del cluster. Questo file dei requisiti viene usato ogni volta che viene creata un'istanza Spark dal pool Spark.

> [!IMPORTANT]
>
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

### <a name="python-library-user-interface"></a>Interfaccia utente della libreria Python

L'interfaccia utente per l'aggiunta di librerie si trova nella scheda **Impostazioni aggiuntive** della pagina **crea pool di Apache Spark** del portale di Azure.

Caricare il file di configurazione dell'ambiente utilizzando il selettore file nella sezione **pacchetti** della pagina.

![Aggiungere librerie Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Aggiungere librerie Python")

### <a name="verifying-installed-libraries"></a>Verifica delle librerie installate

Per verificare se sono installate le versioni corrette delle librerie corrette, eseguire il codice seguente

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione di Apache Spark](https://spark.apache.org/docs/2.4.4/)
