---
title: Ridimensionamento automatico e ambiente del servizio app | Documentazione Microsoft
description: Ridimensionamento automatico e ambiente del servizio app
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 41ad9eb4d248b1f3e6c888c3037b121511799193
ms.lasthandoff: 12/08/2016


---
# <a name="autoscaling-and-app-service-environment"></a>Ridimensionamento automatico e ambiente del servizio app
Gli ambienti del servizio app di Azure supportano il *ridimensionamento automatico*. È possibile ridimensionare automaticamente singoli pool di lavoro in base alle metriche o alla pianificazione.

![Opzioni di ridimensionamento automatico per un pool di lavoro.][intro]

Il ridimensionamento automatico consente di ottimizzare l'utilizzo delle risorse aumentando o riducendo automaticamente le risorse di un ambiente del servizio app, in base al budget o al profilo di carico.

## <a name="configure-worker-pool-autoscale"></a>Configurare il ridimensionamento automatico del pool di lavoro
È possibile accedere alla funzionalità di ridimensionamento automatico dalla scheda **Impostazioni** del pool di lavoro.

![Scheda Impostazioni del pool di lavoro.][settings-scale]

L'interfaccia utente risulterà familiare, perché si tratta della stessa esperienza visualizzata quando si ridimensiona un piano di servizio app. 

![Impostazioni di ridimensionamento manuale.][scale-manual]

È anche possibile configurare un profilo di ridimensionamento automatico.

![Impostazioni di ridimensionamento automatico.][scale-profile]

I profili di ridimensionamento automatico consentono di impostare i limiti per il piano. Ciò consente di ottenere prestazioni coerenti impostando un valore del piano con un limite minimo (1) e un tetto di spesa prevedibile impostando un limite massimo (2).

![Impostazioni di ridimensionamento nel profilo.][scale-profile2]

Dopo aver definito il profilo, è possibile aggiungere regole di ridimensionamento automatico per aumentare o ridurre il numero di istanze nel pool di lavoro entro i limiti definiti nel profilo. Le regole di ridimensionamento automatico sono basate su metriche.

![Regola di ridimensionamento.][scale-rule]

 Per definire le regole di ridimensionamento automatico, è possibile usare qualsiasi metrica del pool di lavoro o del front-end. Si tratta delle stesse metriche che è possibile monitorare nei grafici del pannello delle risorse o per cui si possono impostare avvisi.

## <a name="autoscale-example"></a>Esempio di ridimensionamento automatico
Per illustrare il ridimensionamento automatico di un ambiente del servizio app, si userà uno scenario con procedure dettagliate.

Questo articolo descrive tutte le considerazioni necessarie per configurare il ridimensionamento automatico, nonché tutte le interazioni che entrano in gioco quando si configura il ridimensionamento automatico di ambienti del servizio app ospitati in un ambiente del servizio app.

### <a name="scenario-introduction"></a>Introduzione dello scenario
Diego è amministratore di sistema presso una società e ha eseguito la migrazione di una parte dei carichi di lavoro che gestisce a un ambiente del servizio app.

L'ambiente del servizio app è configurato per la scalabilità manuale come segue:

* **Front-end:** 3
* **Pool di lavoro 1**: 10
* **Pool di lavoro 2**: 5
* **Pool di lavoro 3**: 5

Il pool di lavoro 1 viene usato per i carichi di lavoro di produzione, mentre il pool di lavoro 2 e il pool di lavoro 3 sono usati per il controllo di qualità e i carichi di lavoro di sviluppo.

I piani del servizio app per il controllo di qualità e lo sviluppo vengono configurati per il ridimensionamento manuale. Il piano del servizio app di produzione è impostato per il ridimensionamento automatico, in modo da adeguarsi alle variazioni del carico e del traffico.

