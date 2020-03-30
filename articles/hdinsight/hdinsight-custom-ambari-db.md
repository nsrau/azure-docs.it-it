---
title: Database Apache Ambari personalizzato in Azure HDInsightCustom Apache Ambari database on Azure HDInsight
description: Informazioni su come creare cluster HDInsight con il proprio database Apache Ambari personalizzato.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240164"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configurare i cluster HDInsight con un database Ambari personalizzatoSet up HDInsight clusters with a custom Ambari DB

Apache Ambari semplifica la gestione e il monitoraggio di un cluster Apache Hadoop. Ambari offre un'interfaccia utente Web e un'API REST facili da usare. Ambari è incluso nei cluster HDInsight e viene usato per monitorare il cluster e apportare modifiche alla configurazione.

Nella normale creazione di cluster, come descritto in altri articoli, ad esempio [Set up clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari viene distribuito in un database SQL di [Azure S0](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) gestito da HDInsight e non accessibile agli utenti.

La funzionalità personalizzata Di ambari DB consente di distribuire un nuovo cluster e di configurare Ambari in un database esterno gestito. La distribuzione viene eseguita con un modello di Azure Resource Manager.The deployment is done with an Azure Resource Manager template. Questa funzionalità presenta i vantaggi seguenti:This feature has the following benefits:

- Personalizzazione: è possibile scegliere le dimensioni e la capacità di elaborazione del database. Se si dispone di cluster di grandi dimensioni che elaborano carichi di lavoro intensivi, un database Ambari con specifiche inferiori potrebbe diventare un collo di bottiglia per le operazioni di gestione.
- Flessibilità: è possibile ridimensionare il database in base alle esigenze.
- Controllo: è possibile gestire i backup e la sicurezza del database in modo conforme ai requisiti dell'organizzazione.

Nella parte restante di questo articolo vengono illustrati i punti seguenti:

- requisiti per utilizzare la funzione personalizzata Ambari DB
- i passaggi necessari per eseguire il provisioning dei cluster HDInsight usando il proprio database esterno per Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Requisiti personalizzati di Ambari DB

È possibile distribuire un database Ambari personalizzato con tutti i tipi di cluster e le versioni. Più cluster non possono utilizzare lo stesso DB Ambari.

Il DB Ambari personalizzato ha i seguenti altri requisiti:

- È necessario disporre di un database e un database SQL di Azure esistenti.
- Il database fornito per l'installazione di Ambari deve essere vuoto. Non devono essere presenti tabelle nello schema dbo predefinito.
- L'utente utilizzato per connettersi al database deve disporre delle autorizzazioni SELECT, CREATE TABLE e INSERT per il database.
- Attivare l'opzione [Consenti accesso ai servizi](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) di Azure nel server SQL di Azure in cui verrà ospitato Ambari.Turn on the option to Allow access to Azure services on the Azure SQL server where you will host Ambari.
- Gli indirizzi IP di gestione dal servizio HDInsight devono essere consentiti in SQL Server.Management IP addresses from HDInsight service need to be allowed in the SQL Server. Vedere Indirizzi IP di gestione HDInsight per un elenco degli indirizzi IP che devono essere aggiunti al firewall di SQL Server.See [HDInsight management IP addresses](hdinsight-management-ip-addresses.md) for a list of the IP addresses that must be added to the SQL server firewall.

Quando si ospita il database Apache Ambari in un database esterno, ricordare i seguenti punti:

- L'utente è responsabile dei costi aggiuntivi del database SQL di Azure che contiene Ambari.
- Eseguire periodicamente il backup dell'Ambari DB personalizzato. Il database SQL di Azure genera automaticamente i backup, ma il tempo di conservazione dei backup varia. Per altre informazioni, vedere [Informazioni sui backup automatici del database SQL](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Distribuire cluster con un database Ambari personalizzatoDeploy clusters with a custom Ambari DB

Per creare un cluster HDInsight che utilizza il proprio database Ambari esterno, utilizzare il [modello di guida introduttiva Ambari DB personalizzato.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)

Modificare i parametri `azuredeploy.parameters.json` in per specificare le informazioni sul nuovo cluster e sul database che conterrà Ambari.

È possibile iniziare la distribuzione usando l'interfaccia della riga di comando di Azure.You can begin the deployment using the Azure CLI. Sostituire `<RESOURCEGROUPNAME>` con il gruppo di risorse in cui si vuole distribuire il cluster.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare gli archivi di metadati esterni in Azure HDInsight](hdinsight-use-external-metadata-stores.md)