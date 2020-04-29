---
title: Domande frequenti su Monitoraggio di Azure per le macchine virtuali (GA) | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali è una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo delle macchine virtuali di Azure, nonché l'individuazione automatica dei componenti e delle dipendenze delle applicazioni con altre risorse e mappa la comunicazione tra questi elementi. Questo articolo risponde alle domande più comuni sulla versione GA.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283889"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Monitoraggio di Azure per le macchine virtuali domande frequenti disponibili a livello generale (GA)
Queste domande frequenti sulla disponibilità generale riguardano le modifiche apportate in Q4 2019 e Q1 2020, come preparato per GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Aggiornamenti per Monitoraggio di Azure per le macchine virtuali
È stata rilasciata una nuova versione di Monitoraggio di Azure per le macchine virtuali a gennaio 2020 in anticipo rispetto al nostro annuncio GA. I clienti che abilitano Monitoraggio di Azure per le macchine virtuali riceveranno ora la versione GA, ma i clienti esistenti che usano la versione di Monitoraggio di Azure per le macchine virtuali a partire da Q4 2019 e versioni precedenti verrà richiesto di eseguire l'aggiornamento. Queste domande frequenti offrono indicazioni per eseguire un aggiornamento su larga scala se si dispone di distribuzioni di grandi dimensioni in più aree di lavoro.


Con questo aggiornamento, Monitoraggio di Azure per le macchine virtuali i dati sulle prestazioni vengono archiviati nella stessa tabella *InsightsMetrics* di [monitoraggio di Azure per i contenitori](container-insights-overview.md), semplificando l'esecuzione di query sui due set di dati. Inoltre, è possibile archiviare set di dati più diversi che non è stato possibile archiviare nella tabella usata in precedenza. 

Le visualizzazioni delle prestazioni ora usano i dati archiviati nella tabella *InsightsMetrics* .  Se non è ancora stato eseguito l'aggiornamento per usare la soluzione VMInsights più recente nell'area di lavoro, i grafici non visualizzeranno più informazioni.  È possibile eseguire l'aggiornamento dalla pagina **introduttiva** come descritto di seguito.


## <a name="what-is-changing"></a>Cosa cambierà
È stata rilasciata una nuova soluzione, denominata VMInsights, che include funzionalità aggiuntive per la raccolta di dati insieme a una nuova posizione per archiviare questi dati nell'area di lavoro Log Analytics. 

In passato, la soluzione ServiceMap è stata abilitata nell'area di lavoro e i contatori delle prestazioni sono stati impostati nell'area di lavoro Log Analytics per inviare i dati alla tabella *Perf* . Questa nuova soluzione invia i dati a una tabella denominata *InsightsMetrics* usata anche da monitoraggio di Azure per i contenitori. Questo schema di tabella consente di archiviare metriche e set di dati del servizio aggiuntivi che non sono compatibili con il formato di tabella delle *prestazioni* .

I grafici delle prestazioni sono stati aggiornati per usare i dati archiviati nella tabella *InsightsMetrics* . È possibile eseguire l'aggiornamento per usare la tabella *InsightsMetrics* dalla pagina **introduttiva** come descritto di seguito.


## <a name="how-do-i-upgrade"></a>Ricerca per categorie l'aggiornamento?
Quando un'area di lavoro di Log Analytics viene aggiornata alla versione più recente di monitoraggio di Azure alle macchine virtuali, eseguirà l'aggiornamento dell'agente di dipendenza in ognuna delle macchine virtuali collegate a tale area di lavoro. Ogni macchina virtuale che richiede l'aggiornamento verrà identificata nella scheda attività **iniziali** Monitoraggio di Azure per le macchine virtuali nel portale di Azure. Quando si sceglie di aggiornare una macchina virtuale, l'area di lavoro per tale macchina virtuale verrà aggiornata insieme a qualsiasi altra VM collegata a tale area di lavoro. È possibile selezionare una singola VM o più macchine virtuali, gruppi di risorse o sottoscrizioni. 

