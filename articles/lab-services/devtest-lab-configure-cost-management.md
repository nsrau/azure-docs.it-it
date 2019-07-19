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
ms.openlocfilehash: 9180c29b807ef26c6426aab75fe74870fef9669a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318172"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Tenere traccia dei costi associati a un Lab in Azure DevTest Labs
Questo articolo fornisce informazioni su come tenere traccia dei costi del Lab. Viene illustrato come visualizzare il costo stimato Trent per il mese di calendario corrente per il Lab. Questo articolo illustra anche come visualizzare i costi mensili per ogni risorsa nel Lab.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visualizza la tendenza del costo mensile stimato del Lab 
In questa sezione viene illustrato come utilizzare il grafico della **tendenza dei costi mensili stimati** per visualizzare il costo stimato del mese di calendario corrente e il costo di fine del mese previsto per il mese di calendario corrente. Si apprenderà anche come gestire i costi del Lab impostando obiettivi e soglie di spesa che, quando raggiunti, attiverà DevTest Labs per segnalare i risultati.

Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco di lab selezionare il proprio lab.  
4. Selezionare **configurazione e criteri** dal menu a sinistra.  
4. Selezionare **tendenza costo** nella sezione **rilevamento costi** nel menu a sinistra. Nella schermata seguente viene illustrato un esempio di grafico dei costi. 
   
    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

    Il valore del **Costo stimato** corrisponde al costo attualmente stimato per il mese di calendario corrente. Il **Costo previsto** corrisponde al costo stimato per l'intero mese di calendario corrente, calcolato sulla base del costo del lab dei cinque giorni precedenti.

    Gli importi vengono arrotondati al numero intero successivo. Ad esempio: 

   * 5,01 arrotondato a 6 
   * 5,50 arrotondato a 6
   * 5,99 arrotondato a 6

     Come indicato sopra il grafico, i costi visualizzati nel grafico per impostazione predefinita sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). È possibile impostare gli obiettivi di spesa visualizzati nei grafici anche [gestendo gli obiettivi di costo relativi al lab.](#managing-cost-targets-for-your-lab)

     I costi seguenti *non* sono inclusi nel calcolo dei costi:

   * Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto, per calcolare il costo del lab, Azure DevTest Labs usa le [API di fatturazione di Azure](../billing/billing-usage-rate-card-overview.md) , che non supportano le sottoscrizioni CSP o Dreamspark.
   * Le tariffe della propria offerta. Al momento non è possibile includere le tariffe (indicate nella sottoscrizione) negoziate con Microsoft o i partner Microsoft. Vengono usate solo tariffe a consumo.
   * Le imposte
   * Gli sconti
   * La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gestione degli obiettivi di costo per il lab
DevTest Labs consente di migliorare la gestione dei costi nel lab impostando un obiettivo di spesa che è possibile visualizzare nel grafico Tendenza mensile costo stimato. DevTest Labs può anche inviare una notifica quando viene raggiunta la soglia o l'obiettivo di spesa specificato. 

1. Nella pagina **tendenza costo** selezionare Gestisci **destinazione**.

    ![Pulsate Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Nella pagina **Gestisci destinazione** specificare un target di spesa e le soglie. È possibile anche specificare se ogni soglia selezionata deve essere riportata nel grafico della tendenza dei costi o segnalata tramite una notifica webhook.

    ![Riquadro Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selezionare un periodo di tempo durante il quale si vuole che vengano monitorati gli obiettivi di costo.
      - **Mensile**: gli obiettivi di costo vengono monitorati a livello mensile.
      - **Corretto**: gli obiettivi di costo vengono monitorati per l'intervallo di date specificato nelle date di inizio e di fine. In genere, questi valori rappresentano il tempo pianificato per l'esecuzione del progetto.
   - Specificare un **Costo target**. Ad esempio, quanto si prevede di spendere in questo Lab nel periodo di tempo definito.
   - Selezionare questa opzione per abilitare o disabilitare qualsiasi soglia che si vuole venga segnalata, in incrementi del 25%, fino al 125% del **Costo target** specificato.
      - **Notifica**: Quando questa soglia viene soddisfatta, viene inviata una notifica tramite un URL del webhook specificato.
      - **Traccia nel grafico**: Quando viene soddisfatta questa soglia, i risultati vengono tracciati sul grafico della tendenza dei costi che è possibile visualizzare, come descritto in visualizzazione del grafico della tendenza dei costi mensili stimati.
   - Se si sceglie l'opzione **Invia notifica** quando viene raggiunta la soglia, è necessario specificare un URL webhook. Nell'area Integrazioni dei costi selezionare **Fare clic qui per aggiungere un'integrazione**. Immettere un **URL webhook** nel riquadro Configura notifica e quindi fare clic su **OK**.

       ![Riquadro Configura notifica](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se si specifica **Invia notifica**, è necessario definire un URL webhook.
     - Analogamente, se si definisce un URL webhook, è necessario impostare l'opzione **Invia notifica** su **On** nel riquadro Soglia per costo.
     - È necessario creare un webhook prima di poterlo inserire qui.  

       Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Visualizza costo per risorsa 
La funzionalità relativa alla tendenza dei costi mensili in Labs consente di visualizzare quanto speso nel mese di calendario corrente. Mostra inoltre la proiezione della spesa fino alla fine del mese, sulla base della spesa negli ultimi sette giorni. Per comprendere il motivo per cui la spesa nel Lab soddisfa le soglie iniziali, è possibile usare la funzionalità **costo per risorsa** che mostra il costo mensile per **ogni risorsa** in una tabella.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Selezionare **configurazione e criteri** dal menu a sinistra.
5. Selezionare **cost by Resource** nella sezione **cost Tracking** nel menu a sinistra. Vengono visualizzati i costi associati a ogni risorsa associata a un Lab. 

    ![Costo per risorsa](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Questa funzionalità consente di identificare facilmente le risorse più costose, in modo da poter intraprendere azioni per ridurre la spesa del Lab. Il costo di una macchina virtuale, ad esempio, si basa sulle dimensioni della macchina virtuale. Maggiore è la dimensione della macchina virtuale, il costo è maggiore. È possibile trovare facilmente le dimensioni di una macchina virtuale e del proprietario, in modo che sia possibile comunicare con il proprietario della macchina virtuale per comprendere il motivo per cui sono necessarie le dimensioni della macchina virtuale e se è possibile ridurre le dimensioni.

I [criteri di arresto automatico](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) consentono di ridurre il costo arrestando le macchine virtuali del Lab in una determinata ora del giorno. Tuttavia, un utente del Lab può rifiutare esplicitamente i criteri di arresto, che aumentano il costo di esecuzione della macchina virtuale. È possibile selezionare una macchina virtuale nella tabella per verificare se è stata esclusa dal criterio di arresto automatico. In tal caso, è possibile comunicare con il proprietario della macchina virtuale per trovare il motivo per cui la macchina virtuale è stata esclusa dal criterio.
 
## <a name="next-steps"></a>Passaggi successivi
Altre operazioni da eseguire:

* [Definire i criteri del lab](devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM. 
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare le immagini del Marketplace](devtest-lab-configure-marketplace-images.md): DevTest Labs supporta la creazione di macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

