---
title: Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure
description: Panoramica dell'uso della pagina Utilizzo e costi stimati di Monitoraggio di Azure
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936616"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per i diversi modelli tariffari. Gli articoli correlati per i componenti specifici di monitoraggio di Azure includono:
> - [Gestire l'utilizzo e i costi con i log di monitoraggio di Azure](manage-cost-storage.md) descrive come controllare i costi modificando il periodo di conservazione dei dati e come analizzare e inviare avvisi sull'utilizzo dei dati.
> - [Gestire l'utilizzo e i costi per Application Insights](../../azure-monitor/app/pricing.md) descrive come analizzare l'utilizzo dei dati in Application Insights.

La pagina **Utilizzo e costi stimati** dell'hub Monitoraggio del portale di Azure descrive l'utilizzo delle funzionalità di monitoraggio di base, come [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Per i clienti con piani tariffari disponibili prima di aprile 2018, è incluso anche l'utilizzo di Log Analytics acquistato tramite l'offerta Insights e Analytics.

In questa pagina gli utenti possono visualizzare l'utilizzo delle relative risorse per gli ultimi 31 giorni, aggregate per sottoscrizione. Gli approfondimenti mostrano le tendenze di utilizzo per il periodo di 31 giorni. Per effettuare questa stima, è necessario assemblare una grande quantità di dati. Attendere con pazienza il caricamento della pagina.

Questo esempio mostra il monitoraggio dell'utilizzo, con una stima dei costi risultanti:

![Schermata del portale Utilizzo e costi stimati](./media/usage-estimated-costs/001.png)

Selezionare il collegamento nella colonna relativa all'utilizzo mensile per aprire un grafico che mostra le tendenze di utilizzo negli ultimi 31 giorni:

![Schermata con grafico a barre Inclusi per nodo](./media/usage-estimated-costs/002.png)

Di seguito è riportato un altro riepilogo analogo dell'utilizzo e dei costi. Questo esempio mostra una sottoscrizione nel nuovo modello tariffario in base al consumo di aprile 2018. Si noti la mancanza di fatturazione in base ai nodi. L'inserimento e la conservazione dati per Log Analytics e Application Insights vengono ora segnalati con un nuovo contatore comune.

![Schermata del portale Utilizzo e costi stimati - Modello tariffario di aprile 2018](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>Modello di prezzi

Ad aprile 2018 è stato rilasciato un [nuovo modello tariffario per il servizio di monitoraggio](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/),  Questa funzionalità offre prezzi semplici e basati sul consumo (con pagamento in base al consumo). Si paga solo per le risorse usate, senza vincoli legati ai nodi. Il nuovo modello tariffario fornisce informazioni dettagliate per [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Oltre al modello con pagamento in base al consumo, nel settembre 2019 sono state aggiunte le prenotazioni di capacità per Log Analytics che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al consumo. I prezzi per la prenotazione della capacità ti permettono di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. [Altre](https://azure.microsoft.com/pricing/details/monitor/) informazioni sui prezzi di prenotazione della capacità.

Per l'onboarding dei clienti in Log Analytics o Application Insights dopo il 2 aprile 2018, il nuovo modello tariffario è l'unica opzione disponibile. Per i clienti che usano già questi servizi, il passaggio al nuovo modello tariffario è facoltativo.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Valutazione dell'impatto del nuovo modello tariffario
Il nuovo modello di determinazione dei prezzi avrà ripercussioni diverse su ogni cliente in base ai relativi criteri di monitoraggio dell'utilizzo. Per i clienti che usavano già Log Analytics o Application Insights prima del 2 aprile 2018, la pagina **Utilizzo e costi stimati** in Monitoraggio di Azure consente di stimare qualsiasi modifica dei costi in caso di passaggio al nuovo modello tariffario. Consente inoltre di spostare una sottoscrizione nel nuovo modello. Per la maggior parte dei clienti il nuovo modello di determinazione dei prezzi sarà vantaggioso. Per i clienti con modelli di utilizzo dei dati particolarmente elevati o che si trovano in aree con costi più elevati potrebbe non essere la scelta più appropriata.

Per visualizzare una stima dei costi per le sottoscrizioni scelte nella pagina **Utilizzo e costi stimati**, selezionare l'intestazione blu nella parte superiore della pagina. È consigliabile eseguire questa operazione una sottoscrizione alla volta, dal momento che è il livello in corrispondenza del quale può essere adottato il nuovo modello tariffario.

![Schermata di monitoraggio dell'utilizzo e dei costi stimati nel nuovo modello tariffario](./media/usage-estimated-costs/004.png)

La nuova pagina mostra una versione simile della pagina precedente con un'intestazione di colore verde:

![Schermata di monitoraggio dell'utilizzo e dei costi stimati nell'attuale modello tariffario](./media/usage-estimated-costs/005.png)

La pagina mostra anche un set diverso di contatori, corrispondenti al nuovo modello tariffario. Questo elenco è un esempio:

- Informazioni dettagliate e analisi\Eccedenza per nodo
- Informazioni dettagliate e analisi\Inclusi per nodo
- Application Insights\Dati in eccedenza Basic
- Application Insights\Dati inclusi

Il nuovo modello tariffario non ha allocazioni di dati inclusi in base ai nodi. Di conseguenza, questi contatori per l'inserimento dati vengono combinati in un nuovo contatore comune per l'inserimento dati chiamato **Servizi condivisi\Inserimento dati**. 

È presente un'altra modifica ai dati inseriti in Log Analytics o Application Insights in aree con costi più elevati. I dati per tali aree con costi elevati verranno visualizzati con i nuovi contatori a livello di area. Un esempio è **Inserimento dati (Stati Uniti centro-occidentali)** .

> [!NOTE]
> I costi stimati per sottoscrizione non vengono presi in considerazione nei diritti per nodo a livello di account della sottoscrizione Operations Management Suite (OMS). Contattare il rappresentante Microsoft per una discussione più approfondita del nuovo modello tariffario in un caso come questo.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nuovo modello tariffario e diritti della sottoscrizione di Operations Management Suite

I clienti che hanno acquistato Microsoft Operations Management Suite E1 ed E2 possono usufruire dei diritti di inserimento dati per nodo per [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Per ricevere questi diritti per le aree di lavoro di Log Analytics o le risorse di Application Insights in una determinata sottoscrizione: 

- Il modello tariffario della sottoscrizione deve essere antecedente ad aprile 2018.
- Le aree di lavoro di Log Analytics devono usare il piano tariffario "Per nodo (OMS)".
- Le risorse di Application Insights devono usare il piano tariffario "Enterprise".

A seconda del numero di nodi della suite acquistata dall'organizzazione, potrebbe essere vantaggioso spostare alcune sottoscrizioni al nuovo modello tariffario, ma è necessario fare prima un'attenta valutazione. In generale è consigliabile mantenere il modello 2018 antecedente ad aprile, come descritto in precedenza.

> [!WARNING]
> Se la propria organizzazione ha acquistato Microsoft Operations Management Suite E1 ed E2, in genere è consigliabile mantenere le sottoscrizioni nel modello tariffario 2018 antecedente ad aprile. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Modifiche in caso di passaggio al nuovo modello tariffario

Il nuovo modello tariffario semplifica le opzioni dei prezzi di Log Analytics e Application Insights a un singolo piano. Lo spostamento di una sottoscrizione al nuovo modello tariffario determinerà:

- La modifica del piano tariffario per ogni sessione di Log Analytics in un nuovo livello per GB, denominato "pergb2018" in Azure Resource Manager.
- Questo passaggio modificherà anche eventuali risorse di Application Insights nel piano Enterprise in piano Basic.

La stima dei costi mostra gli effetti di queste modifiche.

> [!WARNING]
> Ecco una nota importante se si usa Azure Resource Manager o PowerShell per distribuire [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) o [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) in una sottoscrizione passata a un nuovo modello tariffario. Se si specifica un piano tariffario diverso da "pergb2018" per Log Analytics o "Basic" per Application Insights, anziché generare un errore di distribuzione a causa della specifica di un piano tariffario non valido, l'operazione verrà eseguita con esito positivo **ma usando solo il piano tariffario valido**. Ciò non si applica al livello gratuito di Log Analytics dove viene generato un messaggio di piano tariffario non valido.
>

## <a name="moving-to-the-new-pricing-model"></a>Passaggio al nuovo modello tariffario

Se si è deciso di adottare il nuovo modello di determinazione dei prezzi per una determinata sottoscrizione, passare a ogni risorsa Application Insights, aprire l' **utilizzo e i costi stimati** e assicurarsi che si trovi nel piano tariffario Basic e passare a ogni area di lavoro log Analytics, aprire ogniPagina del piano tariffario e passare al piano tariffario **per GB (2018)** . 

> [!NOTE]
> Il requisito che tutti i Application Insights risorse e le aree di lavoro Log Analytics all'interno di una determinata sottoscrizione adotti il modello di determinazione dei prezzi più recente è stato rimosso, consentendo una maggiore flessibilità e una configurazione più semplice. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizzare il passaggio al nuovo modello di prezzi

Come indicato in precedenza, non è più necessario spostare contemporaneamente tutte le risorse di monitoraggio in una sottoscrizione al nuovo modello di determinazione dei prezzi, quindi l'azione ``migratetonewpricingmodel`` non avrà più alcun effetto. A questo punto è possibile spostare le risorse Application Insights e le aree di lavoro Log Analytics separatamente nei piani tariffari più recenti.  

L'automazione di questa modifica è documentata per Application Insights usando [set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) con ``-PricingPlan "Basic"`` e log Analytics usando [set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) con ``-sku "PerGB2018"``. 