Usare il comando seguente per aggiornare un'area di lavoro usando PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Cosa devo fare per i contatori delle prestazioni nell'area di lavoro se si installa la soluzione VMInsights?

Il metodo precedente per abilitare i contatori delle prestazioni Monitoraggio di Azure per le macchine virtuali utilizzati nell'area di lavoro. La versione corrente archivia questi dati in una tabella denominata `InsightsMetrics`. È possibile scegliere di disabilitare questi contatori delle prestazioni nell'area di lavoro se non è più necessario usarli. 

>[!NOTE]
>Se sono presenti regole di avviso che fanno riferimento a questi `Perf` contatori nella tabella, è necessario aggiornarli per fare riferimento a nuovi dati `InsightsMetrics` archiviati nella tabella. Vedere la documentazione per le query di log di esempio che è possibile usare per fare riferimento a questa tabella.
>

Se si decide di tenere i contatori delle prestazioni abilitati, verranno addebitati i dati inseriti e archiviati nella `Perf` tabella in base a [log Analytics prezzi [(.https://azure.microsoft.com/pricing/details/monitor/)

## <a name="how-will-this-change-affect-my-alert-rules"></a>In che modo questa modifica influirà sulle regole di avviso?

Se sono stati creati [avvisi del log](../platform/alerts-unified-log.md) che eseguono `Perf` una query sulla tabella destinata ai contatori delle prestazioni abilitati nell'area di lavoro, è necessario aggiornare queste `InsightsMetrics` regole per fare riferimento alla tabella. Queste linee guida sono valide anche per le regole di `ServiceMapComputer_CL` ricerca `ServiceMapProcess_CL`dei log che usano e, perché tali `VMComputer` Set `VMProcess` di dati vengono spostati nelle tabelle e.

Le domande frequenti e la documentazione per includere le regole di avviso di ricerca log di esempio per i set di dati raccolti vengono aggiornate.

## <a name="how-will-this-affect-my-bill"></a>Come influirà la fattura?

La fatturazione è ancora basata sui dati inseriti e conservati nell'area di lavoro Log Analytics.

I dati sulle prestazioni a livello di computer raccolti sono gli stessi, con dimensioni simili ai dati archiviati nella `Perf` tabella e che costeranno approssimativamente la stessa quantità.

## <a name="what-if-i-only-want-to-use-service-map"></a>Cosa accade se si vuole usare solo Mapping dei servizi?

Questo è un problema. Quando si visualizzano Monitoraggio di Azure per le macchine virtuali sull'aggiornamento imminente, vengono visualizzate richieste nella portale di Azure. Una volta rilasciato, verrà visualizzato un messaggio che richiede di eseguire l'aggiornamento alla nuova versione. Se si preferisce usare solo la funzionalità [Maps](vminsights-maps.md) , è possibile scegliere di non eseguire l'aggiornamento e continuare a usare la funzionalità maps in monitoraggio di Azure per le macchine virtuali e la soluzione mapping dei servizi a cui si accede dall'area di lavoro o dal riquadro del dashboard.

Se si sceglie di abilitare manualmente i contatori delle prestazioni nell'area di lavoro, potrebbe essere possibile visualizzare i dati in alcuni dei grafici delle prestazioni visualizzati da monitoraggio di Azure. Una volta rilasciata la nuova soluzione, i grafici delle prestazioni vengono aggiornati per eseguire query sui `InsightsMetrics` dati archiviati nella tabella. Se si desidera visualizzare i dati di tale tabella in questi grafici, sarà necessario eseguire l'aggiornamento alla nuova versione di Monitoraggio di Azure per le macchine virtuali.

Le modifiche per spostare i dati `ServiceMapComputer_CL` da `ServiceMapProcess_CL` e influiranno su mapping dei servizi e monitoraggio di Azure per le macchine virtuali, quindi è necessario pianificare l'aggiornamento.

Se si sceglie di non eseguire l'aggiornamento alla soluzione **VMInsights** , si continuerà a fornire le versioni legacy delle cartelle di lavoro delle prestazioni che fanno riferimento `Perf` ai dati nella tabella.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>I set di dati Mapping dei servizi verranno archiviati anche in InsightsMetrics?

I set di dati non verranno duplicati se si utilizzano entrambe le soluzioni. Entrambe le offerte condividono i set di dati che verranno archiviati nelle `VMComputer` tabelle (in precedenza ServiceMapComputer_CL `VMProcess` ), (in precedenza `VMConnection`ServiceMapProcess_CL) `VMBoundPort` , e per archiviare i set di dati della mappa raccolti.  

Nella `InsightsMetrics` tabella verranno archiviati i set di dati di VM, processi e servizi raccolti e verranno popolati solo se si usano monitoraggio di Azure per le macchine virtuali e la soluzione VM Insights. La soluzione Mapping dei servizi non raccoglie né o archivia i dati nella `InsightsMetrics` tabella.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Viene addebitato un costo doppio se si hanno le soluzioni Mapping dei servizi e VMInsights nell'area di lavoro?

No, le due soluzioni condividono i set di dati della mappa archiviati in `VMComputer` (in precedenza ServiceMapComputer_CL) `VMProcess` , (in precedenza ServiceMapProcess_CL `VMConnection`), `VMBoundPort`e. I dati non verranno addebitati a doppio se si dispone di entrambe le soluzioni nell'area di lavoro.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se si rimuove la soluzione Mapping dei servizi o VMInsights, i dati vengono rimossi?

No, le due soluzioni condividono i set di dati della mappa archiviati in `VMComputer` (in precedenza ServiceMapComputer_CL) `VMProcess` , (in precedenza ServiceMapProcess_CL `VMConnection`), `VMBoundPort`e. Se si rimuove una delle soluzioni, questi set di dati si notano che è ancora disponibile una soluzione che utilizza i dati e rimane nell'area di lavoro Log Analytics. È necessario rimuovere entrambe le soluzioni dall'area di lavoro per consentire la rimozione dei dati.

## <a name="health-feature-is-in-limited-public-preview"></a>La funzionalità di integrità è in anteprima pubblica limitata

Abbiamo ricevuto numerosi commenti e suggerimenti dai clienti sul set di funzionalità per l'integrità delle macchine virtuali. Questa funzionalità è molto interessante e può essere utile per supportare i flussi di lavoro di monitoraggio. Si prevede di apportare una serie di modifiche per aggiungere funzionalità e risolvere i commenti ricevuti. 

Per ridurre al minimo l'effetto di queste modifiche apportate ai nuovi clienti, questa funzionalità è stata spostata in un' **anteprima pubblica limitata**. Questo aggiornamento è stato eseguito nel 2019 ottobre.

Si prevede di riavviare questa funzionalità di integrità in 2020, dopo che Monitoraggio di Azure per le macchine virtuali è disponibile in GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>In che modo i clienti esistenti accedono alla funzionalità di integrità?

I clienti esistenti che usano la funzionalità di integrità continueranno ad avere accesso a tale funzionalità, ma non verranno offerti ai nuovi clienti.  

Per accedere alla funzionalità, è possibile aggiungere il flag `feature.vmhealth=true` di funzionalità seguente all'URL [https://portal.azure.com](https://portal.azure.com)portale di Azure. Esempio `https://portal.azure.com/?feature.vmhealth=true`.

È inoltre possibile utilizzare questo breve URL, che imposta automaticamente il flag di funzionalità [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview):.

Come cliente esistente, è possibile continuare a usare la funzionalità di integrità nelle VM connesse a una configurazione dell'area di lavoro esistente con la funzionalità di integrità.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Si usa ora l'integrità VM con un ambiente e si vuole distribuirla in una nuova

Se si è un cliente esistente che utilizza la funzionalità di integrità e si desidera utilizzarlo per un nuovo Rollup, contattare Microsoft all'indirizzo vminsights@microsoft.com per richiedere istruzioni.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).
