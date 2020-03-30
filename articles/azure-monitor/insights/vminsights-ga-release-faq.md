---
title: Domande frequenti su Monitoraggio di Azure per le macchine virtuali (GA). Documenti Microsoft
description: Monitoraggio di Azure per le macchine virtuali è una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo delle macchine virtuali di Azure, nonché l'individuazione automatica dei componenti e delle dipendenze delle applicazioni con altre risorse e mappa la comunicazione tra questi elementi. Questo articolo risponde alle domande più comuni sulla versione GA.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283889"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per le macchine virtuali generalmente disponibili (GA)
Queste domande frequenti sulla disponibilità generale coprono le modifiche apportate nel quarto trimestre 2019 e nel primo trimestre 2020, preparati per GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Aggiornamenti per Monitoraggio di Azure per le macchine virtualiUpdates for Azure Monitor for VMs
È stata rilasciata una nuova versione di Monitoraggio di Azure per le macchine virtuali nel gennaio 2020 prima dell'annuncio dell'assembly di database. I clienti che abilitano Monitoraggio di Azure per le macchine virtuali riceveranno ora la versione GA, ma ai clienti esistenti che usano la versione di Monitoraggio di Azure per le macchine virtuali del quarto trimestre 2019 e versioni precedenti verrà richiesto di eseguire l'aggiornamento. Queste domande frequenti offrono indicazioni per eseguire un aggiornamento su larga scala se sono presenti distribuzioni di grandi dimensioni in più aree di lavoro.


Con questo aggiornamento, i dati sulle prestazioni di Monitoraggio di Azure per le macchine virtuali vengono archiviati nella stessa tabella *InsightsMetrics* di [Monitoraggio di Azure per i contenitori,](container-insights-overview.md)in modo da semplificare l'esecuzione di query sui due set di dati. Inoltre, è possibile archiviare set di dati più diversi che non è stato possibile archiviare nella tabella utilizzata in precedenza. 

Le nostre visualizzazioni delle prestazioni ora usano i dati archiviati nella tabella *InsightsMetrics.Our* performance views are now using the data we store in the InsightsMetrics table.  Se non è ancora stato eseguito l'aggiornamento per usare la soluzione VMInsights più recente nell'area di lavoro, i grafici non visualizzeranno più informazioni.  Puoi eseguire l'aggiornamento dalla nostra pagina **introduttiva** come descritto di seguito.


## <a name="what-is-changing"></a>Cosa cambierà
È stata rilasciata una nuova soluzione, denominata VMInsights, che include funzionalità aggiuntive per la raccolta dei dati insieme a una nuova posizione per l'archiviazione di questi dati nell'area di lavoro di Log Analytics.We have released a new solution, named VMInsights, that includes additional capabilities for data collection along with a new location for storing this data in your Log Analytics workspace. 

In passato, è stata abilitata la soluzione ServiceMap nell'area di lavoro e i contatori delle prestazioni di installazione nell'area di lavoro di Log Analytics sono stati abilitati per inviare i dati alla tabella *Perf.* Questa nuova soluzione invia i dati a una tabella denominata *InsightsMetrics* usata anche da Monitoraggio di Azure per i contenitori. Questo schema di tabella consente di archiviare metriche aggiuntive e set di dati del servizio non compatibili con il formato della tabella *Perf.This* table schema allows us to store additional metrics and service data sets that are not compatible with the Perf table format.

Abbiamo aggiornato i nostri grafici sulle prestazioni per usare i dati archiviati nella tabella *InsightsMetrics.* Puoi eseguire l'aggiornamento per usare la tabella *InsightsMetrics* dalla nostra pagina **introduttiva** come descritto di seguito.


## <a name="how-do-i-upgrade"></a>Come si esegue l'aggiornamento?
Quando un'area di lavoro di Log Analytics viene aggiornata alla versione più recente di Monitoraggio di Azure alle macchine virtuali, aggiornerà l'agente di dipendenza in ognuna delle macchine virtuali associate a tale area di lavoro. Ogni macchina virtuale che richiede l'aggiornamento verrà identificata nella scheda Introduzione in Monitoraggio di Azure per macchine virtuali nel portale di Azure.Each VM requiring upgrade will be identified in the **Get Started** tab in Azure Monitor for VMs in the Azure portal. Quando si sceglie di aggiornare una macchina virtuale, questa aggiornerà l'area di lavoro per tale macchina virtuale insieme a tutte le altre macchine virtuali associate a tale area di lavoro. È possibile selezionare una singola macchina virtuale o più macchine virtuali, gruppi di risorse o sottoscrizioni. 

