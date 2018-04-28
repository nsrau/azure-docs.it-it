---
title: Coorti di utilizzo di Azure Application Insights | Microsoft Docs
description: Analizzare set diversi di utenti, sessioni, eventi o operazioni con elementi in comune
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Coorti di Application Insights

Una coorte è un set di utenti, sessioni, eventi o operazioni con elementi in comune. In Azure Application Insights le coorti vengono definite da una query di Analytics. Se è necessario analizzare più volte un set specifico di utenti o eventi, le coorti offrono ancora più flessibilità per esprimere con esattezza il set a cui si è interessati.

![Riquadro Coorti](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Coorti e filtri di base

Anche se le coorti vengono usate in modo analogo ai filtri, il fatto che la definizione di una coorte sia compilata in base alle query personalizzate di Analytics le rende molto più adattabili e complesse. A differenza dei filtri, è possibile salvare le coorti in modo che altri membri del team possano riutilizzarle.

È possibile definire una coorte di utenti che hanno provato una nuova funzionalità nell'app. Con questa coorte salvata nella risorsa di Application Insights, per eseguire l'analisi di questo gruppo specifico di utenti in futuro, sarà sufficiente un semplice clic.

> [!NOTE]
> Dopo essere state create, le coorti sono disponibili dagli strumenti Utenti, Sessioni, Eventi e Flussi utente.

## <a name="example-engaged-users"></a>Esempio: utenti attivi

In base alla definizione elaborata dal team, un utente attivo è chi usa l'app cinque o più volte in un determinato mese. Verrà ora definita una coorte di questi utenti attivi.

1. Aprire lo strumento **Coorti**.

2. Fare clic sulla scheda **Raccolta modelli**, dove è disponibile una raccolta di modelli per diverse coorti.

3. Scegliere **Engaged Users** – by Days Used (Utenti attivi - Per giorni di utilizzo).

    Sono disponibili tre parametri per questa coorte:
      * **Activities**: consente di scegliere gli eventi e le visualizzazioni pagina da considerare come "utilizzo".
      * **Period**: definizione di un mese.
      * **UsedAtleastCustom**: numero di volte in cui è necessario usare un elemento in un determinato periodo per essere considerato un utente attivo.

4. Impostare **UsedAtleastCustom** su "5+ days" (5+ giorni) e lasciare **Period** impostato sul valore predefinito di 28 giorni.

    ![Image](.\media\app-insights-usage-cohorts\003.png)

    Questa coorte rappresenta ora tutti gli ID utente inviati con gli eventi personalizzati o le visualizzazioni pagina in cinque giorni distinti nell'arco degli ultimi 28 giorni.

5. Fare clic su **Save**.

   > [!TIP]
   >  Assegnare alla coorte un nome, ad esempio "Utenti attivi (5+ giorni)" e salvarla in "Report personali" o in "Report condivisi" a seconda che si voglia consentire o meno ad altri utenti con accesso a questa risorsa di Application Insights di visualizzare questa coorte.

6. Fare clic su **Torna alla raccolta**.

### <a name="what-can-you-do-with-this-cohort"></a>A cosa serve questa coorte?

Aprire lo strumento **Utenti** > Nell'elenco a discesa **Mostra** > Scegliere la coorte creata in **Utenti appartenenti a**.

Allo strumento Utenti viene ora applicato il filtro basato su questa coorte di utenti:

![Riquadro Utenti filtrato in base a una determinata coorte](.\media\app-insights-usage-cohorts\004.png)

Alcuni aspetti importanti da notare:
   * Si tratta di un set che non sarebbe stato possibile creare tramite i normali filtri. La logica della data è più avanzata.
   * È possibile filtrare ulteriormente questa coorte usando i normali filtri nello strumento Utenti, quindi, anche se la coorte è definita in una finestra di 28 giorni, è tuttavia possibile impostare l'intervallo di tempo nello strumento Utenti su 30, 60 o 90 giorni. 

Ciò consente di porre domande più accurate, ad esempio: _le persone che sono state attive negli ultimi 28 giorni come si sono comportate negli ultimi 60 giorni?_. Sarebbe impossibile esprimere tali domande tramite il generatore di query.

## <a name="example-events-cohort"></a>Esempio: coorte di eventi

È anche possibile creare coorti di eventi. Verrà ora definita una coorte degli eventi e delle visualizzazioni pagina e quindi verrà illustrato come usarla dagli altri strumenti. Potrebbe rivelarsi utile per definire un set di eventi che il team considera _utilizzo attivo_ o per definire un set di eventi correlati a una nuova funzionalità.

1. Aprire lo strumento **Coorti**.

2. Fare clic sulla scheda **Raccolta modelli**, dove è disponibile una raccolta di modelli per diverse coorti.

3. Scegliere **Events Picker** (Selezione eventi).

    ![Screenshot di Events Picker (Selezione eventi)](.\media\app-insights-usage-cohorts\006.png)

4. Nell'elenco a discesa **Activities** selezionare gli eventi da inserire nella coorte

5. Salvare la coorte e assegnarle un nome.

## <a name="example-active-users-where-you-modify-query"></a>Esempio: utenti attivi in cui si modifica query

Le due coorti precedenti sono state definite usando elenchi a discesa, ma è anche possibile definire le coorti con le query di Analytics per una flessibilità ottimale. A questo scopo, verrà ora creata una coorte di utenti del Regno Unito.

![Immagine animata che illustra l'uso dello strumento Coorti](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Aprire lo strumento **Coorti** > Fare clic sulla scheda **Raccolta modelli** > Scegliere **Coorte utenti vuota**.

    ![Coorte utenti vuota](.\media\app-insights-usage-cohorts\001.png)

    Sono disponibili tre sezioni:
       * Una sezione di testo Markdown in cui è possibile descrivere più dettagliatamente la coorte per gli altri membri del team.

       * Una sezione di parametri che è possibile usare per creare i propri parametri, ad esempio **Activities** e gli altri elenchi a discesa dei due esempi precedenti.

       * Una sezione di query usata per definire la coorte con una query di Analytics.

    Nella sezione di query si [scrive una query di Analytics](https://docs.loganalytics.io/index) che seleziona il set specifico di righe che descrivono la coorte che si vuole definire. Lo strumento Coorti aggiunge quindi in modo implicito una clausola "| summarize by user_Id" alla query. L'anteprima viene visualizzata sotto la query in una tabella, per poter verificare che la query restituisca risultati.

    > [!NOTE]
    > Se la query non viene visualizzata, provare a ridimensionare la sezione per aumentarne l'altezza e poter vedere la query. L'immagine animata all'inizio di questa sezione illustra il comportamento di ridimensionamento.

2. Copiare e incollare il codice seguente nell'editor di query:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Fare clic su **Esegui query**. Gli ID utente verranno visualizzati nella tabella. In caso contrario, sostituire il paese con uno in cui esistono utenti dell'applicazione.

4. Salvare la coorte e assegnarle un nome.

## <a name="frequently-asked-questions"></a>Domande frequenti

_È stata definita una coorte di utenti di un determinato paese. Quando si confronta questa coorte nello strumento Utenti con la semplice impostazione di un filtro per tale paese nello strumento Utenti, i risultati sono diversi. Perché?_

Le coorti e i filtri sono diversi. Si supponga di avere una coorte di utenti del Regno Unito (definita come l'esempio precedente) e di confrontarne i risultati con l'impostazione del filtro "Country or region = United Kingdom" (Paese o area geografica = Regno Unito).

* La versione della coorte visualizzerà tutti gli eventi degli utenti che hanno inviato almeno un evento dal regno Unito nell'intervallo di tempo corrente. Se si divide per paese o area geografica, si noteranno probabilmente diversi paesi e aree geografiche.
* La versione dei filtri mostrerà solo gli eventi del Regno Unito. Se invece si divide per paese o area geografica, verrà visualizzato solo il Regno Unito.

## <a name="learn-more"></a>Altre informazioni
- [Linguaggio di query di Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
- [Utenti, sessioni ed eventi](app-insights-usage-segmentation.md)
- [Flussi degli utenti](app-insights-usage-flows.md)
- [Panoramica sull'uso](app-insights-usage-overview.md)