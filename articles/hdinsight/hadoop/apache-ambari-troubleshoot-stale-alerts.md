---
title: Avvisi di Apache Ambari non aggiornati in Azure HDInsight
description: Discussione e analisi delle possibili cause e delle soluzioni per gli avvisi di Apache Ambari non aggiornati in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722811"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: avvisi non aggiornati di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Dall'interfaccia utente di Apache Ambari, è possibile che venga visualizzato un avviso simile all'immagine seguente:

![Esempio di avviso di Apache Ambari non aggiornato](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Gli agenti Ambari eseguono continuamente controlli di integrità per monitorare l'integrità di molte risorse. Ogni avviso è configurato per essere eseguito a intervalli di tempo predefiniti. Dopo l'esecuzione di ogni avviso, gli agenti Ambari restituiscono lo stato al server Ambari. A questo punto, se il server Ambari rileva che gli avvisi non sono stati eseguiti tempestivamente, viene attivato un "avviso Server Ambari". Esistono diversi motivi per cui un controllo di integrità potrebbe non essere eseguito all'intervallo definito:

* Quando gli host sono sottoposti a un utilizzo intensivo (CPU elevata), è possibile che l'agente Ambari non sia stato in grado di ottenere risorse di sistema sufficienti per eseguire gli avvisi in modo tempestivo.

* Il cluster è occupato a eseguire molti processi/servizi durante un carico elevato.

* Pochi host del cluster possono ospitare molti componenti e pertanto saranno necessari per eseguire molti avvisi. Se il numero di componenti è elevato, è possibile che i processi di avviso non abbiano intervalli pianificati

## <a name="resolution"></a>Risoluzione

### <a name="increase-alert-interval-time"></a>Aumenta tempo intervallo avvisi

È possibile scegliere di aumentare il valore di un singolo intervallo di avvisi in base al tempo di risposta del cluster e al relativo carico.

1. Dall'interfaccia utente di Apache Ambariri selezionare la scheda **avvisi** .
1. Selezionare il nome della definizione di avviso desiderata.
1. Nella definizione selezionare **modifica**.
1. Modificare il valore dell' **intervallo di controllo** nel modo desiderato, quindi selezionare **Salva**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Aumenta il tempo di intervallo di avviso per gli avvisi del server Ambari

1. Dall'interfaccia utente di Apache Ambariri selezionare la scheda **avvisi** .
1. Dall'elenco a discesa **gruppi** selezionare **AMBARI default**.
1. Selezionare Alert **Ambari server Alerts**.
1. Nella definizione selezionare **modifica**.
1. Modificare il valore dell' **intervallo di controllo** nel modo desiderato.
1. Modificare il valore del **moltiplicatore di intervallo** nel modo desiderato, quindi selezionare **Salva**.

### <a name="disable-and-enable-the-alert"></a>Disabilitare e abilitare l'avviso

È possibile disabilitare e quindi abilitare di nuovo l'avviso per eliminare eventuali avvisi non aggiornati.

1. Dall'interfaccia utente di Apache Ambariri selezionare la scheda **avvisi** .
1. Selezionare il nome della definizione di avviso desiderata.
1. Dalla definizione selezionare **Enabled** situato all'estrema destra.
1. Nella finestra popup di **conferma** selezionare **Conferma disabilitazione**.
1. Attendere alcuni secondi che tutti gli avvisi "istanze" visualizzati nella pagina siano cancellati.
1. Dalla definizione selezionare **Disabled (disabilitato** ) all'estrema destra.
1. Nella finestra popup di **conferma** selezionare **conferma Abilita**.

### <a name="increase-alert-grace-time"></a>Aumenta tempo di tolleranza avviso

Prima che l'agente Ambari segnali che un avviso configurato non ha superato la pianificazione, è stato applicato un tempo di tolleranza. Anche se l'avviso non ha superato l'ora pianificata ma è stato attivato entro il tempo di tolleranza dell'avviso, l'avviso non aggiornato non viene generato.

Il valore predefinito `alert_grace_period` è 5 secondi. Questa impostazione `alert_grace_period` è configurabile nella `/etc/ambari-agent/conf/ambari-agent.ini`. Per gli host da cui vengono generati gli avvisi non aggiornati a intervalli regolari, provare ad aumentare il valore 10. Quindi riavviare l'agente Ambari

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
