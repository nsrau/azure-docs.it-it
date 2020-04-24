---
title: Punteggio sicuro nel centro sicurezza di Azure | Microsoft Docs
description: " Assegnare priorità ai consigli di sicurezza usando il Punteggio sicuro nel centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415762"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure

> [!NOTE]
> Questo articolo riguarda la versione precedente del Punteggio sicuro. Questa esperienza di Punteggio sicuro è ancora disponibile dall'interfaccia utente, ma verrà gradualmente eliminata. Per consentire una transizione più uniforme, le due esperienze di valutazione sicura sono in esecuzione side-by-side.
>
> Per informazioni dettagliate sul nuovo punteggio sicuro, vedere [qui](secure-score-security-controls.md).
>

A fronte di così tanti servizi che offrono vantaggi in termini di sicurezza, è spesso difficile sapere quali passaggi eseguire prima per proteggere e rafforzare il carico di lavoro. Il Punteggio sicuro esamina le raccomandazioni sulla sicurezza e le assegna una priorità, in modo da essere in grado di stabilire quali consigli eseguire prima. Questo è utile per trovare le vulnerabilità della sicurezza più gravi e stabilire di conseguenza le priorità di indagine. Il Punteggio sicuro è uno strumento che consente di valutare il comportamento di sicurezza del carico di lavoro.

## <a name="secure-score-calculation"></a>Calcolo del Punteggio sicuro

Il Centro sicurezza simula il lavoro di un analista della sicurezza esaminando i consigli sulla sicurezza e applicando algoritmi avanzati per determinare il livello di importanza di ogni consiglio.
Il Centro sicurezza di Azure esamina costantemente le raccomandazioni attive e calcola il Punteggio sicuro in base a tali raccomandazioni, il Punteggio di una raccomandazione deriva dalla gravità e dalle procedure ottimali di sicurezza che incidono maggiormente sulla sicurezza del carico di lavoro.

Il Centro sicurezza offre anche un **Punteggio sicuro complessivo**. 

Il **Punteggio sicuro complessivo** è un accumulo di tutti i punteggi di raccomandazione. È possibile visualizzare il Punteggio sicuro complessivo tra le sottoscrizioni o i gruppi di gestione, a seconda di ciò che si seleziona. Il punteggio varia in base alla sottoscrizione selezionata e alle raccomandazioni attive in ognuna di queste sottoscrizioni.

Per verificare quali consigli influiscano maggiormente sul punteggio sicuro, è possibile visualizzare le prime tre raccomandazioni più interessate nel dashboard del Centro sicurezza oppure è possibile ordinare le raccomandazioni nel pannello dell'elenco di raccomandazioni usando la colonna **Secure Score Impact** .

Per visualizzare il Punteggio sicuro globale:

1. Nel dashboard di Azure fare clic su **Centro sicurezza** e quindi su **Punteggio sicuro**.

2. Nella parte superiore è possibile visualizzare le evidenziazioni del Punteggio sicuro:
   - Il **Punteggio sicuro generale** rappresenta il punteggio per ogni criterio, per ogni sottoscrizione selezionata
   - Il **Punteggio sicuro per categoria** indica le risorse che richiedono maggiore attenzione
   - I **suggerimenti principali per l'effetto di un punteggio sicuro** forniscono un elenco delle raccomandazioni che miglioreranno il Punteggio sicuro se vengono implementate.
 
   ![Punteggio di sicurezza](./media/security-center-secure-score/secure-score-dashboard.png)

3. Nella tabella riportata di seguito è possibile visualizzare ogni sottoscrizione e il Punteggio sicuro complessivo per ogni sottoscrizione.

   > [!NOTE]
   > La somma del Punteggio sicuro di ogni sottoscrizione non è uguale al Punteggio sicuro complessivo. Il Punteggio sicuro è un calcolo basato sul rapporto tra le risorse integre e le risorse totali per raccomandazione, non una somma dei punteggi sicuri tra le sottoscrizioni. 
   >
4. Fare clic su **Visualizza raccomandazioni** per visualizzare le raccomandazioni per la sottoscrizione che è possibile correggere per migliorare il Punteggio sicuro.
4. Nell'elenco di raccomandazioni è possibile notare che per ogni raccomandazione è presente una colonna che rappresenta l'effetto del **Punteggio sicuro**. Questo numero rappresenta quanto il Punteggio sicuro complessivo sarà migliore se si seguono le raccomandazioni. Ad esempio, nella schermata seguente, se si **aggiornano le vulnerabilità nelle configurazioni di sicurezza del contenitore**, il Punteggio sicuro aumenterà di 35 punti.

   ![Punteggio di sicurezza](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Punteggio sicuro singolo

Inoltre, per visualizzare i singoli punteggi sicuri, è possibile trovarli nel pannello delle singole raccomandazioni.  

Il **Punteggio sicuro** per le raccomandazioni è un calcolo basato sul rapporto tra le risorse integre e le risorse totali. Se il numero di risorse integre è uguale al numero totale di risorse, si ottiene il Punteggio sicuro massimo della raccomandazione 50. Per provare a ottenere il Punteggio sicuro più vicino al punteggio massimo, correggere le risorse non integre seguendo le indicazioni.

L' **effetto della raccomandazione** consente di conoscere la quantità di Punteggio sicuro migliore se si applicano le procedure consigliate. Se, ad esempio, il Punteggio sicuro è 42 e l' **effetto raccomandazione** è + 3, l'esecuzione dei passaggi descritti nella raccomandazione consente di migliorare il punteggio fino a 45.

La raccomandazione indica a quali minacce viene esposto il carico di lavoro se non si esegue la procedura di correzione.

![punteggio di sicurezza di una singola raccomandazione](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato come migliorare il comportamento di sicurezza usando un **Punteggio sicuro** nel centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere:

* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
