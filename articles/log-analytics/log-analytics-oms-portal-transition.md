---
title: Portale di che si sposta in Azure | Microsoft Docs
description: Il portale di OMS sta terminando il servizio con tutte le funzionalità che si trasferiscono nel portale di Azure. Questo articolo fornisce informazioni dettagliate su questa transizione.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: bwren
ms.openlocfilehash: 5719dc3719739fb561626e307ee295729752c1fa
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297754"
---
# <a name="oms-portal-moving-to-azure"></a>Portale di che si sposta in Azure
Grazie per aver usato il portale di OMS. L'azienda è stata consigliata dal supporto dei clienti e continuerà a investire molto sui servizi di monitoraggio e di gestione. Una parte del feedback ascoltato ripetutamente dai clienti è la necessità di un'esperienza di utente singolo per monitorare e gestire sia in locale sia i carichi di lavoro Azure. Probabilmente si sa che il portale di Azure è l'hub per tutti i servizi di Azure e offre un'avanzata esperienza di gestione con funzionalità come ad esempio i dashboard per il blocco delle risorse, la ricerca intelligente per le risorse di individuazione e assegnazione di tag per la gestione delle risorse. Per consolidare e semplificare il flusso di lavoro di monitoraggio e gestione, l'azienda ha iniziato ad aggiungere le funzionalità del portale di OMS al portale di Azure. Microsoft è lieta di annunciare che la maggior parte delle funzionalità del portale di OMS fanno ora parte del portale di Azure. In realtà, alcune delle nuove funzionalità, ad esempio Gestione traffico, sono disponibili solo nel portale di Azure. Sono presenti solo pochi gap rimanenti, le cose che hanno colpito di più sono stati i messaggi in cinque soluzioni che devono ancora essere spostati sul portale di Azure. Se non si usano queste funzionalità, l'utente sarà in grado di eseguire tutto ciò che era nel portale di OMS con il portale di Azure e molto altro. Se non è già stato fatto, è consigliabile iniziare a usare il portale di Azure oggi stesso! 

Si prevede di chiudere i gap restanti tra i due portali entro agosto 2018. In base al feedback dei clienti, l'azienda comunicherà la sequenza temporale per il ritiro del portale di OMS. L'azienda è entusiasta di passare al portale di Azure e prevede che la transizione sarà semplice. Ma è comprensibile che le modifiche siano complesse e che possano rivelarsi dannose. Inviare eventuali domande, feedback o preoccupazioni a LAUpgradeFeedback@microsoft.com. Il resto di questo articolo viene indirizzato su scenari chiave, i gap attuali e la Guida di orientamento per questa transizione. 


## <a name="what-will-change"></a>Quali modifiche verranno apportate? 
Vengono annunciate le seguenti modifiche con la deprecazione del portale di OMS. Ognuna di queste modifiche viene descritta più nei dettagli nelle sezioni seguenti.

- La nuova esperienza di gestione degli avvisi sostituirà la Soluzione Gestione avvisi.
- La gestione degli accessi utente sarà effettuata nel portale di Azure tramite il controllo degli accessi in base al ruolo di Azure.
- Il Connettore di Application Insights non è più necessario perché la stessa funzionalità può essere abilitata tramite le query tra aree di lavoro.
- L'App per dispositivi mobili OMS verrà deprecata. 
- La soluzione di sicurezza NSG viene sostituita con la funzionalità avanzata disponibile tramite una soluzione di Analisi del traffico.



## <a name="current-known-gaps"></a>Gap attualmente noti 
Esistono attualmente alcuni gap di funzionalità per i quali è necessario comunque usare il portale di OMS. Questi gap vengono chiusi e questo documento verrà aggiornato in modo appropriato. È necessario fare riferimento anche agli [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=log-analytics) per gli annunci in corso sulle estensioni e le modifiche.

