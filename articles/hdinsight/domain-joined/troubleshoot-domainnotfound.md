---
title: Creazione cluster non riesce con errore DomainNotFound in Azure HDInsight
description: Passaggi per la risoluzione dei problemi e possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di AzureTroubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776235"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: Cluster creation fails with DomainNotFound error in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Creazione del cluster HDI Secure (Enterprise Security Package) non riesce con `DomainNotFound` messaggio di errore.

## <a name="cause"></a>Causa

Impostazioni DNS non corrette.

## <a name="resolution"></a>Risoluzione

Quando vengono distribuiti i cluster aggiunti al dominio, HDI crea un nome utente interno e una password in AAD DS (per ogni cluster) e aggiunge tutti i nodi del cluster a questo dominio. L'aggiunta al dominio viene eseguita utilizzando gli strumenti Samba. Accertarsi di aver soddisfatto i prerequisiti seguenti:

* Il nome di dominio deve essere risolto tramite DNS.
* L'indirizzo IP dei controller di dominio deve essere impostato nelle impostazioni DNS per la rete virtuale in cui viene distribuito il cluster.
* Se la rete virtuale viene sottoposta a peering con la rete virtuale di AAD DS, è necessario farlo manualmente.
* Se si utilizzano server d'inoltro DNS, il nome di dominio deve essere risolto correttamente all'interno della rete virtuale.
* I criteri di sicurezza non devono bloccare l'aggiunta al dominio.

### <a name="additional-debugging-steps"></a>Ulteriori passaggi di debug

* Distribuire una macchina virtuale windows nella stessa subnet, aggiungere un dominio al computer usando un nome utente e una password (questa operazione può essere eseguita tramite l'interfaccia utente del pannello di controllo) o

* Distribuire una macchina virtuale ubuntu nella stessa subnet e aggiungere un dominio alla macchinaDeploy a ubuntu VM in the same subnet and domain join the machine
  * SSH nella macchina
  * sudo su
  * Eseguire lo script con nome utente e password
  * Lo script eseguirà il ping, creerà i file di configurazione necessari e quindi il dominio. Se ha esito positivo, le impostazioni DNS sono buone.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
