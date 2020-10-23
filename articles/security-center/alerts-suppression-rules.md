---
title: Uso delle regole di eliminazione avvisi per eliminare i falsi positivi o altri avvisi di sicurezza indesiderati nel Centro sicurezza di Azure.
description: Questo articolo illustra come usare le regole di eliminazione del Centro sicurezza di Azure per nascondere gli avvisi di Azure Defender indesiderati
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/10/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 50426e0b0920e89cf83dc5a81c515b06c06c09c5
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342094"
---
# <a name="suppress-alerts-from-azure-defender"></a>Eliminare gli avvisi di Azure Defender

Questa pagina illustra come è possibile usare le regole di eliminazione degli avvisi per disattivare falsi positivi o altri avvisi di sicurezza indesiderati da Azure Defender.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima|
|Prezzi:|Livello gratuito<br>(La maggior parte degli avvisi di sicurezza è disponibile solo con Azure Defender)|
|Autorizzazioni e ruoli obbligatori:|Il **proprietario** e l' **amministratore della sicurezza** possono creare o eliminare regole.<br>**amministratore che legge i dati di sicurezza** e **lettore** possono visualizzare le regole.|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||


## <a name="what-are-suppression-rules"></a>Che cosa sono le regole di eliminazione?

I diversi piani di Azure Defender rilevano le minacce in qualsiasi area dell'ambiente e generano avvisi di sicurezza.

Quando un singolo avviso non è interessante o pertinente, è possibile ignorarlo manualmente. In alternativa, usare la funzionalità delle regole di eliminazione per ignorare automaticamente gli avvisi simili in futuro. In genere, si usa una regola di eliminazione per:

- Non visualizzare gli avvisi identificati come falsi positivi

- Non visualizzare gli avvisi che vengono attivati troppo spesso per essere utili

Le regole di eliminazione definiscono i criteri per cui gli avvisi devono essere automaticamente ignorati.

> [!CAUTION]
> L'eliminazione degli avvisi di sicurezza riduce l'efficacia della protezione dalle minacce di Azure Defender. È necessario valutare con attenzione l'impatto potenziale di qualsiasi regola di eliminazione e monitorarlo nel tempo.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Crea regola di eliminazione avvisi":::

## <a name="create-a-suppression-rule"></a>Creare una regola di eliminazione

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


## <a name="edit-a-suppression-rules"></a>Modificare le regole di eliminazione

Per modificare le regole create, usare la pagina regole di eliminazione.

1. Nella pagina degli avvisi di sicurezza del Centro sicurezza selezionare il collegamento **Regole di eliminazione** nella parte superiore della pagina.
1. Verrà visualizzata la pagina regole di eliminazione con tutte le regole per le sottoscrizioni selezionate.

    [![Elenco delle regole di eliminazione](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Per modificare una singola regola, aprire il menu con i puntini di sospensione (...) per la regola e selezionare **Modifica**.
1. Apportare le modifiche necessarie e selezionare **Applica**. 

## <a name="delete-a-suppression-rule"></a>Eliminare una regola di eliminazione

Per eliminare una o più regole create, usare la pagina delle regole di eliminazione.

1. Nella pagina degli avvisi di sicurezza del Centro sicurezza selezionare il collegamento **Regole di eliminazione** nella parte superiore della pagina.
1. Verrà visualizzata la pagina regole di eliminazione con tutte le regole per le sottoscrizioni selezionate.
1. Per eliminare una singola regola, aprire il menu con i puntini di sospensione (...) per la regola e selezionare **Elimina**.
1. Per eliminare più regole, selezionare le caselle di controllo per le regole da eliminare e selezionare **Elimina**.
    ![Eliminazione di una o più regole di eliminazione](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>Visualizza avvisi eliminati

Gli avvisi che corrispondono alle regole di eliminazione abilitate vengono comunque generati, ma il loro stato viene impostato su **Ignorato**. È possibile visualizzare lo stato nel portale di Azure o accedendo agli avvisi di sicurezza del Centro sicurezza. 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) non creerà eventi imprevisti per gli avvisi eliminati. Per le altre tecnologie SIEM, è possibile filtrare gli avvisi eliminati usando lo stato degli avvisi "Ignorato".

Usare il filtro del Centro sicurezza per visualizzare gli avvisi che sono stati ignorati in base alle regole.

* Nella pagina degli avvisi di sicurezza del Centro sicurezza aprire le opzioni di filtro e selezionare **Ignorato**.  

   [![Visualizzazione degli avvisi ignorati](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>Creare e gestire le regole di eliminazione con l'API

È possibile creare, visualizzare o eliminare regole di eliminazione degli avvisi con l'API REST del Centro sicurezza. 

I metodi HTTP pertinenti per le regole di eliminazione nell'API REST sono:

- **PUT**: per creare o aggiornare una regola di eliminazione in una sottoscrizione specificata.

- **GET**:

    - Per elencare tutte le regole configurate per una sottoscrizione specificata. Questo metodo restituisce una matrice delle regole applicabili.

    - Per ottenere i dettagli di una regola specifica in una sottoscrizione specificata. Questo metodo restituisce una regola di eliminazione.

    - Per simulare l'impatto di una regola di eliminazione ancora nella fase di progettazione. Questa chiamata identifica gli avvisi esistenti che verrebbero ignorati se la regola fosse attiva.

- **DELETE**: elimina una regola esistente ma non modifica lo stato degli avvisi già ignorati in base alla regola.

Per informazioni complete ed esempi di utilizzo, vedere la [documentazione dell'API](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive le regole di eliminazione nel Centro sicurezza di Azure che ignorano automaticamente gli avvisi indesiderati.

Per ulteriori informazioni sugli avvisi di sicurezza di Azure Defender, vedere le pagine seguenti:

- [Avvisi di sicurezza e la catena di Kill Intent](alerts-reference.md) : una guida di riferimento per gli avvisi di sicurezza che si potrebbero ottenere da Azure Defender.