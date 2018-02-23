---
title: Informazioni sui report dei costi in Gestione costi di Azure | Microsoft Docs
description: Questo articolo contiene informazioni utili per comprendere le funzioni e la struttura di base dei report Cloudyn.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 38c1313f42a58403e158cad9c2930b6541da5adc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="understanding-cost-reports"></a>Informazioni sui report dei costi

Questo articolo contiene informazioni utili per comprendere le funzioni e la struttura di base dei report Cloudyn. La maggior parte dei report Cloudyn presenta le informazioni in modo intuitivo e con un aspetto grafico uniforme. Dopo aver letto questo articolo, sarà possibile usare qualsiasi report. Nei vari report sono disponibili molte funzionalità standard che ne facilitano l'esplorazione. I report sono personalizzabili ed è possibile selezionare diverse opzioni per calcolare e visualizzare i risultati.

## <a name="report-fields-and-options"></a>Campi e opzioni dei report

Ecco un esempio del report Cost Over Time (Costo nel tempo). Il layout è simile a quello della maggior parte dei report Cloudyn.

![report di esempio](./media/understanding-cost-reports/sample-report.png)

Ogni area numerata nella figura è descritta in dettaglio nei paragrafi seguenti:

1. **Date Range** (Intervallo di date)

    Usare questo elenco per definire un intervallo di tempo nel report usando un valore predefinito o personalizzato.
2. **Saved Filter** (Filtro salvato)

    Usare questo elenco per salvare i gruppi e i filtri correnti applicati al report. I filtri salvati sono disponibili nei report relativi a costi e prestazioni, tra cui:

      - Cost Analysis (Analisi dei costi)
      - Allocation (Allocazione)
      - Asset Management (Gestione degli asset)
      - Ottimizzazione

  Digitare un nome di filtro e quindi fare clic su **Save** (Salva).

3. **Tag**

    Usare questa area per raggruppare i dati in base a categorie di tag. I tag elencati nel menu si riferiscono a centri di costo o reparti di Azure oppure a sottoscrizioni o entità di costo di Cloudyn. Selezionare i tag per filtrare i risultati. Per eseguire questa operazione è anche possibile digitare un nome di tag (parola chiave).

    ![selezionare le opzioni](./media/understanding-cost-reports/select-options.png)

    Fare clic su **Add** (Aggiungi) per aggiungere un nuovo filtro.

    ![aggiungere un filtro](./media/understanding-cost-reports/add-filter.png)

    La funzionalità per il filtraggio o il raggruppamento in base a tag non è correlata ai tag dei gruppi di risorse o alle risorse di Azure.

    Il filtraggio e il raggruppamento in base a tag di allocazione costi sono disponibili nel menu **Groups** (Gruppi).

4. **Gruppi nei report**

    I gruppi nei report di analisi dei costi mostrano le categorie di dettaglio standard relative ai dati di fatturazione nel report,  mentre i gruppi nei report di allocazione costi mostrano le categorie basate su tag. Queste ultime sono definite nel modello di allocazione costi, diversamente dalle categorie di dettaglio standard, che sono ricavate dai dati di fatturazione.

    ![tag dei gruppi](./media/understanding-cost-reports/groups-tags01.png)

    ![tag dei gruppi](./media/understanding-cost-reports/groups-tags02.png)

    Nei report di allocazione costi i gruppi delle categorie basate su tag possono includere:
      - Tag
      - Tag di gruppi di risorse
      - Tag di entità di costo Cloudyn
      - Tag di sottoscrizione a scopo di allocazione costi

  Ecco alcuni esempi:
     - Centro di costo
     - department
     - Applicazione
     - Environment
     - Codice di costo

5. **Filtri**

    Usare i filtri di selezione multipla o singola per impostare gli intervalli sui valori selezionati. Per impostare un filtro, fare clic su **Add** (Aggiungi) e quindi selezionare le categorie e i valori del filtro.

6. **Cost Model** (Modello di costi)

    Usare questo elenco per selezionare un modello di costi creato in precedenza con Cost Allocation 360 (Allocazione costi 360). È possibile definire più modelli di costi Cloudyn, a seconda dei propri requisiti di allocazione costi. Alcuni team aziendali possono avere requisiti di allocazione costi diversi da quelli di altri team. Pertanto, ogni team può avere un modello di costi dedicato.

    Per informazioni su come creare una definizione di modello di allocazione costi, vedere [Usare tag personalizzati per allocare i costi](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortization** (Ammortamento)

    Usare questo elenco nei report di allocazione costi per visualizzare le spese di servizio non basate sull'utilizzo o i costi "una tantum" e ripartirli in modo uniforme nel corso della vita utile del servizio. Ecco alcuni esempi di spese "una tantum":
    - Spese annuali per il supporto tecnico
    - Spese annuali per i componenti di sicurezza
    - Spese per l'acquisto di istanze riservate
    - Alcuni elementi di Azure Marketplace

  In Amortization (Ammortamento) selezionare **Amortized cost** (Costo ammortizzato) o **Actual Cost** (Costo effettivo).

8. **Risoluzione**

    Usare questo elenco per selezionare la risoluzione dei dati temporali nell'intervallo di date selezionato. La risoluzione determina il numero di unità visualizzate nel report e può essere:
    - Giornaliera
    - Settimanale
    - Mensile
    - Trimestrale
    - Annuale

9. **Allocation rules** (Regole di allocazione)

    Usare questo elenco per applicare o disabilitare il ricalcolo dell'allocazione costi. È possibile abilitare o disabilitare questa operazione di ricalcolo per i dati di fatturazione. Il ricalcolo si applica alle categorie selezionate nel report. Usando questa opzione è possibile valutare l'impatto del ricalcolo dell'allocazione costi rispetto ai dati di fatturazione non elaborati.

10. **Uncategorized** (Senza categoria)

    Usare questo elenco per includere o escludere i costi non assegnati a una categoria nel report.

11. **Show/Hide Fields** (Mostra/Nascondi campi)

    Questa opzione non ha alcun effetto sui report.

12.   **Formati di visualizzazione**

    Usare le icone dei formati di visualizzazione per selezionare varie visualizzazioni di grafici o tabelle.

    ![formati di visualizzazione](./media/understanding-cost-reports/display-formats.png)

13. **Multi-color** (Multicolore)

    Usare questa opzione per impostare il colore dei grafici nel report.

14. **Actions**

    Usare questa opzione per salvare, esportare o pianificare il report.

## <a name="next-steps"></a>Passaggi successivi

- Se non si è ancora completata la prima esercitazione per Gestione costi, leggere le informazioni riportate in [Esaminare l'utilizzo e i costi](tutorial-review-usage.md).
