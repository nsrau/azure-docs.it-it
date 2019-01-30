---
title: Creazione di avvisi con soglie dinamiche in Monitoraggio di Azure
description: Creare avvisi con apprendimento automatico in base a soglie dinamiche
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449011"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Avvisi delle metriche con soglie dinamiche in Monitoraggio di Azure (anteprima pubblica)

La funzionalità Avviso metrica con rilevamento di soglie dinamiche sfrutta la tecnologia avanzata di Machine Learning (ML) per ottenere informazioni sul comportamento delle metriche nel tempo e identificare modelli e anomalie che indicano possibili problemi del servizio. Offre un'interfaccia utente semplice e supporto per operazioni su larga scala, consentendo agli utenti di configurare regole di avviso tramite l'API di Azure Resource Manager, in modo completamente automatico.

Una volta creata, una regola di avviso viene attivata solo quando la metrica monitorata non si comporta come previsto, in base alle relative soglie personalizzate.

Il feedback degli utenti è bene accetto ed è possibile inviarlo all'indirizzo azurealertsfeedback@microsoft.com.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Perché e quando è consigliabile usare condizioni di tipo dinamico?

1. **Scalabilità nella generazione di avvisi**: le regole di avviso con soglie dinamiche possono definire soglie personalizzate per centinaia di serie di metriche alla volta, ma con la stessa facilità con cui si definisce una regola di avviso per una singola metrica. Che si usi l'interfaccia utente o l'API di Azure Resource Manager, ciò si traduce in un numero minore di regole di avviso da gestire. L'approccio scalabile è particolarmente utile quando si usano metriche con dimensioni o quando si applicano le metriche a più risorse, ad esempio tutte le risorse della sottoscrizione. Ciò si traduce in un notevole risparmio di tempo nel gestire e creare regole di avviso. [Altre informazioni su come configurare gli avvisi delle metriche con soglie dinamiche usando modelli](alerts-metric-create-templates.md).

1. **Riconoscimento intelligente di modelli di metriche**: usando l'esclusiva tecnologia di Machine Learning offerta da Microsoft, è possibile rilevare automaticamente modelli di metriche e adattare la generazione di avvisi in base alla variazione delle metriche nel tempo, che spesso può avere carattere ricorrente (su base oraria, giornaliera o settimanale). L'adattamento al comportamento delle metriche nel tempo e la generazione di avvisi in base alle deviazioni dal modello evita la necessità di conoscere la soglia "giusta" per ogni metrica. L'algoritmo di Machine Learning usato per le soglie dinamiche è stato progettato in modo da impedire valori soglia non significativi (bassa precisione) o troppo ampi (basso richiamo) che non seguono un modello previsto.

1. **Configurazione intuitiva**: le soglie dinamiche consentono di configurare gli avvisi delle metriche usando concetti generali, riducendo così la necessità di avere informazioni complete sulla metrica a livello di dominio.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Come configurare le regole di avviso con soglie dinamiche?

Gli avvisi con soglie dinamiche possono essere configurati tramite la funzionalità Avvisi delle metriche in Monitoraggio di Azure. [Altre informazioni su come configurare gli avvisi delle metriche](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Come vengono calcolate le soglie?

Una soglia dinamica apprende costantemente i dati della serie di metriche e prova a modellarli usando un set di algoritmi e metodi. Rileva modelli nei dati, ad esempio la ricorrenza (su base oraria, giornaliera o settimanale), ed è in grado di gestire le metriche non significative (come la memoria o la CPU del computer) e quelle con bassa dispersione (come la percentuale di disponibilità ed errore).

Le soglie vengono selezionate in modo che un'eventuale deviazione da queste indichi un'anomalia nel comportamento delle metriche.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>A cosa serve l'impostazione "Sensibilità" per le soglie dinamiche?

La sensibilità delle soglie di avviso è un concetto generale che controlla il grado di deviazione dal comportamento della metrica necessario per attivare un avviso.
Questa opzione non richiede una conoscenza della metrica a livello di dominio come la soglia statica. Le opzioni disponibili sono:

- Alta: le soglie saranno estremamente vicine al modello della serie di metriche. La regola di avviso verrà attivata alla minima deviazione e verranno quindi generati più avvisi.
- Media: le soglie saranno meno sensibili e più bilanciate, generando così meno avvisi rispetto alla sensibilità alta (impostazione predefinita).
- Bassa: le soglie saranno meno rigorose, con maggiore distanza dal modello della serie di metriche. La regola di avviso verrà attivata solo in caso di ampia deviazione e verranno quindi generati meno avvisi.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quali sono le opzioni dell'impostazione "Operatore" per le soglie dinamiche?

Usando la stessa regola di avviso con soglie dinamiche è possibile creare soglie personalizzate in base al comportamento delle metriche per il limite minimo e quello massimo.
È possibile specificare che l'avviso venga attivato se si verifica una delle tre condizioni seguenti:

- Valore maggiore della soglia superiore o minore della soglia inferiore (impostazione predefinita)
- Valore maggiore della soglia superiore
- Valore minore della soglia inferiore.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>A cosa servono le impostazioni avanzate per le soglie dinamiche?

**Periodi di errore**: le soglie dinamiche consentono anche di configurare l'opzione "Number violations to trigger the alert" (Numero di violazioni per attivare l'avviso), ovvero un numero minimo di deviazioni che devono verificarsi entro un determinato intervallo di tempo affinché il sistema generi un avviso (l'intervallo di tempo predefinito è quattro deviazioni in 20 minuti). L'utente può configurare i periodi di errore e scegliere il criterio in base al quale essere avvisato modificando i periodi di errore e l'intervallo di tempo. Questa opzione consente di ridurre la generazione di avvisi non significativi a causa di picchi temporanei. Ad esempio: 

Per attivare un avviso quando il problema continua per 20 minuti, 4 volte consecutive con periodicità di 5 minuti, usare le impostazioni seguenti:

![Impostazioni di periodi di errore relativi a un problema che continua per 20 minuti, 4 volte consecutive con periodicità di 5 minuti](media/alerts-dynamic-thresholds/0008.png)

Per attivare un avviso quando si è verificata una violazione di una soglia dinamica per 20 minuti sugli ultimi 30, con periodicità di 5 minuti, usare le impostazioni seguenti:

![Impostazioni di periodi di errore relativi a un problema che si è verificato per 20 minuti sugli ultimi 30, con periodicità di 5 minuti](media/alerts-dynamic-thresholds/0009.png)

**Ignorare i dati precedenti**: gli utenti possono anche definire una data a partire dalla quale il sistema dovrebbe iniziare il calcolo delle soglie. Un tipico caso d'uso può verificarsi quando una risorsa era in esecuzione in modalità di test e ora è stata promossa per gestire un carico di lavoro di produzione, e pertanto il comportamento di qualsiasi metrica durante la fase di test deve essere ignorato.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Una lenta variazione nel comportamento della metrica attiverà un avviso?

La risposta è probabilmente negativa. Le soglie dinamiche sono utili per rilevare deviazioni significative anziché problemi che si evolvono lentamente.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Quanti dati vengono usati per visualizzare in anteprima le soglie e quindi calcolarle?

Le soglie visualizzate nel grafico, prima della creazione di una regola di avviso per la metrica, vengono calcolate in base agli ultimi 10 giorni di dati cronologici. Una volta creata una regola di avviso, le soglie dinamiche acquisiranno altri dati cronologici disponibili e apprenderanno continuamente dai nuovi dati per raggiungere un maggiore livello di accuratezza.
