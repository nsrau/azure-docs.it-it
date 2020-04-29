---
title: Riduzione delle prestazioni nel rapporto gravità della traccia-applicazione Azure Insights
description: Monitora le tracce dell'applicazione con applicazione Azure Insights per modelli insoliti nella telemetria delle tracce con rilevamento intelligente.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671682"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradazione del rapporto tra i livelli di gravità delle tracce (anteprima)

Le tracce vengono usate di frequente nelle applicazioni, perché permettono di comprendere cosa succede dietro le quinte. In caso di problemi, le tracce offrono una visibilità essenziale della sequenza degli eventi che generano lo stato indesiderato. Sebbene siano in genere non strutturate, le tracce indicano un'informazione concreta, cioè il loro livello di gravità. Nello stato stazionario di un'applicazione, il rapporto tra le tracce "positive" (*Informazioni* e *Dettagli*) e le tracce "negative" (*Avviso*, *Errore* e *Critico*) rimane stabile. L'ipotesi è che le tracce "negative" possano verificarsi a intervalli regolari per diverse ragioni (ad esempio problemi di rete temporanei). Tuttavia, quando un problema reale inizia a diventare sempre più importante, si verifica in genere un aumento della percentuale di tracce "negative" rispetto alle tracce "positive". Il Rilevamento intelligente in Application Insights analizza automaticamente le tracce registrate dall'applicazione e avvisa l'utente se nel livello di gravità dei dati di telemetria relativi alle tracce vi sono modelli anomali.

Questa funzionalità non richiede una configurazione speciale, oltre a quella della registrazione delle tracce per la propria app (vedere come configurare un listener di log di traccia per [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) o [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). È attiva quando l'app genera un numero sufficiente di dati di telemetria per le eccezioni.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
Questo tipo di notifica può essere visualizzato se il rapporto tra le tracce "valide" (tracce registrate con un livello di *informazioni* o *dettagliato*) e le tracce "negative" (tracce registrate con un livello di *avviso*, *errore*o *irreversibile*) viene degradato in un giorno specifico, rispetto a una baseline calcolata nei sette giorni precedenti.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Sebbene un deterioramento del rapporto tra le tracce "positive" e "negative" può indicare una problema a livello applicativo, questa modifica nel rapporto potrebbe essere benigna. Ad esempio, l'aumento potrebbe essere dovuto a un nuovo flusso nell'applicazione che emette più tracce "negative" rispetto ai flussi esistenti.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche includono informazioni di diagnostica a supporto del processo di diagnostica:
1. **Valutazione.** La notifica mostra il numero di operazioni interessate. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il problema interessa solo alcune operazioni o tutto il traffico? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** È possibile usare gli elementi e i report correlati che rimandano a informazioni di supporto, per diagnosticare meglio il problema.


