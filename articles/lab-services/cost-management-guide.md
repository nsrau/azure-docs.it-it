---
title: Guida alla gestione dei costi per Azure Lab Services
description: Informazioni sui diversi modi per visualizzare i costi per i servizi Lab.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98ce4d5e82d65d911984dc45615253ddcae33ae1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589868"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestione dei costi per Azure Lab Services

Gestione costi può essere suddiviso in due aree distinte: stima dei costi e analisi dei costi.  La stima dei costi si verifica quando si configura il Lab per assicurarsi che la struttura iniziale del Lab rientri nel budget previsto.  L'analisi dei costi in genere si verifica alla fine del mese per analizzare i costi e determinare le azioni necessarie per il mese successivo.

## <a name="estimate-the-lab-costs"></a>Stimare i costi del Lab

Ogni dashboard del Lab ha un **costo &** sezione relativa alla fatturazione che prevede una stima approssimativa del costo del Lab per il mese.  La stima dei costi riepiloga l'utilizzo dell'ora con il numero massimo di utenti in base al costo stimato per ore.  Per ottenere una stima più accurata, configurare il Lab, inclusa la [pianificazione](how-to-create-schedules.md), e il dashboard rifletterà il costo stimato.  

Questa stima potrebbe non corrispondere a tutti i costi possibili, ma alcune risorse non sono incluse.  Il costo di preparazione del modello non viene inserito nella stima dei costi.  Può variare in modo significativo nella quantità di tempo necessaria per la creazione del modello. Il costo per l'esecuzione del modello equivale al costo complessivo del Lab all'ora. Eventuali costi della [raccolta di immagini condivise](how-to-use-shared-image-gallery.md) non sono inclusi nel dashboard del Lab perché una raccolta può essere condivisa tra più Lab.  Infine, le ore incorse quando il creatore del Lab avvia una macchina viene esclusa dalla stima.

