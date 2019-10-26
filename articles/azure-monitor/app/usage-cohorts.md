---
title: Coorti di utilizzo di Azure Application Insights | Microsoft Docs
description: Analizzare set diversi di utenti, sessioni, eventi o operazioni con elementi in comune
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d6762ac9253c838f715588451441aa9aa467f673
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899503"
---
# <a name="application-insights-cohorts"></a>Coorti di Application Insights

Una coorte è un set di utenti, sessioni, eventi o operazioni con elementi in comune. In Azure Application Insights le coorti vengono definite da una query di analisi. Se è necessario analizzare più volte un set specifico di utenti o eventi, le coorti offrono più flessibilità per esprimere con esattezza il set a cui si è interessati.

![Riquadro Coorti](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Coorti e filtri di base

Le coorti vengono usate in modo simile ai filtri, ma le definizioni delle coorti sono costituite dalle query di analisi personalizzate, in modo che siano molto più flessibili e complesse. A differenza dei filtri, è possibile salvare le coorti in modo che altri membri del team possano riutilizzarle.

È possibile definire una coorte di utenti che hanno provato una nuova funzionalità nell'app. È possibile salvare questa coorte nella risorsa di Application Insights. In futuro sarà facile analizzare questo gruppo di utenti specifici salvato.

> [!NOTE]
> Dopo essere state create, le coorti sono disponibili dagli strumenti Utenti, Sessioni, Eventi e Flussi utente.

## <a name="example-engaged-users"></a>Esempio: utenti attivi

In base alla definizione elaborata dal team, un utente attivo è chi usa l'app cinque o più volte in un determinato mese. In questa sezione si definisce una coorte di questi utenti attivi.

1. Aprire lo strumento Coorti.

2. Selezionare la scheda **raccolta modelli** . Viene visualizzata una raccolta di modelli per varie coorti.

3. Selezionare **Engaged Users -- by Days Used** (Utenti attivi - Per giorni di utilizzo).

    Sono disponibili tre parametri per questa coorte:
    * **Activities**, dove si scelgono gli eventi e le visualizzazioni pagina da considerare come "utilizzo".
    * **Period**, la definizione di un mese.
    * **UsedAtLeastCustom**, il numero di volte in cui gli utenti devono usare un elemento in un determinato periodo per essere considerati attivi.

4. Impostare **UsedAtLeastCustom** su **5+ days** (5+ giorni) e lasciare **Period** impostato sul valore predefinito di 28 giorni.

    ![Utenti attivi](./media/usage-cohorts/003.png)

    Questa coorte rappresenta ora tutti gli ID utente inviati con gli eventi personalizzati o le visualizzazioni pagina in 5 giorni distinti nell'arco degli ultimi 28.

5. Selezionare **Salva**.

   > [!TIP]
   > Assegnare un nome alla coorte, ad esempio "Utenti attivi (5+ giorni)". Salvarla in "Report personali" o in "Report condivisi", a seconda che si voglia consentire o meno ad altri utenti che hanno accesso a questa risorsa di Application Insights di visualizzare questa coorte.

6. Selezionare **Torna alla raccolta**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Operazioni consentite dall'uso di questa coorte

Aprire lo strumento Utenti. Nella casella di riepilogo a discesa **Mostra** scegliere la coorte creata in **Utenti appartenenti a**.

Allo strumento Utenti viene ora applicato il filtro basato su questa coorte di utenti:

![Riquadro Utenti filtrato in base a una determinata coorte](./media/usage-cohorts/004.png)

Alcuni aspetti importanti da notare:

* Non è possibile creare questo set con i normali filtri. La logica della data è più avanzata.
* È possibile filtrare ulteriormente questa coorte usando i normali filtri nello strumento Utenti, quindi, anche se la coorte è definita in una finestra di 28 giorni, è tuttavia possibile impostare l'intervallo di tempo nello strumento Utenti su 30, 60 o 90 giorni.

Questi filtri supportano domande più sofisticate, impossibili da esprimere con il generatore di query. Un esempio è _quello degli utenti coinvolti negli ultimi 28 giorni. In che modo le stesse persone si comportano negli ultimi 60 giorni?_

## <a name="example-events-cohort"></a>Esempio: coorte di eventi

È anche possibile creare coorti di eventi. In questa sezione si definisce una coorte degli eventi e delle visualizzazioni pagina, quindi si vedrà come usarli dagli altri strumenti. Questa coorte potrebbe definire un set di eventi che il team considera _utilizzo attivo_ o impostare un set correlato a una nuova funzionalità.

1. Aprire lo strumento Coorti.

2. Selezionare la scheda **raccolta modelli** . Verrà visualizzata una raccolta di modelli per varie coorti.

3. Selezionare **Events Picker** (Selezione eventi).

    ![Screenshot di Events Picker (Selezione eventi)](./media/usage-cohorts/006.png)

4. Nella casella di riepilogo a discesa **Activities** selezionare gli eventi da inserire nella coorte.

5. Salvare la coorte e assegnarle un nome.

## <a name="example-active-users-where-you-modify-a-query"></a>Esempio: utenti attivi in cui si modifica una query

Le due coorti precedenti sono state definite usando caselle di riepilogo a discesa, ma è anche possibile definire le coorti usando query di analisi per una flessibilità ottimale. A questo scopo, creare una coorte di utenti del Regno Unito.

![Immagine animata che illustra l'uso dello strumento Coorti](./media/usage-cohorts/cohorts0001.gif)

1. Aprire lo strumento Coorti, selezionare la scheda **Raccolta modelli** e selezionare **Coorte utenti vuota**.

    ![Coorte utenti vuota](./media/usage-cohorts/001.png)

    Sono disponibili tre sezioni:
   * Una sezione di testo Markdown, in cui si descrive più dettagliatamente la coorte per gli altri membri del team.

   * Una sezione di parametri, in cui si creano i propri parametri, ad esempio **Activities** e le altre caselle di riepilogo a discesa dei due esempi precedenti.

   * Una sezione di query, in cui si definisce la coorte con una query di analisi.

     Nella sezione di query si [scrive una query di analisi](/azure/kusto/query). La query seleziona il set specifico di righe che descrivono la coorte che si vuole definire. Lo strumento Coorti aggiunge quindi in modo implicito una clausola "| summarize by user_Id" alla query. L'anteprima dei dati viene visualizzata sotto la query in una tabella, per poter verificare che la query restituisca risultati.

     > [!NOTE]
     > Se la query non viene visualizzata, provare a ridimensionare la sezione per aumentarne l'altezza e poter vedere la query. L'immagine animata all'inizio di questa sezione illustra il comportamento di ridimensionamento.

2. Copiare e incollare il seguente testo nell'editor di query:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selezionare **Esegui query**. Se nella tabella non vengono visualizzati gli ID utente, passare a un paese/area geografica in cui l'applicazione dispone di utenti.

4. Salvare la coorte e assegnarle un nome.

## <a name="frequently-asked-questions"></a>Domande frequenti

_Ho definito una coorte di utenti di un determinato paese/area geografica. Quando si confronta questa coorte nello strumento utenti per impostare semplicemente un filtro su tale paese/area geografica, vengono visualizzati risultati diversi. Perché?_

Le coorti e i filtri sono diversi. Si supponga di avere una coorte di utenti del Regno Unito (definita come l'esempio precedente) e di confrontarne i risultati con l'impostazione del filtro "Country or region = United Kingdom" (Paese o area geografica = Regno Unito).

* La versione della coorte visualizza tutti gli eventi degli utenti che hanno inviato uno o più eventi dal regno Unito nell'intervallo di tempo corrente. Se si divide per paese o area geografica, si notano probabilmente diversi paesi e aree geografiche.
* La versione dei filtri mostra solo gli eventi del Regno Unito, ma, se si divide per paese o area geografica, viene visualizzato solo il Regno Unito.

## <a name="learn-more"></a>Altre informazioni.

* [Linguaggio di query di Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Utenti, sessioni ed eventi](usage-segmentation.md)
* [Flussi degli utenti](usage-flows.md)
* [Panoramica sull'uso](usage-overview.md)