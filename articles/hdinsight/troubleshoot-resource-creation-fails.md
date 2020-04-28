---
title: Risolvere gli errori di creazione di risorse in Azure HDInsight
description: In questo articolo sono disponibili gli errori e le tecniche di mitigazione comuni relativi alla capacità.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 527d2d8cb8086ed6b5e87417e2bc80dd52aa6e63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188413"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Risolvere gli errori di creazione di risorse in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Errore: la distribuzione supererà la quota di ' 800'

Azure prevede un limite di quota di 800 distribuzioni per gruppo di risorse. Si applicano quote diverse per gruppo di risorse, sottoscrizione, account o per altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Il tentativo di distribuire una macchina virtuale con un numero di core superiore a quello consentito restituisce un messaggio di errore che informa che la quota è stata superata.

Per risolvere questo problema, eliminare le distribuzioni che non sono più necessarie usando il portale di Azure, l'interfaccia della riga di comando o PowerShell.

Per altre informazioni, vedere [Risolvere gli errori delle quote di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Errore: il nodo massimo supera i core disponibili in questa area

La sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Il tentativo di distribuire una risorsa con un numero di core superiore a quello consentito restituisce un messaggio di errore che informa che la quota è stata superata.

Per richiedere un aumento delle quote, seguire questi passaggi:

1. Passare alla [portale di Azure](https://portal.azure.com)e selezionare Guida e **supporto**.

1. Selezionare **Nuova richiesta di supporto**.

1. Nella scheda informazioni di **base** della pagina **nuova richiesta di supporto** specificare le informazioni seguenti:

   * **Tipo di problema:** Selezionare i **limiti del servizio e della sottoscrizione (quote)**.
   * **Sottoscrizione:** Selezionare la sottoscrizione che si desidera modificare.
   * **Tipo di quota:** Selezionare **HDInsight**.

Per altre informazioni, vedere [Creare un ticket di supporto per aumentare i core](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
