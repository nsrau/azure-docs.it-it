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
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 9deb5e4dbd925c4fdc523d8d21afbcfbf85947b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Pianificare cluster Hadoop aggiunti a un dominio di Azure in HDInsight

Il cluster Hadoop tradizionale è un cluster a utente singolo, adatto alla maggior parte delle aziende con team di piccole dimensioni che compilano carichi di lavoro di dati di grandi dimensioni. Man mano che aumenta la diffusione di Hadoop, molte aziende stanno passando a un modello in cui i cluster sono gestiti da team IT e condivisi tra più team di sviluppo. Di conseguenza, le funzionalità basate su cluster multiutente sono tra le più richieste in Azure HDInsight.

Anziché compilare autenticazione e autorizzazione multiutente proprie, HDInsight si basa sul provider di identità più diffuso, ovvero Active Directory (AD). La funzionalità di sicurezza di AD permette di gestire l'autorizzazione multiutente in HDInsight. Integrando HDInsight con AD, è possibile comunicare con i cluster usando le credenziali di AD. Poiché HDInsight esegue il mapping di un utente AD a un utente Hadoop locale, tutti i servizi in esecuzione in HDInsight, come Ambari, server Hive, Ranger, server Thrift Spark e altri, funzionano senza problemi per l'utente autenticato.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrare HDInsight con Active Directory

L'integrazione di HDInsight con Active Directory permette di aggiungere i nodi del cluster HDInsight a un dominio di Active Directory. HDInsight crea le entità servizio per i servizi Hadoop in esecuzione nel cluster e le inserisce in un'unità organizzativa (OU) specificata nel dominio. HDInsight crea anche mapping con DNS inverso nel dominio per gli indirizzi IP dei nodi che vengono aggiunti al dominio.

Ci sono due opzioni di distribuzione per Active Directory:
* **[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md):** questo servizio fornisce un dominio di Active Directory gestito che è completamente compatibile con Windows Server Active Directory. Microsoft si occupa della gestione, dell'applicazione di patch e del monitoraggio del dominio AD. È possibile distribuire il cluster senza doversi preoccupare di gestire i controller di dominio. Utenti, gruppi e password vengono sincronizzati da Azure Active Directory, per consentire agli utenti di accedere al cluster usando le credenziali aziendali.

* **Dominio di Windows Server Active Directory in macchine virtuali IaaS di Azure:** in questo caso, si distribuisce e si gestisce il proprio dominio di Windows Server Active Directory in macchine virtuali IaaS di Azure. 

È possibile eseguire questa configurazione usando più architetture. È possibile scegliere tra le seguenti architetture.


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>HDInsight integrato con un dominio AD gestito di Azure AD Domain Services
È possibile distribuire un dominio gestito di [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure Active Directory Domain Services fornisce un dominio AD gestito in Azure, che viene gestito, aggiornato e monitorato da Microsoft. Crea due controller di dominio per garantire la disponibilità elevata e include i servizi DNS. È quindi possibile integrare il cluster HDInsight con questo dominio gestito. Con questa opzione di distribuzione, non è necessario preoccuparsi della gestione, dell'applicazione di patch, dell'aggiornamento e del monitoraggio dei controller di dominio.

![Topologia del cluster HDInsight aggiunto a un dominio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Prerequisiti per l'integrazione con Azure AD Domain Services:

* [Effettuare il provisioning di un dominio gestito di Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started.md).
* Creare un'[unità organizzativa](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) in cui inserire le macchine virtuali del cluster HDInsight e le entità servizio usate dal cluster.
* Configurare [LDAPS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) durante la configurazione di Azure Active Directory Domain Services. Il certificato usato per configurare LDAPS deve essere rilasciato da un'autorità di certificazione pubblica e non deve essere un certificato autofirmato.
* Creare zone con DNS inverso nel dominio gestito per l'intervallo di indirizzi IP della subnet HDInsight, ad esempio 10.2.0.0/24 nella figura precedente.
* Configurare la [sincronizzazione degli hash delle password necessari per l'autenticazione NTLM e Kerberos](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) da Azure al dominio gestito di Azure Active Directory Domain Services.
* È necessario un account del servizio o un account utente. Usare questo account per creare il cluster HDInsight. L'account deve avere le autorizzazioni seguenti:

    - Autorizzazioni per creare oggetti entità servizio e oggetti computer nell'unità organizzativa
    - Autorizzazioni per creare regole proxy per DNS inverso.
    - Autorizzazioni per aggiungere computer al dominio di Azure AD


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>HDInsight integrato con Windows Server AD in esecuzione in soluzioni IaaS Azure

È possibile distribuire il ruolo Active Directory Domain Services di Windows Server in una (o più) macchine virtuali (VM) in Azure e alzarle di livello in modo da farle diventare controller di dominio. Queste macchine virtuali controller di dominio possono essere distribuite tramite il modello di distribuzione Resource Manager nella stessa rete virtuale del cluster HDInsight. Se i controller di dominio vengono distribuiti in una rete virtuale diversa, è necessario eseguire il peering di queste reti virtuali tramite il [peering da rete virtuale a rete virtuale](../../virtual-network/virtual-network-create-peering.md). 

[Altre informazioni - Distribuzione di Windows Server Active Directory in macchine virtuali di Azure](../../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![Topologia del cluster HDInsight aggiunto a un dominio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> In questa architettura non è possibile usare Azure Data Lake Store con il cluster HDInsight.


Prerequisiti per l'integrazione con Windows Server Active Directory in macchine virtuali di Azure:

* È necessario creare un'[unità organizzativa](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) in cui inserire le macchine virtuali del cluster HDInsight e le entità servizio usate dal cluster.
* È necessario configurare [Lightweight Directory Access Protocol](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP) per comunicare con AD. Il certificato usato per configurare i protocolli LDAP deve essere un certificato reale (non un certificato autofirmato).
* È necessario creare zone con DNS inverso nel dominio per l'intervallo di indirizzi IP della subnet HDInsight, ad esempio 10.2.0.0/24 nella figura precedente.
* È necessario un account del servizio o un account utente. Usare questo account per creare il cluster HDInsight. L'account deve avere le autorizzazioni seguenti:

    - Autorizzazioni per creare oggetti entità servizio e oggetti computer nell'unità organizzativa
    - Autorizzazioni per creare regole proxy per DNS inverso.
    - Autorizzazioni per aggiungere computer al dominio di Active Directory


## <a name="next-steps"></a>Passaggi successivi
* Per configurare un cluster HDInsight aggiunto al dominio, vedere [Configurare i cluster HDInsight aggiunti al dominio](apache-domain-joined-configure.md).
* Per gestire i cluster HDInsight aggiunti a un dominio, vedere [Gestire i cluster HDInsight aggiunti al dominio](apache-domain-joined-manage.md).
* Per configurare i criteri ed eseguire query Hive, vedere [Configurare criteri Hive per cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md).
* Per eseguire query Hive usando SSH nei cluster HDInsight aggiunti al dominio, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
