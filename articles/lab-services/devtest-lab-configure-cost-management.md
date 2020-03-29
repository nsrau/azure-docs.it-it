---
title: Visualizzare la tendenza dei costi mensili stimati per il lab in Azure DevTest Labs
description: Questo articolo fornisce informazioni su come tenere traccia del costo del lab (grafico di tendenza dei costi stimati mensili) in Azure DevTest Labs.This article provides information on how to track the cost of your lab (monthly estimated cost trend chart) in Azure DevTest Labs.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721728"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Tenere traccia dei costi associati a un lab in Azure DevTest LabsTrack costs associated with a lab in Azure DevTest Labs
In questo articolo vengono fornite informazioni su come tenere traccia del costo del lab. Viene illustrato come visualizzare il costo stimato per il mese di calendario corrente per il lab. L'articolo illustra inoltre come visualizzare il costo da inizio mese per risorsa nel lab.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visualizzare l'andamento dei costi di laboratorio stimati mensilmente 
In questa sezione viene illustrato come utilizzare il grafico **Tendenza costo mensile stimato** per visualizzare il costo stimato del mese di calendario corrente e il costo previsto di fine mese per il mese di calendario corrente. È inoltre possibile imparare a gestire i costi di laboratorio impostando gli obiettivi di spesa e le soglie che, una volta raggiunti, attivano DevTest Labs per segnalare i risultati all'utente.

