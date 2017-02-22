---
title: Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs | Documentazione Microsoft
description: Informazioni sul grafico relativo alla tendenza dei costi mensili stimati di Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d428e0e3aaf8fd4a2138648411da644ccd308f6
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e


---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs
La funzionalità di gestione dei costi dei lab di sviluppo/test consente di tenere traccia dei costi del lab. Questo articolo spiega come usare il grafico della **tendenza dei costi mensili stimati** grafico per visualizzare i costi stimati del mese in corso fino alla data odierna e la proiezione dell'ammontare dei costi a fine mese per il mese in corso. Questo articolo illustra come visualizzare il grafico della tendenza dei costo mensili stimati nel portale di Azure.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizzazione del grafico della tendenza dei costi mensili stimati
Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura: 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.   
4. Nel pannello del lab, selezionare **Cost settings**(Impostazioni dei costi).
5. Nel pannello **Impostazioni dei costi** del lab selezionare **Tendenza costo lab**.
6. La schermata seguente illustra un esempio di grafico dei costi. 
   
    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

Il valore del **Costo stimato** corrisponde al costo attualmente stimato per il mese di calendario corrente. Il **Costo previsto** corrisponde al costo stimato per l'intero mese di calendario corrente, calcolato sulla base del costo del lab dei cinque giorni precedenti.

Gli importi vengono arrotondati al numero intero successivo. Ad esempio: 

* 5,01 arrotondato a 6 
* 5,50 arrotondato a 6
* 5,99 arrotondato a 6

Come indicato sopra il grafico, i costi visualizzati nel grafico sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) .
Il calcolo dei costi *non* include quanto segue:

* Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto, per calcolare il costo del lab, Azure DevTest Labs usa le [API di fatturazione di Azure](../billing/billing-usage-rate-card-overview.md) , che non supportano le sottoscrizioni CSP o Dreamspark.
* Le tariffe della propria offerta. Al momento non è possibile includere le tariffe, indicate nella sottoscrizione, negoziate con Microsoft o i partner Microsoft. Vengono usate tariffe a consumo.
* Le imposte
* Gli sconti
* La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Two more things to keep your cost on track in DevTest Labs (Altri due suggerimenti per contenere i costi in DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds? (Perché usare le soglie dei costi?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Passaggi successivi
Altre operazioni da eseguire:

* [Definire i criteri del lab](devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM. 
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare le immagini del Marketplace](devtest-lab-configure-marketplace-images.md): DevTest Labs supporta la creazione di VM basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm-with-artifacts.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.




<!--HONumber=Feb17_HO2-->


