---
title: Guida alla gestione dei costi per Azure Lab Services
description: Informazioni sui diversi modi per visualizzare i costi per i servizi Lab.
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445305"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestione dei costi per Azure Lab Services

Gestione costi può essere suddiviso in due aree distinte: stima dei costi e analisi dei costi.  La stima dei costi si verifica quando si configura il Lab per assicurarsi che la struttura iniziale del Lab rientri nel budget previsto.  L'analisi dei costi in genere si verifica alla fine del mese per analizzare i costi e determinare le azioni necessarie per il mese successivo.

## <a name="estimating-the-lab-costs"></a>Stima dei costi del Lab

Ogni dashboard del Lab ha un **costo &** sezione relativa alla fatturazione che prevede una stima approssimativa del costo del Lab per il mese.  La stima dei costi riepiloga l'utilizzo dell'ora con il numero massimo di utenti in base al costo stimato per ore.  Per ottenere una stima più accurata, configurare il Lab, inclusa la [pianificazione](how-to-create-schedules.md), e il dashboard rifletterà il costo stimato.  

Questa stima potrebbe non corrispondere a tutti i costi possibili, ma alcune risorse non sono incluse.  Il costo di preparazione del modello non viene inserito nella stima dei costi.  Può variare in modo significativo nella quantità di tempo necessaria per la creazione del modello. Il costo per l'esecuzione del modello equivale al costo complessivo del Lab all'ora. Eventuali costi della [raccolta di immagini condivise](how-to-use-shared-image-gallery.md) non sono inclusi nel dashboard del Lab perché una raccolta può essere condivisa tra più Lab.  Infine, le ore incorse quando il creatore del Lab avvia una macchina viene esclusa dalla stima.

