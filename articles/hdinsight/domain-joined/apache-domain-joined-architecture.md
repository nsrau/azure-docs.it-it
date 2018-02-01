---
title: Architettura di HDInsight aggiunto a un dominio di Azure | Microsoft Docs
description: Informazioni su come pianificare HDInsight aggiunto a un dominio.
services: hdinsight
documentationcenter: 
author: bhanupr
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2017
ms.author: bprakash
ms.openlocfilehash: 5285199d22528ed6b9fa3b7dbc85e382e7b28569
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Pianificare cluster Hadoop aggiunti a un dominio di Azure in HDInsight

Il cluster HDInsight standard è un cluster a utente singolo. adatto alla maggior parte delle aziende con team di piccole dimensioni che compilano carichi di lavoro di dati di grandi dimensioni. Man mano che aumenta la diffusione di Hadoop, molte aziende stanno passando a un modello in cui i cluster sono gestiti da team IT e condivisi tra più team di sviluppo. Di conseguenza, le funzionalità basate su cluster multiutente sono tra le più richieste in Azure HDInsight.

Anziché compilare autenticazione e autorizzazione multiutente proprie, HDInsight si basa sul provider di identità più diffuso, ovvero Active Directory (AD). La funzionalità di sicurezza di AD permette di gestire l'autenticazione multiutente in HDInsight. Integrando HDInsight con AD, è possibile comunicare con i cluster usando le credenziali di AD. Le macchine virtuali in HDInsight vengono aggiunte al dominio di Active Directory; in questo modo HDInsight esegue il mapping di un utente AD a un utente Hadoop locale e pertanto tutti i servizi in esecuzione in HDInsight, come Ambari, server Hive, Ranger, server Thrift Spark e altri, funzionano senza problemi per l'utente autenticato. Gli amministratori possono quindi creare criteri di autorizzazione sicuri usando Apache Ranger per fornire il controllo di accesso basato sui ruoli per le risorse in HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrare HDInsight con Active Directory

L'integrazione di HDInsight con Active Directory permette di aggiungere i nodi del cluster HDInsight a un dominio di Active Directory. La sicurezza Kerberos è configurata per i componenti Hadoop nel cluster. Per ciascun componenti Hadoop viene creata un'entità servizio in Active Directory. Viene creata anche un'entità computer corrispondente per ogni computer aggiunto al dominio. Queste entità servizio ed entità computer possono creare confusione in Active Directory. Di conseguenza, in Active Directory è necessario fornire un'unità organizzativa dove vengono inserite queste entità. 

In sintesi, è necessario configurare un ambiente con gli elementi seguenti:

- Un controller di dominio Active Directory con LDAPS configurato.
- Connettività dalla rete virtuale di HDInsight al controller di dominio Active Directory.
- Un'unità organizzativa creata in Active Directory.
- Un account del servizio che dispone delle autorizzazioni per:

    - Creare le entità servizio nell'unità organizzativa.
    - Aggiungere computer al dominio e creare entità computer nell'unità organizzativa.

Lo screenshot seguente mostra un'unità organizzativa creata in contoso.com. Nello screenshot sono visualizzate anche alcune delle entità servizio ed entità computer.

![Unità organizzativa contenente i cluster HDInsight aggiunti al dominio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="two-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Due modi per usare i propri controller di dominio Active Directory

Esistono due modi per usare controller di dominio Active Directory per creare cluster HDInsight aggiunti a un dominio. 

- **Azure Active Directory Domain Services**: questo servizio fornisce un dominio di Active Directory gestito che è completamente compatibile con Windows Server Active Directory. Microsoft si occupa della gestione, dell'applicazione di patch e del monitoraggio del dominio AD. È possibile distribuire il cluster senza doversi preoccupare di gestire i controller di dominio. Utenti, gruppi e password vengono sincronizzati da Azure Active Directory, per consentire agli utenti di accedere al cluster usando le credenziali aziendali. Per altre informazioni, vedere [Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

- **Active Directory in macchine virtuali IaaS di Azure:** in questo caso, si distribuisce e si gestisce il proprio dominio di Windows Server Active Directory in macchine virtuali IaaS di Azure. Per altre informazioni, vedere [Configurare l'ambiente sandbox aggiunto al dominio](./apache-domain-joined-configure.md).

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un cluster HDInsight aggiunto al dominio, vedere [Configurare i cluster HDInsight aggiunti al dominio](apache-domain-joined-configure.md).
* Per gestire i cluster HDInsight aggiunti a un dominio, vedere [Gestire i cluster HDInsight aggiunti al dominio](apache-domain-joined-manage.md).
* Per configurare i criteri ed eseguire query Hive, vedere [Configurare criteri Hive per cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md).
* Per eseguire query Hive usando SSH nei cluster HDInsight aggiunti al dominio, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
