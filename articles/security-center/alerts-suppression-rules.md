---
title: Uso delle regole di eliminazione avvisi per eliminare i falsi positivi o altri avvisi di sicurezza indesiderati nel Centro sicurezza di Azure.
description: Questo articolo illustra come usare le regole di eliminazione del Centro sicurezza di Azure per nascondere gli avvisi di sicurezza indesiderati.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 84b5cd8a59103f60249da861238acb45f8aa2fd5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871706"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Eliminazione avvisi della protezione contro le minacce del Centro sicurezza di Azure

Questo articolo illustra come usare le regole di eliminazione avvisi per eliminare i falsi positivi o altri avvisi di sicurezza indesiderati nel Centro sicurezza di Azure.

## <a name="availability"></a>Disponibilità

- Data di rilascio: **Anteprima**
- Ruoli richiesti: **amministratore della sicurezza** e **proprietario** possono creare/eliminare regole. **amministratore che legge i dati di sicurezza** e **lettore** possono visualizzare le regole.
- Cloud: 
    - ✔ Cloud commerciali
    - ✔ Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)


## <a name="introduction-to-suppression-rules"></a>Introduzione alle regole di eliminazione

I componenti di protezione dalle minacce del Centro sicurezza di Azure rilevano le minacce in qualsiasi area dell'ambiente e generano avvisi di sicurezza.

Quando un singolo avviso non è interessante o pertinente, è possibile ignorarlo manualmente. In alternativa, usare la funzionalità delle regole di eliminazione per ignorare automaticamente gli avvisi simili in futuro. In genere, si usa una regola di eliminazione per:

- eliminare gli avvisi identificati come falsi positivi

- eliminare gli avvisi che vengono attivati troppo spesso per essere utili

Le regole di eliminazione definiscono i criteri per cui gli avvisi devono essere automaticamente ignorati.

> [!CAUTION]
> L'eliminazione degli avvisi di sicurezza riduce la protezione dalle minacce del Centro sicurezza. È necessario valutare con attenzione l'impatto potenziale di qualsiasi regola di eliminazione e monitorarlo nel tempo.

