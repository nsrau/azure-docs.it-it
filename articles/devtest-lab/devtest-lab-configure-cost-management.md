<properties
	pageTitle="Tendenza dei costi mensili stimati | Microsoft Azure"
	description="Informazioni sul grafico relativo alla tendenza dei costi mensili stimati di DevTest Labs."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="tarcher"/>

# Tendenza dei costi mensili stimati

## Panoramica

La funzionalità di gestione dei costi dei lab di sviluppo/test consente di tenere traccia dei costi del lab. Questo articolo spiega come usare il grafico della **tendenza dei costi mensili stimati** grafico per visualizzare i costi stimati del mese in corso fino alla data odierna e la proiezione dell'ammontare dei costi a fine mese per il mese in corso.

## Visualizzazione del grafico della tendenza dei costi mensili stimati

Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Sfoglia** e poi **Lab di sviluppo e test** dall’elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Selezionare **Impostazioni**.

	![Impostazioni](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. Nel pannello **Impostazioni** del lab selezionare **Soglie** in **Gestione dei costi**.

	![Menu](./media/devtest-lab-configure-cost-management/menu.png)
 
1. La schermata seguente illustra un esempio di grafico dei costi.

    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

Il valore **Costo stimato** è il costo stimato per il mese in corso fino alla data odierna mentre **Costo previsto** è il costo stimato per l'intero mese in corso, calcolato usando il costo del lab nei 5 giorni precedenti.
 
Si noti che gli importi di costo vengono arrotondati al numero intero successivo. ad esempio:

- 5,01 arrotondato a 6
- 5,50 arrotondato a 6
- 5,99 arrotondato a 6

Come indicato sopra il grafico, i costi visualizzati nel grafico sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Il calcolo dei costi *non* include quanto segue:

- Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto DevTest Labs usa le [API di fatturazione di Azure](../billing-usage-rate-card-overview.md) per calcolare il costo del lab e queste ultime non supportano le sottoscrizioni CSP o Dreamspark.
- Le tariffe della propria offerta. Al momento non è possibile includere le tariffe, indicate nella sottoscrizione, negoziate con Microsoft o i partner Microsoft. Vengono usate tariffe a consumo.
- Le imposte
- Gli sconti
- La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

## Post di blog correlati

- [Two more things to keep your cost on track in DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/) (Altri due suggerimenti per contenere i costi in DevTest Labs)
- [Why Cost Thresholds?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/) (Perché usare le soglie dei costi?)

## Passaggi successivi

Altre operazioni da eseguire:

- [Definire i criteri del lab](./devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM.
- [Creare un'immagine personalizzata](./devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
- [Configurare le impostazioni dell'immagine di Azure Marketplace in un lab](./devtest-lab-configure-marketplace-images.md): DevTest Labs supporta la creazione di nuove VM basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di nuove macchine virtuali in un lab.
- [Aggiungere una macchina virtuale con elementi a un lab](./devtest-lab-add-vm-with-artifacts.md): questo articolo illustra come creare una nuova VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

<!---HONumber=AcomDC_0803_2016-->