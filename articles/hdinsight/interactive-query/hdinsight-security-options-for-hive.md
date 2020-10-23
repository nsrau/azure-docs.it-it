---
title: Opzioni di sicurezza per hive in Azure HDInsight
description: Opzioni di sicurezza per hive nei cluster standard ed ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222650"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Opzioni di sicurezza per hive in Azure HDInsight

Questo documento descrive le opzioni di sicurezza consigliate per hive in HDInsight. Queste opzioni possono essere configurate tramite Ambari.

![' Opzioni di sicurezza per hive '](./media/hdinsight-security-options-for-hive/security-options-hive.png "Opzioni di sicurezza per hive")

## <a name="hiveserver2-authentication"></a>Autenticazione HiveServer2

Per i cluster standard, l'impostazione consigliata per l'autenticazione HiveServer2 è quella predefinita, ovvero None. Per abilitare l'autenticazione, è consigliabile eseguire l'aggiornamento a un cluster [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) (Enterprise Security Package). 

Per i cluster ESP, l'autenticazione [Kerberos](https://web.mit.edu/Kerberos/) è abilitata per impostazione predefinita. I moduli di autenticazione plug-in (PAM) e gli schemi di autenticazione personalizzati non sono supportati.

## <a name="hiveserver2-authorization"></a>Autorizzazione HiveServer2

Per i cluster standard, l'impostazione predefinita è None. È possibile abilitare [SqlStdAuth (autorizzazione basata su standard SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) . L'autorizzazione tramite [Apache Ranger](https://ranger.apache.org/) non è supportata per i cluster standard. Si consiglia di eseguire l'aggiornamento a un cluster ESP per l'autorizzazione Ranger. 

Per i cluster ESP, l'autorizzazione tramite Ranger è abilitata per impostazione predefinita. 


## <a name="ssl-encryption-for-hiveserver2"></a>Crittografia SSL per HiveServer2

L'abilitazione di Hiveserver2 SSL non è consigliata per i cluster standard o ESP. Il protocollo SSL è invece abilitato sul gateway. La [crittografia in transito](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit) può essere abilitata per crittografare le comunicazioni tra i nodi del cluster mediante [IPSec (Internet Protocol Security)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Passaggi successivi
* [Panoramica dell'autenticazione HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Panoramica dell'autorizzazione di HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [Abilitazione dell'autorizzazione hive basata su standard SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger con hive](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
