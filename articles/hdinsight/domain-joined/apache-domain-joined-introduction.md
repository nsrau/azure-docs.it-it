---
title: Introduzione alla sicurezza Apache Hadoop con Enterprise Security Package
description: Informazioni su come Enterprise Security Package supporta i quattro capisaldi della sicurezza aziendale.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b120d50b6b0f72b5977d238866cfdf26fd9be5ff
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436892"
---
# <a name="an-introduction-to-apache-hadoop-security-with-enterprise-security-package"></a>Introduzione alla sicurezza Apache Hadoop con Enterprise Security Package

Fino a questo momento, Azure HDInsight ha supportato solo un unico amministratore locale degli utenti. Questo funzionava bene per i team di applicazioni o i reparti di dimensioni ridotte. Man mano che i carichi di lavoro basati su Apache Hadoop hanno continuato ad affermarsi nel settore aziendale, l'esigenza di funzionalità di livello aziendale come autenticazione basata su Active Directory, supporto multiutente e controllo degli accessi in base al ruolo è diventata sempre più importante. 

È possibile creare un cluster HDInsight con Enterprise Security Package (ESP) aggiunto a un dominio Active Directory. È quindi possibile configurare un elenco di dipendenti dell'azienda autorizzati a eseguire l'autenticazione tramite Azure Active Directory per accedere al cluster HDInsight. Nessun utente esterno all'azienda può accedere al cluster HDInsight. 

L'amministratore può configurare il controllo degli accessi basato sui ruoli per la sicurezza di Apache Hive usando [Apache Ranger](https://hortonworks.com/apache/ranger/). La configurazione di RBAC limita l'accesso ai dati solo ai ruoli interessati. Infine, l'amministratore può controllare l'accesso ai dati per i dipendenti e le eventuali modifiche apportate ai criteri di controllo degli accessi. L'amministratore può quindi ottenere un elevato livello di governance delle risorse aziendali.

> [!NOTE]  
> Apache Oozie ora è abilitato sui cluster ESP. Per poter accedere all'interfaccia utente Web Oozie, gli utenti devono essere abilitati a eseguire le operazioni di [tunneling](../hdinsight-linux-ambari-ssh-tunnel.md).

La protezione aziendale si basa su quattro importanti pilastri: protezione perimetrale, autenticazione, autorizzazione e crittografia.

![Vantaggi dei cluster HDInsight Enterprise Security Package nei quattro capisaldi della sicurezza aziendale](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Protezione perimetrale
La protezione perimetrale in HDInsight si raggiunge con l'uso di reti virtuali e con il servizio gateway VPN di Azure. Un amministratore aziendale può creare un cluster ESP all'interno di una rete virtuale e usare gruppi di sicurezza di rete (regole del firewall) per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP definiti nelle regole del firewall in entrata potranno comunicare con il cluster HDInsight. Questa configurazione offre sicurezza perimetrale.

Un altro livello di protezione perimetrale viene garantito grazie al servizio gateway VPN. Il gateway funge da prima linea di difesa per qualsiasi richiesta in entrata nel cluster HDInsight. Accetta la richiesta, la convalida e, solo a questo punto, consente alla richiesta di passare agli altri nodi nel cluster. In questo modo, il gateway fornisce protezione perimetrale ad altri nomi e nodi di dati nel cluster.

## <a name="authentication"></a>Authentication
Un amministratore può creare un cluster HDInsight con ESP in una [rete virtuale](https://azure.microsoft.com/services/virtual-network/). Tutti i nodi del cluster HDInsight vengono aggiunti al dominio gestito dall'azienda. Questo è possibile grazie all'uso di [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Con questa configurazione, i dipendenti dell'azienda possono accedere ai nodi del cluster usando le credenziali del dominio. Possono usare le credenziali del dominio anche per eseguire l'autenticazione con altri endpoint approvati, ad esempio visualizzazioni di Apache Ambari, ODBC, JDBC, PowerShell e API REST per l'interazione con il cluster. L'amministratore ha il pieno controllo sulla limitazione del numero di utenti che interagiscono con il cluster tramite questi endpoint.

## <a name="authorization"></a>Authorization
Una procedura consigliata seguita dalla maggior parte delle aziende è assicurarsi di limitare l'accesso a tutte le risorse aziendali da parte dei dipendenti. In modo analogo, l'amministratore può definire i criteri di controllo degli accessi in base al ruolo per le risorse del cluster. 

Ad esempio, l'amministratore può configurare [Apache Ranger](https://hortonworks.com/apache/ranger/) per impostare criteri di controllo degli accessi per Hive. Questa funzionalità garantisce che i dipendenti possano accedere solo ai dati di cui hanno bisogno per svolgere il loro lavoro in modo corretto. L'accesso SSH al cluster è limitato solo all'amministratore.

## <a name="auditing"></a>Controllo
Controllare tutti gli accessi per le risorse del cluster e i dati, è necessario per tenere traccia dell'accesso non autorizzato o non intenzionale delle risorse. È importante tanto quanto proteggere le risorse del cluster HDInsight da utenti non autorizzati e garantire la sicurezza dei dati. 

L'amministratore può visualizzare e segnalare tutti gli accessi ai dati e alle risorse del cluster HDInsight. L'amministratore può anche visualizzare e segnalare tutte le modifiche ai criteri di controllo degli accessi creati negli endpoint supportati da Apache Ranger. 

Un cluster HDInsight con ESP usa la già nota interfaccia utente di Apache Ranger per effettuare ricerche nei log di controllo. Ranger usa [Apache Solr](https://hortonworks.com/apache/solr/) per l'archiviazione e la ricerca dei log nel back-end.

## <a name="encryption"></a>Crittografia
La protezione dei dati è importante per rispettare i requisiti di sicurezza e conformità dell'organizzazione. Oltre a limitare l'accesso ai dati da parte di dipendenti non autorizzati, è necessario crittografarli. 

Entrambi gli archivi di dati per i cluster HDInsight, il BLOB di archiviazione di Azure e Azure Data Lake Storage di 1° generazione supportano la [crittografia dei dati](../../storage/common/storage-service-encryption.md) inattivi trasparente lato server. La protezione dei cluster HDInsight sarà perfettamente compatibile con questa funzionalità di crittografia lato server dei dati inattivi.

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare i cluster HDInsight con ESP](apache-domain-joined-architecture.md)
* [Configurare i cluster HDInsight con ESP](apache-domain-joined-configure.md)
* [Gestire i cluster HDInsight con ESP](apache-domain-joined-manage.md)
* [Configurare i criteri Apache Hive per i cluster HDInsight con ESP](apache-domain-joined-run-hive.md)
* [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

