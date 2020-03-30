---
title: Domini di errore insufficienti nell'errore di area in Azure HDInsight
description: Creazione del cluster non riuscita a causa di domini di errore insufficienti nell'area in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8be7e05ac85ce0b1b10edf18d3885a07e016b9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895021"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenario: Creazione del `not sufficient fault domains in region` cluster non riuscita a causa di Azure HDInsightScenario: Cluster creation failed due to in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Messaggio di errore `not sufficient fault domains in region` simile a quando si tenta di creare cluster Apache Kafka.

## <a name="cause"></a>Causa

Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Le macchine virtuali e i dischi gestiti che implementano i nodi in un cluster HDInsight sono distribuiti tra i domini di errore. Questa architettura limita il potenziale impatto dei guasti dell'hardware fisico.

Ogni area di Azure include un numero specifico di domini di errore. Per un elenco dei domini e il numero di domini di errore in essi contenuti, fare riferimento alla documentazione relativa ai set di [disponibilità](../../virtual-machines/windows/manage-availability.md).

In HDInsight, è necessario eseguire il provisioning dei cluster Kafka in un'area con almeno tre domini di errore.

## <a name="resolution"></a>Risoluzione

Se l'area che si desidera creare il cluster non dispone di domini di errore sufficienti, contattare il team del prodotto per consentire il provisioning del cluster anche se non sono presenti tre domini di errore.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
