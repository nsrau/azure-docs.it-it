---
title: Monitorare lo stato della replica di Active Directory con Monitoraggio di Azure | Microsoft Docs
description: Il pacchetto della soluzione Stato replica di Active Directory controlla periodicamente l'ambiente Active Directory per rilevare eventuali errori di replica.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: f7bbde98c6ef35021cc03b2646193d3601ca1cff
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425849"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitorare lo stato della replica di Active Directory con Monitoraggio di Azure

![Simbolo di Stato replica di AD](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory è un componente chiave di un ambiente IT aziendale. Per garantire disponibilità e prestazioni elevate, ogni controller di dominio ha la propria copia del database di Active Directory. I controller di dominio si replicano tra loro per propagare le modifiche all'interno dell'azienda. Gli errori in questo processo di replica possono causare una serie di problemi all'interno dell'azienda.

Il pacchetto della soluzione Stato replica di Active Directory controlla periodicamente l'ambiente Active Directory per rilevare eventuali errori di replica.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

### <a name="install-agents-on-domain-controllers"></a>Installare agenti nei controller di dominio
È necessario installare agenti nei controller di dominio membri del dominio da valutare. In alternativa, è necessario installare gli agenti nei server membri e configurare gli agenti per inviare dati di replica di Active Directory a Monitoraggio di Azure. Per informazioni su come connettere i computer Windows a Monitoraggio di Azure, vedere [Connettere computer Windows a Monitoraggio di Azure](../../azure-monitor/platform/agent-windows.md). Se il controller di dominio fa già parte di un ambiente System Center Operations Manager esistente che si intende connettere a Monitoraggio di Azure, vedere [Connettere Operations Manager a Monitoraggio di Azure](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Abilitare controller non di dominio
Se non si intende connettere i controller di dominio direttamente a Monitoraggio di Azure, è possibile usare qualsiasi altro computer del dominio connesso a Monitoraggio di Azure per raccogliere i dati per il pacchetto della soluzione Stato replica di Active Directory e abilitarlo per l'invio dei dati.

1. Verificare che il computer sia membro del dominio da monitorare con la soluzione Stato replica di Active Directory.
2. [Connettere il computer Windows a Monitoraggio di Azure](../../azure-monitor/platform/om-agents.md) oppure [connetterlo usando l'ambiente Operations Manager esistente a Monitoraggio di Azure](../../azure-monitor/platform/om-agents.md), se non è già connesso.
3. Nel computer impostare la chiave del Registro di sistema seguente:<br>Chiave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>Valore: **IsTarget**<br>Dati valore: **true**

   > [!NOTE]
   > Queste modifiche non diventano effettive solo dopo il riavvio del servizio Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Installare soluzione
   > Seguire il processo descritto in [Installare una soluzione di monitoraggio](solutions.md#install-a-monitoring-solution) per aggiungere la soluzione **Stato replica di Active Directory** all'area di lavoro di Log Analytics. Non è richiesta alcuna ulteriore configurazione.


## <a name="ad-replication-status-data-collection-details"></a>Dettagli sulla raccolta dati di Stato replica di Active Directory
La tabella seguente descrive i metodi di raccolta dati e altri dettagli sul modo in cui vengono raccolti i dati per Stato replica di Active Directory.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |ogni cinque giorni |



## <a name="understanding-replication-errors"></a>Informazioni sugli errori di replica

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Il riquadro Stato replica di AD mostra il numero di errori di replica presenti. Gli **errori di replica critici** sono errori che si trovano almeno al 75% della [durata di rimozione definitiva](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) della foresta Active Directory.

![Riquadro Stato replica di Active Directory](./media/ad-replication-status/oms-ad-replication-tile.png)

Facendo clic sul riquadro è possibile visualizzare altre informazioni sugli errori.
![Dashboard Stato replica di Active Directory](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stato del server di destinazione e stato del server di origine
Queste colonne indicano lo stato dei server di destinazione e dei server di origine in cui si verificano errori di replica. Il numero accanto al nome di ogni controller di dominio indica il numero di errori di replica presenti.

Gli errori vengono visualizzati sia per i server di destinazione, sia per i server di origine perché alcuni problemi sono più facili da risolvere dalla prospettiva del server di origine e altri dalla prospettiva del server di destinazione.

In questo esempio si noterà che molti server di destinazione mostrano approssimativamente lo stesso numero di errori, ma uno dei server di origine (ADDC35) presenta più errori di tutti gli altri. È probabile che un problema nel server ADDC35 impedisca l'invio dei dati ai partner di replica. Con la correzione dei problemi in ADDC35 si potrebbero risolvere molti degli errori visualizzati nell'area dei server di destinazione.

### <a name="replication-error-types"></a>Tipi di errori di replica
Quest'area mostra informazioni sui tipi di errori rilevati nell'azienda. Ogni errore ha un codice numerico univoco e un messaggio che consentono di determinare la causa radice dell'errore.

L'anello nella parte superiore dà un'idea degli errori più e meno frequenti nell'ambiente.

Mostra quando più controller di dominio presentano lo stesso errore di replica. In questo caso, una soluzione individuata o identificata per un controller di dominio potrà essere riprodotta in altri controller di dominio interessati dallo stesso errore.

### <a name="tombstone-lifetime"></a>durata di rimozione definitiva
La durata di rimozione definitiva determina per quanto tempo un oggetto eliminato viene conservato nel database di Active Directory. Quando un oggetto eliminato supera la durata di rimozione definitiva, un processo di garbage collection lo rimuove automaticamente dal database di Active Directory.

La durata di rimozione definitiva predefinita è di 180 giorni per le versioni più recenti di Windows, mentre è di 60 giorni per le versioni precedenti e può essere modificata in modo esplicito da un amministratore di Active Directory.

È importante sapere se sono presenti errori di replica che stanno per raggiungere o hanno superato la durata di rimozione definitiva. Se due controller di dominio presentano un errore di replica che persiste oltre la durata di rimozione definitiva, la replica viene disabilitata tra i due controller di dominio, anche se viene risolto l'errore di replica sottostante.

L'area della durata di rimozione definitiva consente di identificare le aree in cui la replica disabilitata potrebbe incorrere in questo rischio. Ogni errore della categoria **Over 100% TSL** rappresenta una partizione che non è stata replicata tra server di origine e di destinazione per almeno la durata di rimozione definitiva della foresta.

In questo caso non sarà sufficiente correggere l'errore di replica. È necessario eseguire almeno un'analisi manuale per identificare e pulire gli oggetti residui prima di poter riavviare la replica. Potrebbe anche essere necessario ritirare un controller di dominio.

Oltre a identificare eventuali errori di replica che si sono protratti oltre la durata di rimozione definitiva, è opportuno prestare attenzione a eventuali errori che rientrano nelle categorie **50-75% TSL** o **75-100% TSL**.

Si tratta di errori chiaramente residui, non temporanei, quindi è probabilmente necessario l'intervento dell'utente per risolverli. L'aspetto positivo è che non hanno ancora raggiunto la durata di rimozione definitiva. Se questi problemi vengono risolti tempestivamente e *prima* che raggiungano la durata di rimozione definitiva, la replica potrà essere riavviata con un intervento manuale minimo.

Come accennato in precedenza, il riquadro del dashboard per la soluzione Stato replica di Active Directory indica il numero di errori di replica *critici* presenti nell'ambiente, ovvero gli errori oltre il 75% della durata di rimozione definitiva, compresi gli errori oltre il 100% della durata di rimozione definitiva. Cercare di mantenere questo numero a 0.

> [!NOTE]
> Tutti i calcoli della percentuale della durata di rimozione definitiva si basano sulla durata di rimozione definitiva effettiva per la foresta di Active Directory in uso, quindi tali percentuali sono accurate anche se è impostato un valore di durata di rimozione definitiva personalizzato.
>
>

### <a name="ad-replication-status-details"></a>Dettagli di Stato replica di Active Directory
Quando si fa clic su un elemento in uno degli elenchi, è possibile accedere ad altre informazioni usando una query di log. I risultati vengono filtrati per indicare solo gli errori correlati all'elemento. Se ad esempio si fa clic sul primo controller di dominio elencato in **Stato server di destinazione (ADDC02)**, i risultati della query vengono filtrati per indicare gli errori con quel controller di dominio elencato come server di destinazione:

![Errori di stato replica di Active Directory nei risultati della query](./media/ad-replication-status/oms-ad-replication-search-details.png)

A questo punto è possibile filtrare ulteriormente, modificare la query di log e così via. Per altre informazioni sull'uso di query di log in Monitoraggio di Azure, vedere [Analizzare i dati di log in Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).

Il campo **HelpLink** indica l'URL di una pagina di TechNet con altre informazioni sullo specifico errore. È possibile copiare e incollare il collegamento nella finestra del browser per visualizzare le informazioni sulla risoluzione dei problemi e la correzione dell'errore.

È anche possibile fare clic su **Export** per esportare i risultati in Excel. Esportando i dati è possibile visualizzare i dati degli errori di replica nel modo desiderato.

![errori di stato replica di Active Directory esportati in Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Domande frequenti su Stato replica di Active Directory
**D: Frequenza vengono aggiornati i dati di stato replica AD?**
R: Le informazioni vengono aggiornate ogni cinque giorni.

**D: È possibile configurare la frequenza di aggiornamento dati?**
R: Attualmente non è possibile.

**D: È necessario aggiungere tutti i controller di dominio per l'area di lavoro di Log Analitica per vedere lo stato della replica?**
R: No, è necessario aggiungere un solo controller di dominio. Se nell'area di lavoro di Log Analytics sono presenti più controller di dominio, i dati di tutti i controller verranno inviati a Monitoraggio di Azure.

**D: Non si vuole aggiungere controller di dominio all'area di lavoro di Log Analytics. È possibile utilizzare la soluzione stato replica di Active Directory?**

R: Sì. È possibile impostare il valore di una chiave del Registro di sistema per abilitarla. Vedere [Abilitare controller non di dominio](#enable-non-domain-controller).

**D: Che cos'è il nome del processo che esegue la raccolta dei dati?**
R: AdvisorAssessment.exe

**D: Quanto tempo occorre per la raccolta dei dati?**
R: Il tempo necessario per la raccolta di dati dipende dalle dimensioni dell'ambiente Active Directory, ma in genere è inferiore a 15 minuti.

**D: Che tipo di dati viene raccolto?**
R: Le informazioni di replica vengono raccolte tramite LDAP.

**D: È possibile configurare la raccolta di dati?**
R: Attualmente non è possibile.

**D: Quali autorizzazioni necessarie per raccogliere i dati?**
R: Le normali autorizzazioni utente in Active Directory sono sufficienti.

## <a name="troubleshoot-data-collection-problems"></a>Risolvere i problemi di raccolta dati
Per raccogliere i dati, il pacchetto della soluzione Stato replica di Active Directory richiede la connessione di almeno un controller di dominio all'area di lavoro di Log Analytics. Fino a quando non ci si connette a un controller di dominio, viene visualizzato un messaggio che indica che **la raccolta di dati è ancora in corso**.

Se occorre assistenza per la connessione di uno dei controller di dominio è possibile vedere il documento [Connettere computer Windows a Monitoraggio di Azure](../../azure-monitor/platform/om-agents.md). In alternativa, se il controller di dominio è già connesso a un ambiente System Center Operations Manager esistente, è possibile vedere il documento [Connettere System Center Operations Manager a Monitoraggio di Azure](../../azure-monitor/platform/om-agents.md).

Se non si intende connettere i controller di dominio direttamente a Monitoraggio di Azure o a System Center Operations Manager, vedere [Abilitare un controller non di dominio](#enable-non-domain-controller).

## <a name="next-steps"></a>Passaggi successivi
* Usare le [query di log in Monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md) per visualizzare dati dettagliati dello stato di replica di Active Directory.
