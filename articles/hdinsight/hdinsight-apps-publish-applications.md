---
title: Pubblicare applicazioni Azure HDInsight | Microsoft Docs
description: Informazioni su come creare un'applicazione HDInsight e quindi pubblicarla in Azure Marketplace.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 597ea68f063d02541132d275de815c1673369ae0
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Pubblicare un'applicazione HDInsight in Azure Marketplace
È possibile installare un'applicazione Azure HDInsight in un cluster HDInsight basato su Linux. Questo articolo fornisce informazioni su come pubblicare un'applicazione HDInsight in Azure Marketplace. Per informazioni generali sulla pubblicazione in Azure Marketplace, vedere [Pubblicare un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Le applicazioni HDInsight usano il modello *Bring Your Own License (BYOL)*. In uno scenario BYOL il provider di un'applicazione è responsabile della concessione in licenza dell'applicazione agli utenti dell'app. Gli utenti dell'app pagano solo le risorse di Azure che creano, come il cluster HDInsight e i nodi e le macchine virtuali del cluster. Attualmente, la fatturazione per l'applicazione non avviene in Azure.

Per altre informazioni, vedere questi articoli correlati alle applicazioni HDInsight:

* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md). Informazioni su come installare un'applicazione HDInsight nei cluster.
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md). Informazioni su come installare e testare applicazioni HDInsight personalizzate.

## <a name="prerequisites"></a>prerequisiti
Prima dell'invio al Marketplace, è necessario [creare e testare l'applicazione personalizzata](hdinsight-apps-install-custom-applications.md).

È anche necessario registrare l'account per sviluppatore. Per altre informazioni, vedere [Pubblicare un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [Creare un account di Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-the-application"></a>Definire l'applicazione
Per la pubblicazione di applicazioni nel Marketplace sono necessari due passaggi. Definire prima di tutto un file *createUiDef.json*. Il file createUiDef.json indica i cluster con cui l'applicazione è compatibile. Pubblicare quindi il modello dal portale di Azure. Ecco un esempio di file createUiDef.json:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Campo | DESCRIZIONE | Valori possibili |
| --- | --- | --- |
| types |Tipi di cluster con cui è compatibile l'applicazione. |Hadoop, HBase, Storm, Spark (o qualsiasi combinazione di questi) |
| versions |Tipi di cluster HDInsight con cui è compatibile l'applicazione. |3.4 |

## <a name="application-installation-script"></a>Script di installazione dell'applicazione
Quando un'applicazione viene installata in un cluster, esistente o nuovo, viene creato un nodo perimetrale. Lo script di installazione dell'applicazione viene eseguito nel nodo perimetrale.

  > [!IMPORTANT]
  > Il nome dello script di installazione dell'applicazione deve essere univoco per un cluster specifico. Il nome dello script deve avere il formato seguente:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName')]"
  > 
  > Il nome dello script è costituito da tre parti:
  > 
  > * Un prefisso del nome dello script, che include il nome dell'applicazione o un nome rilevante per l'applicazione.
  > * Un trattino, per migliorare la leggibilità.
  > * Una funzione stringa univoca, con il nome dell'applicazione come parametro.
  > 
  > Nell'elenco di azioni script persistenti, l'esempio precedente è indicato come **hue-install-v0-4wkahss55hlas**. Vedere un [payload JSON di esempio](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Lo script di installazione deve avere le caratteristiche seguenti:
* Lo script è idempotente. Le chiamate multiple allo script producono lo stesso risultato.
* Allo script deve essere applicato correttamente il controllo delle versioni. Quando si esegue l'aggiornamento o si testano le modifiche, usare un percorso diverso per lo script. In questo modo, l'aggiornamento o i test non influiscono sui clienti che stanno installando l'applicazione. 
* Lo script offre funzionalità di registrazione adeguate in ogni punto. In genere, i log degli script sono l'unico modo per eseguire il debug dei problemi di installazione delle applicazioni.
* Le chiamate alle risorse o ai servizi esterni dispongono di un numero di tentativi adeguato, in modo che l'installazione non sia compromessa da problemi di rete temporanei.
* Se lo script avvia servizi sui nodi, i servizi sono monitorati e configurati per l'avvio automatico in caso di riavvio di un nodo.

## <a name="package-the-application"></a>Creare il pacchetto dell'applicazione
Creare un file ZIP contenente tutti i file necessari per installare l'applicazione HDInsight. Il file ZIP verrà usato per [pubblicare l'applicazione](#publish-application). Il file ZIP include i file seguenti:

* [createUiDefinition.json](#define-application)
* mainTemplate.json (per un esempio, vedere [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md))
* Tutti gli script necessari

> [!NOTE]
> È possibile ospitare i file dell'applicazione (inclusi eventuali file dell'app Web) in qualsiasi endpoint accessibile pubblicamente.
> 

## <a name="publish-the-application"></a>Pubblicare l'applicazione
Per pubblicare un'applicazione HDInsight:

1. Accedere al [portale di pubblicazione di Azure](https://publish.windowsazure.com/).
2. Nel menu a sinistra selezionare **Modelli di soluzioni**.
3. Immettere un titolo e quindi selezionare **Create a new solution template** (Crea un nuovo modello di soluzione).
4. Se l'organizzazione non è ancora stata registrata, selezionare **Create Dev Center account and join the Azure program** (Creare un account Dev Center e partecipare al programma di Azure).  Per altre informazioni, vedere [Creare un account di Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Selezionare **Define some Topologies to get Started** (Definire alcune topologie per iniziare). Un modello di soluzione è un elemento padre per tutte le relative topologie. È possibile definire più topologie in un singolo modello di soluzione o offerta. Quando un'offerta passa alla fase di gestione temporanea, passano a tale fase anche tutte le relative topologie. 
6. Immettere il nome di una topologia e quindi selezionare **+**.
7. Immettere una nuova versione e quindi selezionare **+**.
8. Caricare il file ZIP creato quando al momento della [creazione del pacchetto dell'applicazione](#package-application).  
9. Selezionare **Request Certification** (Richiedi certificazione). Il team di certificazione Microsoft esamina i file e certifica la topologia.

## <a name="next-steps"></a>Passaggi successivi
* Leggere le informazioni su come [installare applicazioni HDInsight](hdinsight-apps-install-applications.md) nei cluster.
* Leggere le informazioni su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md) e distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* Leggere le informazioni su come [usare l'azione script per personalizzare cluster HDInsight basati su Linux](hdinsight-hadoop-customize-cluster-linux.md) e aggiungere altre applicazioni. 
* Leggere le informazioni su come [creare cluster Hadoop basati su Linux in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Leggere le informazioni su come [usare un nodo perimetrale vuoto in HDInsight](hdinsight-apps-use-edge-node.md) per accedere ai cluster HDInsight, testare le applicazioni HDInsight e ospitare le applicazioni HDInsight.

