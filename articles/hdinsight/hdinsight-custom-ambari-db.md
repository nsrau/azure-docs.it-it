---
title: Database Apache Ambari personalizzato in Azure HDInsight
description: Informazioni su come creare cluster HDInsight con un database Apache Ambari personalizzato.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: 1858e06567a0ab0907e6d2cb60358ff4ac00f9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086348"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configurare cluster HDInsight con un database Ambari personalizzato

Apache Ambari semplifica la gestione e il monitoraggio di un cluster Apache Hadoop. Ambari offre un'interfaccia utente Web e un'API REST di facile utilizzo. Ambari è incluso nei cluster HDInsight e viene usato per monitorare il cluster e apportare modifiche di configurazione.

Nella normale creazione del cluster, come descritto in altri articoli come la [configurazione di cluster in HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari viene distribuito in un [database SQL di Azure S0](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) gestito da HDInsight e non è accessibile agli utenti.

La funzionalità Custom Ambari DB consente di distribuire un nuovo cluster e di configurare Ambari in un database esterno gestito. La distribuzione viene eseguita con un modello di Azure Resource Manager. Questa funzionalità offre i vantaggi seguenti:

- Personalizzazione: è possibile scegliere le dimensioni e la capacità di elaborazione del database. Se sono presenti cluster di grandi dimensioni che elaborano carichi di lavoro intensi, un database Ambari con specifiche inferiori potrebbe diventare un collo di bottiglia per le operazioni di gestione.
- Flessibilità: è possibile ridimensionare il database in base alle esigenze.
- Controllo: è possibile gestire i backup e la protezione per il database in modo che soddisfi i requisiti dell'organizzazione.

Nella parte restante di questo articolo vengono illustrati gli aspetti seguenti:

- requisiti per l'uso della funzionalità di database Ambari personalizzato
- passaggi necessari per eseguire il provisioning di cluster HDInsight usando il proprio database esterno per Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Requisiti del database Ambari personalizzati

È possibile distribuire un database Ambari personalizzato con tutti i tipi di cluster e le versioni. Più cluster non possono usare lo stesso database Ambari.

Il database Ambari personalizzato presenta gli altri requisiti seguenti:

- Il nome del database non può contenere trattini o spazi
- È necessario disporre di un server e un database SQL di Azure esistenti.
- Il database specificato per l'installazione di Ambari deve essere vuoto. Nello schema dbo predefinito non devono essere presenti tabelle.
- L'utente utilizzato per la connessione al database deve disporre di autorizzazioni SELECT, CREATE TABLE e INSERT per il database.
- Attivare l'opzione per [consentire l'accesso ai servizi di Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) nel server in cui sarà ospitato Ambari.
- Gli indirizzi IP di gestione del servizio HDInsight devono essere consentiti nella regola del firewall. Per un elenco degli indirizzi IP che devono essere aggiunti alla regola del firewall a livello di server, vedere [indirizzi IP di gestione di HDInsight](hdinsight-management-ip-addresses.md) .

Quando si ospita il database Apache Ambari in un database esterno, tenere presente quanto segue:

- L'utente è responsabile per i costi aggiuntivi del database SQL di Azure che include Ambari.
- Eseguire periodicamente il backup del database Ambari personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il periodo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Distribuire i cluster con un database Ambari personalizzato

Per creare un cluster HDInsight che usa il proprio database Ambari esterno, usare il [modello di avvio rapido database Ambari personalizzato](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Modificare i parametri nella `azuredeploy.parameters.json` per specificare le informazioni sul nuovo cluster e sul database che conterrà Ambari.

È possibile iniziare la distribuzione usando l'interfaccia della riga di comando di Azure. Sostituire `<RESOURCEGROUPNAME>` con il gruppo di risorse in cui si vuole distribuire il cluster.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare gli archivi di metadati esterni in Azure HDInsight](hdinsight-use-external-metadata-stores.md)
