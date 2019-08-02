---
title: 'InvalidNetworkSecurityGroupSecurityRules: la creazione del cluster non riesce in Azure HDInsight'
description: La creazione del cluster non riesce con il codice ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: 35e583fa31ee99c9d6307c01287c2c0fde021280
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700613"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules: la creazione del cluster non riesce in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il codice `InvalidNetworkSecurityGroupSecurityRules` di errore viene visualizzato con una descrizione simile a "le regole di sicurezza nel gruppo di sicurezza di rete configurato con la subnet non consentono la connettività in ingresso e/o in uscita".

## <a name="cause"></a>Causa

Probabilmente un problema con le regole del [gruppo di sicurezza di rete](../../virtual-network/virtual-network-vnet-plan-design-arm.md) in ingresso configurate per il cluster.

## <a name="resolution"></a>Risoluzione

Passare alla portale di Azure e identificare il NSG associato alla subnet in cui viene distribuito il cluster. Nella sezione **regole di sicurezza in ingresso** verificare che le regole consentano l'accesso in ingresso alla porta 443 per gli indirizzi IP indicati [qui](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
