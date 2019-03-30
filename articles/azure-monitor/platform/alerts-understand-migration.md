---
title: Comprendere come volontari utilità di migrazione di avvisi in Monitoraggio di Azure funziona
description: Comprendere il funzionamento dello strumento di migrazione degli avvisi e risoluzione dei problemi se si riscontrano problemi.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632319"
---
# <a name="understand-how-the-migration-tool-works"></a>Comprendere il funzionamento dello strumento di migrazione

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati nel luglio 2019. Lo strumento di migrazione per attivare volontariamente la migrazione è disponibile nel portale di Azure e viene implementata per i clienti che usano le regole di avviso di classiche.

Questo articolo illustra come funziona lo strumento di migrazione volontaria. Descrive inoltre risolvere i problemi comuni problemi riscontrati.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Le regole di avviso classica possono essere migrate?

Sebbene quasi tutte le regole di avviso classica possono essere migrate utilizzando lo strumento, esistono alcune eccezioni. Le regole di avviso seguente non sarà possibile eseguire la migrazione usando lo strumento (o durante la migrazione automatica in 2019 luglio)

- Regole di avviso classica sulle metriche guest macchina virtuale (Windows e Linux). [Vedere le indicazioni su come ricreare tali regole di avviso in nuovi avvisi delle metriche](#guest-metrics-on-virtual-machines)
- Regole di avviso classica sulle metriche di archiviazione classico. [Vedere le indicazioni sul monitoraggio degli account di archiviazione classico](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Regole di avviso classiche su alcune metriche di account di archiviazione. [Dettagli di seguito](#storage-account-metrics)

Se la sottoscrizione include eventuali regole classica di questo tipo, verrà eseguita la migrazione di altre regole, ma queste regole saranno necessario eseguire manualmente la migrazione. Non è possibile offrire una migrazione automatica, qualsiasi tali esistenti avvisi delle metriche classici continuerà ad arrivare a giugno 2020 a garantire il tempo per passare a nuovi avvisi. Tuttavia, nessun nuovo avviso classico è possibile creare post giugno 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Metriche guest nelle macchine virtuali

Per essere in grado di creare nuovi avvisi delle metriche per le metriche guest, le metriche guest devono essere inviati all'archivio di metriche personalizzate di monitoraggio di Azure. Seguire le istruzioni seguenti per abilitare il sink di monitoraggio di Azure in impostazioni di diagnostica.

- [Abilitazione delle metriche guest per le macchine virtuali Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Abilitazione delle metriche guest per macchine virtuali Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Dopo aver completati i passaggi precedenti, è possibile creare nuovi avvisi delle metriche per le metriche guest. Dopo avere ricreato nuovi avvisi delle metriche, è possibile eliminare gli avvisi classici.

### <a name="storage-account-metrics"></a>Metriche relative all'account di archiviazione

Tutti gli avvisi classici in account di archiviazione possono essere migrati tranne gli avvisi sulle metriche seguenti:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Le regole di avviso classica sulle metriche percentuale saranno necessario eseguire la migrazione in base alle [il mapping tra le metriche di archiviazione di vecchi e nuovi](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Le soglie dovrà essere modificato in modo appropriato, poiché la nuova metrica disponibile è assoluto.

Le regole di avviso classiche AnonymousThrottlingError e SASThrottlingError dovranno essere suddivisa in due nuovi avvisi presenti non è Nessuna metrica combinata che offre le stesse funzionalità. Le soglie dovrà essere adattato in modo appropriato.

## <a name="roll-out-phases"></a>Fasi di implementazione

Lo strumento di migrazione è l'implementazione in fasi ai clienti che usano le regole di avviso di classiche. **I proprietari di sottoscrizioni** riceverà un messaggio di posta elettronica quando la sottoscrizione è pronta per eseguire la migrazione usando lo strumento.

> [!NOTE]
> Come lo strumento viene implementato in fasi, nelle prime fasi, si noterà che la maggior parte delle sottoscrizioni non sono ancora pronta per eseguire la migrazione.

Attualmente un **sottoinsieme** delle sottoscrizioni, quale **solo** dispone di regole di avviso classiche sulla risorsa seguente tipi sono contrassegnati come pronti per la migrazione. Verrà aggiunto il supporto per più tipi di risorse in fasi imminente.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- DataFactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Logic/Workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.Network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.Search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Qualsiasi utente che ha il ruolo predefinito del **collaboratore al monitoraggio** alla sottoscrizione di livello sarà in grado di attivare la migrazione. Gli utenti con un ruolo personalizzato con le autorizzazioni seguenti possono attivare anche la migrazione:

- */lettura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Problemi comuni e le correzioni

Dopo aver [attivare la migrazione](alerts-using-migration-tool.md), si userà gli indirizzi di posta elettronica fornito per ricevere una notifica del completamento della migrazione o se è disponibile un'azione necessaria da parte dell'utente. La sezione seguente descrive alcuni problemi comuni e come monitorarle e aggiornarle

### <a name="validation-failed"></a>Convalida non riuscita

A causa di alcune modifiche recenti alle regole di avviso classiche nella sottoscrizione, è Impossibile eseguire la migrazione della sottoscrizione. Si tratta di un problema temporaneo. È possibile riavviare la migrazione dopo che lo stato di migrazione consente di tornare **pronto per la migrazione** in pochi giorni.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Blocco/ambito dei criteri impedisce la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi delle metriche e i nuovi gruppi di azioni e le regole di avviso di classiche verranno eliminate (una volta create le nuove regole). Tuttavia, vi è un criterio o un ambito di blocco impedisce la creazione di risorse. A seconda del blocco di criteri o ambito, potrebbe non essere migrate alcune o tutte le regole. È possibile risolvere questo problema rimuovendo temporaneamente i criteri o un blocco ambito e attivare nuovamente la migrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Preparare la migrazione](alerts-prepare-migration.md)
