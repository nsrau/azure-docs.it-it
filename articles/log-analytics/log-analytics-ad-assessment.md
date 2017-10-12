---
title: Ottimizzare l'ambiente Active Directory con Log Analytics di Azure|Documentazione Microsoft
description: "È possibile usare la soluzione Active Directory Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97368f0b9e89ffd0cd982b6e8670d5a1f62ad42c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Ottimizzare l'ambiente Active Directory con la soluzione Active Directory Assessment in Log Analytics

![Simbolo di Valutazione AD](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

È possibile usare la soluzione Active Directory Assessment per valutare i rischi e l'integrità degli ambienti server a intervalli regolari. Questo articolo descrive come installare e usare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in quattro aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione fornisce informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato una valutazione, nel dashboard **AD Assessment** per l'infrastruttura nell'ambiente verranno visualizzate le informazioni di riepilogo per l'area di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard **AD Assessment** , in cui sarà possibile intraprendere le azioni consigliate per l'infrastruttura dei server di Active Directory.

![immagine del riquadro  SQL Assessment](./media/log-analytics-ad-assessment/ad-tile.png)

![Immagine del dashboard SQL Assessment ](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare le soluzioni.

* Gli agenti devono essere installati nei controller di dominio membri del dominio da valutare.
* La soluzione Active Directory Assessment richiede l'installazione di una versione di .NET Framework 4 (4.5.2 o superiore) supportata in ogni computer che contiene un agente OMS.
* Aggiungere la soluzione Active Directory Assessment all'area di lavoro OMS da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) o seguendo la procedura descritta in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

  > [!NOTE]
  > Dopo aver aggiunto la soluzione, il file AdvisorAssessment.exe viene aggiunto al server con agenti. I dati di configurazione vengono letti e quindi inviati al servizio OMS nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati per Active Directory Assessment

Valutazione Active Directory raccoglie i dati dalle origini seguenti tramite gli agenti abilitati:

- Agenti di raccolta del Registro di sistema
- Agenti di raccolta LDAP
- .NET Framework
- Agenti di raccolta del registro eventi
- Active Directory Service Interfaces (ADSI)
- Windows PowerShell
- Agenti di raccolta dei dati di file
- Strumentazione gestione Windows (WMI)
- API dello strumento DCDIAG
- API di File Replication Service (NTFRS)
- Codice personalizzato in C#


La tabella seguente illustra i metodi di raccolta dati per gli agenti, se Operations Manager (SCOM) è obbligatorio e la frequenza con cui i dati vengono raccolti da un agente.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |7 giorni |

## <a name="understanding-how-recommendations-are-prioritized"></a>Informazioni sulla classificazione in ordine di priorità delle raccomandazioni
A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le 10 raccomandazioni più importanti.

### <a name="how-weights-are-calculated"></a>Come vengono calcolate le ponderazioni
Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

* La *probabilità* che un problema identificato causi inconvenienti. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.
* L' *impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.
* Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Ad esempio, se una raccomandazione nell'area di interesse Sicurezza e conformità ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenta del 5% il punteggio complessivo di quell'area.

### <a name="focus-areas"></a>Aree di interesse
**Sicurezza e conformità** : quest'area di interesse descrive raccomandazioni relative alle potenziali minacce e violazioni della sicurezza, ai criteri aziendali e ai requisiti di conformità tecnici, legali e normativi.

**Disponibilità e continuità aziendale** : quest'area di interesse descrive raccomandazioni relative alla disponibilità del servizio, alla resilienza dell'infrastruttura e alla protezione aziendale.

**Prestazioni e scalabilità** : quest'area di interesse descrive raccomandazioni utili per favorire la crescita dell'infrastruttura IT dell'organizzazione, assicurando che l'ambiente IT soddisfi gli attuali requisiti in termini di prestazioni e possa rispondere alle mutevoli esigenze dell'infrastruttura.

**Aggiornamento, migrazione e distribuzione** : quest'area di interesse descrive raccomandazioni utili per aggiornare, eseguire la migrazione e distribuire Active Directory nell'infrastruttura esistente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?
Non necessariamente. Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione include informazioni aggiuntive sui motivi per cui potrebbe essere importante. È consigliabile usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## <a name="use-assessment-focus-area-recommendations"></a>Usare le raccomandazioni relative all'area di interesse della valutazione
Prima di poter usare una soluzione di valutazione in OMS, è necessario averla installata. Per altre informazioni sull'installazione di soluzioni, vedere [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Dopo l'installazione, sarà possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro Assessment della pagina Overview in OMS.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva
1. Nella pagina **Overview** (Panoramica) fare clic sul riquadro **Assessment** (Valutazione) per l'infrastruttura del server.
2. Nella pagina **Assessment** verificare le informazioni di riepilogo in uno dei pannelli dell'area di interesse, quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.
3. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** (Oggetti interessati) per visualizzare i dettagli relativi al motivo per cui è stata generata.  
    ![immagine delle raccomandazioni di Assessment (Valutazione)](./media/log-analytics-ad-assessment/ad-focus.png)
4. È possibile eseguire le azioni correttive suggerite in **Suggested Actions**(Azioni suggerite). Dopo la risoluzione dell'elemento, le valutazioni successive indicano che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## <a name="ignore-recommendations"></a>Ignorare le raccomandazioni
Per ignorare delle raccomandazioni è possibile creare un file di testo che OMS userà per impedirne la visualizzazione nei risultati della valutazione.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Per identificare le raccomandazioni che verranno ignorate
1. Accedere all'area di lavoro e aprire Ricerca log. Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), la query precedente verrà sostituita dalla seguente.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

   Ecco lo screenshot che mostra la query di ricerca nei log: ![raccomandazioni con esito negativo](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Per creare e usare un file di testo IgnoreRecommendations.txt
1. Creare un file denominato IgnoreRecommendations.txt.
2. Incollare o digitare ciascun ID raccomandazione per ogni raccomandazione che Log Analytics dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3. Inserire il file nella cartella seguente in ogni computer in cui si vuole che OMS ignori le raccomandazioni.
   * Nei computer con Microsoft Monitoring Agent, connesso direttamente o tramite Operations Manager, *SystemDrive*:\Programmi\Microsoft Monitoring Agent\Agent
   * Nel server di gestione Operations Manager *SystemDrive*: \Programmi\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Per verificare che le raccomandazioni vengano ignorate
Dopo la successiva esecuzione della valutazione pianificata, per impostazione predefinita ogni 7 giorni, le raccomandazioni specificate sono contrassegnate come *ignorate* e non verranno visualizzate nel dashboard di valutazione.

1. È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), la query precedente verrà sostituita dalla seguente.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure è possibile rimuovere gli ID raccomandazione dagli stessi.

## <a name="ad-assessment-solutions-faq"></a>Domande frequenti sulle soluzioni AD Assessment
*Con quale frequenza viene eseguita la valutazione?*

* La valutazione viene eseguita ogni 7 giorni.

*È possibile configurare la frequenza di esecuzione della valutazione?*

* Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta della soluzione per la valutazione, il server verrà valutato?*

* Sì, a partire dal rilevamento verrà valutato ogni 7 giorni.

*Se un server viene rimosso, quando sarà rimosso dalla valutazione?*

* Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*

* AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*

* La raccolta di dati effettiva sul server richiede circa 1 ora. Potrebbe essere necessario più tempo nei server in cui è presente un numero elevato di server di Active Directory.

*È possibile definire l'orario per la raccolta di dati?*

* Attualmente non è possibile.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*

* Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare Ricerca log per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?*

* Sì, vedere la sezione [Ignorare le raccomandazioni](#ignore-recommendations) sopra.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare dati e raccomandazioni dettagliati di AD Assessment usare [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) .
