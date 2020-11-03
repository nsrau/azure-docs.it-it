---
title: Errore InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight
description: La creazione del cluster non riesce con il codice ErrorCode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289094"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenario: InvalidNetworkSecurityGroupSecurityRules-la creazione del cluster non riesce in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il codice di errore viene visualizzato `InvalidNetworkSecurityGroupSecurityRules` con una descrizione simile a "le regole di sicurezza nel gruppo di sicurezza di rete configurato con la subnet non consentono la connettività in ingresso e/o in uscita".

## <a name="cause"></a>Causa

Probabilmente un problema con le regole del [gruppo di sicurezza di rete](../../virtual-network/virtual-network-vnet-plan-design-arm.md) in ingresso configurate per il cluster.

## <a name="resolution"></a>Soluzione

Passare alla portale di Azure e identificare il NSG associato alla subnet in cui viene distribuito il cluster. Nella sezione **regole di sicurezza in ingresso** verificare che le regole consentano l'accesso in ingresso alla porta 443 per gli indirizzi IP indicati [qui](../control-network-traffic.md).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]