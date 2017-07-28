---
title: Pubblicare applicazioni HDInsight in Azure | Microsoft Docs
description: Informazioni su come creare e pubblicare applicazioni HDInsight.
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
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 6aa66cac35bc317fc87003e6c3d824544c53de88
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Pubblicare applicazioni HDInsight in Azure Marketplace
Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight basato su Linux. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente. In questo articolo si apprenderà come pubblicare un'applicazione HDInsight in Azure Marketplace.  Per informazioni generali sulla pubblicazione in Azure Marketplace, vedere [Pubblicare un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Le applicazioni HDInsight usano il modello *Bring Your Own License (BYOL)* , in cui il provider dell'applicazione è responsabile di concedere in licenza l'applicazione agli utenti finali e agli utenti finali vengono addebitate da Azure solo le risorse create, ad esempio il cluster HDInsight e le VM o i nodi. Attualmente la fatturazione per l'applicazione in sé non viene effettuata con Azure.

Altro articolo correlato all'applicazione HDInsight:

* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come installare e testare applicazioni HDInsight personalizzate.

## <a name="prerequisites"></a>Prerequisiti
Prima di inviare l'applicazione personalizzata al marketplace, è necessario crearla e testarla. Vedere gli articoli seguenti:

* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come installare e testare applicazioni HDInsight personalizzate.

È anche necessario registrare l'account per sviluppatore. Vedere [Pubblicare un'offerta in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [Creare un account di Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Definire l'applicazione
La pubblicazione di applicazioni in Azure Marketplace richiede due passaggi.  Prima si definisce un file **createUiDef.json** per indicare i cluster con cui è compatibile l'applicazione e quindi si pubblica il modello dal portale di Azure. La sezione seguente rappresenta un file createUiDef.json di esempio.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| Campo | Descrizione | Valori possibili |
| --- | --- | --- |
| types |Tipi di cluster con cui è compatibile l'applicazione. |Hadoop, HBase, Storm, Spark (o qualsiasi combinazione di questi valori) |
| tiers |Livelli di cluster con cui è compatibile l'applicazione. |Standard, Premium (o entrambi) |
| versions |Tipi di cluster HDInsight con cui è compatibile l'applicazione. |3.4 |

## <a name="application-install-script"></a>Script di installazione dell'applicazione
Ogni volta che un'applicazione viene installata in un cluster, esistente o nuovo, viene creato un nodo perimetrale e lo script di installazione dell'applicazione viene eseguito su di esso.
  > [!IMPORTANT]
  > Il nome dello script di installazione dell'applicazione deve essere univoco per un determinato cluster con il formato seguente.
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Si noti che il nome dello script è composto da tre parti:
  > 
  > 1. Un prefisso del nome file che includerà il nome dell'applicazione o un nome rilevante per l'applicazione.
  > 2. Un "-" per migliorare la leggibilità.
  > 3. Una funzione stringa univoca con il nome dell'applicazione come parametro.
  > 
  > Ad esempio, la stringa indicata sopra alla fine diventerà: hue-install-v0-4wkahss55hlas nell'elenco di azioni script persistenti. Per un payload JSON di esempio, vedere [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
Lo script di installazione deve avere le caratteristiche seguenti:
1. Assicurarsi che lo script sia idempotente. Le chiamate multiple alo script devono produrre lo stesso risultato.
2. Lo script deve disporre di versioni correttamente. Usare un percorso diverso per lo script quando si aggiorna o si testano le modifiche in modo che i clienti che tentano di installare l'applicazione non siano interessati. 
3. Aggiungere registrazioni adeguate agli script in ogni punto. In genere i log degli script sono l'unico modo per eseguire il debug di problemi di installazione dell'applicazione.
4. Assicurarsi che le chiamate ai servizi esterni o le risorse dispongano di tentativi adeguati in modo che l'installazione non subisca i problemi di rete temporanei.
5. Se lo script avvia i servizi sui nodi, verificare che i servizi siano monitorati e configurati per l'avvio automatico in caso di riavvio del nodo.

## <a name="package-application"></a>Inserire l'applicazione in un pacchetto
Creare un file ZIP contenente tutti i file necessari per installare le applicazioni HDInsight. Il file ZIP sarà necessario in [Pubblicare l'applicazione](#publish-application).

* [createUiDefinition.json](#define-application).
* mainTemplate.json. Vedere un esempio in [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).
* Tutti gli script necessari.

> [!NOTE]
> I file dell'applicazione (inclusi eventuali file dell'applicazione Web) possono trovarsi in qualsiasi endpoint accessibile pubblicamente.
> 

## <a name="publish-application"></a>Pubblicare l'applicazione
Seguire questa procedura per pubblicare un'applicazione HDInsight:

1. Accedere al [portale di pubblicazione di Azure](https://publish.windowsazure.com/).
2. Fare clic su **Modelli di soluzioni** per creare un nuovo modello di soluzione.
3. Immettere un titolo, quindi fare clic su **Create a new solution template** (Crea un nuovo modello di soluzione).
4. Fare clic su **Create Dev Center account and join the Azure program** (Creare un account Dev Center e partecipare al programma di Azure) per registrare la società, se non è ancora stato fatto.  Vedere [Creare un account di Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Fare clic su **Define some Topologies to get Started**(Definire alcune topologie per iniziare). Un modello di soluzione è un elemento padre per tutte le relative topologie. È possibile definire più topologie in un singolo modello di soluzione/offerta. Quando un'offerta passa alla fase di gestione temporanea, passano a tale fase anche tutte le relative topologie. 
6. Immettere il nome di una topologia, quindi fare clic sul segno più.
7. Immettere una nuova versione, quindi fare clic sul segno più.
8. Caricare il file ZIP preparato in [Inserire l'applicazione in un pacchetto](#package-application).  
9. Fare clic su **Request Certification**(Richiedi certificazione). Il team di certificazione Microsoft esaminerà i file e certificherà la topologia.

## <a name="next-steps"></a>Passaggi successivi
* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight, il test di applicazioni HDInsight e l'hosting di applicazioni HDInsight.


