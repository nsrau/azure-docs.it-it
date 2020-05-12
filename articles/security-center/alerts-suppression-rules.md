---
title: Uso delle regole di eliminazione degli avvisi per disattivare falsi positivi o altri avvisi di sicurezza indesiderati nel centro sicurezza di Azure.
description: Questo articolo illustra come usare le regole di eliminazione del Centro sicurezza di Azure per nascondere gli avvisi di sicurezza indesiderati.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 5a5d6a96bd73a67c2611e7f334eba3daeda031ba
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007700"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Eliminazione degli avvisi dalla protezione dalle minacce del Centro sicurezza di Azure

Questa pagina illustra come è possibile usare le regole di eliminazione degli avvisi per disattivare falsi positivi o altri avvisi di sicurezza indesiderati nel centro sicurezza di Azure.

## <a name="availability"></a>Disponibilità

- Stato versione: **Anteprima**
- Ruoli necessari: l'amministratore della sicurezza e il proprietario possono creare o eliminare regole. Il lettore e il lettore di sicurezza possono visualizzare le regole.
- Cloud: tutti (globale, nazionale, governativo e sovrano)


## <a name="introduction-to-suppression-rules"></a>Introduzione alle regole di eliminazione

I componenti di protezione dalle minacce del Centro sicurezza di Azure rilevano le minacce in qualsiasi area dell'ambiente e generano avvisi di sicurezza.

Quando un singolo avviso non è interessante o pertinente, è possibile ignorarlo manualmente. In alternativa, utilizzare la funzionalità regole di eliminazione per ignorare automaticamente gli avvisi simili in futuro. In genere, si usa una regola di eliminazione per:

- non visualizzare gli avvisi identificati come falsi positivi

- non visualizzare gli avvisi che vengono attivati troppo spesso per essere utili

Le regole di eliminazione definiscono i criteri per i quali gli avvisi devono essere rilasciati automaticamente.

> [!CAUTION]
> L'eliminazione degli avvisi di sicurezza riduce la protezione dalle minacce del Centro sicurezza. È necessario controllare con attenzione il potenziale impatto di qualsiasi regola di eliminazione e monitorarlo nel tempo.