> [!div class="mx-imgBorder"]
> ![Stima dei costi del dashboard](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>Analisi dell'utilizzo di mesi precedenti

L'analisi dei costi è destinata alla revisione dell'utilizzo dei mesi precedenti per determinare eventuali rettifiche per il Lab.  La suddivisione dei costi in passato si trova nell' [analisi dei costi della sottoscrizione](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  Nella portale di Azure è possibile digitare "sottoscrizioni" nel campo di ricerca superiore e quindi selezionare l'opzione sottoscrizioni.  

> [!div class="mx-imgBorder"]
> ![Ricerca di sottoscrizioni](./media/cost-management-guide/subscription-search.png)

Selezionare la sottoscrizione specifica da rivedere.

> [!div class="mx-imgBorder"]
> ![Selezione della sottoscrizione](./media/cost-management-guide/subscription-select.png)

 Selezionare "analisi dei costi" nel riquadro a sinistra in **Gestione costi**.

 > [!div class="mx-imgBorder"]
> ![Analisi dei costi della sottoscrizione](./media/cost-management-guide/subscription-cost-analysis.png)

Questo dashboard consente l'analisi approfondita dei costi, inclusa la possibilità di esportare in diversi tipi di file in base a una pianificazione.  Gestione costi offre numerose funzionalità per altre informazioni, vedere [Panoramica della fatturazione di gestione costi](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtro per tipo di risorsa: `microsoft.labservices/labaccounts` visualizzerà solo il costo associato a Lab Services.

## <a name="understanding-the-usage"></a>Informazioni sull'utilizzo

Di seguito è riportato un esempio dell'analisi dei costi.

> [!div class="mx-imgBorder"]
> ![Analisi dei costi della sottoscrizione](./media/cost-management-guide/cost-analysis.png)

Per impostazione predefinita sono presenti sei colonne: risorsa, tipo di risorsa, percorso, nome del gruppo di risorse, tag, costo.  La colonna risorsa contiene le informazioni relative all'account Lab, al nome del Lab e alla VM.  Le righe con account Lab/nome Lab/valore predefinito corrispondono al costo del Lab, che può essere visualizzato nella seconda e nella terza riga.  Le macchine virtuali usate avranno un costo in base al nome Lab/nome Lab/valore predefinito/VM.  In questo esempio, l'aggiunta della prima riga con la seconda riga, che inizia con "aaalab/dockerlab", fornirà il costo totale per il Lab "dockerlab" nell'account Lab "aaalab".

Per ottenere informazioni sulla raccolta immagini condivise, modificare il tipo di risorsa in `Microsoft.Compute/Galleries` , che fornirà il costo complessivo per la raccolta immagini.  Le raccolte di immagini di condivisione potrebbero non essere visualizzate nei costi a seconda della posizione in cui è archiviata la raccolta.

> [!NOTE]
> La raccolta di immagini condivise è connessa all'account Lab.  Ciò significa che più laboratori potrebbero usare la stessa immagine.

## <a name="separating-costs"></a>Separazione dei costi

Alcune università hanno usato l'account Lab e il gruppo di risorse come modo per separare le diverse classi.  Ogni classe avrà un proprio account Lab e un gruppo di risorse. Nel riquadro analisi costi aggiungere un filtro in base al nome del gruppo di risorse con il nome del gruppo di risorse appropriato per la classe e saranno visibili solo i costi per tale classe.  Ciò consente una delineatura più chiara tra le diverse classi durante la visualizzazione dei costi.  La funzionalità di [esportazione pianificata](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) dell'analisi dei costi consente di scaricare i costi di ogni classe in file distinti.

## <a name="managing-costs"></a>Gestione dei costi

A seconda del tipo di classe, esistono modi per gestire i costi per ridurre il fatto che le macchine virtuali siano in esecuzione senza uno studente che usa il computer.

### <a name="auto-shutdown-on-disconnect"></a>Arresto automatico alla disconnessione

Al momento della creazione del Lab, il proprietario del Lab può impostare le macchine virtuali nel Lab in modo che vengano [arrestate quando la connessione RDP alla VM è disconnessa](how-to-enable-shutdown-disconnect.md).  Questa impostazione riduce lo scenario in cui lo studente si disconnette ma dimentica di arrestare la macchina virtuale.

### <a name="quota-vs-scheduled-time"></a>Quota rispetto a tempo pianificato

La comprensione del tempo di [quota](classroom-labs-concepts.md#quota) rispetto al [tempo pianificato](classroom-labs-concepts.md#schedules) consentirà al proprietario del Lab di configurare il Lab in modo da soddisfare al meglio le esigenze del professore e degli studenti.  L'ora pianificata è un set di tempo in cui tutte le macchine virtuali degli studenti sono state avviate e sono disponibili per la connessione a.  Comunemente pianificata viene usato in una situazione in cui tutti gli studenti avranno una propria macchina virtuale e seguono le direzioni del professore in un momento stabilito durante il giorno, ad esempio le ore di classe.  Lo svantaggio è che tutte le macchine virtuali degli studenti vengono avviate e stanno accumulando costi, anche se uno studente non accede alla macchina virtuale.  Il tempo di quota è il tempo allocato a ogni studente che può utilizzare a propria discrezione e viene spesso utilizzato per lo studio indipendente. Le macchine virtuali non vengono avviate fino a quando lo studente non avvia la macchina virtuale.  

Un Lab può utilizzare una quota, un'ora pianificata o una combinazione di entrambi. Se una classe non necessita dell'ora pianificata, usare solo il tempo di quota per l'uso più efficace delle macchine virtuali.

### <a name="scheduled-event---stop-only"></a>Evento pianificato-solo arresto

Nella pianificazione è possibile aggiungere un tipo di evento stop only, che arresterà tutti i computer in un momento specifico.  Alcuni proprietari di Lab hanno impostato un evento stop only per ogni giorno a mezzanotte per ridurre i costi e l'utilizzo delle quote quando uno studente dimentica di arrestare la macchina virtuale in uso.  Lo svantaggio di questo tipo di evento è che tutte le macchine virtuali verranno arrestate anche se lo studente usa la macchina virtuale.

### <a name="other-costs-related-to-labs"></a>Altri costi correlati ai Lab 

Sono previsti costi che non vengono inseriti nei servizi Lab, ma possono essere collegati a un servizio lab.  Una raccolta di immagini condivise può essere connessa ai laboratori, ma non viene visualizzata in base ai costi dei servizi Lab e ha costi.  Per ridurre i costi complessivi è necessario rimuovere tutte le immagini non usate dalla raccolta perché le immagini hanno un costo di archiviazione che eredita.  I laboratori possono avere connessioni ad altre risorse di Azure tramite una rete virtuale (VNet) quando viene rimosso un Lab. è necessario rimuovere VNet e altre risorse.

## <a name="conclusion"></a>Conclusioni

Le informazioni riportate sopra consentono di comprendere meglio i costi di utilizzo e come usare gli strumenti per ridurre i costi in eccesso.
