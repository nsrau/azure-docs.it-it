---
title: Esercitazione - Ridurre i costi di Azure con gli elementi consigliati di ottimizzazione | Microsoft Docs
description: Questa esercitazione consente di ridurre i costi di Azure quando si agisce seguendo gli elementi consigliati di ottimizzazione.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229842"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Esercitazione: Ottimizzare i costi grazie agli elementi consigliati

Gestione costi di Azure funziona con Azure Advisor per fornire elementi consigliati di ottimizzazione dei costi. Azure Advisor consente di ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate. Questa esercitazione illustra un esempio in cui si identificano le risorse di Azure sottoutilizzate e quindi si interviene per ridurre i costi.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare gli elementi consigliati per l'ottimizzazione dei costi per identificare potenziali inefficienze di utilizzo
> * Agire seguendo gli elementi consigliati di ridimensionare una macchina virtuale a un'opzione più conveniente
> * Verificare l'azione per assicurarsi che la macchina virtuale sia stata ridimensionata correttamente

## <a name="prerequisites"></a>Prerequisiti
Recommendations are available for a variety of scopes and Azure account types. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). È necessario avere l'accesso in lettura ad almeno uno degli ambiti seguenti per visualizzare i dati dei costi. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

- Sottoscrizione
- Gruppo di risorse

È necessario disporre di macchine virtuali attive con almeno 14 giorni di attività.

## <a name="sign-in-to-azure"></a>Accedere a Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Visualizzare gli elementi consigliati per l'ottimizzazione dei costi

To view cost optimization recommendations for a subscription, open the desired scope in the Azure portal and select **Advisor recommendations**.

To view recommendations for a management group, open the desired scope in the Azure portal and select **Cost analysis** in the menu. Use the **Scope** pill to switch to a different scope, such as a management group. Select **Advisor recommendations** in the menu. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

![Consigli di Advisor di Gestione costi visualizzati nel portale di Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

L'elenco degli elementi consigliati identifica inefficienze di utilizzo o mostra raccomandazioni d'acquisti che consentono di risparmiare denaro aggiuntivo. Il totale **Risparmi annuali potenziali** indica l'importo totale che è possibile risparmiare se si arrestano o deallocano tutte le macchine virtuali che soddisfano le regole di raccomandazione. Se non si desidera arrestarle, considerare il ridimensionamento a un SKU di macchina virtuale meno costoso.

La categoria **Impatto**, insieme a **Risparmi annuali potenziali**, è progettata per consentire di identificare gli elementi consigliati con la possibilità di risparmiare quanto possibile.

High impact recommendations include:
- [Buy reserved virtual machine instances to save money over pay-as-you-go costs](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimize virtual machine spend by resizing or shutting down underutilized instances](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Use Standard Storage to store Managed Disks snapshots](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Medium impact recommendations include:
- [Delete Azure Data Factory pipelines that are failing](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduce costs by eliminating un-provisioned ExpressRoute circuits](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduce costs by deleting or reconfiguring idle virtual network gateways](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Implementare un elemento consigliato

Azure Advisor monitors your virtual machine usage for seven days and then identifies underutilized virtual machines. Le macchine virtuali con un utilizzo della CPU pari o inferiore al 5% e un utilizzo di rete pari o inferiore 7 MB per quattro o più giorni sono considerate macchine virtuali a utilizzo ridotto.

Per impostazione predefinita, l'utilizzo della CPU è pari o inferiore al 5%, ma è possibile modificare le impostazioni. Per altre informazioni sulla modifica dell'impostazione, vedere l'articolo [Configurare la regola di utilizzo della CPU medio per la raccomandazione sulle macchine virtuali a utilizzo ridotto](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Sebbene in alcuni scenari possa verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro modificando le dimensioni della macchine virtuale in dimensioni meno costose. Il risparmio effettivo può variare se si sceglie un'azione di ridimensionamento. Di seguito viene illustrato un esempio di ridimensionamento di una macchina virtuale.

Nell'elenco degli elementi consigliati, fare clic sulla raccomandazione **Ridimensionare correttamente o arrestare le macchine virtuali sottoutilizzate**. Nell'elenco dei candidati di macchina virtuale, scegliere una macchina virtuale da ridimensionare e quindi fare clic sulla macchina virtuale. Vengono visualizzati i dettagli della macchina virtuale in modo che sia possibile verificare le metriche di utilizzo. Il valore dei **Risparmi annuali potenziali** è ciò che si può risparmiare se si arresta o rimuove la macchina virtuale. Il ridimensionamento di una macchina virtuale consente di risparmiare sui costi, ma non di risparmiare l'intero importo dei risparmi annuali potenziali.

![Esempio di dettagli dei consigli](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nei dettagli della macchina virtuale, controllare l'utilizzo della macchina virtuale per confermare che sia un candidato di ridimensionamento appropriato.

![Esempio di dettagli di una macchina virtuale](./media/tutorial-acm-opt-recommendations/vm-details.png)

Notare le dimensioni della macchina virtuale corrente. Dopo aver verificato che la macchina virtuale deve essere ridimensionata, chiudere i dettagli della macchina virtuale per visualizzare l'elenco delle macchine virtuali.

In the list of candidates to shut down or resize, select **Resize *&lt;FromVirtualMachineSKU&gt;* to *&lt;ToVirtualMachineSKU&gt;***.
![Consiglio di esempio con la possibilità di ridimensionare la macchina virtuale](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Successivamente, viene visualizzato un elenco di opzioni di ridimensionamento disponibili. Scegliere quella in grado di offrire prestazioni ottimali e convenienza economica per il proprio scenario. In the following example, the option chosen resizes from **Standard_D8s_v3** to **Standard_D2s_v3**.

![Esempio di elenco di dimensioni di VM disponibili in cui è possibile scegliere una dimensione](./media/tutorial-acm-opt-recommendations/choose-size.png)

After you choose a suitable size, click **Resize** to start the resize action.

Il ridimensionamento richiede il riavvio di una macchina virtuale in esecuzione. Se la macchina virtuale è in ambiente di produzione, è consigliabile eseguire l'operazione di ridimensionamento dopo l'orario di ufficio. La pianificazione del riavvio può ridurre le interruzioni causate da mancata disponibilità temporanea.

## <a name="verify-the-action"></a>Verificare l'azione

Quando il ridimensionamento della macchina virtuale viene completato correttamente, viene visualizzata una notifica di Azure.

![Notifica dell'esito positivo del ridimensionamento della macchina virtuale](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come:

> [!div class="checklist"]
> * Visualizzare gli elementi consigliati per l'ottimizzazione dei costi per identificare potenziali inefficienze di utilizzo
> * Agire seguendo gli elementi consigliati di ridimensionare una macchina virtuale a un'opzione più conveniente
> * Verificare l'azione per assicurarsi che la macchina virtuale sia stata ridimensionata correttamente

L'articolo sulle procedure consigliate Gestione costi fornisce materiale sussidiario di alto livello e i principi da considerare per facilitare la gestione dei costi.

> [!div class="nextstepaction"]
> [Procedure consigliate per Gestione costi](cost-mgt-best-practices.md)
