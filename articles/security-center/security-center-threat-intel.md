---
title: Intelligence per le minacce nel Centro sicurezza di Azure | Microsoft Docs
description: "Questo documento illustra come usare la funzionalità di intelligence per le minacce del Centro sicurezza di Azure per identificare le potenziali minacce nelle VM e nei computer."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Intelligence per le minacce nel Centro sicurezza di Azure
Questo documento illustra come usare la funzionalità di intelligence per le minacce del Centro sicurezza di Azure per risolvere i problemi relativi alla sicurezza.

## <a name="what-is-threat-intelligence"></a>Informazioni sull'intelligence per le minacce
Usando l'opzione Intelligence per le minacce disponibile nel Centro sicurezza, gli amministratori IT possono identificare le minacce alla sicurezza dell'ambiente, ad esempio determinare se un computer è parte di una botnet. I computer possono diventare nodi di una botnet quando utenti malintenzionati installano illecitamente malware che connette il computer al comando e al controllo all'insaputa dell'utente. Questa funzionalità può anche identificare potenziali minacce provenienti da canali di comunicazione sotterranei, ad esempio una darknet.

Per creare l'intelligence per le minacce, il Centro sicurezza usa dati provenienti da più origini nell'ambito di Microsoft e sfrutta questi dati per identificare le potenziali minacce all'ambiente. Il riquadro Threat Intelligence (Intelligence per le minacce) è composto da tre aree principali:

- Tipi di minacce rilevate
- Origine delle minacce
- Mappa di intelligence per le minacce


## <a name="when-should-i-use-threat-intelligence"></a>Quando usare l'intelligence per le minacce
Uno dei passaggi del [processo di risposta agli eventi imprevisti per la sicurezza](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) consiste nell'identificare la gravità della compromissione dei sistemi. In questa fase è consigliabile eseguire queste attività:

- Determinare la natura dell'attacco.
- Determinare il punto di origine dell'attacco.
- Determinare l'intento dell'attacco. Si tratta di un attacco casuale o di un attacco appositamente mirato ad acquisire informazioni specifiche dell'organizzazione?
- Identificare i sistemi che sono stati compromessi.
- Identificare i file a cui è stato eseguito l'accesso e determinarne la riservatezza. Le informazioni dell'intelligence per le minacce nel Centro sicurezza consentono di semplificare queste attività. 

## <a name="how-to-access-the-threat-intelligence"></a>Come accedere all'intelligence per le minacce
Per visualizzare l'intelligence per le minacce corrente per il proprio ambiente, è prima di tutto necessario selezionare l'area di lavoro in cui risiedono le informazioni. Se non sono presenti più aree di lavoro, non verrà visualizzato il selettore delle aree di lavoro e si passerà direttamente al dashboard **Intelligence per le minacce**. Per accedere al dashboard Intelligence per le minacce, seguire questa procedura:

1.  Aprire il dashboard del **Centro sicurezza**.
2.  Nel riquadro sinistro fare clic su **Intelligence per le minacce** in **Rilevamento**. Verrà visualizzato il dashboard **Intelligence per le minacce**.

    ![Intelligence per le minacce](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Se l'ultima colonna visualizza **UPGRADE PLAN** (AGGIORNA PIANO), l'area di lavoro usa la sottoscrizione gratuita ed è necessario eseguire l'aggiornamento al piano Standard per usare questa funzionalità. Se la colonna visualizza REQUIRES UPDATE (AGGIORNAMENTO NECESSARIO), per usare questa funzionalità è necessario aggiornare [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). Per altre informazioni sul piano tariffario, vedere i prezzi del Centro sicurezza di Azure. 
    > 
3. Se sono presenti più aree di lavoro da analizzare, è possibile assegnare una priorità all'analisi in base alla colonna **INDIRIZZI IP DANNOSI**, che indica l'attuale numero di indirizzi IP dannosi nell'area di lavoro. Selezionare l'area di lavoro da usare per visualizzare il dashboard **Intelligence per le minacce**.

    ![Intelligence per le minacce](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Questo dashboard è suddiviso in quattro riquadri.
    * **Tipi di minacce**: offre un riepilogo dei tipi di minacce rilevati nell'area di lavoro selezionata.
    * **Paese di origine**: aggrega la quantità di traffico in base alla località di origine.
    * **Posizione minaccia**: consente di identificare le località nel mondo che attualmente comunicano con l'ambiente. Le frecce di colore arancione (in ingresso) e di colore rosso (in uscita) sulla mappa identificano la direzione del traffico. Facendo clic su una di queste frecce verranno visualizzati il tipo di minaccia e la direzione del traffico.
    * **Dettagli minaccia**: mostra altri dettagli sulla minaccia selezionata nella mappa.

Indipendentemente dal riquadro di opzioni selezionato, il dashboard visualizzato si basa sulla query di [Ricerca log](https://docs.microsoft.com/azure/security-center/security-center-search), l'unica differenza è il tipo di query e il risultato.

### <a name="threat-types"></a>Tipi di minacce
Facendo clic sul riquadro **Tipi di minacce** verrà visualizzato il dashboard **Ricerca log**, con le opzioni di filtro a sinistra e i risultati della query a destra.

![Ricerca per intelligence per le minacce](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Il risultato della query mostra le minacce aggregate in base al nome. È possibile usare il riquadro sinistro per selezionare l'attributo da filtrare. Se si vogliono visualizzare solo le minacce attualmente connesse ai computer, ad esempio, si seleziona **Connected** in **SESSIONSTATE** e si fa clic sul pulsante **Applica**.

![Stato della sessione](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Per le VM di Azure, nel dashboard Intelligence per le minacce vengono visualizzati solo i dati di rete che passano attraverso l'agente. Nell'intelligence per le minacce vengono usati anche i tipi di dati seguenti:

- Dati CEF (Type=CommonSecurityLog)
- Dati in transito (Type= WireData)
- Log di IIS (Type=W3CIISLog)
- Windows Firewall (Type=WindowsFirewall)
- Eventi DNS (Type=DnsEvents)


## <a name="see-also"></a>Vedere anche
In questo documento si è appreso come usare la funzionalità di intelligence per le minacce del Centro sicurezza per identificare le attività sospette. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.


