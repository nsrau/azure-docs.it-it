---
title: La creazione del cluster non riesce con errore DomainNotFound in Azure HDInsight
description: Procedure di risoluzione dei problemi e possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285567"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: la creazione del cluster non riesce con errore DomainNotFound in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

La creazione di un cluster protetto da HDI (Enterprise Security Package) non riesce con il `DomainNotFound` messaggio di errore.

## <a name="cause"></a>Causa

Impostazioni DNS non corrette.

## <a name="resolution"></a>Soluzione

Quando vengono distribuiti i cluster aggiunti a un dominio, HDI crea un nome utente e una password interni in AAD DS (per ogni cluster) e unisce tutti i nodi del cluster a questo dominio. L'aggiunta a un dominio viene eseguita usando gli strumenti di Samba. Accertarsi di aver soddisfatto i prerequisiti seguenti:

* Il nome di dominio deve essere risolto tramite DNS.
* L'indirizzo IP dei controller di dominio deve essere impostato nelle impostazioni DNS per la rete virtuale in cui viene distribuito il cluster.
* Se viene eseguito il peering della rete virtuale con la rete virtuale di AAD DS, è necessario eseguirla manualmente.
* Se si usano server d'inoltri DNS, il nome di dominio deve essere risolto correttamente all'interno della rete virtuale.
* I criteri di sicurezza (gruppi) non devono bloccare l'aggiunta al dominio.

### <a name="additional-debugging-steps"></a>Ulteriori passaggi di debug

* Distribuire una VM Windows nella stessa subnet, aggiungere il dominio al computer usando un nome utente e una password (questa operazione può essere eseguita tramite l'interfaccia utente del pannello di controllo) o

* Distribuire una macchina virtuale Ubuntu nella stessa subnet e nell'aggiunta al dominio
  * SSH nel computer
  * sudo su
  * Eseguire lo script con nome utente e password
  * Lo script effettuerà il ping, creerà i file di configurazione necessari e quindi il dominio. In caso di esito positivo, le impostazioni DNS sono valide.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]