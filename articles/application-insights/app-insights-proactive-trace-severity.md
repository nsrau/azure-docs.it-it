---
title: "Rilevamento intelligente - Degradazione del rapporto tra i livelli di gravità delle tracce, in Azure Application Insights | Microsoft Docs"
description: Monitorare le tracce delle applicazioni con Azure Application Insights per individuare i modelli anomali nei dati di telemetria relativi alle tracce.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 29ed195dadb7230df425d6d981a0a482e09ee79f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradazione del rapporto tra i livelli di gravità delle tracce (anteprima)

Le tracce vengono usate di frequente nelle applicazioni, perché permettono di comprendere cosa succede dietro le quinte. In caso di problemi, le tracce offrono una visibilità essenziale della sequenza degli eventi che generano lo stato indesiderato. Sebbene siano in genere non strutturate, le tracce indicano un'informazione concreta, cioè il loro livello di gravità. Nello stato stazionario di un'applicazione, il rapporto tra le tracce "positive" (*Info* (Informazioni) e *Verbose* (Commenti)) e le tracce "negative" (*Avviso*, *Errore* e *Critico*) rimane stabile. L'ipotesi è che le tracce "negative" possano verificarsi a intervalli regolari per diverse ragioni (ad esempio problemi di rete temporanei). Tuttavia, quando un problema reale inizia a diventare sempre più importante, si verifica in genere un aumento della percentuale di tracce "negative" rispetto alle tracce "positive". Il Rilevamento intelligente in Application Insights analizza automaticamente le tracce registrate dall'applicazione e avvisa l'utente se nel livello di gravità dei dati di telemetria relativi alle tracce vi sono modelli anomali.

Questa funzionalità non richiede una configurazione speciale, oltre a quella della registrazione delle tracce per la propria app (vedere come configurare un listener di log di traccia per [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) o [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). È attiva quando l'app genera un numero sufficiente di dati di telemetria per le eccezioni.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
È possibile ottenere questo tipo di notifica se il rapporto tra le tracce "positive" (tracce registrate con un livello di *Info* (Informazioni) e *Verbose* (Commenti)) e le tracce "negative" (tracce registrate con un livello di *Avviso*, *Errore o *Errore irreversibile*) viene compromesso in un giorno specifico, rispetto a un valore di riferimento calcolato nei sette giorni precedenti.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Sebbene un deterioramento del rapporto tra le tracce "positive" e "negative" può indicare una problema a livello applicativo, questa modifica nel rapporto potrebbe essere benigna. Ad esempio, l'aumento potrebbe essere dovuto a un nuovo flusso nell'applicazione che emette più tracce "negative" rispetto ai flussi esistenti.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche includono informazioni di diagnostica a supporto del processo di diagnostica:
1. **Valutazione.** La notifica mostra il numero di operazioni interessate. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il problema interessa solo alcune operazioni o tutto il traffico? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi.** È possibile usare gli elementi e i report correlati che rimandano a informazioni di supporto, per diagnosticare meglio il problema.