Per visualizzare il grafico della tendenza dei costi mensili stimati, seguire questa procedura: 

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco di lab selezionare il proprio lab.  
4. Selezionare **Configurazione e criteri** nel menu a sinistra.  
4. Seleziona **Tendenza costi** nella sezione Monitoraggio **costi** nel menu a sinistra. La schermata seguente mostra un esempio di grafico dei costi. 
   
    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

    Il valore del **Costo stimato** corrisponde al costo attualmente stimato per il mese di calendario corrente. Il **Costo previsto** corrisponde al costo stimato per l'intero mese di calendario corrente, calcolato sulla base del costo del lab dei cinque giorni precedenti.

    Gli importi vengono arrotondati al numero intero successivo. Ad esempio: 

   * 5,01 arrotondato a 6 
   * 5,50 arrotondato a 6
   * 5,99 arrotondato a 6

     Come indicato sopra il grafico, i costi visualizzati nel grafico per impostazione predefinita sono costi *stimati* in base alle tariffe del piano di [pagamento a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). È possibile impostare gli obiettivi di spesa visualizzati nei grafici anche [gestendo gli obiettivi di costo relativi al lab.](#managing-cost-targets-for-your-lab)

     I seguenti costi *non* sono inclusi nel calcolo dei costi:

   * Le sottoscrizioni CSP e Dreamspark non sono attualmente supportate in quanto, per calcolare il costo del lab, Azure DevTest Labs usa le [API di fatturazione di Azure](../cost-management-billing/manage/usage-rate-card-overview.md) , che non supportano le sottoscrizioni CSP o Dreamspark.
   * Le tariffe della propria offerta. Al momento non è possibile includere le tariffe (indicate nella sottoscrizione) negoziate con Microsoft o i partner Microsoft. Vengono usate solo tariffe a consumo.
   * Le imposte
   * Gli sconti
   * La valuta di fatturazione Al momento i costi del lab vengono visualizzati solo in USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gestione degli obiettivi di costo per il lab
DevTest Labs consente di migliorare la gestione dei costi nel lab impostando un obiettivo di spesa che è possibile visualizzare nel grafico Tendenza mensile costo stimato. DevTest Labs può anche inviare una notifica quando viene raggiunta la soglia o l'obiettivo di spesa specificato. 

1. Nella pagina **Tendenza costi** selezionare **Gestisci destinazione**.

    ![Pulsate Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Nella pagina **Gestisci destinazione** specificare un obiettivo di spesa e le soglie. È possibile anche specificare se ogni soglia selezionata deve essere riportata nel grafico della tendenza dei costi o segnalata tramite una notifica webhook.

    ![Riquadro Gestisci target](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selezionare un periodo di tempo durante il quale si vuole che vengano monitorati gli obiettivi di costo.
      - **Mensile**: gli obiettivi di costo vengono monitorati a livello mensile.
      - **Fisso**: gli obiettivi di costo vengono tracciati per l'intervallo di date specificato nelle date di inizio e di fine. In genere, questi valori rappresentano per quanto tempo è pianificata l'esecuzione del progetto.
   - Specificare un **Costo target**. Ad esempio, quanto si prevede di spendere per questo lab nel periodo di tempo definito.
   - Selezionare questa opzione per abilitare o disabilitare qualsiasi soglia che si vuole venga segnalata, in incrementi del 25%, fino al 125% del **Costo target** specificato.
      - **Invia notifica**: quando questa soglia viene raggiunta, viene inviata una notifica da un URL webhook specificato.
      - **Grafico sul grafico**: quando viene raggiunta questa soglia, i risultati vengono tracciati nel grafico della tendenza dei costi che è possibile visualizzare, come descritto in Visualizzazione del grafico Tendenza costo stimato mensile.
   - Se si sceglie l'opzione **Invia notifica** quando viene raggiunta la soglia, è necessario specificare un URL webhook. Nell'area Integrazioni dei costi selezionare **Fare clic qui per aggiungere un'integrazione**. Immettere un **URL Webhook** nel riquadro di notifica Configura e quindi selezionare **OK**.

       ![Riquadro Configura notifica](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se si specifica **Invia notifica**, è necessario definire un URL webhook.
     - Analogamente, se si definisce un URL webhook, è necessario impostare l'opzione **Invia notifica** su **On** nel riquadro Soglia per costo.
     - È necessario creare un webhook prima di poterlo inserire qui.  

       Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Visualizzare il costo per risorsa 
La funzionalità di tendenza dei costi mensili nei laboratori consente di visualizzare l'elemento speso nel mese di calendario corrente. Mostra anche la proiezione della spesa fino alla fine del mese, in base alla spesa negli ultimi sette giorni. Per comprendere il motivo per cui la spesa in laboratorio soddisfa le soglie nelle prime fasi, è possibile usare la funzionalità **di costo in base** alle risorse che mostra il costo da inizio mese per **risorsa** in una tabella.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.  
4. Selezionare **Configurazione e criteri** nel menu a sinistra.
5. Selezionare **Costo per risorsa** nella sezione **Tracciabilità costi** nel menu a sinistra. Vengono visualizzati i costi associati a ogni risorsa associata a un lab. 

    ![Costo per risorsa](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Questa funzionalità consente di identificare facilmente le risorse che costano di più in modo da poter intraprendere azioni per ridurre le spese di laboratorio. Ad esempio, il costo di una macchina virtuale si basa sulle dimensioni della macchina virtuale. Maggiore è la dimensione della macchina virtuale, maggiore è il costo. È possibile trovare facilmente le dimensioni di una macchina virtuale e il proprietario, in modo da poter parlare con il proprietario della macchina virtuale per comprendere perché sono necessarie tali dimensioni della macchina virtuale e se è possibile ridurre le dimensioni.

[I criteri di arresto automatico](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) consentono di ridurre i costi arrestando le macchine virtuali del lab a un'ora specifica della giornata. Tuttavia, un utente del lab può rifiutare esplicitamente i criteri di arresto, aumentando così il costo di esecuzione della macchina virtuale. È possibile selezionare una macchina virtuale nella tabella per verificare se è stata rifiutata esplicitamente il criterio di arresto automatico. In questo caso, è possibile parlare con il proprietario della macchina virtuale per individuare il motivo per cui la macchina virtuale è stata rifiutata esplicitamente del criterio.
 
## <a name="next-steps"></a>Passaggi successivi
Altre operazioni da eseguire:

* [Definire i criteri del lab](devtest-lab-set-lab-policy.md): impostare i vari criteri che consentono di gestire il modo in cui vengono usati il lab e le relative VM. 
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare le immagini del Marketplace](devtest-lab-configure-marketplace-images.md): DevTest Labs supporta la creazione di macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

