---
title: Punteggio sicuro nel Centro sicurezza di Azure Documenti Microsoft
description: " Assegnare una priorità ai consigli sulla sicurezza usando il punteggio di sicurezza in Centro sicurezza di Azure.Prioritize your security recommendations using the Secure Score in Azure Security Center. "
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
> Questo articolo riguarda la versione precedente del punteggio sicuro. Questa esperienza di punteggio sicuro è ancora disponibile nell'interfaccia utente, ma verrà eliminata gradualmente nel tempo. Le due esperienze di punteggio sicuro sono in esecuzione side-by-side per consentire una transizione più agevole.
>
> Per i dettagli del punteggio di sicurezza più recente, vedere [qui](secure-score-security-controls.md).
>

A fronte di così tanti servizi che offrono vantaggi in termini di sicurezza, è spesso difficile sapere quali passaggi eseguire prima per proteggere e rafforzare il carico di lavoro. Il punteggio sicuro esamina i consigli sulla sicurezza e li assegna le priorità per te, in modo da sapere quali consigli eseguire per primi. Questo è utile per trovare le vulnerabilità della sicurezza più gravi e stabilire di conseguenza le priorità di indagine. Secure Score è uno strumento che ti aiuta a valutare la sicurezza del carico di lavoro.

## <a name="secure-score-calculation"></a>Calcolo del punteggio sicuro

Il Centro sicurezza simula il lavoro di un analista della sicurezza esaminando i consigli sulla sicurezza e applicando algoritmi avanzati per determinare il livello di importanza di ogni consiglio.
Il Centro sicurezza di Azure esamina costantemente i consigli attivi e calcola il punteggio sicuro in base a tali punti di sicurezza, il punteggio di una raccomandazione deriva dalle procedure consigliate per la gravità e la sicurezza che influiranno maggiormente sulla sicurezza del carico di lavoro.

Il Centro sicurezza fornisce inoltre un **punteggio sicuro generale**. 

**Nel complesso Secure Score** è un accumulo di tutti i punteggi di raccomandazione. È possibile visualizzare il punteggio sicuro complessivo tra le sottoscrizioni o i gruppi di gestione, a seconda di ciò che si seleziona. Il punteggio varia in base alla sottoscrizione selezionata e alle raccomandazioni attive in ognuna di queste sottoscrizioni.

Per verificare quali consigli influiscono maggiormente sul punteggio sicuro, è possibile visualizzare i tre consigli più importanti nel dashboard del Centro sicurezza oppure è possibile ordinare i suggerimenti nel riquadro dell'elenco dei suggerimenti usando la colonna **Impatto punteggio sicuro.**

Per visualizzare il punteggio sicuro complessivo:

1. Nel dashboard di Azure fare clic su **Centro sicurezza** e quindi su **Punteggio sicuro**.

2. In alto puoi vedere i punti salienti di Secure Score:
   - Il **punteggio sicuro complessivo** rappresenta il punteggio per i criteri, per ogni sottoscrizione selezionata
   - **Secure Score per categoria** ti mostra quali risorse hanno bisogno di più attenzione
   - **I principali consigli per impatto sul punteggio sicuro** forniscono un elenco dei consigli che miglioreranno al meglio il tuo Secure Score se li implementi.
 
   ![Punteggio di sicurezza](./media/security-center-secure-score/secure-score-dashboard.png)

3. Nella tabella seguente puoi vedere ogni tuo abbonamento e il punteggio sicuro complessivo per ciascuno.

   > [!NOTE]
   > La somma del punteggio sicuro di ogni sottoscrizione non è uguale al punteggio sicuro complessivo. Il punteggio sicuro è un calcolo basato sul rapporto tra le risorse integre e le risorse totali per ogni raccomandazione, non una somma di secure Score nelle sottoscrizioni. 
   >
4. Fare clic su **Visualizza suggerimenti** per visualizzare i consigli per la sottoscrizione che è possibile correggere per migliorare il punteggio sicuro.
4. Nell'elenco dei consigli, è possibile vedere che per ogni raccomandazione è presente una colonna che rappresenta **l'impatto del punteggio sicuro**. Questo numero rappresenta quanto migliorerà il tuo punteggio sicuro complessivo se segui le raccomandazioni. Ad esempio, nella schermata seguente, se si **correggino le vulnerabilità nelle configurazioni**di sicurezza dei contenitori, il punteggio sicuro aumenterà di 35 punti.

   ![Punteggio di sicurezza](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Punteggio sicuro individuale

Inoltre, per visualizzare i singoli Secure Score, è possibile trovarli all'interno del pannello di raccomandazione individuale.  

Il **punteggio sicuro di raccomandazione** è un calcolo basato sul rapporto tra le risorse integre e le risorse totali. Se il numero di risorse integre è uguale al numero totale di risorse, si ottiene il punteggio massimo sicuro della raccomandazione di 50. Per cercare di avvicinare il punteggio sicuro al punteggio massimo, correggere le risorse non integre seguendo le raccomandazioni.

**L'impatto di raccomandazione** consente di sapere quanto migliora il punteggio sicuro se si applicano i passaggi di raccomandazione. Ad esempio, se il tuo punteggio sicuro è 42 e **l'impatto della raccomandazione** è di 3, l'esecuzione dei passaggi descritti nella raccomandazione migliora il punteggio in 45.

La raccomandazione indica a quali minacce viene esposto il carico di lavoro se non si esegue la procedura di correzione.

![punteggio di sicurezza di una singola raccomandazione](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Passaggi successivi
Questo articolo illustra come migliorare la sicurezza usando **il punteggio sicuro** nel Centro sicurezza di Azure.This article showed you how to improve your security posture using Secure Score in Azure Security Center. Per altre informazioni sul Centro sicurezza, vedere:

* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
