---
title: Esercitazione - Ridurre i costi di Azure con gli elementi consigliati di ottimizzazione | Microsoft Docs
description: Questa esercitazione consente di ridurre i costi di Azure quando si agisce seguendo gli elementi consigliati di ottimizzazione.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4d9e47d6da45eaba19cbe089de3fdf053c36046a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030678"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Esercitazione: Ottimizzare i costi grazie agli elementi consigliati

Gestione costi di Azure funziona con Azure Advisor per fornire elementi consigliati di ottimizzazione dei costi. Azure Advisor consente di ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate. Questa esercitazione illustra un esempio in cui si identificano le risorse di Azure sottoutilizzate e quindi si interviene per ridurre i costi.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare gli elementi consigliati per l'ottimizzazione dei costi per identificare potenziali inefficienze di utilizzo
> * Agire seguendo gli elementi consigliati di ridimensionare una macchina virtuale a un'opzione più conveniente
> * Verificare l'azione per assicurarsi che la macchina virtuale sia stata ridimensionata correttamente

## <a name="prerequisites"></a>Prerequisiti
Gli elementi consigliati sono disponibili per tutti i clienti con [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/). È necessario avere l'accesso in lettura ad almeno uno degli ambiti seguenti per visualizzare i dati dei costi.

- Sottoscrizione
- Gruppo di risorse

È necessario disporre di macchine virtuali attive con almeno 14 giorni di attività.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Visualizzare gli elementi consigliati per l'ottimizzazione dei costi

Nel portale di Azure fare clic su **Gestione dei costi e fatturazione** nell'elenco dei servizi. Quindi nell'elenco in **Gestione costi di Azure**, selezionare **Raccomandazioni di Advisor**. Vengono visualizzate gli elementi consigliati di Advisor sui costi.

![Elementi consigliati di Advisor](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

L'elenco degli elementi consigliati identifica inefficienze di utilizzo o mostra raccomandazioni d'acquisti che consentono di risparmiare denaro aggiuntivo. Il totale **Risparmi annuali potenziali** indica l'importo totale che è possibile risparmiare se si arrestano o deallocano tutte le macchine virtuali che soddisfano le regole di raccomandazione. Se non si desidera arrestarle, considerare il ridimensionamento a un SKU di macchina virtuale meno costoso.

La categoria **Impatto**, insieme a **Risparmi annuali potenziali**, è progettata per consentire di identificare gli elementi consigliati con la possibilità di risparmiare quanto possibile. Gli elementi consigliati a impatto elevato sono [Acquistare istanze di macchine virtuali riservate per risparmiare sui costi con pagamento in base al consumo](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e [Ottimizzare la spesa correlata alla macchina virtuale ridimensionando o arrestando le istanze sottoutilizzate](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances). Gli elementi consigliati a impatto medio sono[Ridurre i costi eliminando i circuiti ExpressRoute senza provisioning](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits) e [Ridurre i costi eliminando o riconfigurando i gateway di rete virtuale inattivi](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways).

## <a name="act-on-a-recommendation"></a>Implementare un elemento consigliato

Azure Advisor monitora l'utilizzo delle macchine virtuali per 14 giorni e identifica le macchine virtuali sottoutilizzate. Le macchine virtuali con un utilizzo della CPU pari o inferiore al 5% e un utilizzo di rete pari o inferiore 7 MB per quattro o più giorni sono considerate macchine virtuali a utilizzo ridotto.

Per impostazione predefinita, l'utilizzo della CPU è pari o inferiore al 5%, ma è possibile modificare le impostazioni. Per altre informazioni sulla modifica dell'impostazione, vedere l'articolo [Configurare la regola di utilizzo della CPU medio](../advisor/advisor-get-started.md#configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation) [per la raccomandazione sulle macchine virtuali a utilizzo ridotto](../advisor/advisor-get-started.md#configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation).

Sebbene in alcuni scenari possa verificarsi un utilizzo ridotto legato alla progettazione, è comunque possibile risparmiare denaro modificando le dimensioni della macchine virtuale in dimensioni meno costose. Il risparmio effettivo può variare se si sceglie un'azione di ridimensionamento. Di seguito viene illustrato un esempio di ridimensionamento di una macchina virtuale.

Nell'elenco degli elementi consigliati, fare clic sulla raccomandazione **Ridimensionare correttamente o arrestare le macchine virtuali sottoutilizzate**. Nell'elenco dei candidati di macchina virtuale, scegliere una macchina virtuale da ridimensionare e quindi fare clic sulla macchina virtuale. Vengono visualizzati i dettagli della macchina virtuale in modo che sia possibile verificare le metriche di utilizzo. Il valore dei **Risparmi annuali potenziali** è ciò che si può risparmiare se si arresta o rimuove la macchina virtuale. Il ridimensionamento di una macchina virtuale consente di risparmiare sui costi, ma non di risparmiare l'intero importo dei risparmi annuali potenziali.

![Dettagli delle raccomandazioni](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nei dettagli della macchina virtuale, controllare l'utilizzo della macchina virtuale per confermare che sia un candidato di ridimensionamento appropriato.

![Dettagli macchina virtuale](./media/tutorial-acm-opt-recommendations/vm-details.png)

Notare le dimensioni della macchina virtuale corrente. Dopo aver verificato che la macchina virtuale deve essere ridimensionata, chiudere i dettagli della macchina virtuale per visualizzare l'elenco delle macchine virtuali.

Nell'elenco dei candidati per arrestare o ridimensionare, selezionare **Ridimensionare la macchina virtuale**.
![Ridimensionare la macchina virtuale](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Successivamente, viene visualizzato un elenco di opzioni di ridimensionamento disponibili. Scegliere quella in grado di offrire prestazioni ottimali e convenienza economica per il proprio scenario. Nell'esempio seguente, l'opzione scelta permette di ridimensionare da un **DS14\_V2** a un **DS13\_V2**. Seguendo l'elemento consigliato è possibile risparmiare $ 551.30/ mese o $ 6,615.60/ anno.

![Scegliere le dimensioni](./media/tutorial-acm-opt-recommendations/choose-size.png)

Dopo aver scelto delle dimensioni appropriate, fare clic su **Seleziona** per avviare l'azione di ridimensionamento.

Il ridimensionamento richiede il riavvio di una macchina virtuale in esecuzione. Se la macchina virtuale è in ambiente di produzione, è consigliabile eseguire l'operazione di ridimensionamento dopo l'orario di ufficio. La pianificazione del riavvio può ridurre le interruzioni causate da mancata disponibilità temporanea.

## <a name="verify-the-action"></a>Verificare l'azione

Quando il ridimensionamento della macchina virtuale viene completato correttamente, viene visualizzata una notifica di Azure.

![Notifica di ridimensionamento](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Visualizzare gli elementi consigliati per l'ottimizzazione dei costi per identificare potenziali inefficienze di utilizzo
> * Agire seguendo gli elementi consigliati di ridimensionare una macchina virtuale a un'opzione più conveniente
> * Verificare l'azione per assicurarsi che la macchina virtuale sia stata ridimensionata correttamente

L'articolo sulle procedure consigliate Gestione costi fornisce materiale sussidiario di alto livello e i principi da considerare per facilitare la gestione dei costi.

> [!div class="nextstepaction"]
> [Procedure consigliate per Gestione costi](cost-mgt-best-practices.md)
