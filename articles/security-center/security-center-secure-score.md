---
title: Punteggio di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: " Definire la priorità delle raccomandazioni sulla sicurezza con il punteggio di sicurezza nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 4bc426e291a47ae42e7628ad911e2960e654819f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261299"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure


A fronte di così tanti servizi che offrono vantaggi in termini di sicurezza, è spesso difficile sapere quali passaggi eseguire prima per proteggere e rafforzare il carico di lavoro. Il punteggio di sicurezza di Azure esamina i consigli sulla sicurezza e assegna a ciascuno un livello di priorità che indica quali consigli implementare per primi. Questo è utile per trovare le vulnerabilità della sicurezza più gravi e stabilire di conseguenza le priorità di indagine. Il punteggio di sicurezza è uno strumento che consente di valutare le condizioni di sicurezza del carico di lavoro.

## <a name="secure-score-calculation"></a>Calcolo del punteggio di sicurezza

Il Centro sicurezza simula il lavoro di un analista della sicurezza esaminando i consigli sulla sicurezza e applicando algoritmi avanzati per determinare il livello di importanza di ogni consiglio.
Il Centro sicurezza di Azure verifica costantemente i consigli attivi e, basandosi su questi, calcola il punteggio di sicurezza dell'utente. Il punteggio del consiglio è ottenuto dal relativo livello di gravità e dalle procedure di sicurezza consigliate che influiscono maggiormente sulla sicurezza del carico di lavoro.

Il Centro sicurezza offre anche un **punteggio di sicurezza complessivo**. 

Il **punteggio di sicurezza complessivo** è dato dalla somma dei punteggi di tutti i consigli. È possibile visualizzare il punteggio di sicurezza complessivo per le sottoscrizioni o i gruppi di gestione, a seconda della scelta. Il punteggio varia in base alla sottoscrizione selezionata e alle raccomandazioni attive in ognuna di queste sottoscrizioni.

 
Per controllare quali consigli influiscono maggiormente sul punteggio di sicurezza, è possibile visualizzare i tre consigli con maggiore impatto nel dashboard del Centro sicurezza oppure è possibile ordinare l'elenco dei consigli nel pannello relativo usando la colonna **Impatto di punteggio di sicurezza**.


Per visualizzare il punteggio di sicurezza complessivo:

1. Nel dashboard Azure fare clic su **Centro sicurezza** e quindi su **Punteggio di sicurezza**.
2. Nella parte superiore è possibile visualizzare le informazioni in evidenza del punteggio di sicurezza:
   - Il **punteggio di sicurezza complessivo** rappresenta il punteggio in base ai criteri e alla sottoscrizione selezionata
   - Il **punteggio di sicurezza in base alla categoria** mostra le risorse che richiedono più attenzione
   - **Le raccomandazioni principali in base all'impatto del punteggio di sicurezza** forniscono un elenco di raccomandazioni che consentono di migliorare il punteggio di sicurezza al massimo vengono corrette.
 
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