- Le seguenti soluzioni non sono ancora completamente funzionali nel portale di Azure. È consigliabile continuare a usare queste soluzioni nel portale classico fino a quando non verranno aggiornate.

    - Soluzioni Windows Analytics ([Upgrade Readiness](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [Integrità dispositivi](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), e [Conformità aggiornamenti](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Analisi DNS](log-analytics-dns.md) 
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Per accedere alla risorsa Log Analytics in Azure, all'utente deve essere concesso l'accesso tramite l'[accesso basato sui ruoli di Azure](#user-access-and-role-migration).
- Le pianificazioni di aggiornamento che sono state create con il portale di OMS potrebbero non essere riflesse nelle distribuzioni di aggiornamenti pianificate o nella cronologia processo di aggiornamento del dashboard di gestione dell'aggiornamento nel portale di Azure. Questo gap dovrà essere colmato entro la fine del mese di giugno 2018.
- La funzionalità di anteprima dei log personalizzati può essere abilitata solo tramite il portale di OMS. Entro la fine del mese di giugno 2018, questo verrà attivato automaticamente per tutte le aree di lavoro.
 
## <a name="what-should-i-do-now"></a>Qual è il passo da fare ora?  
È consigliabile consultare [Domande frequenti per la transizione dal portale di OMS al portale di Azure per gli utenti di Log Analytics](../log-analytics/log-analytics-oms-portal-faq.md) per informazioni su come effettuare la transizione al portale di Azure. Se i [gap descritti in precedenza](#current-known-gaps) non sono applicabili al proprio ambiente, è necessario considerare l'avvio tramite il portale di Azure come esperienza primaria. Inviare eventuali commenti e suggerimenti, domande o problemi a LAUpgradeFeedback@microsoft.com.

## <a name="changes-to-alerts"></a>Modifiche apportate agli avvisi 

### <a name="alert-extension"></a>Estensione dell'avviso  
Gli avvisi sono in corso di [estensione al portale di Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Dopo questa procedura guidata, le azioni di gestione degli avvisi saranno disponibili solo nel portale di Azure. Gli avvisi esistenti continueranno a essere elencati nel portale di OMS, Se si accede agli avvisi a livello di codice tramite l'API REST per gli avvisi di Log Analytics o il modello risorse degli avvisi di Log Analytics sarà necessario usare i gruppi di azioni invece delle azioni nelle chiamate API, dei modelli di Azure Resource Manager e dei comandi di PowerShell.

### <a name="alert-management-solution"></a>soluzione Alert Management
Anziché la [soluzione di gestione di avviso](log-analytics-solution-alert-management.md), è possibile usare l'[interfaccia di avviso unificata del Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) per visualizzare e gestire gli avvisi. Questa nuova esperienza aggrega avvisi provenienti da più origini tra cui gli avvisi di registro di Azure da Log Analytics. È possibile vedere le distribuzioni degli avvisi, sfruttare i vantaggi del raggruppamento automatizzato degli avvisi correlati tramite gruppi smart e visualizzare gli avvisi tra più sottoscrizioni durante l'applicazione di filtri avanzati. Tutte queste funzionalità sono disponibili in anteprima di avvio il 4 giugno 2018. La soluzione di gestione degli avvisi non sarà disponibile nel portale di Azure. 

I dati raccolti dalla soluzione di gestione degli avvisi (record con un tipo di avviso) continuano a essere in Log Analytics fino a quando la soluzione è installata per l'area di lavoro. All'inizio di agosto 2018, il flusso di avvisi dagli avvisi unificati nelle aree di lavoro verrà abilitato, sostituendo questa funzionalità. Alcune modifiche dello schema sono previste e verranno annunciate in un secondo momento.

## <a name="user-access-and-role-migration"></a>Migrazione del ruolo e dell'accesso utente
La gestione di accesso al portale Azure è più completa e più potente rispetto all gestione di accesso nel portale di OMS, ma richiede alcune conversioni. Vedere [Gestire le aree di lavoro](log-analytics-manage-access.md#manage-accounts-and-users) per informazioni dettagliate sulla gestione dell'accesso in Log Analytics.

Inizia il 25 giugno e continua anche a luglio, verrà avviata la conversione automatica delle autorizzazioni per il controllo dell'accesso dal portale di OMS al portale di Azure. Una volta completata la conversione, la sezione di gestione utente del portale di OMS indirizzerà gli utenti a controllo di accesso (IAM) in Azure. 

Durante la conversione, il sistema verificherà ogni utente o gruppo di sicurezza che dispone delle autorizzazioni nel portale di OMS e determinerà se ha stesso livello o le autorizzazioni in Azure. Se mancano le autorizzazioni, si assegneranno i ruoli seguenti per le aree di lavoro pertinenti e le soluzioni.

| Autorizzazione del portale di OMS | Ruolo di Azure |
|:---|:---|
| ReadOnly | Lettore di Log Analytics |
| Collaboratore | Collaboratore di Log Analytics |
| Amministratore | Proprietario |

Per assicurarsi che agli utenti non vengano assegnate autorizzazioni eccessive, il sistema non assegnerà automaticamente queste autorizzazioni a livello di gruppo di risorse. Di conseguenza, gli amministratori dell'area di lavoro devono manualmente autoassegnarsi i ruoli di _proprietario_ oppure _collaboratore_ a livello di gruppo di risorse o di sottoscrizione per realizzare le azioni seguenti.

- Aggiungere o rimuovere soluzioni
- Definire nuove visualizzazioni personalizzate
- Gestisci avvisi 

In alcuni casi, la conversione automatica non può applicare le autorizzazioni e richiederà all'amministratore di assegnare manualmente le autorizzazioni.

## <a name="oms-mobile-app"></a>OMS Mobile App
L'app per dispositivi mobili OMS subirà la terminazione del servizio insieme al portale di OMS. Anziché l'app per dispositivi mobili OMS, per accedere alle informazioni sull'infrastruttura IT, sui dashboard e sulle query salvate, è possibile accedere al portale di Azure direttamente dal browser nel dispositivo mobile. Per ottenere avvisi, è necessario configurare i [Gruppi di azioni di Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) per ricevere le notifiche sotto forma di SMS o di chiamata vocale

## <a name="application-insights-connector-and-solution"></a>Connettore di Application Insights e soluzione
[Connettore di Application Insights](log-analytics-app-insights-connector.md) fornisce un modo per importare i dati di Application Insights in un'area di lavoro di Log Analytics. Questa duplicazione dei dati è stata necessaria per abilitare la visibilità tra i dati dell'infrastruttura e dell'applicazione.

Con il supporto delle [query tra risorse](log-analytics-cross-workspace-search.md), non sarà più presente questa necessità di duplicare i dati. Di conseguenza, la soluzione Application Insights esistente verrà deprecata. A partire da luglio, non sarà possibile collegare le nuove risorse di Application Insights alle Aree di lavoro di Log Analytics. I dashboard e i collegamenti esistenti continueranno a funzionare fino a novembre 2018.


## <a name="azure-network-security-group-analytics"></a>Analisi del gruppo di sicurezza di rete di Azure
La [soluzione Analisi gruppo di sicurezza di rete di Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) verrà sostituita con l'ultimo lancio [Analisi del traffico](../network-watcher/traffic-analytics.md) che offre visibilità nelle attività e nell'applicazione dell'utente su reti cloud. Analisi del traffico consente di controllare l'attività di rete dell'organizzazione, proteggere applicazioni e dati, ottimizzare le prestazioni dei carichi di lavoro e garantire la conformità. 

Questa soluzione consente di analizzare i Log del flusso del gruppo di sicurezza di rete e offre informazioni approfondite su quanto segue.

- Il flusso di traffico nelle reti tra Azure e Internet, nelle aree di cloud pubblico, nelle reti virtuali e nelle subnet
- Applicazioni e protocolli di rete, senza la necessità di sniffer o appliance di agenti di raccolta di flussi dedicati
- Talker principali, applicazioni con un livello di comunicazioni elevato, conversazioni tra macchine virtuali nel cloud e aree sensibili del traffico
- Origini e destinazioni del traffico su reti virtuali, interrelazioni tra applicazioni e servizi aziendali critici
- La sicurezza include il traffico dannoso, le porte aperte a Internet, le applicazioni o macchine virtuali che tentano di accedere a Internet.
- Utilizzo della capacità, che consente di eliminare i problemi di provisioning o sottoutilizzo.

È possibile continuare a fare affidamento sulle impostazioni di diagnostica per inviare i log di NSG a Log Analytics Log in modo che le ricerche avanzate, gli avvisi, i dashboard esistenti continueranno a funzionare correttamente. I clienti che hanno già installato la soluzione possono continuare a usarla fino al prossimo avviso. Dal 20 giugno la soluzione NSG verrà rimossa dal marketplace e resa disponibile tramite la community come un [modello di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="next-steps"></a>Passaggi successivi
- Consultare [Domande frequenti per la transizione dal portale di OMS al portale di Azure per gli utenti di Log Analytics](log-analytics-oms-portal-faq.md) per il materiale sussidiario sullo spostamento dal portale di OMS al portale di Azure.