---
title: Introduzione alla sicurezza Hadoop con i cluster HDInsight di Azure aggiunti al dominio
description: Informazioni su come i cluster HDInsight di Azure aggiunti al dominio supportano i quattro capisaldi della sicurezza aziendale.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 6f2c41aff8aaa389a8f2288cbb445e1ba2e7fd14
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112537"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Introduzione alla sicurezza Hadoop con i cluster HDInsight aggiunti al dominio

Fino a questo momento, Azure HDInsight ha supportato solo un unico amministratore locale degli utenti. Questo funzionava bene per i team di applicazioni o i reparti di dimensioni ridotte. Man mano che i carichi di lavoro basati su Hadoop hanno continuato ad affermarsi nel settore aziendale, l'esigenza di funzionalità di livello aziendale come autenticazione basata su Active Directory, supporto multiutente e controllo degli accessi in base al ruolo è diventata sempre più importante. 

È possibile creare un cluster HDInsight aggiunto a un dominio Active Directory. È quindi possibile configurare un elenco di dipendenti dell'azienda autorizzati a eseguire l'autenticazione tramite Azure Active Directory per accedere al cluster HDInsight. Nessun utente esterno all'azienda può accedere al cluster HDInsight. 

L'amministratore può configurare il controllo degli accessi basato sui ruoli per la sicurezza di Hive usando [Apache Ranger](http://hortonworks.com/apache/ranger/). La configurazione di RBAC limita l'accesso ai dati solo ai ruoli interessati. Infine, l'amministratore può controllare l'accesso ai dati per i dipendenti e le eventuali modifiche apportate ai criteri di controllo degli accessi. L'amministratore può quindi ottenere un elevato livello di governance delle risorse aziendali.

> [!NOTE]
> Le nuove funzionalità descritte in questo articolo sono disponibili solo in versione di anteprima nei seguenti tipi di cluster: Hadoop, Spark e Interactive Query. Oozie ora è abilitato sui cluster aggiunti al dominio. Per poter accedere all'interfaccia utente Web Oozie, gli utenti devono essere abilitati a eseguire le operazioni di [tunneling](../hdinsight-linux-ambari-ssh-tunnel.md).

La protezione aziendale si basa su quattro importanti pilastri: protezione perimetrale, autenticazione, autorizzazione e crittografia.

![Vantaggi dei cluster HDInsight di Azure aggiunti al dominio nei quattro capisaldi della sicurezza aziendale](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Protezione perimetrale
La protezione perimetrale in HDInsight si raggiunge con l'uso di reti virtuali e con il servizio gateway VPN di Azure. Un amministratore aziendale può creare un cluster HDInsight all'interno di una rete virtuale e usare gruppi di sicurezza di rete (regole del firewall) per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP definiti nelle regole del firewall in entrata potranno comunicare con il cluster HDInsight. Questa configurazione offre sicurezza perimetrale.

Un altro livello di protezione perimetrale viene garantito grazie al servizio gateway VPN. Il gateway funge da prima linea di difesa per qualsiasi richiesta in entrata nel cluster HDInsight. Accetta la richiesta, la convalida e, solo a questo punto, consente alla richiesta di passare agli altri nodi nel cluster. In questo modo, il gateway fornisce protezione perimetrale ad altri nomi e nodi di dati nel cluster.

## <a name="authentication"></a>Authentication
Un amministratore può creare un cluster HDInsight aggiunto al dominio in una [rete virtuale](https://azure.microsoft.com/services/virtual-network/). Tutti i nodi del cluster HDInsight vengono aggiunti al dominio gestito dall'azienda. Questo è possibile grazie all'uso di [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Con questa configurazione, i dipendenti dell'azienda possono accedere ai nodi del cluster usando le credenziali del dominio. Possono usare anche le credenziali del dominio per eseguire l'autenticazione con altri endpoint approvati, ad esempio visualizzazioni di Ambari, ODBC, JDBC, PowerShell e le API REST per interagire con il cluster. L'amministratore ha il pieno controllo sulla limitazione del numero di utenti che interagiscono con il cluster tramite questi endpoint.

## <a name="authorization"></a>Authorization
Una procedura consigliata seguita dalla maggior parte delle aziende è assicurarsi di limitare l'accesso a tutte le risorse aziendali da parte dei dipendenti. In modo analogo, l'amministratore può definire i criteri di controllo degli accessi in base al ruolo per le risorse del cluster. 

Ad esempio, l'amministratore può configurare [Apache Ranger](http://hortonworks.com/apache/ranger/) per impostare criteri di controllo degli accessi per Hive. Questa funzionalità garantisce che i dipendenti possano accedere solo ai dati di cui hanno bisogno per svolgere il loro lavoro in modo corretto. L'accesso SSH al cluster è limitato solo all'amministratore.

## <a name="auditing"></a>Controllo
Controllare tutti gli accessi per le risorse del cluster e i dati, è necessario per tenere traccia dell'accesso non autorizzato o non intenzionale delle risorse. È importante tanto quanto proteggere le risorse del cluster HDInsight da utenti non autorizzati e garantire la sicurezza dei dati. 

L'amministratore può visualizzare e segnalare tutti gli accessi ai dati e alle risorse del cluster HDInsight. L'amministratore può anche visualizzare e segnalare tutte le modifiche ai criteri di controllo degli accessi creati negli endpoint supportati da Apache Ranger. 

Un cluster HDInsight aggiunto al dominio usa la già nota interfaccia utente di Apache Ranger per effettuare ricerche nei log di controllo. Ranger usa [Apache Solr](http://hortonworks.com/apache/solr/) per l'archiviazione e la ricerca dei log nel back-end.

## <a name="encryption"></a>Crittografia
La protezione dei dati è importante per rispettare i requisiti di sicurezza e conformità dell'organizzazione. Oltre a limitare l'accesso ai dati da parte di dipendenti non autorizzati, è necessario crittografarli. 

Entrambi gli archivi di dati per i cluster HDInsight, il BLOB di archiviazione di Azure e Azure Data Lake Storage di 1° generazione supportano la [crittografia dei dati](../../storage/common/storage-service-encryption.md) inattivi trasparente lato server. La protezione dei cluster HDInsight sarà perfettamente compatibile con questa funzionalità di crittografia lato server dei dati inattivi.

## <a name="next-steps"></a>Passaggi successivi
* [Pianificare i cluster HDInsight aggiunti al dominio](apache-domain-joined-architecture.md)
* [Configurare i cluster HDInsight aggiunti al dominio](apache-domain-joined-configure.md)
* [Gestire i cluster HDInsight aggiunti al dominio](apache-domain-joined-manage.md)
* [Configurare criteri Hive per i cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md)
* [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

