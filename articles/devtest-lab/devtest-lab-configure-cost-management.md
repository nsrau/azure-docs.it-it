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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs
La funzionalità di gestione dei costi dei lab di sviluppo/test consente di tenere traccia dei costi del lab. Questo articolo spiega come usare il grafico della **tendenza dei costi mensili stimati** grafico per visualizzare i costi stimati del mese in corso fino alla data odierna e la proiezione dell'ammontare dei costi a fine mese per il mese in corso. Questo articolo illustra anche come migliorare la gestione dei costi dei lab impostando soglie e obiettivi di spesa. Quando questi vengono raggiunti, DevTest Labs segnala i risultati all'utente.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizzazione del grafico della tendenza dei costi mensili stimati
Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura: 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessario, selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco. Il lab potrebbe essere già visualizzato nel dashboard in **Tutte le risorse**.
1. Nell'elenco dei lab selezionare il lab desiderato.  
1. Nell'area **Panoramica** del lab selezionare **Configurazione e criteri**.   
1. A sinistra sotto **VERIFICA COSTI** selezionare **Tendenza costo**.

   La schermata seguente illustra un esempio di grafico dei costi. 
   
    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

Il valore del **Costo stimato** corrisponde al costo attualmente stimato per il mese di calendario corrente. Il **Costo previsto** corrisponde al costo stimato per l'intero mese di calendario corrente, calcolato sulla base del costo del lab dei cinque giorni precedenti.

Gli importi vengono arrotondati al numero intero successivo. Ad esempio:  

* 5,01 arrotondato a 6 
* 5,50 arrotondato a 6
* 5,99 arrotondato a 6

Come indicato sopra il grafico, i costi visualizzati nel grafico per impostazione predefinita sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). È possibile impostare gli obiettivi di spesa visualizzati nei grafici anche [gestendo gli obiettivi di costo relativi al lab.](#managing-cost-targets-for-your-lab)

Il calcolo dei costi *non* include quanto segue:

* Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto, per calcolare il costo del lab, Azure DevTest Labs usa le [API di fatturazione di Azure](../billing/billing-usage-rate-card-overview.md) , che non supportano le sottoscrizioni CSP o Dreamspark.
* Le tariffe della propria offerta. Al momento non è possibile includere le tariffe (indicate nella sottoscrizione) negoziate con Microsoft o i partner Microsoft. Vengono usate solo tariffe a consumo.
* Le imposte
* Gli sconti
* La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

## <a name="managing-cost-targets-for-your-lab"></a>Gestione degli obiettivi di costo per il lab
DevTest Labs consente di migliorare la gestione dei costi nel lab impostando un obiettivo di spesa che è possibile visualizzare nel grafico Tendenza mensile costo stimato. DevTest Labs può anche inviare una notifica quando viene raggiunta la soglia o l'obiettivo di spesa specificato. 

1. Nel riquadro **Panoramica** del lab selezionare **Configurazione e criteri**.
1. A sinistra sotto **VERIFICA COSTI** selezionare **Tendenza costo**.

    ![Pulsate Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. Nel riquadro **Tendenza costo** selezionare **Gestisci target**.

    ![Pulsate Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. Nel riquadro Gestisci target specificare le soglie e gli obiettivi di spesa desiderati. È possibile anche specificare se ogni soglia selezionata deve essere riportata nel grafico della tendenza dei costi o segnalata tramite una notifica webhook.

    ![Riquadro Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selezionare un periodo di tempo durante il quale si vuole che vengano monitorati gli obiettivi di costo.
      - **Mensile**: gli obiettivi di costo vengono monitorati a livello mensile.
      - **Fisso**: gli obiettivi di costo vengono monitorati per l'intervallo di date specificato nei campi Data inizio e Data fine. In genere, questo intervallo corrisponde alla durata pianificata del progetto.
   - Specificare un **Costo target**. Questa cifra potrebbe corrispondere, ad esempio, all'importo che si prevede di spendere per questo lab nel periodo di tempo definito.
   - Selezionare questa opzione per abilitare o disabilitare qualsiasi soglia che si vuole venga segnalata, in incrementi del 25%, fino al 125% del **Costo target** specificato.
      - **Invia notifica**: quando questa soglia viene raggiunta, viene inviata una notifica da un URL webhook specificato.
      - **Traccia nel grafico**: quando questa soglia viene raggiunta, i risultati vengono tracciati sul grafico della tendenza dei costi che è possibile visualizzare seguendo le istruzioni contenute in [Visualizzazione del grafico della tendenza dei costi mensili stimati](#viewing-the-monthly-estimated-cost-trend-chart).
   - Se si sceglie l'opzione **Invia notifica** quando viene raggiunta la soglia, è necessario specificare un URL webhook. Nell'area Integrazioni dei costi selezionare **Fare clic qui per aggiungere un'integrazione**.

      Immettere un URL webhook nel riquadro Configura notifica e quindi selezionare **OK**.

       ![Riquadro Configura notifica](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Se si specifica **Invia notifica**, è necessario definire un URL webhook.
      - Analogamente, se si definisce un URL webhook, è necessario impostare l'opzione **Invia notifica** su **On** nel riquadro Soglia per costo.
      - È necessario creare un webhook prima di poterlo inserire qui.  

      Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Two more things to keep your cost on track in DevTest Labs (Altri due suggerimenti per contenere i costi in DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds? (Perché usare le soglie dei costi?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Passaggi successivi
Altre operazioni da eseguire:

* [Definire i criteri del lab](devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM. 
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare le immagini del Marketplace](devtest-lab-configure-marketplace-images.md): DevTest Labs supporta la creazione di macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

