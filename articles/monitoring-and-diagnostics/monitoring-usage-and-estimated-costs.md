---
title: Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure | Microsoft Docs
description: Panoramica dell'uso della pagina Utilizzo e costi stimati di Monitoraggio di Azure
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitoraggio dell'utilizzo e dei costi stimati

La pagina **Utilizzo e costi stimati** dell'hub Monitoraggio del portale di Azure è pensata espressamente per fornire informazioni utili sull'utilizzo delle funzionalità di monitoraggio di base, come [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Per i clienti con piani tariffari disponibili prima di aprile 2018, è incluso anche l'utilizzo di Log Analytics acquistato tramite l'offerta Insights e Analytics.

In questa pagina gli utenti possono visualizzare l'utilizzo di queste risorse per gli ultimi 31 giorni, aggregate per sottoscrizione, con approfondimenti per visualizzare la tendenza di utilizzo per uno specifico periodo. Per effettuare questa stima, è necessario assemblare una grande quantità di dati. Attendere con pazienza il caricamento della pagina.
Di seguito è riportato un esempio di monitoraggio dell'utilizzo, con una stima dei costi risultanti:

![Schermata del portale Utilizzo e costi stimati](./media/monitoring-usage-and-estimated-costs/001.png)

Facendo clic sul collegamento nella colonna Utilizzo mensile, verrà aperto un grafico che mostra le tendenze di utilizzo negli ultimi 31 giorni:

![Schermata Inclusi per nodo 671,47 GB](./media/monitoring-usage-and-estimated-costs/002.png)

Di seguito è riportato un altro riepilogo analogo dell'utilizzo e dei costi, in questo caso per una sottoscrizione nel nuovo modello tariffario in base al consumo di aprile 2018. Si noti la mancanza di fatturazione in base ai nodi e che l'inserimento e la conservazione dati per Log Analytics e Application Insights vengono ora segnalati con un nuovo contatore comune.

![Schermata del portale Utilizzo e costi stimati](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nuovo modello tariffario

Nel mese di aprile 2018, è stato rilasciato un nuovo modello tariffario per il servizio di monitoraggio,  che prevede la determinazione di prezzi in base al consumo e pensati per il cloud. Si paga solo per le risorse usate, senza vincoli legati ai nodi. Il nuovo modello tariffario fornisce informazioni dettagliate per [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Per l'onboarding dei clienti in Log Analytics o Application Insights dopo il 2 aprile 2018, il nuovo modello tariffario è l'unica opzione disponibile. Per i clienti che hanno già usato questi servizi, il passaggio al nuovo modello tariffario è facoltativo.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Valutazione dell'impatto del nuovo modello tariffario

Il nuovo modello di determinazione dei prezzi avrà ripercussioni diverse per ogni cliente in base ai relativi criteri di utilizzo del monitoraggio. Per i clienti che hanno già usato Log Analytics o Application Insights prima del 2 aprile 2018, la pagina **Utilizzo e costi stimati** in Monitoraggio di Azure offre gli strumenti per stimare qualsiasi modifica dei costi in caso di passaggio al nuovo modello tariffario e consente di spostare una sottoscrizione nel nuovo modello. Per la maggior parte dei clienti il nuovo modello di determinazione dei prezzi sarà vantaggioso, ma per i clienti con modelli di utilizzo dei dati particolarmente elevati o che si trovano in aree con costi più elevati potrebbe non essere la scelta più appropriata.

Per visualizzare una stima dei costi per le sottoscrizioni selezionate nella pagina **Utilizzo e costi stimati**, fare clic sull'intestazione blu nella parte superiore della pagina. È consigliabile eseguire questa operazione una sottoscrizione alla volta, dal momento che è il livello in corrispondenza del quale può essere adottato il nuovo modello tariffario.

![Schermata Selezione modello di prezzi](./media/monitoring-usage-and-estimated-costs/004.png)

A questo punto verrà visualizzata una versione analoga di questa pagina con un'intestazione di colore verde:

![Schermata Selezione modello di prezzi](./media/monitoring-usage-and-estimated-costs/005.png)

Si noterà un set diverso di contatori, corrispondenti al nuovo modello tariffario. Ad esempio, contatori per l'inserimento dati quali

1. Insight & Analytics\Eccedenza per nodo
2. Insight & Analytics\Inclusi per nodo
3. Application Insights\Dati in eccedenza Basic
4. Application Insights\Dati inclusi

vengono combinati in un nuovo contatore comune per l'inserimento dati chiamato **Servizi condivisi\Inserimento dati** poiché il nuovo modello tariffario non ha diritti per i dati inclusi per nodo.

Un'altra modifica che si noterà è che i dati inseriti in Log Analytics o Application Insights in aree con costi più elevati verranno visualizzati con i nuovi contatori a livello di area in modo da riflettere correttamente questa differenza, ad esempio **Inserimento dati (Stati Uniti centro-occidentali)**.

> [!NOTE]
> I costi stimati per sottoscrizione non vengono presi in considerazione nei diritti per nodo a livello di account della sottoscrizione Operations Management Suite (OMS). Contattare il rappresentante Microsoft per una discussione più approfondita del nuovo modello tariffario in un caso come questo.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nuovo modello tariffario e diritti della sottoscrizione di Operations Management Suite

I clienti che hanno acquistato Microsoft Operations Management Suite E1 ed E2 possono usufruire dei diritti di inserimento dati per nodo per [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Per ricevere questi diritti per le aree di lavoro di Log Analytics o le risorse di Application Insights in una determinata sottoscrizione, il modello tariffario della sottoscrizione deve essere un modello antecedente ad aprile 2018 in cui sono disponibili il piano tariffario "Per nodo (OMS)" di Log Analytics e il piano tariffario "Enterprise" di Application Insights. A seconda del numero di nodi della suite acquistata dall'organizzazione, potrebbe essere vantaggioso spostare alcune sottoscrizioni al nuovo modello tariffario, ma è necessario fare prima un'attenta valutazione. 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Modifiche in caso di passaggio al nuovo modello tariffario

Lo spostamento di una sottoscrizione nel nuovo modello di determinazione dei prezzi modificherà il piano tariffario per ogni sessione di Log Analytics in un nuovo livello per GB e comporterà uno spostamento generale ("pergb2018" in Azure Resource Manager). Questo passaggio modificherà anche eventuali risorse di Application Insights nel piano Enterprise in piano Basic. Gli effetti di queste modifiche vengono visualizzati nella stima dei costi riportata in precedenza. 

## <a name="moving-to-the-new-pricing-model"></a>Passaggio al nuovo modello tariffario

Se si è deciso di adottare il nuovo modello di determinazione dei prezzi per una sottoscrizione, fare clic sull'opzione **Selezione modello di prezzi** nella parte superiore della pagina **Utilizzo e costi stimati**:

![Schermata di Monitoraggio - Utilizzo e costi stimati nel nuovo modello tariffario](./media/monitoring-usage-and-estimated-costs/006.png)

Verrà aperta la pagina **Selezione modello di prezzi**, che elenca ognuna delle sottoscrizioni visualizzate nella pagina precedente:

![Schermata Selezione modello di prezzi](./media/monitoring-usage-and-estimated-costs/007.png)

Per spostare una sottoscrizione nel nuovo modello tariffario, è sufficiente selezionare la casella e fare clic su **Salva**.  È possibile tornare al modello tariffario precedente nello stesso modo. Tenere presente che sono richieste autorizzazioni di proprietario della sottoscrizione o di collaboratore per modificare il modello tariffario.
