---
title: Comprendere il funzionamento dello strumento di migrazione volontaria per gli avvisi di monitoraggio di Azure
description: Comprendere il funzionamento dello strumento di migrazione di avvisi e risoluzione dei problemi.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015600"
---
# <a name="understand-how-the-migration-tool-works"></a>Comprendere il funzionamento dello strumento di migrazione

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati nel mese di settembre 2019 (è stato originariamente 2019 luglio). Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso di classiche e che desiderano migrazione si attivano automaticamente.

Questo articolo spiega come funziona lo strumento di migrazione volontaria. Descrive inoltre informazioni su come risolvere alcuni problemi comuni.

> [!NOTE]
> A causa di un ritardo nell'implementazione dello strumento di migrazione, è stata la data di ritiro per la migrazione di avvisi classici [esteso al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data di originariamente annunciata del 30 giugno 2019.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Le regole di avviso classica possono essere migrate?

Anche se lo strumento può eseguire la migrazione di quasi tutte classiche regole di avviso, esistono alcune eccezioni. Le regole di avviso seguente non sarà possibile eseguire la migrazione tramite lo strumento (o durante la migrazione automatica in settembre 2019):

- Regole di avviso classica sulle metriche guest macchina virtuale (Windows e Linux). Vedere le [materiale sussidiario per ricreare tali regole di avviso in nuovi avvisi delle metriche](#guest-metrics-on-virtual-machines) più avanti in questo articolo.
- Regole di avviso classica sulle metriche di archiviazione classico. Vedere le [materiale sussidiario per il monitoraggio degli account di archiviazione classico](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regole di avviso classiche su alcune metriche di account di archiviazione. Visualizzare [dettagli](#storage-account-metrics) più avanti in questo articolo.

Se la sottoscrizione include eventuali regole di questo tipo classiche, è necessario eseguirne la migrazione manualmente. Poiché non è possibile offrire una migrazione automatica, qualsiasi avvisi delle metriche classici, esistenti di questi tipi continueranno a funzionare fino a giugno 2020. Questa estensione offre ora per passare a nuovi avvisi. Tuttavia, è possibile creare nessun nuovo avviso classico dopo agosto 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Metriche guest nelle macchine virtuali

Prima di creare nuovi avvisi delle metriche per le metriche guest, le metriche guest devono essere inviate all'archivio di metriche personalizzate di monitoraggio di Azure. Seguire queste istruzioni per abilitare il sink di monitoraggio di Azure in impostazioni di diagnostica:

- [Abilitazione delle metriche guest per le macchine virtuali Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Abilitazione delle metriche guest per macchine virtuali Linux](collect-custom-metrics-linux-telegraf.md)

Dopo questi passaggi vengono eseguiti, è possibile creare nuovi avvisi delle metriche per le metriche guest. E dopo aver creato i nuovi avvisi delle metriche, è possibile eliminare gli avvisi classici.

### <a name="storage-account-metrics"></a>Metriche relative all'account di archiviazione

Tutti gli avvisi classici in account di archiviazione possono essere migrati, ad eccezione degli avvisi su queste metriche:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Avviso classico delle regole per le metriche percentuale devono essere migrate in base a [il mapping tra le metriche di archiviazione di vecchi e nuovi](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Le soglie dovrà essere modificata in modo appropriato in quanto la nuova metrica disponibile è assoluto.

Le regole di avviso di classiche AnonymousThrottlingError e SASThrottlingError devono essere suddivisa in due nuovi avvisi, poiché non esiste alcuna metrica combinata che offre le stesse funzionalità. Le soglie dovrà essere adattato in modo appropriato.

## <a name="rollout-phases"></a>Fasi di implementazione

Lo strumento di migrazione è l'implementazione in fasi ai clienti che usano le regole di avviso di classiche. I proprietari delle sottoscrizioni riceverà un messaggio di posta elettronica quando la sottoscrizione è pronta per eseguire la migrazione usando lo strumento.

> [!NOTE]
> Poiché lo strumento viene implementato in fasi, si noterà che la maggior parte delle sottoscrizioni non sono ancora pronta per eseguire la migrazione durante le fasi iniziali.

Attualmente un sottoinsieme delle sottoscrizioni viene contrassegnato come pronto per la migrazione. Il subset include le sottoscrizioni con le regole di avviso di classiche solo nei seguenti tipi di risorse. Verrà aggiunto il supporto per più tipi di risorse in fasi imminente.

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

Qualsiasi utente che ha il ruolo predefinito collaboratore al monitoraggio a livello di sottoscrizione può attivare la migrazione. Gli utenti che dispongono di un ruolo personalizzato con le autorizzazioni seguenti possono attivare anche la migrazione:

- */lettura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Problemi comuni e soluzioni

Dopo aver [attivare la migrazione](alerts-using-migration-tool.md), si riceverà la posta elettronica all'indirizzo di specificato per ricevere una notifica che la migrazione è stata completata o se è necessaria alcuna azione da parte dell'utente. In questa sezione descrive alcuni problemi comuni e come gestirli.

### <a name="validation-failed"></a>Convalida non riuscita

A causa di alcune modifiche recenti alle regole di avviso classiche nella sottoscrizione, è Impossibile eseguire la migrazione della sottoscrizione. Questo problema è temporaneo. È possibile riavviare la migrazione dopo che lo stato di migrazione consente di tornare **pronto per la migrazione** in pochi giorni.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Ambito o criteri di blocco impedisce di eseguire la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi delle metriche e i nuovi gruppi di azioni e quindi le regole di avviso di classiche verranno eliminate. Tuttavia, vi è un criterio o un ambito di blocco impedisce la creazione di risorse. A seconda del blocco di criteri o ambito, potrebbe non essere migrate alcune o tutte le regole. È possibile risolvere questo problema rimuovendo temporaneamente il blocco dell'ambito o i criteri e attivare nuovamente la migrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Preparare la migrazione](alerts-prepare-migration.md)
