---
title: Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs | Documentazione Microsoft
description: Informazioni sul grafico relativo alla tendenza dei costi mensili stimati di Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517267"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Tenere traccia dei costi associati a un lab in Azure DevTest Labs
Questo articolo fornisce informazioni su come tenere traccia dei costi del lab. Viene illustrato come visualizzare stimati trent dei costi per il mese di calendario corrente per l'ambiente lab. L'articolo illustra anche come visualizzare i costi di month-to-date per ogni risorsa nel lab.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visualizzare la tendenza dei costi mensili stimati per il lab 
In questa sezione descrive come usare il **tendenza dei costi mensili stimati** grafico per visualizzare stimato costi-to-date il mese di calendario corrente e il costo previsto di fine del mese per mese di calendario corrente. Anche informazioni su come gestire i costi dei lab impostando soglie e obiettivi di spesa che, quando raggiunti, DevTest Labs segnala i risultati all'utente.

Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco di lab selezionare il proprio lab.  
4. Selezionare **configurazione e criteri** nel menu a sinistra.  
4. Selezionare **tendenza dei costi** nel **verifica dei costi** sezione nel menu a sinistra. Lo screenshot seguente mostra un esempio di un grafico dei costi. 
   
    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

    Il valore del **Costo stimato** corrisponde al costo attualmente stimato per il mese di calendario corrente. Il **Costo previsto** corrisponde al costo stimato per l'intero mese di calendario corrente, calcolato sulla base del costo del lab dei cinque giorni precedenti.

    Gli importi vengono arrotondati al numero intero successivo. Ad esempio:  

   * 5,01 arrotondato a 6 
   * 5,50 arrotondato a 6
   * 5,99 arrotondato a 6

     Come indicato sopra il grafico, i costi visualizzati nel grafico per impostazione predefinita sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). È possibile impostare gli obiettivi di spesa visualizzati nei grafici anche [gestendo gli obiettivi di costo relativi al lab.](#managing-cost-targets-for-your-lab)

     I costi seguenti sono rappresentati *non* inclusi nel calcolo dei costi:

   * Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto, per calcolare il costo del lab, Azure DevTest Labs usa le [API di fatturazione di Azure](../billing/billing-usage-rate-card-overview.md) , che non supportano le sottoscrizioni CSP o Dreamspark.
   * Le tariffe della propria offerta. Al momento non è possibile includere le tariffe (indicate nella sottoscrizione) negoziate con Microsoft o i partner Microsoft. Vengono usate solo tariffe a consumo.
   * Le imposte
   * Gli sconti
   * La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gestione degli obiettivi di costo per il lab
DevTest Labs consente di migliorare la gestione dei costi nel lab impostando un obiettivo di spesa che è possibile visualizzare nel grafico Tendenza mensile costo stimato. DevTest Labs può anche inviare una notifica quando viene raggiunta la soglia o l'obiettivo di spesa specificato. 

1. Nel **tendenza dei costi** pagina, selezionare **Gestisci target**.

    ![Pulsate Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Nel **Gestisci target** , specificare un obiettivo di spesa e le soglie. È possibile anche specificare se ogni soglia selezionata deve essere riportata nel grafico della tendenza dei costi o segnalata tramite una notifica webhook.

    ![Riquadro Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selezionare un periodo di tempo durante il quale si vuole che vengano monitorati gli obiettivi di costo.
      - **Mensile**: gli obiettivi di costo vengono monitorati a livello mensile.
      - **Fisso**: gli obiettivi di costo vengono monitorati per l'intervallo di date specificato in date di inizio e fine. In genere, questi valori rappresentano la durata pianificata del progetto per l'esecuzione.
   - Specificare un **Costo target**. Ad esempio, quanto si prevede di spendere questo lab nel periodo di tempo definito.
   - Selezionare questa opzione per abilitare o disabilitare qualsiasi soglia che si vuole venga segnalata, in incrementi del 25%, fino al 125% del **Costo target** specificato.
      - **Inviare una notifica**: Quando questa soglia viene raggiunta, ricevono una notifica tramite un URL webhook specificato.
      - **Vykreslit v grafu**: Quando questa soglia viene raggiunta, i risultati vengono tracciati sul grafico di tendenza dei costi che è possibile visualizzare, come descritto nella visualizzazione del grafico della tendenza dei costo mensili stimati.
   - Se si sceglie l'opzione **Invia notifica** quando viene raggiunta la soglia, è necessario specificare un URL webhook. Nell'area Integrazioni dei costi selezionare **Fare clic qui per aggiungere un'integrazione**. Immettere un **URL del Webhook** nel riquadro Configura notifica e quindi selezionare **OK**.

       ![Riquadro Configura notifica](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se si specifica **Invia notifica**, è necessario definire un URL webhook.
     - Analogamente, se si definisce un URL webhook, è necessario impostare l'opzione **Invia notifica** su **On** nel riquadro Soglia per costo.
     - È necessario creare un webhook prima di poterlo inserire qui.  

       Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Visualizzare il costo per risorsa 
La funzionalità di tendenza dei costi mensili nei lab consente di vedere quanto speso nel mese di calendario corrente. Indica inoltre la proiezione di spesa fino alla fine del mese, basato sulla spesa negli ultimi sette giorni. Per comprendere il motivo per cui la spesa nel lab soddisfa le soglie in anticipo, è possibile usare la **costo per risorsa** funzionalità che mostra il costo di month-to-date **per ogni risorsa** in una tabella.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Selezionare **configurazione e criteri** nel menu a sinistra.
5. Selezionare **costo per risorsa** nel **verifica dei costi** sezione nel menu a sinistra. Noterete che i costi associati a ogni risorsa associata a un lab. 

    ![Costo per risorsa](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Questa funzionalità consente di identificare facilmente le risorse che costano più in modo che si possano intraprendere azioni per ridurre la spesa lab. Ad esempio, il costo di una macchina virtuale è basato sulle dimensioni della macchina virtuale. Maggiori sono le dimensioni della macchina virtuale, più sono il costo. È possibile trovare facilmente le dimensioni di una macchina virtuale e il proprietario, in modo che possono comunicare con il proprietario della macchina virtuale per comprendere il motivo per cui sono necessario tali dimensioni di macchina virtuale e se è possibile ridurre le dimensioni.

[Criteri di arresto automatico](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) aiuta a ridurre i costi, arresto di macchine virtuali del lab a una determinata ora del giorno. Tuttavia, un utente del lab può rifiutare esplicitamente i criteri di arresto, aumentando così il costo di esecuzione della macchina virtuale. Nella tabella per vedere se si è stato scelto esplicitamente i criteri di arresto automatico, è possibile selezionare una macchina virtuale. Se è questo il caso, può comunicare con il proprietario della macchina virtuale per individuare il motivo per cui la macchina virtuale ha stato scelto esplicitamente i criteri.
 
## <a name="next-steps"></a>Passaggi successivi
Altre operazioni da eseguire:

* [Definire i criteri del lab](devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM. 
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare le immagini del Marketplace](devtest-lab-configure-marketplace-images.md): DevTest Labs supporta la creazione di macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

