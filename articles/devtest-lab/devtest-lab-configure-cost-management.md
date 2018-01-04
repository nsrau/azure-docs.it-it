---
title: Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs | Documentazione Microsoft
description: Informazioni sul grafico relativo alla tendenza dei costi mensili stimati di Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: v-craic
ms.openlocfilehash: 660180fac4f4743bc543b1babf7dbe921bf8c26b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs
La funzionalità di gestione dei costi dei lab di sviluppo/test consente di tenere traccia dei costi del lab. Questo articolo spiega come usare il grafico della **tendenza dei costi mensili stimati** grafico per visualizzare i costi stimati del mese in corso fino alla data odierna e la proiezione dell'ammontare dei costi a fine mese per il mese in corso. Questo articolo illustra anche come una migliore gestione dei costi lab impostando spesa target e soglie che, quando raggiunto, i trigger DevTest Labs per riportare i risultati all'utente.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizzazione del grafico della tendenza dei costi mensili stimati
Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura: 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessario, selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco. Il lab potrebbe essere già visualizzato nel dashboard in **Tutte le risorse**.
1. Nell'elenco dei lab selezionare il lab desiderato.  
1. Nell'area **Panoramica** del lab selezionare **Configurazione e criteri**.   
1. A sinistra sotto **costo rilevamento**selezionare **tendenza costo**.

   La schermata seguente illustra un esempio di grafico dei costi. 
   
    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

Il valore del **Costo stimato** corrisponde al costo attualmente stimato per il mese di calendario corrente. Il **Costo previsto** corrisponde al costo stimato per l'intero mese di calendario corrente, calcolato sulla base del costo del lab dei cinque giorni precedenti.

Gli importi vengono arrotondati al numero intero successivo. Ad esempio:  

* 5,01 arrotondato a 6 
* 5,50 arrotondato a 6
* 5,99 arrotondato a 6

Come è indicato sopra il grafico, i costi viene visualizzato per impostazione predefinita nel grafico sono *stimato* costi utilizzando [consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) offrono tariffe. È inoltre possibile impostare la propria spesa destinazioni che vengono visualizzate nei grafici da [gestione le destinazioni di costo per l'ambiente lab.](#managing-cost-targets-for-your-lab)

Il calcolo dei costi *non* include quanto segue:

* Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto, per calcolare il costo del lab, Azure DevTest Labs usa le [API di fatturazione di Azure](../billing/billing-usage-rate-card-overview.md) , che non supportano le sottoscrizioni CSP o Dreamspark.
* Le tariffe della propria offerta. Attualmente, non è possibile utilizzare le tariffe dell'offerta (mostrate sotto della sottoscrizione) che hanno negoziate con Microsoft o Microsoft partner. Vengono utilizzati solo tariffe di consumo.
* Le imposte
* Gli sconti
* La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

## <a name="managing-cost-targets-for-your-lab"></a>La gestione delle destinazioni di costo per l'ambiente lab
DevTest Labs consente di gestire meglio i costi nell'ambiente lab tramite l'impostazione di una destinazione di spesa è quindi possibile visualizzare nel grafico di tendenza di costo stimato mensile. DevTest Labs può inoltre inviare una notifica quando viene raggiunta la soglia o spesa di destinazione specificato. 

1. Il lab **Panoramica** riquadro, selezionare **criteri di configurazione e**.
1. A sinistra sotto **costo rilevamento**selezionare **tendenza costo**.

    ![Gestire il pulsante di destinazione](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. Nel **tendenza costo** riquadro, selezionare **Gestisci destinazione**.

    ![Gestire il pulsante di destinazione](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. Nel riquadro destinazione gestione, specificare la destinazione di spesa desiderato e alle soglie. È inoltre possibile specificare se ogni limite selezionato viene segnalato nel grafico di tendenza costo o tramite una notifica webhook.

    ![Gestire il riquadro di destinazione](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selezionare un periodo di tempo durante il quale si desidera destinazioni costo rilevate.
      - **Mensile**: costo destinazioni vengono registrate per ogni mese.
      - **Predefinito**: costo destinazioni vengono tenuta traccia per l'intervallo di date specificate nei campi data di fine e data di inizio. In genere, ciò potrebbe corrispondere con quanto tempo il progetto è pianificato l'esecuzione.
   - Specificare un **destinazione costo**. Ad esempio, potrebbe trattarsi quanto si prevede di impiegare questo lab nel periodo di tempo definito.
   - Selezionare questa opzione per abilitare o disabilitare qualsiasi soglia desiderata segnalati – in incrementi di 25%: fino a 125% lo specificato **destinazione costo**.
      - **Notificare**: quando questa soglia viene raggiunta, verrà inviata una notifica è specificare un URL del webhook.
      - **Tracciare sul grafico**: quando questa soglia viene raggiunta, i risultati vengono tracciati sul grafico di tendenza di costo che è possibile visualizzare, come descritto in [visualizzazione del grafico di tendenza di costo stimato mensile](#viewing-the-monthly-estimated-cost-trend-chart).
   - Se si sceglie di **notifica** quando la soglia viene raggiunta, è necessario specificare un URL del webhook. Nell'area di integrazioni di costo, selezionare **fare clic qui per aggiungere un'integrazione**.

      Immettere un URL del Webhook nel riquadro Configura notifica e quindi selezionare **OK**.

       ![Configurare riquadro delle notifiche](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Se si specifica **notifica**, è necessario definire un URL del webhook.
      - Analogamente, se si definisce un URL del webhook, è necessario impostare **notifica** a **su** nel riquadro di soglia di costo.
      - È necessario creare un webhook prima immetterlo qui.  

      Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Two more things to keep your cost on track in DevTest Labs (Altri due suggerimenti per contenere i costi in DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds? (Perché usare le soglie dei costi?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Passaggi successivi
Altre operazioni da eseguire:

* [Definire i criteri del lab](devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM. 
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare le immagini di Marketplace](devtest-lab-configure-marketplace-images.md) - DevTest Labs supporta la creazione di macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

