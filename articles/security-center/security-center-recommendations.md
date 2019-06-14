---
title: Raccomandazioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2019
ms.author: v-mohabe
ms.openlocfilehash: fe1d4bf27f3c4bb1f70c1c1fa9767c27f8767998
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064268"
---
# <a name="security-recommendations-in-azure-security-center"></a>Raccomandazioni di sicurezza nel Centro sicurezza di Azure 
Questo argomento illustra come visualizzare e comprendere le raccomandazioni nel Centro sicurezza di Azure per proteggere le risorse di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>

## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza
Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli necessari.

## <a name="implementing-security-recommendations"></a>Implementare le raccomandazioni di sicurezza
### <a name="set-recommendations"></a>Impostare le raccomandazioni
In [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md)si apprende come:

* Configurare i criteri di sicurezza.
* Attivare la raccolta dei dati.
* Scegliere le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza.

Le raccomandazioni relative ai criteri di sicurezza si basano attualmente su aggiornamenti di sistema, regole di base, programmi antimalware, [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per subnet e interfacce di rete, controllo del database SQL, Transparent Data Encryption per il database SQL e web application firewall.  [Impostazione dei criteri di sicurezza](tutorial-security-policy.md) fornisce una descrizione di ogni opzione relativa alle raccomandazioni.

### <a name="monitor-recommendations"></a>Monitorare le raccomandazioni
Una volta impostato un criterio di sicurezza, il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il **consigli** riquadro sotto **Panoramica** Mostra il numero totale di raccomandazioni identificate dal Centro sicurezza.

![Panoramica del Centro sicurezza](./media/security-center-recommendations/asc-overview.png)

1. Selezionare il **riquadro raccomandazioni** sotto **Panoramica**. Il **raccomandazioni** viene visualizzato l'elenco.
    
      ![Visualizzare raccomandazioni](./media/security-center-recommendations/view-recommendations.png)

    È possibile filtrare le raccomandazioni. Per filtrare le raccomandazioni, selezionare **Filtro** nel pannello **Raccomandazioni**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.

   * **RACCOMANDAZIONI**: La raccomandazione.
   * **SECURE SCORE IMPATTO**: Un punteggio generato dal Centro sicurezza usando le raccomandazioni sulla sicurezza e applicando algoritmi avanzati per determinare come essenziale ogni raccomandazione è. Per altre informazioni, vedere [proteggere il calcolo del punteggio](security-center-secure-score.md#secure-score-calculation).
   * **RISORSA**: elenca le risorse a cui si applica la raccomandazione.
   * **BARRE DI STATO**:  descrive il livello di gravità della raccomandazione:
       * **Alta (rosso)** : è presente una vulnerabilità associata a una risorsa significativa, ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete, che richiede attenzione.
       * **Medium (arancione)** : è presente una vulnerabilità e sono necessari passaggi aggiuntivi, o non critici, per eliminarla o completare un processo.
       * **Basso (blu)** : è presente una vulnerabilità che deve essere risolta ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente. 
       * **Integro (verde)** :
       * **Non è disponibile (grigio)** :

1. Per visualizzare i dettagli di ogni Consiglio, fare clic sulla raccomandazione.

    ![Dettagli delle raccomandazioni](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Visualizzare [modelli di distribuzione Resource Manager e classica](../azure-classic-rm.md) per risorse di Azure.
  
 ### <a name="apply-recommendations"></a>Applicare le raccomandazioni
> Dopo aver esaminato tutte le raccomandazioni, decidere quale applicare prima. È consigliabile usare il sicuro impatto per valutare quali raccomandazioni devono essere applicate per primo assegnare un punteggio.

1. Dall'elenco, fare clic su richiesta.
1. Seguire le istruzioni di *i passaggi correttivi* sezione.

## <a name="next-steps"></a>Passaggi successivi
Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.
