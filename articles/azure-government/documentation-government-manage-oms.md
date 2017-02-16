---
title: Operations Management Suite di Azure per enti pubblici | Documentazione Microsoft
description: Questo articolo descrive in che modo Log Analytics in Operations Management Suite sia applicabile alle agenzie governative e ai fornitori di soluzioni degli Stati Uniti
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Sicurezza informatica di Azure per enti pubblici: monitoraggio e protezione degli asset con Operations Management Suite 

## <a name="cybersecurity-in-the-cloud"></a>Sicurezza informatica nel cloud
Un problema importante per i clienti che passano al cloud è la possibilità di mantenere la gestione e la sicurezza dei servizi di Azure per enti pubblici distribuiti nel cloud. I firewall delle macchine virtuali devono essere configurati correttamente. Alle reti virtuali devono essere applicati i gruppi di sicurezza di rete appropriati. L'accesso agli asset deve essere bloccato nel momento giusto. Tutti questi flussi di lavoro necessari devono essere pianificati, progettati e sottoposti a provisioning per consentire all'ente di usare un'infrastruttura sicura.

La configurazione di un ambiente di questo tipo può risultare complessa. L'onboarding dell'infrastruttura di server su qualsiasi servizio di monitoraggio è un'operazione difficile da scalare e può anche risultare difficile aggiornare il servizio di monitoraggio. Il monitoraggio dell'infrastruttura in provider di servizi cloud diversi, ma anche nel cloud e in locale è complicato. Infine, tenere aggiornato il sistema di monitoraggio e abilitare Azure Application Insights per monitorare, rilevare, generare avvisi e prendere provvedimenti contro le minacce alla sicurezza informatica richiede tempo, risorse e potenza di calcolo.

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
Microsoft Operations Management Suite, ora disponibile in Azure per enti pubblici, è un set di servizi di Azure che consente di eseguire tutte queste operazioni in modo semplice e rapido. Questo articolo illustra l'impiego di Log Analytics che usa una ricerca dei log iperscalabile per analizzare i dati rapidamente e mostrare le minacce nell'ambiente.

Log Analytics di Azure è in grado di:

* Distribuire agenti in singole VM (Linux e Windows) in Azure, in altri provider di servizi cloud e in locale.
* Connettere i log esistenti tramite un account di archiviazione di Azure per enti pubblici o un endpoint System Center Operations Manager ai dati di registrazione esistenti.

Scopriamo come integrare Log Analytics nell'infrastruttura esistente e osserviamo alcune delle soluzioni incorporate che risolvono i problemi descritti fino ad ora.

## <a name="onboarding-servers-to-log-analytics"></a>Onboarding di server in Log Analytics
Il primo passaggio per integrare le risorse nel cloud con Log Analytics è l'installazione dell'agente di Log Analytics nelle origini dei log. Per le macchine virtuali è sufficiente scaricare manualmente l'agente dal portale di Log Analytics.

![Figura 1: Server Windows connessi a Operations Management Suite](./media/documentation-government-oms-figure1.png)
<p align="center">Figura 1: Server Windows connessi a Log Analytics</p>

È possibile connettere le macchine virtuali di Azure a Log Analytics direttamente nel portale di Azure. Per istruzioni, vedere [New ways to enable Log Analytics on your Azure VMs](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/) (Nuovi modi per abilitare Log Analytics nelle macchine virtuali Azure).

È anche possibile connetterle a livello di codice o configurare l'estensione macchina virtuale di Log Analytics direttamente nei modelli di Azure Resource Manager. Per le istruzioni relative alle macchine basate su Windows, vedere [Connettere computer Windows a Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents). Per le istruzioni relative alle macchine basate su Linux, vedere [Connettere computer Linux a Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>Onboarding di account di archiviazione e Operations Manager a Log Analytics
Log Analytics può anche connettersi all'account di archiviazione e/o alle distribuzioni di System Center Operations Manager esistenti per consentire la gestione delle operazioni in scenari ibridi (in provider di servizi cloud o in infrastrutture cloud/locali).

![Figura 2: Connessione di Archiviazione di Azure e Operations Manager a Log Analytics](./media/documentation-government-oms-figure2.png)
<p align="center">Figura 2: Connessione di Archiviazione di Azure e Operations Manager a Log Analytics</p>

Log Analytics supporta anche la raccolta delle informazioni di registrazione da altri servizi di monitoraggio, ad esempio Chef o Puppet. Per le distribuzioni di Azure sono disponibili modelli di Azure Resource Manager abilitati per le VM con Log Analytics per poter distribuire la funzionalità di calcolo ed eseguire l'onboarding nell'area di lavoro di Log Analytics contemporaneamente.

