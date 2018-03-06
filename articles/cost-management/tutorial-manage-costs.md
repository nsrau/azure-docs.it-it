---
title: 'Esercitazione: Gestire i costi tramite Gestione costi di Azure | Microsoft Docs'
description: "In questa esercitazione si apprenderà come gestire i costi usando l'allocazione dei costi e i report di showback e chargeback."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7e9f11e702dbbe8864f8a0c307777c922e5f028d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-manage-costs-by-using-azure-cost-management"></a>Esercitazione: Gestire i costi tramite Gestione costi di Azure

Per gestire i costi e generare i report di showback in Gestione costi di Azure di Cloudyn, si esegue l'allocazione dei costi in base a tag. Il processo di allocazione dei costi assegna i costi alle risorse cloud utilizzate. I costi sono interamente allocati quando tutte le risorse sono suddivise in categorie con tag. Dopo aver allocato i costi, è possibile mostrare agli utenti le informazioni di showback o chargeback usando dashboard e report. Tuttavia, quando si inizia a usare Gestione costi, è possibile che molte risorse non siano contrassegnate né contrassegnabili con tag.

Si supponga ad esempio di voler richiedere un rimborso per costi di progettazione. Sarà necessario essere in grado di dimostrare al team di progettazione che si deve ricevere un importo specifico, in base ai costi delle risorse. A tal fine, è possibile mostrare al team un report per tutte le risorse utilizzate che sono contrassegnate con il tag *progettazione*.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare tag personalizzati per allocare i costi.
> * Creare report di showback e chargeback.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- È necessario disporre di un account Azure.
- È necessario avere una registrazione di valutazione o una sottoscrizione a pagamento per Gestione costi di Azure.

## <a name="use-custom-tags-to-allocate-costs"></a>Usare tag personalizzati per allocare i costi

Quando si inizia il processo di allocazione dei costi, è innanzitutto necessario definire l'ambito usando un modello dei costi. In questo modello i costi non vengono modificati, ma distribuiti. Quando si crea un modello dei costi, si segmentano i dati in base a entità di costo, account o sottoscrizione e anche in base a più tag. Tra i tag di esempio comuni possono essere inclusi un codice di fatturazione, un centro di costo o un nome di gruppo. I tag sono anche utili per eseguire lo showback o il chargeback ad altre parti dell'organizzazione.

Per creare un modello personalizzato di allocazione dei costi, selezionare **Costo** (Costo) &gt; **Cost Management** (Gestione costi) &gt; **Cost Allocation 360°** (Allocazione costi a 360°) dal menu del report.

![Selezione di Cost Allocation 360° (Allocazione costi a 360°)](./media/tutorial-manage-costs/cost-allocation-360.png)

Nella pagina **Cost Allocation 360°** (Allocazione costi a 360°) scegliere **Add** (Aggiungi) e quindi immettere un nome e una descrizione per il modello dei costi. Selezionare tutti gli account oppure singoli account. Se si vogliono usare singoli account, è possibile selezionare più account da diversi provider di servizi cloud. Fare quindi clic su **Categorization** (Categorizzazione) per scegliere i tag individuati per suddividere in categorie i dati dei costi. Scegliere i tag (o categorie) da includere nel modello. Nell'esempio seguente è selezionato il tag **Unit**.

![Esempio di categorizzazione del modello dei costi](./media/tutorial-manage-costs/cost-model01.png)



L'esempio mostra che un importo pari a 14.444 USD è senza categoria, ovvero senza tag.

Selezionare **Uncategorized Resources** (Risorse senza categoria) e specificare i servizi per cui sono presenti costi non allocati. Definire quindi le regole per allocare i costi.

Può ad esempio essere opportuno distribuire equamente i costi di archiviazione di Azure tra le diverse macchine virtuali (VM, Virtual Machine) di Azure. A tale scopo, selezionare il servizio **Azure/Storage** (Azure/Archiviazione), quindi **Proportional to Categorized** (Proporzionale a Con categoria) e infine **Azure/VM**. Scegliere quindi **Create** (Crea).

![Esempio di regola di allocazione del modello dei costi per una distribuzione equa](./media/tutorial-manage-costs/cost-model02.png)



In un altro caso, può invece essere opportuno allocare tutti i costi di rete di Azure a una specifica business unit all'interno dell'organizzazione. A tale scopo, selezionare il servizio **Azure/Network** (Azure/Rete) e quindi **Explicit Distribution** (Distribuzione esplicita). Impostare quindi la percentuale di distribuzione su 100 e selezionare la business unit, ovvero **G&amp;A** nell'immagine seguente:

![Esempio di regola di allocazione del modello dei costo per una specifica business unit](./media/tutorial-manage-costs/cost-model03.png)



Per tutte le risorse senza categoria rimanenti, creare altre regole di allocazione.

Se si hanno istanze riservate di Amazon Web Services (AWS) non allocate, è possibile assegnarle a categorie con tag usando **Reserved Instances** (Istanze riservate).

