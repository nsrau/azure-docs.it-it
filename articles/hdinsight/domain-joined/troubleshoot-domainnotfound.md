---
title: La creazione del cluster non riesce con errore DomainNotFound in Azure HDInsight
description: Procedure di risoluzione dei problemi e possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76776235"
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

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