![Figura 3: Modelli di Azure Resource Manager per VM di Azure con l'estensione di macchina virtuale Log Analytics](./media/documentation-government-oms-figure3a.png)
![Figura 3: Modelli di Azure Resource Manager per VM di Azure con l'estensione di macchina virtuale Log Analytics](./media/documentation-government-oms-figure3b.png)
<p align="center">Figura 3: Modelli di Azure Resource Manager per VM di Azure con l'estensione macchina virtuale di Log Analytics</p>

Le informazioni sull'impostazione di Log Analytics con l'implementazione locale esistente di Operations Manager sono reperibili in [Connettere Operations Manager a Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>Applicazione dell'intelligence tramite pacchetti della soluzione Operations Management Suite
Ora che si dispone di diverse origini per la registrazione dei dati, è necessario usare al meglio tutti i dati.

Log Analytics è fondamentalmente un servizio di ricerca log che consente di scrivere query avanzate per eseguire rapidamente ricerche in migliaia o addirittura in milioni di log, ma individuare i problemi per cui è necessario scrivere query può essere molto difficile.

Le soluzioni di Operations Management Suite sono pacchetti di query nativamente integrati con Log Analytics per fornire in modo proattivo approfondimenti sull'infrastruttura gestita da Log Analytics.

Nell'ambito della sicurezza informatica, verranno brevemente illustrati tre scenari che Log Analytics può immediatamente risolvere.

### <a name="antimalware-assessment"></a>Antimalware Assessment
Antimalware Assessment offre un set predefinito di query, notifiche e dashboard di monitoraggio, che consente di verificare immediatamente il livello di protezione dei server contro il malware.

Questo dashboard fornisce un elenco di quattro elementi:
* I server con minacce attive e/o con correzione.
* Minacce attualmente rilevate.
* Computer non sufficientemente protetti. Log Analytics trova queste informazioni eseguendo una ricerca per indicizzazione nei log dei computer alla ricerca di siti di FW aperti o con regole non correttamente configurate nei Web browser comuni.
* Analisi sulla protezione applicata ai server protetti, ad esempio da soluzioni di protezione da virus nativa per SO Windows o System Center Endpoint Protection.

Ad esempio, la minaccia seguente è stata rilevata e automaticamente valutata da System Center:

![Figura 4: Soluzione Antimalware Assessment di Operations Management Suite](./media/documentation-government-oms-figure4.png)
<p align="center">Figura 4: Soluzione Antimalware Assessment di Operations Management Suite</p>

Altre informazioni sulla soluzione Antimalware Assessment sono disponibili nell'articolo [Soluzione di valutazione antimalware in Log Analytics](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/).

### <a name="identity-and-access"></a>Identità e accesso
Un altro comune scenario di sicurezza informatica nel cloud è quello relativo alla violazione delle credenziali. Non solo la sottoscrizione cloud ha le credenziali, ma a ogni singola VM sono associati un utente e/o un segreto (in genere un certificato o una password).

Log Analytics organizza tutti i tentativi di accesso ai server e li raggruppa a seconda del tipo (remoto, locale, nome utente usato e così via). Nell'esempio seguente si può osservare una considerevole quantità di tentativi di accesso non riusciti da stringhe principalmente casuali come nome utente. Questo indica che sussiste un'elevata probabilità che i computer siano esposti e non correttamente protetti da firewall ed elenchi di controllo di accesso.

![Figura 5: 97,3% degli accessi non riusciti nelle ultime 24 ore](./media/documentation-government-oms-figure5.png)
<p align="center">Figura 5: 97,3% degli accessi non riusciti nelle ultime 24 ore</p>

### <a name="threat-intelligence"></a>Intelligence per le minacce
Log Analytics offre anche protezione per gli scenari relativi a utenti interni malintenzionati, quando si verifica una violazione della sicurezza nell'organizzazione e un utente malintenzionato cerca di sottrarre dati.

L'intelligence per le minacce di Log Analytics esamina tutti i log di rete nel computer e automaticamente cerca e invia notifiche sulle connessioni di rete in ingresso/in uscita a IP dannosi noti, ad esempio indirizzi IP nella darknet non indicizzata.

Nella schermata seguente, ad esempio, si possono osservare connessioni di rete sia in ingresso che in uscita con la Repubblica popolare cinese.

Facendo doppio clic sul tag in ingresso, si può osservare che una VM Linux gestita da Log Analytics stabilisce connessioni in uscita a un indirizzo IP di una darknet nota in Cina.

È anche possibile impostare avvisi per le soluzioni di Operations Management Suite come l'intelligence per le minacce. Nella schermata seguente, viene impostato un avviso in modo tale che se Log Analytics rileva più di 10 connessioni in uscita verso un indirizzo IP noto nocivo, viene inviato un avviso all'utente tramite posta elettronica. Si configura quindi un avviso per generare un processo di Automazione di Azure configurato per arrestare automaticamente tale VM.

![Figura 6: Avvisi e automazione di Operations Management Suite](./media/documentation-government-oms-figure6.png)
<p align="center">Figura 6: Avvisi e automazione di Operations Management Suite</p>

Questo è solo un esempio di soluzione Operations Management Suite integrata che può essere applicata ai server, indipendentemente dal fatto che vengano eseguiti in Azure, in un altro provider di servizi cloud o in locale.

Operations Management Suite continuerà ad aggiornare automaticamente la tecnologia di Machine Learning in base alle minacce più recenti e nuove soluzioni verranno sempre proposte nel marketplace di Azure.

Per altre informazioni su Operations Management Suite, vedere la [pagina della documentazione](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/).



<!--HONumber=Jan17_HO1-->