Per visualizzare informazioni sulle scelte effettuate per allocare i costi, selezionare **Summary** (Riepilogo). Per salvare le informazioni e continuare a creare o modificare altre regole in un secondo momento, selezionare **Save As Draft** (Salva come bozza). In alternativa, per salvare le informazioni e avviare in Cloudyn l'elaborazione del modello di allocazione dei costi, selezionare **Save and Activate** (Salva e attiva).

Nell'elenco è visualizzato il nuovo modello dei costi con lo stato **Processing** (Elaborazione in corso). L'aggiornamento del database di Cloudyn con il modello dei costi può richiedere del tempo. Al termine dell'elaborazione, lo stato viene aggiornato come **Completed** (Completato). È quindi possibile visualizzare i dati del modello dei costi nel report di analisi dei costi in **Extended Filters** (Filtri estesi) &gt; **Cost Model** (Modello costi).

### <a name="category-manager"></a>Gestione delle categorie

Category Manager è uno strumento per la pulizia dei dati che consente di unire i valori di più categorie (tag) per crearne di nuove. È uno strumento semplice basato su regole in cui si seleziona una categoria e si creano regole per unire valori esistenti. Si supponga ad esempio di avere due categorie per **R&amp;D** e **dev** che rappresentano entrambe il gruppo dedicato allo sviluppo.

Nel portale di Cloudyn fare clic sul simbolo dell'ingranaggio nell'angolo in alto a destra e selezionare **Category Manager**. Per creare una nuova categoria, selezionare il segno più (**+**). Immettere un nome per la categoria e quindi in **Keys** (Chiavi) immettere le chiavi delle categorie da includere nella nuova categoria.

Quando si definisce una regola, è possibile aggiungere più valori con una condizione OR e anche eseguire alcune operazioni di base sulla stringa. In entrambi i casi, fare clic sui tre puntini di sospensione (**…**) a destra di **Rule** (Regola).

Per definire una nuova regola, usare l'area **Rules** (Rules). Immettere ad esempio **dev** in **Rules** (Regole) e quindi **R&amp;D** in **Actions** (Azioni). Al termine, salvare la nuova categoria.

La figura seguente mostra un esempio di regole create per una nuova categoria denominata **Work-Load**:

![Categoria di esempio](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Origini di tag e report

I dati dei tag visualizzati nei report di Cloudyn hanno origine in tre posti:

- API delle risorse del provider di servizi cloud
- API di fatturazione del provider di servizi cloud
- Tag creati manualmente dalle origini seguenti:
    - Tag di entità Cloudyn: metadati definiti dall’utente applicati alle entità Cloudyn
    - Gestione delle categorie: uno strumento di pulizia dati che crea nuovi tag in base alle regole applicate ai tag esistenti

Per visualizzare i tag del provider di servizi cloud nei report di costo di Cloudyn, è necessario creare un modello personalizzato di allocazione dei costi usando Cost Allocation 360. A tale scopo, apire **Costo** > **Gestione dei costi** > **Cost Allocation 360**, selezionare i tag desiderati e quindi definire le regole per la gestione dei costi senza tag. Quindi, creare un nuovo modello di costo. In seguito, è possibile visualizzare report nell'analisi di allocazione dei costi per vedere, filtrare e ordinare i tag delle risorse di Azure.

I tag delle risorse di Azure vengono visualizzati solo nei report di **analisi dell’allocazione dei costi**.

I tag di fatturazione del provider di servizi cloud sono visualizzati in tutti i report sui costi.

I tag di entità del cloud creati manualmente sono visualizzati in tutti i report sui costi.


## <a name="create-showback-and-chargeback-reports"></a>Creare report di showback e chargeback

Il metodo usato dalle organizzazioni per eseguire lo showback e il chargeback varia notevolmente. È tuttavia possibile usare uno qualsiasi dei dashboard e dei report disponibili nel portale di Cloudyn come base per entrambe le attività. È possibile concedere l'accesso a qualsiasi utente dell'organizzazione in modo che possa visualizzare dashboard e report su richiesta. Tutti i report di analisi dei costi supportano lo showback in quanto mostrano agli utenti le risorse che hanno utilizzato. Consentono inoltre agli utenti di analizzare i dati relativi ai costi o all'utilizzo specifici del gruppo a cui appartengono all'interno dell'organizzazione.

Per visualizzare i risultati dell'allocazione dei costi, aprire il report di analisi dei costi e selezionare il modello dei costi creato. Aggiungere quindi un raggruppamento in base a uno o più tag selezionati nel modello dei costi.

![Report di analisi dei costi](./media/tutorial-manage-costs/cost-analysis.png)

È possibile creare e salvare facilmente report relativi a servizi specifici utilizzati da determinati gruppi. Se ad esempio è presente un reparto che fa ampio uso di macchine virtuali di Azure, si può creare un report che viene filtrato in base alle macchine virtuali di Azure per visualizzarne l'utilizzo e i costi.

È infine possibile esportare i report in formato PDF o CSV per fornire una sintesi dei dati ad altri team.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare tag personalizzati per allocare i costi.
> * Creare report di showback e chargeback.



Passare all'esercitazione successiva per apprendere come controllare l'accesso ai dati.

> [!div class="nextstepaction"]
> [Controllo dell'accesso ai dati](tutorial-user-access.md)