[![Pagina degli avvisi di sicurezza del Centro sicurezza di Azure con opzioni di eliminazione degli avvisi](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>Creazione di una regola di eliminazione

Esistono diversi modi per creare regole per l'eliminazione degli avvisi di sicurezza indesiderati:

- Per disattivare gli avvisi a livello di gruppo di gestione, usare criteri di Azure

- Per non visualizzare gli avvisi a livello di sottoscrizione, è possibile usare il portale di Azure o l'API REST come illustrato di seguito.

Le regole di eliminazione possono ignorare solo gli avvisi che sono già stati attivati nelle sottoscrizioni selezionate.

Per creare una regola direttamente nel portale di Azure:

1. Dalla pagina degli avvisi di sicurezza del Centro sicurezza:

    - Individuare l'avviso specifico che non si desidera visualizzare più e dal menu con i puntini di sospensione (...) per l'avviso selezionare **Crea regola di eliminazione**:

        [![* * Opzione Crea regola di eliminazione * *](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - In alternativa, selezionare il collegamento **regole di eliminazione** nella parte superiore della pagina e nella pagina regole di eliminazione selezionare **Crea nuova regola di eliminazione**:

        ![Pulsante Crea nuova regola di eliminazione * *](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Nel riquadro nuova regola di eliminazione immettere i dettagli della nuova regola.

    - La regola può ignorare l'avviso per **tutte le risorse** in modo da non ricevere avvisi come questo in futuro. 
    
    - La regola può ignorare l'avviso **in base a criteri specifici** , quando si riferisce a un indirizzo IP specifico, a un nome di processo, a un account utente, a una risorsa di Azure o a una località.

    > [!TIP]
    > Se è stata aperta la pagina nuova regola da un avviso specifico, l'avviso e la sottoscrizione verranno configurati automaticamente nella nuova regola. Se è stato usato il collegamento **Crea nuova regola di eliminazione** , le sottoscrizioni selezionate corrisponderanno al filtro corrente nel portale.

    [![Riquadro di creazione regola di eliminazione](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. Immettere i dettagli della regola:

    - **Nome** : un nome per la regola. I nomi delle regole devono iniziare con una lettera o un numero, avere una lunghezza compresa tra 2 e 50 caratteri e non contenere simboli diversi da trattini (-) o caratteri di sottolineatura (_). 
    - **Stato** : abilitato o disabilitato.
    - **Motivo** : selezionare uno dei motivi predefiniti o "altro" se non soddisfano le proprie esigenze.
    - **Data di scadenza** : data e ora di fine della regola. Le regole possono essere eseguite per un massimo di sei mesi.

1. Facoltativamente, testare la regola usando il pulsante **simula** per visualizzare il numero di avvisi che verrebbero esclusi se questa regola fosse stata attiva.

1. Salvare la regola. 

## <a name="editing-suppression-rules"></a>Modifica di regole di eliminazione

Per modificare le regole create, usare la pagina regole di eliminazione.

1. Nella pagina avvisi di sicurezza del Centro sicurezza selezionare il collegamento **regole di eliminazione** nella parte superiore della pagina.

1. Nella pagina regole di eliminazione viene visualizzato un elenco di tutte le regole disponibili in base alle sottoscrizioni attualmente selezionate. 

    [![Elenco di regole di eliminazione](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Per modificare una singola regola, aprire il menu con i puntini di sospensione (...) per la regola e selezionare **modifica**.

1. Apportare le modifiche necessarie e selezionare **applica**. 

## <a name="deleting-suppression-rules"></a>Eliminazione di regole di eliminazione

Per eliminare una o più regole create, usare la pagina regole di eliminazione.

1. Nella pagina avvisi di sicurezza del Centro sicurezza selezionare il collegamento **regole di eliminazione** nella parte superiore della pagina.

1. Nella pagina regole di eliminazione viene visualizzato un elenco di tutte le regole disponibili in base alle sottoscrizioni attualmente selezionate. 

1. Per eliminare una singola regola, aprire il menu con i puntini di sospensione (...) per la regola e selezionare **Elimina**.

1. Per eliminare più regole, selezionare le caselle di controllo per le regole da eliminare e selezionare **Elimina**.

    ![Eliminazione di una o più regole di eliminazione](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>Visualizzazione di avvisi eliminati

Gli avvisi che corrispondono alle regole di eliminazione abilitate verranno comunque generati, ma il relativo stato verrà impostato su **Dismissed**. È possibile visualizzare lo stato nel portale di Azure o accedere agli avvisi di sicurezza del Centro sicurezza. 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) non creerà eventi imprevisti per gli avvisi eliminati. Per gli altri SIEM, è possibile filtrare gli avvisi eliminati usando lo stato degli avvisi (' Dismissed ').

Usare il filtro del Centro sicurezza per visualizzare gli avvisi che sono stati rilasciati dalle regole.

* Nella pagina avvisi di sicurezza del Centro sicurezza aprire le opzioni di filtro e selezionare non **rilasciata**.  

   [![Visualizzazione di avvisi dimenticati](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>Uso dell'API per creare e gestire le regole di eliminazione

È possibile creare, visualizzare o eliminare regole di eliminazione degli avvisi tramite l'API REST del Centro sicurezza. 

I metodi HTTP pertinenti per le regole di eliminazione nell'API REST sono:

- **Put**: per creare o aggiornare una regola di eliminazione in una sottoscrizione specificata.

- **Ottenere**:

    - Per elencare tutte le regole configurate per una sottoscrizione specificata. Questo metodo restituisce una matrice delle regole applicabili.

    - Per ottenere i dettagli di una regola specifica in una sottoscrizione specificata. Questo metodo restituisce una regola di eliminazione.

    - Per simulare l'effetto di una regola di eliminazione ancora nella fase di progettazione. Questa chiamata identifica gli avvisi esistenti che sarebbero stati rilasciati se la regola era attiva.

- **Delete**: Elimina una regola esistente (ma non modifica lo stato degli avvisi già rilasciati).

Per informazioni complete ed esempi di utilizzo, vedere la [documentazione dell'API](https://docs.microsoft.com/api/securitycenter/). 


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive le regole di eliminazione nel centro sicurezza di Azure che ignorano automaticamente gli avvisi indesiderati.

Per ulteriori informazioni sugli avvisi di sicurezza nel centro sicurezza di Azure, vedere le pagine seguenti:

- [Avvisi di sicurezza e la catena di Kill Intent](alerts-reference.md) : una guida di riferimento per gli avvisi di sicurezza che possono essere visualizzati nel modulo di protezione dalle minacce del Centro sicurezza di Azure.
- [Protezione dalle minacce nel centro sicurezza di Azure](threat-protection.md) : una descrizione dei numerosi aspetti dell'ambiente monitorati dal modulo di protezione dalle minacce del Centro sicurezza di Azure.