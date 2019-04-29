---
title: Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905928"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure
Questo documento illustra come usare le raccomandazioni presenti nel Centro sicurezza di Azure per la protezione delle risorse di Azure.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questo argomento non costituisce una guida dettagliata.
>
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
Una volta impostato un criterio di sicurezza, il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il riquadro **Raccomandazioni** in **Panoramica** consente di conoscere il numero totale di raccomandazioni identificate dal Centro sicurezza.

![Riquadro Raccomandazioni][1]

Per visualizzare i dettagli di ogni raccomandazione, selezionare il **riquadro Raccomandazioni** in **Overview**. Si apre **Raccomandazioni**.

![Filtrare le raccomandazioni][2]

È possibile filtrare le raccomandazioni. Per filtrare le raccomandazioni, selezionare **Filtro** nel pannello **Raccomandazioni**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.


* **RACCOMANDAZIONI**: La raccomandazione.
* **SECURE SCORE IMPATTO**:
* **RISORSA**: elenca le risorse a cui si applica la raccomandazione.
* **BARRE DI STATO**:  descrive il livello di gravità della raccomandazione:
   * **Alta (rosso)**: è presente una vulnerabilità associata a una risorsa significativa, ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete, che richiede attenzione.
   * **Medium (arancione)**: è presente una vulnerabilità e sono necessari passaggi aggiuntivi, o non critici, per eliminarla o completare un processo.
   * **Basso (blu)**: è presente una vulnerabilità che deve essere risolta ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente. 
   * **Integro (verde)**:
   * **Non è disponibile (grigio)**:
 


> [!NOTE]
> È necessario conoscere i [modelli di distribuzione di Resource Manager e classici](../azure-classic-rm.md) per le risorse di Azure.
> 
> 
> ### <a name="apply-recommendations"></a>Applicare le raccomandazioni
> Dopo aver esaminato tutte le raccomandazioni, decidere quale applicare per prima. È consigliabile usare la classificazione di gravità come parametro principale per valutare quali raccomandazioni applicare per prime.



## <a name="next-steps"></a>Passaggi successivi
Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
