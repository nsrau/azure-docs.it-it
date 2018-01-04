---
title: Architettura di HDInsight aggiunto a un dominio di Azure | Microsoft Docs
description: Informazioni su come pianificare HDInsight aggiunto a un dominio.
services: hdinsight
documentationcenter: 
author: saurinsh
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
ms.author: saurinsh
ms.openlocfilehash: eca019fa5e7866ed6281e8cfee105ba1d99249bc
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Pianificare cluster Hadoop aggiunti a un dominio di Azure in HDInsight

Il cluster HDInsight standard è un utente singolo. adatto alla maggior parte delle aziende con team di piccole dimensioni che compilano carichi di lavoro di dati di grandi dimensioni. Come Hadoop ottenuto popolarità, molte aziende avviato lo spostamento verso un modello in cui i cluster sono gestiti da team IT e applicazione di più team i cluster di condivisione. Di conseguenza, le funzionalità basate su cluster multiutente sono tra le più richieste in Azure HDInsight.

Anziché compilare autenticazione e autorizzazione multiutente proprie, HDInsight si basa sul provider di identità più diffuso, ovvero Active Directory (AD). La funzionalità di sicurezza in Active Directory è utilizzabile per gestire l'autenticazione multiutente in HDInsight. Integrando HDInsight con AD, è possibile comunicare con i cluster usando le credenziali di AD. Le macchine virtuali in HDInsight appartengono a un dominio di Active Directory, ovvero come HDInsight esegue il mapping di un utente di Active Directory a un utente di Hadoop locale, pertanto tutti i servizi in esecuzione in HDInsight (Ambari, Hive Spark thrift di server, cane, server e altri) funzionano senza problemi per l'utente autenticato. Gli amministratori possono quindi creare criteri di autorizzazione sicuro usando Apache cane per fornire il controllo di accesso basato sui ruoli per le risorse in HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrare HDInsight con Active Directory

Quando si integra HDInsight con Active Directory, i nodi del cluster HDInsight vengono aggiunti a un dominio a un dominio di Active Directory. Sicurezza di Kerberos è configurata per i componenti di Hadoop nel cluster. Per ognuno dei componenti Hadoop, viene creata un'entità servizio in Active Directory. Un computer corrispondente dell'entità viene creato anche per ogni computer appartenente al dominio. Queste entità servizio e le entità di computer possono creare confusione di Active Directory. Di conseguenza, è necessario fornire un'unità organizzativa (OU) in Active Directory, in cui vengono inserite queste entità. 

Per riepilogare, è necessario impostare un ambiente con:

- Un controller di dominio Active Directory con LDAPS configurato.
- Connettività di rete virtuale di HDInsight al controller di dominio Active Directory.
- Un'unità organizzativa creato in Active Directory.
- Un account del servizio che dispone delle autorizzazioni per:

    - Creare le entità servizio nell'unità Organizzativa.
    - Aggiungere macchine al dominio e creare le entità di computer nell'unità Organizzativa.

La schermata seguente mostra un'unità Organizzativa creata in contoso.com. Alcune delle entità servizio e le entità di computer vengono visualizzati nella schermata anche.

![Unità organizzativa cluster di HDInsight aggiunti a un dominio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="three-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Tre modi per riportare il proprio controller di dominio Active Directory

Esistono tre modi per poter connettere il controller di dominio Active Directory per creare cluster HDInsight appartenenti a un dominio. 

- **Azure Active Directory Domain Services**: il servizio fornisce un dominio di Active Directory gestito, è completamente compatibile con Windows Server Active Directory. Microsoft si occupa della gestione, dell'applicazione di patch e del monitoraggio del dominio AD. È possibile distribuire il cluster senza doversi preoccupare di gestire i controller di dominio. Gli utenti, gruppi e password vengono sincronizzate da Azure Active Directory, consentendo agli utenti di accedere al cluster usando le credenziali aziendali. Per ulteriori informazioni, vedere [HDInsight configurare dominio cluster tramite Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

- **Active Directory in macchine virtuali IaaS di Azure**: In questo caso, distribuire e gestire il proprio dominio di Windows Server Active Directory in macchine virtuali IaaS di Azure. Per ulteriori informazioni, vedere [ambiente sandbox aggiunti a un dominio di Configura](./apache-domain-joined-configure.md).

- **Active Directory locale**: questa opzione, HDInsight si integra con il controller di dominio Active Directory locale.


## <a name="next-steps"></a>Passaggi successivi
* Per configurare un cluster HDInsight aggiunto al dominio, vedere [Configurare i cluster HDInsight aggiunti al dominio](apache-domain-joined-configure.md).
* Per gestire i cluster HDInsight aggiunti a un dominio, vedere [Gestire i cluster HDInsight aggiunti al dominio](apache-domain-joined-manage.md).
* Per configurare i criteri ed eseguire query Hive, vedere [Configurare criteri Hive per cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md).
* Per eseguire query Hive usando SSH nei cluster HDInsight aggiunti al dominio, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
