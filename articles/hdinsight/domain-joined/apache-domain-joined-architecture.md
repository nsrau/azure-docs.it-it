---
title: Architettura di HDInsight aggiunto a un dominio di Azure | Microsoft Docs
description: Informazioni su come pianificare HDInsight aggiunto a un dominio.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: f4380f5d6ec379d5807f697294623a672bd270ae
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715242"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Pianificare cluster Hadoop aggiunti a un dominio di Azure in HDInsight

Il cluster HDInsight standard è un cluster a utente singolo. adatto alla maggior parte delle aziende con team di piccole dimensioni che compilano carichi di lavoro di dati di grandi dimensioni. Ogni utente può avere un cluster dedicato diverso su richiesta ed eliminarlo quando non è più necessario. Tuttavia, molte aziende stanno passando a un modello in cui i cluster sono gestiti dai team IT e condivisi tra più team delle applicazioni. Di conseguenza, è necessario l'accesso multiutente al cluster in Azure HDInsight per queste aziende di grandi dimensioni.

HDInsight si basa sul provider di identità più diffuso, Active Directory (AD), in modo gestito. Grazie all'integrazione di HDInsight con [Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md), è possibile accedere ai cluster usando le credenziali di dominio. Le macchine virtuali in HDInsight vengono aggiunte al dominio specificato, pertanto tutti i servizi in esecuzione in HDInsight, come Ambari, server Hive, Ranger, server Thrift Spark e altri, funzionano senza problemi per l'utente autenticato. Gli amministratori possono quindi creare criteri di autorizzazione sicuri usando Apache Ranger per fornire il controllo di accesso basato sui ruoli per le risorse nel cluster.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrare HDInsight con Active Directory

Hadoop open source si basa su Kerberos per fornire l'autenticazione e la sicurezza. Pertanto, i nodi del cluster HDInsight vengono aggiunti a un dominio gestito tramite AAD-DS. La sicurezza Kerberos è configurata per i componenti Hadoop nel cluster. Per ciascun componente Hadoop viene creata automaticamente un'entità servizio. Viene creata anche un'entità computer corrispondente per ogni computer aggiunto al dominio. Per poter archiviare queste entità servizio e computer, è necessario specificare un'unità organizzativa nel controller di dominio (AAD-DS) in cui posizionare queste entità. 

In sintesi, è necessario configurare un ambiente con gli elementi seguenti:

- Un dominio Active Directory (gestito tramite AAD-DS)
- Secure LDAP (LDAPS) abilitato in AAD-DS.
- Connettività di rete corretta dalla rete virtuale di HDInsight alla rete virtuale di AAD-DS, nel caso si scelga di separare queste reti virtuali. Una macchina virtuale all'interno della rete virtuale HDI deve avere visibilità su AAD-DS tramite il peering reti virtuali. Se HDI e AAD-DS sono distribuiti nella stessa rete virtuale, la connettività viene stabilita automaticamente e non sono necessarie altre operazioni.
- Un'unità organizzativa [creata in AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- Un account del servizio che dispone delle autorizzazioni per:
    - Creare le entità servizio nell'unità organizzativa.
    - Aggiungere computer al dominio e creare entità computer nell'unità organizzativa.

Lo screenshot seguente mostra un'unità organizzativa creata in contoso.com. Nello screenshot sono visualizzate anche alcune delle entità servizio ed entità computer.

![Unità organizzativa contenente i cluster HDInsight aggiunti al dominio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Configurazione diversa dei controller di dominio:
HDInsight attualmente supporta solo AAD-DS come controller di dominio principale con cui il cluster comunicherà per l'autenticazione Kerberos. Sono tuttavia possibili anche altre configurazioni complesse di AD, purché comportino l'abilitazione di AAD-DS per l'accesso HDI.

- **[Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: questo servizio fornisce un dominio gestito che è completamente compatibile con Windows Server Active Directory. Microsoft si occupa della gestione, dell'applicazione di patch e del monitoraggio del dominio AD in una configurazione a disponibilità elevata. È possibile distribuire il cluster senza doversi preoccupare di gestire i controller di dominio. Utenti, gruppi e password vengono sincronizzati da Azure Active Directory (AAD) [Sincronizzazione unidirezionale da AAD ad AAD-DS], per consentire agli utenti di accedere al cluster usando le stesse credenziali aziendali. Per altre informazioni, vedere l'articolo su [come configurare i cluster HDInsight aggiunti al dominio con AAD-DS](./apache-domain-joined-configure-using-azure-adds.md).
- **AD in locale o AD in macchine virtuali IaaS**: se si dispone di Active Directory in locale o di altre configurazioni AD più complesse per il dominio, è possibile sincronizzare le identità in AAD mediante AD Connect e quindi abilitare AAD-DS su tale tenant AD. Poiché Kerberos si basa sugli hash delle password, sarà necessario [abilitare la sincronizzazione degli hash delle password in AAD-DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Se si usa la federazione con Active Directory Federation Services (ADFS), è possibile configurare facoltativamente la sincronizzazione degli hash delle password come backup, nel caso si verifichi un errore dell'infrastruttura ADFS. Per altre informazioni, vedere l'articolo su come [abilitare la sincronizzazione degli hash delle password con la sincronizzazione di AAD Connect](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). L'uso di AD in locale o AD in macchine virtuali IaaS senza AAD e AAD-DS non è una configurazione supportata per l'aggiunta al dominio del cluster HDI.

## <a name="next-steps"></a>Passaggi successivi
* [Configurare i cluster HDInsight aggiunti al dominio](apache-domain-joined-configure-using-azure-adds.md).
* [Configurare criteri Hive per i cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md).
* [Gestire i cluster HDInsight aggiunti al dominio](apache-domain-joined-manage.md). 
