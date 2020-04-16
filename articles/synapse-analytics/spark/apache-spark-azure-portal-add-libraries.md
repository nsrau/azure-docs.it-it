---
title: Aggiungere e gestire librerie per Apache Spark in Azure Synapse Analytics
description: Informazioni su come aggiungere e gestire librerie usate da Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427835"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Aggiungere e gestire librerie per Apache Spark in Azure Synapse Analytics

Apache Spark dipende da molte librerie per fornire funzionalità. Queste librerie possono essere aumentate o sostituite con librerie aggiuntive o versioni aggiornate di quelle precedenti.

I pacchetti Python possono essere aggiunti a livello di pool Spark (anteprima) e .jar a livello di definizione del processo Spark.

## <a name="adding-or-updating-python-libraries"></a>Aggiunta o aggiornamento di librerie Python

Apache Spark in Azure Synapse Analytics ha un'installazione completa di Anacondas e librerie aggiuntive. L'elenco completo delle librerie è disponibile in Supporto per la [versione aPache Spark](apache-spark-version-support.md).

Quando viene avviata un'istanza Di Spark, viene creato un nuovo ambiente virtuale usando questa installazione come base. Inoltre, un file *requirements.txt* (output dal `pip freeze` comando) può essere utilizzato per aggiornare l'ambiente virtuale. I pacchetti elencati in questo file per l'installazione o l'aggiornamento vengono scaricati da Pipi al momento dell'avvio del cluster. Questo file dei requisiti viene utilizzato ogni volta che viene creata un'istanza Spark da tale pool Spark.This requirements file is used every time a Spark instance is created from that Spark pool.

> [!IMPORTANT]
>
> - Se il pacchetto che si sta installando è di grandi dimensioni o richiede molto tempo per l'installazione, ciò influisce sul tempo di avvio dell'istanza di Spark.
> - I pacchetti che richiedono il supporto del compilatore in fase di installazione, ad esempio GCC, non sono supportati.
> - I pacchetti non possono essere declassati, solo aggiunti o aggiornati.

### <a name="requirements-format"></a>Formato dei requisiti

Il frammento di codice seguente mostra il formato per il file dei requisiti. Il nome del pacchetto PyPi è elencato insieme a una versione esatta. Questo file segue il formato descritto nella documentazione di riferimento [sul congelamento pip.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Questo esempio aggiunge una versione specifica. È inoltre possibile specificare "non più grande di" e "minore di" versioni in questo file.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interfaccia utente della libreria Python

L'interfaccia utente per l'aggiunta di librerie è nella scheda Impostazioni aggiuntive della pagina Crea pool Apache Spark nel portale di Azure.The UI for adding libraries is in the **Additional settings** tab of the **Create Apache Spark pool** page on the Azure portal.

Caricare il file di configurazione dell'ambiente utilizzando il selettore di file nella sezione **Pacchetti** della pagina.

![Aggiungere librerie Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Aggiungere librerie Python")

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione di Apache Spark](https://spark.apache.org/docs/2.4.4/)
