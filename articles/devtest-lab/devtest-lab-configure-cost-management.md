<properties
	pageTitle="Configurare la gestione dei costi | Microsoft Azure"
	description="Informazioni su come configurare le funzionalità di gestione dei costi dei lab di sviluppo/test"
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
	ms.date="04/18/2016"
	ms.author="tarcher"/>

# Configurare la gestione dei costi

## Panoramica

La funzionalità di gestione dei costi dei lab di sviluppo/test consente di tenere traccia dei costi del lab. Questo articolo spiega come usare il grafico della **tendenza dei costi mensili stimati** grafico per visualizzare i costi stimati del mese in corso fino alla data odierna e la proiezione dell'ammontare dei costi a fine mese per il mese in corso.

## Abilitazione del grafico della tendenza dei costi mensili stimati

Per abilitare il grafico della tendenza dei costi mensili stimati, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Nell'elenco dei lab toccare il lab desiderato.

1. Toccare **Impostazioni**.

	![Impostazioni](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. Nel pannello delle **impostazioni** del lab toccare **Cost Thresholds** (Soglie di costo) in **Cost Policies** (Criteri di costo).

	![Menu](./media/devtest-lab-configure-cost-management/menu.png)
 
1. Nel pannello **Cost Thresholds** (Soglie dei costi) toccare **On** per abilitare la funzionalità e **Off** per disabilitarla.

1. Toccare **Salva**.

Dopo aver abilitato questa funzionalità, possono trascorrere alcune ore prima che il grafico visualizzi i costi stimati e previsti. Questo accade poiché il servizio di raccolta delle informazioni viene eseguito ogni ora, ma in ritardo di alcune ore rispetto alla raccolta dei dati live. Si supponga ad esempio di avviare una macchina virtuale alle 13:00. Il costo associato a tale macchina probabilmente non verrà incorporato nel grafico dei costi per un paio d'ore.
 
La schermata seguente illustra un esempio di grafico dei costi.

![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

Il valore **Costo stimato** è il costo stimato per il mese in corso fino alla data odierna mentre **Costo previsto** è il costo stimato per l'intero mese in corso.

Come indicato sopra il grafico, i costi visualizzati nel grafico sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Il calcolo dei costi *non* include quanto segue:

- Le tariffe della propria offerta. Al momento non è possibile includere le tariffe, indicate nella sottoscrizione, negoziate con Microsoft o i partner Microsoft. Vengono usate tariffe a consumo.
- Le imposte
- Gli sconti
- La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

## Passaggi successivi

Altre operazioni da eseguire:

- [Definire i criteri dei lab](./devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative macchine virtuali. 
- [Creare un'immagine personalizzata](./devtest-lab-create-template.md): quando si crea una macchina virtuale, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
- [Configurare immagini del Marketplace](./devtest-lab-configure-marketplace-images.md): il lab di sviluppo/test supporta la creazione di nuove macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di nuove macchine virtuali in un lab.
- [Creare una VM in un lab](./devtest-lab-add-vm-with-artifacts.md): questo articolo illustra come creare una nuova macchina virtuale da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

<!---HONumber=AcomDC_0427_2016-->