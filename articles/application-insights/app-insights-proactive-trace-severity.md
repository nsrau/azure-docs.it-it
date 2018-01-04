---
title: "Rilevamento - degradazione nel rapporto di gravità di traccia, in Azure Application Insights smart | Documenti Microsoft"
description: Monitorare le tracce di applicazione con Azure Application Insights per insolito modelli nei dati di telemetria di traccia.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Riduzione delle proporzioni di gravità di traccia (anteprima)

Le tracce vengono ampiamente utilizzate nelle applicazioni, in quanto sono storia di ciò che accade dietro le quinte. In caso di errori, le tracce forniscono visibilità fondamentale la sequenza degli eventi che hanno uno stato indesiderato. Mentre le tracce vengono in genere non strutturate, c'è una cosa che può essere appresi concretamente da essi: il livello di gravità. Nello stato stazionario di un'applicazione, è previsto il rapporto tra le tracce "positiva" (*Info* e *Verbose*) e le tracce "non valida" (*avviso*, *errore* e *critico*) rimane stabile. Il presupposto è che le tracce "errate" possono verificarsi a intervalli regolari per un certo punto a causa di una serie di motivi (ad esempio problemi di rete temporanea). Tuttavia, quando un problema reale inizia in crescita, in genere manifesti come un aumento della quota relativa di tracce di tracce "errati" vs "positiva". Application Insights Smart rilevamento automaticamente consente di analizzare le tracce registrate dall'applicazione e visualizza un avviso sui modelli di insolito nel livello di gravità dei dati di telemetria di traccia.

Questa funzionalità non richiede alcuna configurazione speciale, diverso da configurare la registrazione della traccia per l'app (come configurare un listener di log di traccia per [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) o [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). È attiva quando l'applicazione genera dati di telemetria sufficienti eccezione.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando otterrebbe questo tipo di notifica di rilevamento intelligente
È possibile ottenere questo tipo di notifica se il rapporto tra le tracce "positiva" (tracce registrate con un livello di *Info* o *Verbose*) e le tracce "non valida" (tracce registrate con un livello di *avviso*, *, Errore o *errore irreversibile*) è compromettere le prestazioni in un determinato giorno, rispetto a una linea di base calcolata in sette giorni precedenti.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Sebbene un calo del rapporto tra "positiva" e "errati" tracce potrebbe indicare problemi di un'applicazione, questa modifica nel rapporto potrebbe essere innocui. Ad esempio, l'aumento potrebbe essere dovuto a un nuovo flusso dell'applicazione di creazione di altre tracce "errate" rispetto ai flussi esistenti).

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche riguardano informazioni di diagnostica per il supporto nel processo di diagnostica:
1. **Valutazione.** La notifica viene illustrato il numero di operazioni è interessato. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il problema interessa tutto il traffico o solo alcune operazioni? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** È possibile utilizzare gli elementi correlati e i report collegamento a informazioni che consentono inoltre di supporto di diagnosticare il problema.


