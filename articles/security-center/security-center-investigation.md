---
title: Analizzare gli eventi imprevisti e gli avvisi nel Centro sicurezza di Azure | Microsoft Docs
description: "Questo documento illustra come usare la funzionalità di indagine nel Centro sicurezza di Azure per analizzare gli eventi imprevisti e gli avvisi relativi alla sicurezza."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: 818c257d1959936f0dc326486e372677aacb065a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Analizzare gli eventi imprevisti e gli avvisi nel Centro sicurezza di Azure (anteprima)
Questo documento illustra come usare la funzionalità di indagine nel Centro sicurezza di Azure per analizzare gli eventi imprevisti e gli avvisi relativi alla sicurezza.

## <a name="what-is-investigation-in-security-center"></a>Informazioni sull'indagine nel Centro sicurezza
La funzionalità Indagine del Centro sicurezza consente di valutare, determinare l'ambito e individuare la causa radice di un potenziale [evento imprevisto relativo alla sicurezza](https://docs.microsoft.com/azure/security-center/security-center-incident).
 
L'obiettivo consiste nel semplificare il processo di indagine collegando tutte le entità ([avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), utenti, computer ed eventi imprevisti) correlate all'evento imprevisto analizzato.  Il Centro sicurezza può ottenere questo risultato correlando i dati rilevanti con tutte le entità interessate ed esponendo tale correlazione tramite un grafico in tempo reale che consente di spostarsi tra gli oggetti e visualizzare informazioni rilevanti.


## <a name="how-investigation-works"></a>Funzionamento della funzionalità Indagine
La funzionalità Indagine è costituita da un grafico che occupa l'area centrale del dashboard corrispondente. Il grafico è sempre incentrato su un'entità specifica e presenta le entità correlate a essa. Un'entità può essere un avviso di sicurezza, un utente, un computer o un evento imprevisto.
 
![Mappa](./media/security-center-investigation/security-center-investigation-fig1.png)

L'utente può passare da un'entità all'altra selezionandole nel grafico. Il grafico viene incentrato automaticamente sull'entità selezionata e sulle rispettive entità correlate. È possibile che le entità non più rilevanti vengano rimosse dal grafico.

### <a name="investigation-path"></a>Percorso di indagine
Quando l'utente si sposta tra entità diverse, il percorso di indagine consente di tenere traccia del contesto dell'indagine e di spostarsi rapidamente. L'evento imprevisto che contiene i risultati dell'indagine è sempre l'evento visualizzato all'estrema sinistra del percorso di indagine.

![Path](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Informazioni generali
Quando un'entità viene presentata nel grafico, le schede mostrano informazioni aggiuntive sull'entità. La scheda **Info** presenta informazioni generali sull'entità da diverse origini di informazioni disponibili. 

![Informazioni generali](./media/security-center-investigation/security-center-investigation-fig3.png)

La scheda Info mostra le informazioni rilevanti per l'evento imprevisto selezionato nella mappa. L'evento imprevisto è un contenitore che include i risultati di un'indagine. Ogni indagine viene effettuata nel contesto di un evento imprevisto.

Un evento imprevisto viene creato solo quando un utente fa clic sul pulsante **Avvia indagine** per un avviso specifico. Le funzionalità di base disponibili per il responsabile dell'indagine consentono di contrassegnare entità, ad esempio utente, computer o avviso. Quando un'entità viene contrassegnata come correlata, viene specificato un motivo. A partire da questo punto, l'entità viene visualizzata direttamente sotto l'evento imprevisto nel grafico e nell'elenco di entità dell'evento imprevisto.

### <a name="entities"></a>Entità

La scheda **Entità** mostra tutte le entità correlate raggruppate per tipo. È utile in due casi: quando il numero di entità presenti nel grafico è eccessivo e quando i nomi delle entità sono troppo lunghi e risulta più semplice esaminarle in formato tabulare.

![Entità](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Ricerca

La scheda **Ricerca** presenta tutti i tipi di log disponibili per l'entità. Per ogni tipo di log è possibile visualizzare il numero di record disponibili. La selezione di ogni tipo di log consente di visualizzare la schermata di ricerca. Nella schermata di ricerca è possibile perfezionare la ricerca e usare le diverse funzionalità di ricerca, ad esempio la configurazione degli avvisi. Nella versione corrente la scheda Ricerca è disponibile solo per entità di tipo utenti e computer.

![Ricerca](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Esplorazione

La scheda **Esplorazione** consente al responsabile dell'indagine di esaminare i dati correlati ai diversi problemi relativi all'entità. Quando, ad esempio, si esegue un'indagine su un computer, nella scheda Esplorazione viene presentato l'elenco di processi eseguiti in tale computer. In alcuni casi la scheda Esplorazione presenta dati che potrebbero indicare un problema sospetto. Il responsabile dell'indagine può esaminare i dati nella scheda o aprirli nella schermata di ricerca per esaminare set di dati di grandi dimensioni e per usare le opzioni avanzate di ricerca, ad esempio i filtri e l'esportazione in Excel.

![Esplorazione](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Sequenza temporale

La maggior parte dei dati presentati nel grafico e nelle diverse schede è rilevante per un periodo di tempo specifico. L'ambito temporale viene configurato tramite il selettore corrispondente, disponibile sul lato superiore sinistro del grafico. Il responsabile dell'indagine può selezionare l'ambito temporale in diversi modi. 

![Sequenza temporale](./media/security-center-investigation/security-center-investigation-fig7.png)

Gli elementi seguenti sono sensibili all'ambito temporale:

- Relazione utente-computer nel grafico: vengono presentati solo gli utenti connessi al computer entro l'ambito temporale specifico.
- Avvisi presentati quando si esaminano computer e utenti: vengono presentati solo gli avvisi generati entro l'ambito temporale specifico.
- La scheda Entità segue la stessa logica del grafico.

Gli elementi seguenti vengono presentati indipendentemente dall'ambito temporale selezionato:

- Quando viene presentato un avviso, vengono sempre presentate tutte le entità incluse in tale avviso, ad esempio utenti e computer.
- Se un evento imprevisto contiene un'entità, tale entità viene presentata.

> [!NOTE]
> La scheda **Ricerca** e la scheda **Esplorazione** mostrano solo i record relativi a questo ambito temporale.

## <a name="how-to-perform-an-investigation"></a>Procedura per l'esecuzione di un'indagine

È possibile avviare l'indagine da un evento imprevisto di sicurezza o da un avviso. L'opzione scelta dipenderà dalle esigenze specifiche. La procedura seguente consente di avviare un'indagine da un avviso:

1.  Aprire il dashboard del **Centro sicurezza**.
2.  Fare clic su **Avvisi di sicurezza** e selezionare l'evento imprevisto da analizzare.
3.  Nella pagina dell'evento imprevisto fare clic sul pulsante **Avvia l'indagine**. Verrà visualizzato il dashboard **Indagine**.

    ![Avviso](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Questo dashboard consente di selezionare l'entità nella mappa. Le informazioni rilevanti su questa entità vengono visualizzate sul lato destro dello schermo.

    ![Dashboard Indagine](./media/security-center-investigation/security-center-investigation-fig9.png)

A partire da questo punto è possibile esplorare le entità interessate dall'evento imprevisto ed esplorare altri dettagli su ogni entità. 

## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come usare la funzionalità Indagine nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