[![Pagina degli avvisi di sicurezza del Centro sicurezza di Azure con opzioni di eliminazione degli avvisi](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>Creazione di una regola di eliminazione

Esistono diversi modi per creare regole per l'eliminazione degli avvisi di sicurezza indesiderati:

- Per eliminare gli avvisi a livello di gruppo di gestione, usare Criteri di Azure

- Per eliminare gli avvisi a livello di sottoscrizione, è possibile usare il portale di Azure o l'API REST come illustrato di seguito

Le regole di eliminazione consentono di ignorare solo gli avvisi che sono già stati attivati almeno una volta nelle sottoscrizioni selezionate.

Per creare una regola direttamente nel portale di Azure:

1. Dalla pagina degli avvisi di sicurezza del Centro sicurezza:

    - Individuare l'avviso specifico che non si vuole visualizzare più e dal menu con i puntini di sospensione (...) per l'avviso selezionare **Create suppression rule** (Crea regola di eliminazione):

        [![Opzione **Crea regola di eliminazione**](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - In alternativa, selezionare il collegamento **Regole di eliminazione** nella parte superiore della pagina e nella pagina delle regole di eliminazione selezionare **Crea una nuova regola di eliminazione**:

        ![Pulsante Crea una nuova regola di eliminazione**](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Nel riquadro della nuova regola di eliminazione immettere i dettagli della nuova regola.

    - La regola può far sì che l'avviso venga ignorato per **tutte le risorse** in modo da non ricevere avvisi come questo in futuro. 
    
    - La regola può far sì che l'avviso venga ignorato **in base a criteri specifici**, quando si riferisce a un indirizzo IP, a un nome di processo, a un account utente, a una risorsa di Azure o a una località specifici.

    > [!TIP]
    > Se la pagina della nuova regola è stata aperta da un avviso specifico, l'avviso e la sottoscrizione verranno configurati automaticamente con la nuova regola. Se è stato usato il collegamento **Crea una nuova regola di eliminazione**, le sottoscrizioni selezionate corrisponderanno al filtro corrente nel portale.

    [![Riquadro di creazione regola di eliminazione](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. Immettere i dettagli della regola:

    - **Nome**: nome della regola. I nomi delle regole devono iniziare con una lettera o un numero, avere una lunghezza compresa tra 2 e 50 caratteri e non possono contenere simboli ad eccezione di trattini ( - ) o caratteri di sottolineatura (_). 
    - **Stato**: può essere Abilitato o Disabilitato.
    - **Motivo**: selezionare uno dei motivi predefiniti o "altro" se questi non soddisfano le proprie esigenze.
    - **Data scadenza**: data e ora di fine della regola. Le regole possono essere eseguite per un massimo di sei mesi.

1. Facoltativamente, testare la regola usando il pulsante **Simula** per visualizzare il numero di avvisi che verrebbero ignorati se la regola fosse attiva.

1. Salvare la regola. 

## <a name="editing-suppression-rules"></a>Modifica di regole di eliminazione

Per modificare le regole create, usare la pagina delle regole di eliminazione.

1. Nella pagina degli avvisi di sicurezza del Centro sicurezza selezionare il collegamento **Regole di eliminazione** nella parte superiore della pagina.

1. Si apre la pagina delle regole di eliminazione che elenca tutte le regole disponibili in base alle sottoscrizioni attualmente selezionate. 

    [![Elenco delle regole di eliminazione](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Per modificare una singola regola, aprire il menu con i puntini di sospensione (...) per la regola e selezionare **Modifica**.

1. Apportare le modifiche necessarie e selezionare **Applica**. 

## <a name="deleting-suppression-rules"></a>Eliminazione di regole di eliminazione

Per eliminare una o più regole create, usare la pagina delle regole di eliminazione.

1. Nella pagina degli avvisi di sicurezza del Centro sicurezza selezionare il collegamento **Regole di eliminazione** nella parte superiore della pagina.

1. Si apre la pagina delle regole di eliminazione che elenca tutte le regole disponibili in base alle sottoscrizioni attualmente selezionate. 

1. Per eliminare una singola regola, aprire il menu con i puntini di sospensione (...) per la regola e selezionare **Elimina**.

1. Per eliminare più regole, selezionare le caselle di controllo per le regole da eliminare e selezionare **Elimina**.

    ![Eliminazione di una o più regole di eliminazione](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>Visualizzazione degli avvisi eliminati

Gli avvisi che corrispondono alle regole di eliminazione abilitate vengono comunque generati, ma il loro stato viene impostato su **Ignorato**. È possibile visualizzare lo stato nel portale di Azure o accedendo agli avvisi di sicurezza del Centro sicurezza. 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) non creerà eventi imprevisti per gli avvisi eliminati. Per le altre tecnologie SIEM, è possibile filtrare gli avvisi eliminati usando lo stato degli avvisi "Ignorato".

Usare il filtro del Centro sicurezza per visualizzare gli avvisi che sono stati ignorati in base alle regole.

* Nella pagina degli avvisi di sicurezza del Centro sicurezza aprire le opzioni di filtro e selezionare **Ignorato**.  

   [![Visualizzazione degli avvisi ignorati](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>Uso dell'API per creare e gestire le regole di eliminazione

È possibile creare, visualizzare o eliminare regole di eliminazione degli avvisi con l'API REST del Centro sicurezza. 

I metodi HTTP pertinenti per le regole di eliminazione nell'API REST sono:

- **PUT**: per creare o aggiornare una regola di eliminazione in una sottoscrizione specificata.

- **GET**:

    - Per elencare tutte le regole configurate per una sottoscrizione specificata. Questo metodo restituisce una matrice delle regole applicabili.

    - Per ottenere i dettagli di una regola specifica in una sottoscrizione specificata. Questo metodo restituisce una regola di eliminazione.

    - Per simulare l'impatto di una regola di eliminazione ancora nella fase di progettazione. Questa chiamata identifica gli avvisi esistenti che verrebbero ignorati se la regola fosse attiva.

- **DELETE**: elimina una regola esistente ma non modifica lo stato degli avvisi già ignorati in base alla regola.

Per informazioni complete ed esempi di utilizzo, vedere la [documentazione dell'API](https://docs.microsoft.com/rest/api/securitycenter/). 


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive le regole di eliminazione nel Centro sicurezza di Azure che ignorano automaticamente gli avvisi indesiderati.

Per altre informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure, vedere le pagine seguenti:

- [Avvisi di sicurezza e catena di attacco delle finalità](alerts-reference.md): una guida di riferimento per gli avvisi di sicurezza che possono essere visualizzati nel modulo di protezione dalle minacce del Centro sicurezza di Azure.
- [Protezione dalle minacce nel Centro sicurezza di Azure](threat-protection.md): una descrizione dei numerosi aspetti dell'ambiente monitorati dal modulo di protezione dalle minacce del Centro sicurezza di Azure.