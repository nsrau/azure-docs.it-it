---
title: Apache Ambari stale alerts in Azure HDInsight
description: Discussione e analisi dei possibili motivi e soluzioni per gli avvisi apache Ambari stantii in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539111"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenario: Apache Ambari stale alerts in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Nell'interfaccia utente di Apache Ambari, potresti visualizzare un avviso simile al seguente:

![Esempio di avviso stantio Apache Ambari](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Gli agenti Ambari monitorano continuamente lo stato di salute di molte risorse. *Gli avvisi* possono essere configurati per notificare se specifiche proprietà del cluster rientrano in soglie predeterminate. Dopo l'esecuzione di ogni controllo delle risorse, se la condizione di avviso viene soddisfatta, gli agenti Ambari segnalano lo stato al server Ambari e attivano un avviso. Se un avviso non viene controllato in base all'intervallo nel profilo di avviso, il server attiva un avviso di *avvisi non aggiornati di Ambari Server.*

Esistono vari motivi per cui un controllo di integrità potrebbe non essere eseguito all'intervallo definito:There are various reasons why a health check might not run at its defined interval:

* Gli host sono in uso intenso (utilizzo elevato della CPU), in modo che l'agente Ambari non può ottenere risorse di sistema sufficienti per eseguire gli avvisi in tempo.

* Il cluster è occupato nell'esecuzione di molti processi o servizi durante un periodo di carico elevato.

* Un numero ridotto di host nel cluster ospita molti componenti e pertanto è necessario per eseguire molti avvisi. Se il numero di componenti è elevato, i processi di avviso potrebbero perdere gli intervalli pianificati.

## <a name="resolution"></a>Risoluzione

Provare i seguenti metodi per risolvere i problemi con gli avvisi non aggiornati Ambari.

### <a name="increase-the-alert-interval-time"></a>Aumentare il tempo dell'intervallo di avviso

È possibile aumentare il valore di un singolo intervallo di avvisi, in base al tempo di risposta e al carico del cluster:You can increase the value of a individual alert interval, based on your cluster's response time and load:

1. Nell'interfaccia utente di Apache Ambari, selezionare la scheda **Avvisi.**
1. Selezionare il nome della definizione di avviso desiderato.
1. Dalla definizione, selezionare **Modifica**.
1. Aumentare il valore **Intervallo** di controllo e quindi selezionare **Salva**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Aumentare l'intervallo di avviso per gli avvisi di Ambari Server

1. Nell'interfaccia utente di Apache Ambari, selezionare la scheda **Avvisi.**
1. Nell'elenco a discesa **Gruppi** selezionare **Predefinito AMBARI**.
1. Selezionare l'avviso **Avvisi server Ambari.**
1. Dalla definizione, selezionare **Modifica**.
1. Aumentare il valore **Intervallo assegni.**
1. Aumentare il valore **di Interval Multiplier** e quindi selezionare **Salva**.

### <a name="disable-and-reenable-the-alert"></a>Disabilitare e riattivare l'avviso

Per eliminare un avviso non aggiornato, disabilitarlo e quindi riabilitarlo:

1. Nell'interfaccia utente di Apache Ambari, selezionare la scheda **Avvisi.**
1. Selezionare il nome della definizione di avviso desiderato.
1. Nella definizione selezionare **Abilitato** nella parte all'estrema destra dell'interfaccia utente.
1. Nella finestra popup **Conferma,** selezionare **Conferma disabilitazione**.
1. Attendere alcuni secondi per la cancellazione di tutte le "istanze" di avviso visualizzate nella pagina.
1. Nella definizione selezionare **Disabilitato** nella parte all'estrema destra dell'interfaccia utente.
1. Nella finestra popup **Conferma,** selezionare **Conferma Abilita**.

### <a name="increase-the-alert-grace-period"></a>Aumentare il periodo di tolleranza dell'avviso

C'è un periodo di tolleranza prima che un agente Ambari segnala che un avviso configurato ha mancato la pianificazione. Se l'avviso ha perso l'ora pianificata ma è stato eseguito entro il periodo di tolleranza, l'avviso non aggiornato non viene generato.

Il `alert_grace_period` valore predefinito è 5 secondi. È possibile configurare questa impostazione in /etc/ambari-agent/conf/ambari-agent.ini. Per gli host per i quali gli avvisi non aggiornati si verificano a intervalli regolari, provare ad aumentare il valore a 10. Quindi, riavviare l'agente Ambari.

## <a name="next-steps"></a>Passaggi successivi

Se il tuo problema non è stato menzionato qui o non sei in grado di risolverlo, visita uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure presso il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con su Twitter. Questo è l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connette la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Per arrivarci, selezionare Guida (**?**) dal menu del portale o aprire il riquadro **Guida e supporto** tecnico. Per altre informazioni, vedere Come creare una richiesta di supporto di Azure.For more information, see [How to create an Azure support request.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 

  Il supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure.Support for subscription management and billing is included with your Microsoft Azure subscription. Il supporto tecnico è disponibile tramite i piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