Diego ha una notevole familiarità con l'applicazione. Sa che le ore di picco di carico sono comprese tra le 9:00 e le 18:00, perché si tratta di un'applicazione di line-of-business (LOB) che i dipendenti usano mentre sono in ufficio. L'utilizzo si riduce al termine della giornata lavorativa degli utenti. Al di fuori dagli orari di picco il carico è ancora presente in parte, perché gli utenti possono accedere all'app in modalità remota usando i propri dispositivi mobili o i PC di casa. Il piano di servizio app è già configurato per il ridimensionamento automatico in base all'utilizzo della CPU con le regole seguenti:

![Impostazioni specifiche per l'app LOB.][asp-scale]

| **Profilo di ridimensionamento automatico - Giorni feriali - Piano di servizio app** | **Profilo di ridimensionamento automatico - Fine settimana - Piano di servizio app** |
| --- | --- |
| **Nome:** profilo Giorno feriale |**Nome:** profilo Fine settimana |
| **Ridimensiona in base a:** regole per la pianificazione e le prestazioni |**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |
| **Profilo:** giorni della settimana |**Profilo:** fine settimana |
| **Tipo:** ricorrenza |**Tipo:** ricorrenza |
| **Intervallo di destinazione:** da 5 a 20 istanze |**Intervallo di destinazione:** da 3 a 10 istanze |
| **Giorni:** Lunedì, Martedì, Mercoledì, Giovedì, Venerdì |**Giorni:** Sabato, Domenica |
| **Ora di inizio:** 9:00 |**Ora di inizio:** 9:00 |
| **Fuso orario:** UTC -08 |**Fuso orario:** UTC -08 |
|  | |
| **Regola di ridimensionamento automatico (aumento)** |**Regola di ridimensionamento automatico (aumento)** |
| **Risorsa:** produzione (Ambiente del servizio app) |**Risorsa:** produzione (Ambiente del servizio app) |
| **Metrica:** % CPU |**Metrica:** % CPU |
| **Operazione:** maggiore del 60% |**Operazione:** maggiore del 80% |
| **Durata:** 5 minuti |**Durata:** 10 minuti |
| **Aggregazione temporale:** media |**Aggregazione temporale:** media |
| **Azione:** aumenta numero di 2 |**Azione:** aumenta numero di 1 |
| **Disattiva regole dopo (minuti):** 15 |**Disattiva regole dopo (minuti):** 20 |
|  | |
| **Regola di ridimensionamento automatico (riduzione)** |**Regola di ridimensionamento automatico (riduzione)** |
| **Risorsa:** produzione (Ambiente del servizio app) |**Risorsa:** produzione (Ambiente del servizio app) |
| **Metrica:** % CPU |**Metrica:** % CPU |
| **Operazione:** inferiore al 30% |**Operazione:** inferiore al 20% |
| **Durata:** 10 minuti |**Durata:** 15 minuti |
| **Aggregazione temporale:** media |**Aggregazione temporale:** media |
| **Azione:** riduci numero di 1 |**Azione:** riduci numero di 1 |
| **Disattiva regole dopo (minuti):** 20 |**Disattiva regole dopo (minuti):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Tasso di inflazione del piano di servizio app
I piani di servizio app configurati per il ridimensionamento automatico vengono ridimensionati automaticamente in base al tasso massimo su base oraria. Questa frequenza può essere calcolata in base ai valori specificati nella regola di ridimensionamento automatico.

È importante comprendere e calcolare il *tasso di inflazione del piano di servizio app* per il ridimensionamento automatico dell'ambiente del servizio app, perché l'operazione di ridimensionamento del pool di lavoro non è istantanea.

Il tasso di inflazione del piano di servizio app viene calcolato come segue:

![Calcolo del tasso di inflazione del piano di servizio app.][ASP-Inflation]

In base alla regola Ridimensionamento automatico - Aumento per il profilo Giorno feriale del piano di servizio app di produzione la formula sarà simile alla seguente:

![Tasso di inflazione del piano di servizio app per i giorni feriali basato su Ridimensionamento automatico - Regola di aumento.][Equation1]

Nel caso della regola Ridimensionamento automatico - Aumento per il profilo Fine settimana del piano di servizio app di produzione la formula sarà la seguente:

![Tasso di inflazione del piano di servizio app per i fine settimana basato su Ridimensionamento automatico - Regola di aumento.][Equation2]

Questo valore può anche essere calcolato per le operazioni di riduzione.

In base alla regola Ridimensionamento automatico - Riduzione per il profilo Giorno feriale del piano di servizio app di produzione la formula sarà simile alla seguente:

![Tasso di inflazione del piano di servizio app per i giorni feriali basato su Ridimensionamento automatico - Regola di riduzione.][Equation3]

Nel caso della regola Ridimensionamento automatico - Riduzione per il profilo Fine settimana del piano di servizio app di produzione la formula sarà la seguente:  

![Tasso di inflazione del piano di servizio app per i fine settimana basato su Ridimensionamento automatico - Regola di riduzione.][Equation4]

Il piano di servizio app di produzione può aumentare al tasso massimo di 8 istanze all'ora durante la settimana e di 4 istanze all'ora durante il fine settimana e può rilasciare le istanze al tasso massimo di 4 istanze all'ora durante la settimana e di 6 istanze all'ora durante il fine settimana.

Se in un pool di lavoro sono ospitati più piani di servizio app, è necessario calcolare il *tasso di inflazione totale* come somma dei tassi di inflazione per tutti i piani di servizio app ospitati in quel pool di lavoro.

![Calcolo del tasso di inflazione totale per più piani di servizio app ospitati in un pool di lavoro.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Usare il tasso di inflazione del piano di servizio app per definire le regole di ridimensionamento automatico del pool di lavoro
Ai pool di lavoro che ospitano piani di servizio app configurati per il ridimensionamento automatico è necessario allocare un buffer di capacità. Il buffer consente alle operazioni di ridimensionamento automatico di aumentare e ridurre il piano di servizio app in base alle esigenze. Il buffer minimo sarà costituito dal tasso di inflazione totale calcolato per il piano di servizio app.

Poiché l'applicazione delle operazioni di ridimensionamento dell'ambiente del servizio app richiede tempo, qualsiasi modifica deve tenere conto delle ulteriori variazioni della domanda che possono verificarsi mentre è in corso un'operazione di ridimensionamento. Per questo motivo è consigliabile usare il tasso di inflazione totale calcolato per il piano di servizio app come numero minimo di istanze aggiunte per ogni operazione di ridimensionamento automatico.

Con queste informazioni Diego può definire le regole e il profilo di ridimensionamento automatico seguenti:

![Regole del profilo di ridimensionamento automatico per l'esempio LOB.][Worker-Pool-Scale]

| **Profilo di ridimensionamento automatico - Giorni feriali** | **Profilo di ridimensionamento automatico - Fine settimana** |
| --- | --- |
| **Nome:** profilo Giorno feriale |**Nome:** profilo Fine settimana |
| **Ridimensiona in base a:** regole per la pianificazione e le prestazioni |**Ridimensiona in base a:** regole per la pianificazione e le prestazioni |
| **Profilo:** giorni della settimana |**Profilo:** fine settimana |
| **Tipo:** ricorrenza |**Tipo:** ricorrenza |
| **Intervallo di destinazione:** da 13 a 25 istanze |**Intervallo di destinazione:** da 6 a 15 istanze |
| **Giorni:** Lunedì, Martedì, Mercoledì, Giovedì, Venerdì |**Giorni:** Sabato, Domenica |
| **Ora di inizio:** 7:00 |**Ora di inizio:** 9:00 |
| **Fuso orario:** UTC -08 |**Fuso orario:** UTC -08 |
|  | |
| **Regola di ridimensionamento automatico (aumento)** |**Regola di ridimensionamento automatico (aumento)** |
| **Risorsa:** Pool di lavoro 1 |**Risorsa:** Pool di lavoro 1 |
| **Metrica:** Pool di lavoro disponibili |**Metrica:** Pool di lavoro disponibili |
| **Operazione:** minore di 8 |**Operazione:** minore di 3 |
| **Durata:** 20 minuti |**Durata:** 30 minuti |
| **Aggregazione temporale:** media |**Aggregazione temporale:** media |
| **Azione:** aumenta numero di 8 |**Azione:** aumenta numero di 3 |
| **Disattiva regole dopo (minuti):** 180 |**Disattiva regole dopo (minuti):** 180 |
|  | |
| **Regola di ridimensionamento automatico (riduzione)** |**Regola di ridimensionamento automatico (riduzione)** |
| **Risorsa:** Pool di lavoro 1 |**Risorsa:** Pool di lavoro 1 |
| **Metrica:** Pool di lavoro disponibili |**Metrica:** Pool di lavoro disponibili |
| **Operazione:** maggiore di 8 |**Operazione:** maggiore di 3 |
| **Durata:** 20 minuti |**Durata:** 15 minuti |
| **Aggregazione temporale:** media |**Aggregazione temporale:** media |
| **Azione:** riduci numero di 2 |**Azione:** riduci numero di 3 |
| **Disattiva regole dopo (minuti):** 120 |**Disattiva regole dopo (minuti):** 120 |

L'intervallo di destinazione definito nel profilo è calcolato in base al numero minimo di istanze definito nel profilo per il piano di servizio app più il buffer.

L'intervallo massimo corrisponde alla somma di tutti gli intervalli massimi per tutti i piani di servizio app ospitati nel pool di lavoro.

L'aumento del numero per le regole di aumento deve essere impostato su un valore pari ad almeno 1 volta il tasso di inflazione per il piano di servizio app per l'operazione di aumento.

La riduzione del numero può essere regolata su un valore compreso tra la metà o una volta il tasso di inflazione del piano di servizio app per l'operazione di riduzione.

### <a name="autoscale-for-front-end-pool"></a>Ridimensionamento automatico per il pool front-end
Le regole per il ridimensionamento automatico front-end sono più semplici rispetto ai pool di lavoro. È prima di tutto necessario  
assicurarsi che la durata della misurazione e i timer di raffreddamento tengano presente che le operazioni di ridimensionamento in un piano di servizio app non sono istantanee.

Per questo scenario, Diego sa che la percentuale di errore aumenta quando i pool front-end raggiungono un utilizzo di CPU dell'80% e imposta la regola di scalabilità automatica in modo da aumentare le istanze come descritto di seguito:

![Impostazioni di ridimensionamento automatico per il pool front-end.][Front-End-Scale]

| **Profilo di ridimensionamento automatico - Front-end** |
| --- |
| **Nome:** Ridimensionamento automatico - Front-end |
| **Ridimensiona in base a:** regole per la pianificazione e le prestazioni |
| **Profilo:** ogni giorno |
| **Tipo:** ricorrenza |
| **Intervallo di destinazione:** da 3 a 10 istanze |
| **Giorni:** ogni giorno |
| **Ora di inizio:** 9:00 |
| **Fuso orario:** UTC -08 |
|  |
| **Regola di ridimensionamento automatico (aumento)** |
| **Risorsa:** pool front-end |
| **Metrica:** % CPU |
| **Operazione:** maggiore del 60% |
| **Durata:** 20 minuti |
| **Aggregazione temporale:** media |
| **Azione:** aumenta numero di 3 |
| **Disattiva regole dopo (minuti):** 120 |
|  |
| **Regola di ridimensionamento automatico (riduzione)** |
| **Risorsa:** Pool di lavoro 1 |
| **Metrica:** % CPU |
| **Operazione:** inferiore al 30% |
| **Durata:** 20 minuti |
| **Aggregazione temporale:** media |
| **Azione:** riduci numero di 3 |
| **Disattiva regole dopo (minuti):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

