---
title: Avvisi di Apache Ambari non aggiornati in Azure HDInsight
description: Discussione e analisi delle possibili cause e delle soluzioni per gli avvisi non aggiornati di Apache Ambari in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77539111"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: avvisi non aggiornati di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Nell'interfaccia utente di Apache Ambari potrebbe essere visualizzato un avviso simile al seguente:

![Esempio di avviso di Apache Ambari non aggiornato](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Gli agenti Ambari monitorano costantemente l'integrità di molte risorse. Gli *avvisi* possono essere configurati per notificare se le proprietà specifiche del cluster rientrano in soglie predeterminate. Dopo l'esecuzione di ogni controllo delle risorse, se viene soddisfatta la condizione di avviso, gli agenti Ambari segnalano lo stato al server Ambari e attivano un avviso. Se un avviso non viene controllato in base all'intervallo nel profilo di avviso, il server attiva un avviso di *avvisi non aggiornati del server Ambari* .

Esistono diversi motivi per cui un controllo di integrità potrebbe non essere eseguito all'intervallo definito:

* Gli host sono sottoposti a un utilizzo intensivo (utilizzo elevato della CPU), in modo che l'agente Ambari non possa ottenere risorse di sistema sufficienti per eseguire gli avvisi in tempo.

* Il cluster è occupato a eseguire molti processi o servizi durante un periodo di carico eccessivo.

* Un numero ridotto di host nel cluster ospita molti componenti, quindi è necessario eseguire molti avvisi. Se il numero di componenti è elevato, è possibile che i processi di avviso non abbiano intervalli pianificati.

## <a name="resolution"></a>Soluzione

Provare a usare i metodi seguenti per risolvere i problemi relativi agli avvisi Ambari non aggiornati.

### <a name="increase-the-alert-interval-time"></a>Aumentare l'intervallo di tempo di avviso

È possibile aumentare il valore di un singolo intervallo di avvisi in base al tempo di risposta e al carico del cluster:

1. Nell'interfaccia utente di Apache Ambariri selezionare la scheda **avvisi** .
1. Selezionare il nome della definizione di avviso desiderata.
1. Nella definizione selezionare **modifica**.
1. Aumentare il valore di **intervallo di controllo** e quindi selezionare **Salva**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Aumentare l'intervallo di tempo di avviso per gli avvisi del server Ambari

1. Nell'interfaccia utente di Apache Ambariri selezionare la scheda **avvisi** .
1. Dall'elenco a discesa **gruppi** selezionare **AMBARI default**.
1. Selezionare l'avviso **avvisi server Ambari** .
1. Nella definizione selezionare **modifica**.
1. Aumentare il valore dell' **intervallo di controllo** .
1. Aumentare il valore del **moltiplicatore intervallo** , quindi selezionare **Salva**.

### <a name="disable-and-reenable-the-alert"></a>Disabilitare e riabilitare l'avviso

Per eliminare un avviso non aggiornato, disabilitare e quindi riabilitarlo:

1. Nell'interfaccia utente di Apache Ambariri selezionare la scheda **avvisi** .
1. Selezionare il nome della definizione di avviso desiderata.
1. Nella definizione selezionare **abilitato** nella parte più a destra dell'interfaccia utente.
1. Nella finestra popup di **conferma** selezionare **Conferma disabilitazione**.
1. Attendere alcuni secondi per la cancellazione di tutte le "istanze" dell'avviso visualizzate nella pagina.
1. Dalla definizione selezionare **disabilitato** nella parte più a destra dell'interfaccia utente.
1. Nella finestra popup di **conferma** selezionare **conferma Abilita**.

### <a name="increase-the-alert-grace-period"></a>Aumentare il periodo di tolleranza dell'avviso

Si verifica un periodo di tolleranza prima che un agente Ambari segnali che un avviso configurato ha perso la pianificazione. Se l'avviso non ha superato l'orario pianificato ma è stato eseguito entro il periodo di tolleranza, l'avviso non aggiornato non viene generato.

Il `alert_grace_period` valore predefinito è 5 secondi. È possibile configurare questa impostazione in/etc/Ambari-Agent/conf/ambari-agent.ini. Per gli host in cui si verificano gli avvisi non aggiornati a intervalli regolari, provare ad aumentare il valore di 10. Quindi, riavviare l'agente Ambari.

## <a name="next-steps"></a>Passaggi successivi

Se il problema non è stato specificato o non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore supporto:

* Ottieni risposte dagli esperti di Azure al [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) su Twitter. Si tratta dell'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti. Connette la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Per ottenere il supporto, selezionare **? (?**) dal menu del portale o aprire il riquadro **Guida e supporto** . Per altre informazioni, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). 

  Il supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure. Il supporto tecnico è disponibile tramite i [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
