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
ms.openlocfilehash: 08991565d56ffbf7d798944f108a1b86e4463c58
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32176287"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Monitoraggio dell'utilizzo e dei costi stimati

La pagina **Utilizzo e costi stimati** dell'hub Monitoraggio del portale di Azure descrive l'utilizzo delle funzionalità di monitoraggio di base, come [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Per i clienti con piani tariffari disponibili prima di aprile 2018, è incluso anche l'utilizzo di Log Analytics acquistato tramite l'offerta Insights e Analytics.

In questa pagina gli utenti possono visualizzare l'utilizzo delle relative risorse per gli ultimi 31 giorni, aggregate per sottoscrizione. Gli approfondimenti mostrano le tendenze di utilizzo per il periodo di 31 giorni. Per effettuare questa stima, è necessario assemblare una grande quantità di dati. Attendere con pazienza il caricamento della pagina.

Questo esempio mostra il monitoraggio dell'utilizzo, con una stima dei costi risultanti:

![Schermata del portale Utilizzo e costi stimati](./media/monitoring-usage-and-estimated-costs/001.png)

Selezionare il collegamento nella colonna relativa all'utilizzo mensile per aprire un grafico che mostra le tendenze di utilizzo negli ultimi 31 giorni:

![Schermata con grafico a barre Inclusi per nodo](./media/monitoring-usage-and-estimated-costs/002.png)

Di seguito è riportato un altro riepilogo analogo dell'utilizzo e dei costi. Questo esempio mostra una sottoscrizione nel nuovo modello tariffario in base al consumo di aprile 2018. Si noti la mancanza di fatturazione in base ai nodi. L'inserimento e la conservazione dati per Log Analytics e Application Insights vengono ora segnalati con un nuovo contatore comune.

![Schermata del portale Utilizzo e costi stimati - Modello tariffario di aprile 2018](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nuovo modello tariffario

Ad aprile 2018 è stato rilasciato un [nuovo modello tariffario per il servizio di monitoraggio](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/),  che prevede la determinazione di prezzi in base al consumo e pensati per il cloud. Si paga solo per le risorse usate, senza vincoli legati ai nodi. Il nuovo modello tariffario fornisce informazioni dettagliate per [avvisi, metriche, notifiche](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Per l'onboarding dei clienti in Log Analytics o Application Insights dopo il 2 aprile 2018, il nuovo modello tariffario è l'unica opzione disponibile. Per i clienti che usano già questi servizi, il passaggio al nuovo modello tariffario è facoltativo.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Valutazione dell'impatto del nuovo modello tariffario
Il nuovo modello di determinazione dei prezzi avrà ripercussioni diverse su ogni cliente in base ai relativi criteri di monitoraggio dell'utilizzo. Per i clienti che usavano già Log Analytics o Application Insights prima del 2 aprile 2018, la pagina **Utilizzo e costi stimati** in Monitoraggio di Azure consente di stimare qualsiasi modifica dei costi in caso di passaggio al nuovo modello tariffario. Consente inoltre di spostare una sottoscrizione nel nuovo modello. Per la maggior parte dei clienti il nuovo modello di determinazione dei prezzi sarà vantaggioso. Per i clienti con modelli di utilizzo dei dati particolarmente elevati o che si trovano in aree con costi più elevati potrebbe non essere la scelta più appropriata.

Per visualizzare una stima dei costi per le sottoscrizioni scelte nella pagina **Utilizzo e costi stimati**, selezionare l'intestazione blu nella parte superiore della pagina. È consigliabile eseguire questa operazione una sottoscrizione alla volta, dal momento che è il livello in corrispondenza del quale può essere adottato il nuovo modello tariffario.

![Schermata di monitoraggio dell'utilizzo e dei costi stimati nel nuovo modello tariffario](./media/monitoring-usage-and-estimated-costs/004.png)

La nuova pagina mostra una versione simile della pagina precedente con un'intestazione di colore verde:

![Schermata di monitoraggio dell'utilizzo e dei costi stimati nell'attuale modello tariffario](./media/monitoring-usage-and-estimated-costs/005.png)

La pagina mostra anche un set diverso di contatori, corrispondenti al nuovo modello tariffario. Questo elenco è un esempio:

- Insight & Analytics\Eccedenza per nodo
- Insight & Analytics\Inclusi per nodo
- Application Insights\Dati in eccedenza Basic
- Application Insights\Dati inclusi

Il nuovo modello tariffario non ha allocazioni di dati inclusi in base ai nodi. Di conseguenza, questi contatori per l'inserimento dati vengono combinati in un nuovo contatore comune per l'inserimento dati chiamato **Servizi condivisi\Inserimento dati**. 

È presente un'altra modifica ai dati inseriti in Log Analytics o Application Insights in aree con costi più elevati. I dati per tali aree con costi elevati verranno visualizzati con i nuovi contatori a livello di area. Un esempio è **Inserimento dati (Stati Uniti centro-occidentali)**.

> [!NOTE]
> I costi stimati per sottoscrizione non vengono presi in considerazione nei diritti per nodo a livello di account della sottoscrizione Operations Management Suite (OMS). Contattare il rappresentante Microsoft per una discussione più approfondita del nuovo modello tariffario in un caso come questo.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nuovo modello tariffario e diritti della sottoscrizione di Operations Management Suite

I clienti che hanno acquistato Microsoft Operations Management Suite E1 ed E2 possono usufruire dei diritti di inserimento dati per nodo per [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Per ricevere questi diritti per le aree di lavoro di Log Analytics o le risorse di Application Insights in una determinata sottoscrizione: 

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
> Ecco una nota importante se si usa Azure Resource Manager o PowerShell per distribuire [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) o [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) in una sottoscrizione passata a un nuovo modello tariffario. Se si specifica un piano tariffario diverso da "pergb2018" per Log Analytics o "Basic" per Application Insights, anziché generare un errore di distribuzione a causa della specifica di un piano tariffario non valido, l'operazione verrà eseguita con esito positivo **ma usando solo il piano tariffario valido**. 
>

## <a name="moving-to-the-new-pricing-model"></a>Passaggio al nuovo modello tariffario

Se si è deciso di adottare il nuovo modello di determinazione dei prezzi per una sottoscrizione, selezionare l'opzione **Selezione modello di prezzi** nella parte superiore della pagina **Utilizzo e costi stimati**:

![Schermata di monitoraggio dell'utilizzo e dei costi stimati nel nuovo modello tariffario](./media/monitoring-usage-and-estimated-costs/006.png)

Verrà aperta la pagina **Selezione modello di prezzi**, che elenca ognuna delle sottoscrizioni visualizzate nella pagina precedente:

![Schermata Selezione modello di prezzi](./media/monitoring-usage-and-estimated-costs/007.png)

Per spostare una sottoscrizione nel nuovo modello tariffario, è sufficiente selezionare la casella e quindi selezionare **Salva**. È possibile tornare al modello tariffario precedente nello stesso modo. Tenere presente che sono richieste autorizzazioni di proprietario della sottoscrizione o di collaboratore per modificare il modello tariffario.
