---
title: Incremento anomalo del volume di eccezioni applicazione Azure informazioni dettagliate
description: Monitora le eccezioni dell'applicazione con rilevamento intelligente in applicazione Azure Insights per modelli insoliti nel volume delle eccezioni.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499427"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anomalo nel volume delle eccezioni (anteprima)

Application Insights analizza automaticamente le eccezioni generate nell'applicazione e può avvisare in caso di modelli insoliti nei dati di telemetria delle eccezioni.

Questa funzionalità non richiede una configurazione specifica, a parte la [configurazione della segnalazione delle eccezioni](./asp-net-exceptions.md#set-up-exception-reporting) per l'app. È attiva quando l'app genera un numero sufficiente di dati di telemetria per le eccezioni.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando si riceve questo tipo di notifica di rilevamento intelligente?
Si riceve questo tipo di notifica se l'app presenta un aumento anomalo del numero di eccezioni di un tipo specifico in un giorno, in confronto a una linea di base calcolata nei sette giorni precedenti.
Vengono utilizzati algoritmi di Machine Learning per rilevare l'aumento del numero di eccezioni, tenendo conto di una crescita naturale nell'uso dell'applicazione.

## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app
Una notifica non significa che l'app ha sicuramente un problema. Anche se un numero eccessivo di eccezioni indica in genere problemi nell'applicazione, le eccezioni potrebbero essere innocue e gestite correttamente dall'applicazione.

## <a name="how-do-i-fix-it"></a>Risoluzione
Le notifiche includono informazioni di diagnostica a supporto del processo di diagnostica:
1. **Valutazione.** La notifica mostra il numero di utenti o di richieste interessate. Ciò consente di assegnare una priorità al problema.
2. **Ambito.** Il problema interessa solo alcune operazioni o tutto il traffico? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosticare.** Il rilevamento contiene informazioni sul metodo da cui è stata generata l'eccezione, nonché il tipo di eccezione. È anche possibile usare gli elementi e i report correlati che rimandano a informazioni di supporto, per diagnosticare meglio il problema.
