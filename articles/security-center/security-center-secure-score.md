---
title: Punteggio di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
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
ms.openlocfilehash: 79154a13722654ef5cbbe7ac99bb67d4b761fe60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903454"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Migliorare il Punteggio sicuro nel centro sicurezza di Azure

> [!NOTE]
> È disponibile un punteggio sicuro migliorato in anteprima. Il Punteggio sicuro migliorato sostituirà infine il Punteggio sicuro esistente, ma per un periodo di tempo verrà eseguito side-by-side per facilitare la transizione.
>
> Per informazioni dettagliate sui vantaggi del Punteggio sicuro migliorato, vedere [qui](secure-score-security-controls.md).
>
> Per partecipare all'anteprima, aprire il portale di Azure, avviare il Centro sicurezza di Azure e selezionare Punteggio sicuro. Da qui, viene visualizzato un banner nella parte superiore della pagina che offre la nuova esperienza di assegnazione dei punteggi sicuri. In alternativa, fare clic [qui](https://aka.ms/ascnewscore).

A fronte di così tanti servizi che offrono vantaggi in termini di sicurezza, è spesso difficile sapere quali passaggi eseguire prima per proteggere e rafforzare il carico di lavoro. Il punteggio di sicurezza di Azure esamina i consigli sulla sicurezza e assegna a ciascuno un livello di priorità che indica quali consigli implementare per primi. Questo è utile per trovare le vulnerabilità della sicurezza più gravi e stabilire di conseguenza le priorità di indagine. Secure Score è uno strumento che ti permette di valutare il comportamento di sicurezza del carico di lavoro.

## <a name="secure-score-calculation"></a>Calcolo del punteggio di sicurezza

Il Centro sicurezza simula il lavoro di un analista della sicurezza esaminando i consigli sulla sicurezza e applicando algoritmi avanzati per determinare il livello di importanza di ogni consiglio.
Il Centro sicurezza di Azure esamina costantemente le raccomandazioni attive e calcola il Punteggio sicuro in base a tali raccomandazioni, il Punteggio di una raccomandazione deriva dalla gravità e dalle procedure ottimali di sicurezza che incidono maggiormente sulla sicurezza del carico di lavoro.

Il Centro sicurezza offre anche un **punteggio di sicurezza complessivo**. 

Il **punteggio di sicurezza complessivo** è dato dalla somma dei punteggi di tutti i consigli. È possibile visualizzare il punteggio di sicurezza complessivo per le sottoscrizioni o i gruppi di gestione, a seconda della scelta. Il punteggio varia in base alla sottoscrizione selezionata e alle raccomandazioni attive in ognuna di queste sottoscrizioni.

Per controllare quali consigli influiscono maggiormente sul punteggio di sicurezza, è possibile visualizzare i tre consigli con maggiore impatto nel dashboard del Centro sicurezza oppure è possibile ordinare l'elenco dei consigli nel pannello relativo usando la colonna **Impatto di punteggio di sicurezza**.

Per visualizzare il punteggio di sicurezza complessivo:

1. Nel dashboard Azure fare clic su **Centro sicurezza** e quindi su **Punteggio di sicurezza**.

2. Nella parte superiore è possibile visualizzare le informazioni in evidenza del punteggio di sicurezza:
   - Il **punteggio di sicurezza complessivo** rappresenta il punteggio in base ai criteri e alla sottoscrizione selezionata
   - Il **punteggio di sicurezza in base alla categoria** mostra le risorse che richiedono più attenzione
   - Le **raccomandazioni principali in base all'impatto del punteggio di sicurezza** forniscono un elenco di raccomandazioni che, se implementate, migliorano al massimo il punteggio di sicurezza.
 
   ![punteggio di sicurezza](./media/security-center-secure-score/secure-score-dashboard.png)

3. Nella tabella seguente è possibile visualizzare ogni sottoscrizione e il punteggio di sicurezza complessivo per ognuna.

   > [!NOTE]
   > La somma del punteggio di sicurezza di ogni sottoscrizione non è uguale al punteggio di sicurezza complessivo. Il punteggio di sicurezza è un calcolo basato sul rapporto tra le risorse integre e le risorse totali per raccomandazione, non la somma dei punteggi di sicurezza tra le sottoscrizioni. 
   >
4. Fare clic su **Visualizza le raccomandazioni** per visualizzare le raccomandazioni per la sottoscrizione che è possibile correggere per migliorare il punteggio di sicurezza.
4. Nell'elenco delle raccomandazioni, è possibile vedere che per ogni raccomandazione è presente la colonna con l'**Impatto di punteggio di sicurezza**. Questo numero indica di quanto migliorerà il punteggio di sicurezza complessivo se si seguono le raccomandazioni. Ad esempio, nella schermata seguente, se si segue la raccomandazione **Risolvi le vulnerabilità nelle configurazioni della sicurezza dei contenitori**, il punteggio di sicurezza aumenterà di 35 punti.

   ![punteggio di sicurezza](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Punteggio di sicurezza singolo

Inoltre, i punteggi di sicurezza singoli sono visualizzabili nel pannello delle raccomandazioni singole.  

Il **punteggio di sicurezza** di una raccomandazione è un calcolo basato sul rapporto tra le risorse integre e le risorse totali. Se il numero di risorse integre è uguale al numero di risorse totali, si ottiene il punteggio di sicurezza massimo per il consiglio, pari a 50. Per tentare di ottenere un punteggio di sicurezza più vicino possibile a quello massimo, risolvere i problemi delle risorse non integre, seguendo le raccomandazioni.

L'**impatto del consiglio** consente di sapere di quanto migliora il punteggio di sicurezza se si applicano i passaggi indicati nel consiglio. Ad esempio, se il punteggio di sicurezza è 42 e l'**impatto del consiglio** è + 3, eseguendo la procedura descritta nel consiglio il punteggio di sicurezza diventerà 45.

La raccomandazione indica a quali minacce viene esposto il carico di lavoro se non si esegue la procedura di correzione.

![punteggio di sicurezza di una singola raccomandazione](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come migliorare lo stato di sicurezza utilizzando il **Punteggio sicuro** nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere:

* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.