Usare il comando seguente per aggiornare un'area di lavoro tramite PowerShell:Use the following command to upgrade a workspace using PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Cosa è necessario eseguire sui contatori delle prestazioni nell'area di lavoro se si installa la soluzione VMInsights?

Il metodo precedente per abilitare Monitoraggio di Azure per le macchine virtuali usava i contatori delle prestazioni nell'area di lavoro. La versione corrente archivia questi `InsightsMetrics`dati in una tabella denominata . È possibile scegliere di disabilitare questi contatori delle prestazioni nell'area di lavoro se non è più necessario utilizzarli. 

>[!NOTE]
>Se nella `Perf` tabella sono presenti regole di avviso che fanno riferimento a questi `InsightsMetrics` contatori, è necessario aggiornarle per fare riferimento ai nuovi dati archiviati nella tabella. Fare riferimento alla documentazione per le query di log, ad esempio, che è possibile utilizzare che fanno riferimento a questa tabella.
>

Se si decide di mantenere abilitati i contatori delle prestazioni, verranno fatturati i dati ingeriti e archiviati nella `Perf` tabella in base a [Log Analytics pricing[(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>In che modo questa modifica influirà sulle regole di avviso?

Se sono stati creati [avvisi di log](../platform/alerts-unified-log.md) che eseguono una query sulla `Perf` tabella destinata ai `InsightsMetrics` contatori delle prestazioni abilitati nell'area di lavoro, è necessario aggiornare queste regole per fare riferimento alla tabella. Queste indicazioni si applicano anche `ServiceMapComputer_CL` `ServiceMapProcess_CL`a tutte le regole `VMComputer` di `VMProcess` ricerca dei log che utilizzano e , poiché tali set di dati vengono spostarli in e nelle tabelle.

Questa FAQ e la nostra documentazione includono esempi di regole di avviso per la ricerca nei log per i set di dati raccolti.

## <a name="how-will-this-affect-my-bill"></a>In che modo questo influirà sulla mia fattura?

La fatturazione è ancora basata sui dati ingeriti e conservati nell'area di lavoro di Log Analytics.

I dati sulle prestazioni a livello di computer che raccogliamo sono gli `Perf` stessi, hanno dimensioni simili ai dati archiviati nella tabella e costeranno approssimativamente la stessa quantità.

## <a name="what-if-i-only-want-to-use-service-map"></a>Cosa succede se si desidera utilizzare solo la mappa del servizio?

Va bene così. Verranno visualizzati prompt nel portale di Azure quando si visualizza Monitoraggio di Azure per le macchine virtuali sul prossimo aggiornamento. Una volta rilasciato, si riceverà un prompt che richiede l'aggiornamento alla nuova versione. Se si preferisce usare solo la funzionalità [Mappe,](vminsights-maps.md) è possibile scegliere di non eseguire l'aggiornamento e continuare a usare la funzionalità Mappe in Monitoraggio di Azure per le macchine virtuali e la soluzione Mappa del servizio a cui si accede dal riquadro dell'area di lavoro o del dashboard.

Se si è scelto di abilitare manualmente i contatori delle prestazioni nell'area di lavoro, è possibile visualizzare i dati in alcuni dei grafici delle prestazioni visualizzati da Monitoraggio di Azure.If you chose to manually enable the performance counters in your workspace, then you may be able to see data in some of our performance charts viewed from Azure Monitor. Una volta rilasciata la nuova soluzione, aggiorneremo i `InsightsMetrics` nostri grafici delle prestazioni per eseguire query sui dati archiviati nella tabella. Se si desidera visualizzare i dati di tale tabella in questi grafici, è necessario eseguire l'aggiornamento alla nuova versione di Monitoraggio di Azure per le macchine virtuali.

Le modifiche per `ServiceMapComputer_CL` spostare i dati da e influiranno sia su Mappa del servizio che `ServiceMapProcess_CL` monitoraggio di Azure per le macchine virtuali, pertanto è comunque necessario pianificare questo aggiornamento.

Se si sceglie di non eseguire l'aggiornamento alla soluzione **VMInsights,** continueremo a fornire `Perf` versioni legacy delle cartelle di lavoro sulle prestazioni che fanno riferimento ai dati nella tabella.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>I set di dati della mappa del servizio verranno archiviati anche in InsightsMetrics?

I set di dati non verranno duplicati se si utilizzano entrambe le soluzioni. Entrambe le offerte condividono i `VMComputer` set di `VMProcess` dati che `VMConnection`verranno `VMBoundPort` archiviati (in precedenza ServiceMapComputer_CL), (in precedenza ServiceMapProcess_CL) e le tabelle in cui archiviare i set di dati mapparle raccolti.  

La `InsightsMetrics` tabella archivierà i set di dati di macchine virtuali, processi e servizi raccolti e verranno popolati solo se si usa Monitoraggio di Azure per le macchine virtuali e la soluzione VM Insights.The table will only store VM, process, and service data sets that we collect and will only be populated if you are using Azure Monitor for VMs and the VM Insights solution. La soluzione Mappa servizio non raccoglierà `InsightsMetrics` o non memorizzerà dati nella tabella.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Verrà addebitato due volte se nell'area di lavoro sono disponibili le soluzioni Service Map e VMInsights?

No, le due soluzioni condividono i `VMComputer` set di `VMProcess` dati della `VMConnection`mappa `VMBoundPort`in cui vengono archiviati (in precedenza ServiceMapComputer_CL), (in precedenza ServiceMapProcess_CL), e . Questi dati non verranno addebitati due volte se entrambe le soluzioni sono disponibili nell'area di lavoro.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se si rimuove la soluzione Service Map o VMInsights, i dati verranno rimossi?

No, le due soluzioni condividono i `VMComputer` set di `VMProcess` dati della `VMConnection`mappa `VMBoundPort`in cui vengono archiviati (in precedenza ServiceMapComputer_CL), (in precedenza ServiceMapProcess_CL), e . Se si rimuove una delle soluzioni, questi set di dati notano che esiste ancora una soluzione che utilizza i dati e che rimane nell'area di lavoro di Log Analytics. È necessario rimuovere entrambe le soluzioni dall'area di lavoro per rimuovere i dati da essa.

## <a name="health-feature-is-in-limited-public-preview"></a>La funzionalità di integrità è in anteprima pubblica limitata

Abbiamo ricevuto molti ottimi feedback dai clienti sul set di funzionalità Integrità VM. C'è un sacco di interesse intorno a questa caratteristica e l'eccitazione per il suo potenziale per supportare i flussi di lavoro di monitoraggio. Stiamo progettando di apportare una serie di modifiche per aggiungere funzionalità e rispondere al feedback che abbiamo ricevuto. 

Per ridurre al minimo l'impatto di queste modifiche ai nuovi clienti, questa funzionalità è stata spostata in **un'anteprima pubblica limitata.** Questo aggiornamento è accaduto nel mese di ottobre 2019.

Si prevede di riavviare questa funzionalità di integrità nel 2020, dopo che Monitoraggio di Azure per le macchine virtuali è in GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>In che modo i clienti esistenti accedono alla funzionalità Integrità?

I clienti esistenti che utilizzano la funzionalità Integrità continueranno ad avere accesso ad essa, ma non verranno offerti ai nuovi clienti.  

Per accedere alla funzionalità, è possibile `feature.vmhealth=true` aggiungere il [https://portal.azure.com](https://portal.azure.com)flag di funzionalità seguente all'URL del portale di Azure. Esempio `https://portal.azure.com/?feature.vmhealth=true`.

È inoltre possibile utilizzare questo breve URL, [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)che imposta automaticamente il flag di funzionalità: .

Come cliente esistente, è possibile continuare a usare la funzionalità Integrità nelle macchine virtuali connesse a un'impostazione dell'area di lavoro esistente con la funzionalità di integrità.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Ora uso VM Health con un ambiente e desidero distribuirlo in uno nuovo

Se sei un cliente esistente che utilizza la funzione Integrità e desideri utilizzarla per vminsights@microsoft.com una nuova implementazione, contattaci per richiedere istruzioni.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).
