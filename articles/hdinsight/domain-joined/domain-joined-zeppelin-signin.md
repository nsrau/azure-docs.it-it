---
title: Non è possibile accedere a Zeppelin in Azure HDInsight
description: Non è possibile accedere a Zeppelin in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977404"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scenario: Non è possibile accedere ad Apache Zeppelin in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Non è possibile accedere ad Apache Zeppelin dopo aver modificato la password di aggiunta in Active Directory.

## <a name="cause"></a>Causa

L'utente indicato nella `activeDirectoryRealm.systemUsername` `shiro_ini` del file ha modificato la password di Active Directory.

## <a name="resolution"></a>Risoluzione

1. Verificare che la password modificata sia la causa radice includendo `activeDirectoryRealm.systemPassword = <new password>` nella configurazione Zeppelin `shiro_ini` in Ambari. Rimuovere l' `activeDirectoryRealm.hadoopSecurityCredentialPath` impostazione. Di seguito è riportato `shiro ini`il percorso di.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Se gli utenti possono ora accedere a Zeppelin dopo il passaggio 1, creare un `jceks` nuovo file con la nuova password e `activeDirectoryRealm.hadoopSecurityCredentialPath` sostituire con il nuovo file.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
