---
title: Intelligence e analisi in Azure per enti pubblici | Microsoft Docs
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Intelligence e analisi in Azure per enti pubblici
Questo articolo descrive le variazioni ai servizi di intelligence e analisi e presenta alcune considerazioni sull'ambiente di Azure per enti pubblici.

## <a name="azure-hdinsight"></a>HDInsight di Azure
HDInsight è disponibile a livello generale in Azure per enti pubblici.

### <a name="variations"></a>Varianti
Le funzionalità di HDInsight seguenti non sono attualmente disponibili in Azure per enti pubblici.

* HDInsight non è disponibile in Windows.
* Azure Data Lake Store non è attualmente disponibile in Azure per enti pubblici. Archiviazione BLOB di Azure è al momento l'unica opzione di archiviazione disponibile.
* L'integrazione di Azure Active Directory Domain Services non è ancora disponibile. Per creare cluster Hadoop sicuri senza Active Directory Domain Services, selezionare uno degli scenari di configurazione seguenti:

1. HDINSIGHT INTEGRATO CON ACTIVE DIRECTORY IN ESECUZIONE IN AZURE IAAS

  Si tratta di gran lunga dell'architettura più semplice per l'integrazione di HDInsight in Active Directory. Il diagramma dell'architettura viene illlustrato di seguito. In questa architettura, si otterrà un controller di dominio di Active Directory in esecuzione su una o più macchine virtuali di Azure. In genere queste macchine virtuali si troveranno all'interno di una rete virtuale. È possibile configurare una nuova rete virtuale in cui è possibile inserire il cluster HDInsight. Per far individuare Active Directory da parte di HDInsight, è necessario eseguire il peering di queste reti virtuali usando il [peering reti virtuali] (https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). 
  
  Prerequisiti da configurare su Active Directory
     * È necessario creare un'unità organizzativa in cui si vogliono inserire le macchine virtuali del cluster HDInsight e le entità servizio usate dal cluster. 
     * LDAP deve essere configurato per la comunicazione con Active Directory. Il certificato usato per configurare LDAPS deve essere un certificato reale (non un certificato autofirmato).
     * È necessario creare zone DNS inverse sul dominio per l'intervallo di indirizzi IP della subnet HDI.
     * È necessario un account del servizio o un account utente per creare li cluster HDInsight. Questo account deve disporre delle seguenti autorizzazioni
        * Autorizzazioni per creare oggetti entità servizio e oggetti computer all'interno dell'unità organizzativa.
        * Autorizzazioni per creare regole del proxy DNS inverse.
        * Autorizzazioni per aggiungere computer al dominio di Active Directory.
        
2. HDINSIGHT INTEGRATO CON ACTIVE DIRECTORY LOCALE TRAMITE LA CONFIGURAZIONE VPN
  
  Questa architettura è simile all'architettura n. 1. L'unica differenza è che, in questo caso, Active Directory è locale e la comunicazione da HDInsight e Active Directory avviene tramite una [connessione VPN da Azure alla rete locale] (https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction). Di seguito viene riportato il diagramma dell'architettura per questa configurazione. 
  
  Prerequisiti da configurare su Active Directory locale
     * È necessario creare un'unità organizzativa in cui si vogliono inserire le macchine virtuali del cluster HDInsight e le entità servizio usate dal cluster.
     * LDAP deve essere configurato per la comunicazione con Active Directory. Il certificato usato per configurare LDAPS deve essere un certificato reale (non un certificato autofirmato).
     * È necessario creare zone DNS inverse sul dominio per l'intervallo di indirizzi IP della subnet HDI.
     * È necessario un account del servizio o un account utente per creare li cluster HDInsight. Questo account deve disporre delle seguenti autorizzazioni
        * Autorizzazioni per creare oggetti entità servizio e oggetti computer all'interno dell'unità organizzativa.
        * Autorizzazioni per creare regole del proxy DNS inverse.
        * Autorizzazioni per aggiungere computer al dominio di Active Directory.


Gli URL per Log Analytics sono diversi in Azure per enti pubblici:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Cluster HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Per altre informazioni, vedere la [documentazione pubblica di HDInsight](../hdinsight/hdinsight-hadoop-introduction.md).


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni e aggiornamenti, iscriversi al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici</a>.



<!--HONumber=Jan17_HO2-->


