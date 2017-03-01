---
title: Intelligence e analisi in Azure per enti pubblici | Microsoft Docs
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 14fb62669e15873770ef9b8470e34db70f1ca2ca
ms.openlocfilehash: 2b89d78e20cccee81c9581e762e4c61bacd84fd4
ms.lasthandoff: 02/15/2017


---
# <a name="azure-government-intelligence--analytics"></a>Intelligence e analisi in Azure per enti pubblici
Questo articolo descrive le variazioni ai servizi di intelligence e analisi e presenta alcune considerazioni sull'ambiente di Azure per enti pubblici.

## <a name="azure-hdinsight"></a>HDInsight di Azure
HDInsight su Linux Standard è disponibile a livello generale in Azure per enti pubblici. <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>Qui</a> è possibile visualizzare una dimostrazione su come creare soluzioni incentrate sui dati in Azure per enti pubblici usando HDInsight.

HDInsight in Linux Premium sarà presto disponibile.

### <a name="variations"></a>Varianti
Le funzionalità di HDInsight seguenti non sono attualmente disponibili in Azure per enti pubblici.

* HDInsight non è disponibile in Windows.
* Azure Data Lake Store non è attualmente disponibile in Azure per enti pubblici. Archiviazione BLOB di Azure è al momento l'unica opzione di archiviazione disponibile.

Gli URL per Log Analytics sono diversi in Azure per enti pubblici:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Cluster HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Per altre informazioni, vedere la [documentazione pubblica di HDInsight](../hdinsight/hdinsight-hadoop-introduction.md).

## <a name="power-bi"></a>Power BI
Power BI per il Governo degli Stati Uniti è in genere disponibile come parte delle sottoscrizioni a Office 365 della community del Governo degli Stati Uniti. <a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>Qui</a> è possibile ottenere informazioni su Power BI per il Governo degli Stati Uniti. <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>Qui</a> è possibile visualizzare una dimostrazione su come creare soluzioni incentrate sui dati in Azure per enti pubblici usando Power BI.

Power BI Embedded sarà presto disponibile.

### <a name="variations"></a>Varianti

Gli URL per Power BI sono diversi nel Governo degli Stati Uniti:

| Tipo di servizio | Power BI Commercial | Power BI per il Governo degli Stati Uniti |
| --- | --- | --- |
| URL di Power BI | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni e aggiornamenti, iscriversi al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici</a>.