> [!div class="mx-imgBorder"]
> ![Stima dei costi del dashboard](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Analizza utilizzo mesi precedenti

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

## <a name="understand-the-usage"></a>Informazioni sull'utilizzo

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

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Ottimizzare il controllo dei costi con le impostazioni di arresto automatico

Le funzionalità di controllo dei costi con arresto automatico consentono di evitare gli sprechi di ore di utilizzo delle macchine virtuali all'interno dei Lab. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:

> [!div class="mx-imgBorder"]
> ![Analisi dei costi della sottoscrizione](./media/cost-management-guide/auto-shutdown-disconnect.png)

Queste impostazioni possono essere configurate a livello di account Lab e a livello Lab. Se le impostazioni sono abilitate a livello di account Lab, vengono applicate a tutti i Lab nell'account Lab. Per tutti i nuovi account Lab, queste impostazioni sono attivate per impostazione predefinita. 

#### <a name="details-about-auto-shutdown-settings"></a>Dettagli sulle impostazioni di arresto automatico

* Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo (solo Windows).

    > [!NOTE]
    > Questa impostazione è disponibile solo per le macchine virtuali Windows.

    Quando l'impostazione è attivata, l'utente viene disconnesso da qualsiasi computer nel Lab quando il sistema operativo Windows ritiene che la sessione sia inattiva (incluse le macchine virtuali modello). [La definizione del sistema operativo Windows di Idle](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) usa due criteri: 

    * Assenza utente: nessun input della tastiera o del mouse.
    * Mancanza di consumo di risorse: tutti i processori e tutti i dischi erano inattivi per un determinato periodo di tempo

    Gli utenti visualizzeranno un messaggio simile all'interno della macchina virtuale prima della disconnessione: 

    > [!div class="mx-imgBorder"]
    > ![Analisi dei costi della sottoscrizione](./media/cost-management-guide/idle-timer-expired.png)
    
    La macchina virtuale è ancora in esecuzione quando l'utente è disconnesso. Se l'utente si riconnette alla macchina virtuale eseguendo l'accesso, le finestre o i file aperti o non salvati in precedenza alla disconnessione rimarranno ancora disponibili. In questo stato, poiché la macchina virtuale è in esecuzione, viene comunque conteggiata come attiva e il costo viene accumulato. 
    
    Per arrestare automaticamente le macchine virtuali Windows inattive disconnesse, utilizzare la combinazione di **Disconnetti utenti quando le macchine virtuali sono inattive** e **arrestano le macchine virtuali quando gli utenti disconnettono** le impostazioni.

    Ad esempio, se si configurano le impostazioni come segue:
    
    * Disconnettere gli utenti quando le macchine virtuali sono inattive: 15 minuti dopo il rilevamento dello stato di inattività.
    * Arrestare le macchine virtuali quando gli utenti si disconnettono-5 minuti dopo la disconnessione dell'utente.
    
    Le macchine virtuali Windows verranno arrestate automaticamente 20 minuti dopo l'arresto dell'utilizzo da parte dell'utente. 
    
    > [!div class="mx-imgBorder"]
    > ![Analisi dei costi della sottoscrizione](./media/cost-management-guide/vm-idle-diagram.png)
* Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono (Windows & Linux).
    
    Questa impostazione supporta le macchine virtuali Windows e Linux. Quando questa impostazione è impostata su on, l'arresto automatico si verifica nei casi seguenti:
    
    * Per Windows la connessione Desktop remoto (RDP) è disconnessa.
    * Per Linux, la connessione SSH è disconnessa.
    
    > [!NOTE]
    > Sono supportate solo [le distribuzioni e le versioni specifiche di Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) .
    
    È possibile specificare per quanto tempo le macchine virtuali devono attendere che l'utente si riconnetta prima di arrestarsi automaticamente. 
* Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.
     
    All'interno di un Lab, un utente potrebbe avviare una macchina virtuale ma non connettersi mai. Ad esempio:
    
    * Una pianificazione nel Lab avvia tutte le macchine virtuali per una sessione di classe, ma alcuni studenti non vengono visualizzati e non si connettono ai computer.  
    * Un utente avvia una macchina virtuale, ma dimentica di connettersi. 
    
    L'impostazione "arresta macchine virtuali quando gli utenti non si connette" rileva questi casi e arresta automaticamente le macchine virtuali.  
    
Per informazioni su come configurare e abilitare l'arresto automatico delle macchine virtuali alla disconnessione, vedere questi articoli:

* [Configurare l'arresto automatico delle macchine virtuali tramite l'impostazione di disconnessione per un account lab](how-to-configure-lab-accounts.md)
* [Abilitare l'arresto automatico delle macchine virtuali al termine della connessione](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Quota rispetto a tempo pianificato

La comprensione del tempo di [quota](classroom-labs-concepts.md#quota) rispetto al [tempo pianificato](classroom-labs-concepts.md#schedules) consentirà al proprietario del Lab di configurare il Lab in modo da soddisfare al meglio le esigenze del professore e degli studenti.  L'ora pianificata è un set di tempo in cui tutte le macchine virtuali degli studenti sono state avviate e sono disponibili per la connessione a.  Comunemente pianificata viene usato in una situazione in cui tutti gli studenti avranno una propria macchina virtuale e seguono le direzioni del professore in un momento stabilito durante il giorno, ad esempio le ore di classe.  Lo svantaggio è che tutte le macchine virtuali degli studenti vengono avviate e stanno accumulando costi, anche se uno studente non accede alla macchina virtuale.  Il tempo di quota è il tempo allocato a ogni studente che può utilizzare a propria discrezione e viene spesso utilizzato per lo studio indipendente. Le macchine virtuali non vengono avviate fino a quando lo studente non avvia la macchina virtuale.  

Un Lab può utilizzare una quota, un'ora pianificata o una combinazione di entrambi. Se una classe non necessita dell'ora pianificata, usare solo il tempo di quota per l'uso più efficace delle macchine virtuali.

### <a name="scheduled-event---stop-only"></a>Evento pianificato-solo arresto

Nella pianificazione è possibile aggiungere un tipo di evento stop only, che arresterà tutti i computer in un momento specifico.  Alcuni proprietari di Lab hanno impostato un evento stop only per ogni giorno a mezzanotte per ridurre i costi e l'utilizzo delle quote quando uno studente dimentica di arrestare la macchina virtuale in uso.  Lo svantaggio di questo tipo di evento è che tutte le macchine virtuali verranno arrestate anche se lo studente usa la macchina virtuale.

### <a name="other-costs-related-to-labs"></a>Altri costi correlati ai Lab 

Sono previsti costi che non vengono inseriti nei servizi Lab, ma possono essere collegati a un servizio lab.  Una raccolta di immagini condivise può essere connessa ai laboratori, ma non viene visualizzata in base ai costi dei servizi Lab e ha costi.  Per ridurre i costi complessivi è necessario rimuovere tutte le immagini non usate dalla raccolta perché le immagini hanno un costo di archiviazione che eredita.  I laboratori possono avere connessioni ad altre risorse di Azure tramite una rete virtuale (VNet) quando viene rimosso un Lab. è necessario rimuovere VNet e altre risorse.

## <a name="conclusion"></a>Conclusioni

Le informazioni riportate sopra consentono di comprendere meglio i costi di utilizzo e come usare gli strumenti per ridurre i costi in eccesso.
