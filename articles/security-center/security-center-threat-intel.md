---
title: Intelligence per le minacce nel Centro sicurezza di Azure | Microsoft Docs
description: "Informazioni su come usare la funzionalità di intelligence per le minacce del Centro sicurezza di Azure per identificare le potenziali minacce nelle VM e nei computer."
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Intelligence per le minacce nel Centro sicurezza di Azure
Questo articolo illustra come usare la funzionalità di intelligence per le minacce del Centro sicurezza di Azure per risolvere i problemi relativi alla sicurezza.

## <a name="what-is-threat-intelligence"></a>Informazioni sull'intelligence per le minacce
Usando l'opzione Intelligence per le minacce disponibile nel Centro sicurezza, gli amministratori IT possono identificare le minacce alla sicurezza dell'ambiente, ad esempio determinare se un computer è parte di una botnet. I computer possono diventare nodi di una botnet quando utenti malintenzionati installano illecitamente malware che connette il computer al comando e al controllo all'insaputa dell'utente. La funzionalità Intelligence per le minacce può identificare potenziali minacce provenienti da canali di comunicazione sotterranei, ad esempio il dark web.

Per creare l'intelligence per le minacce, il Centro sicurezza usa dati provenienti da più origini nell'ambito di Microsoft e usa questi dati per identificare le potenziali minacce all'ambiente. Il riquadro **Intelligence per le minacce** è composto da tre aree principali:

- Tipi di minacce rilevate
- Origine delle minacce
- Mappa di intelligence per le minacce


## <a name="when-should-you-use-threat-intelligence"></a>Quando usare l'intelligence per le minacce
Uno dei passaggi del [processo di risposta agli eventi imprevisti per la sicurezza](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) consiste nell'identificare la gravità della compromissione dei sistemi. In questa fase è consigliabile eseguire queste attività:

- Determinare la natura dell'attacco.
- Determinare il punto di origine dell'attacco.
- Determinare l'intento dell'attacco. Si tratta di un attacco casuale o di un attacco mirato ad acquisire informazioni specifiche dell'organizzazione?
- Identificare i sistemi che sono stati compromessi.
- Identificare i file a cui è stato eseguito l'accesso e determinarne il grado di riservatezza.

È possibile usare le informazioni dell'intelligence per le minacce nel Centro sicurezza per semplificare queste attività. 

## <a name="access-the-threat-intelligence"></a>Accedere all'intelligence per le minacce
Per visualizzare l'intelligence per le minacce corrente per il proprio ambiente, è prima di tutto necessario selezionare l'area di lavoro in cui risiedono le informazioni. Se non sono presenti più aree di lavoro, non viene visualizzato il selettore delle aree di lavoro e si passa direttamente al dashboard **Intelligence per le minacce**. Per accedere al dashboard:

1. Aprire il dashboard **Centro sicurezza**.

2. Nel riquadro sinistro fare clic su **Intelligence per le minacce** in **Rilevamento**. Verrà visualizzato il dashboard **Intelligence per le minacce**.

    ![Dashboard Intelligence per le minacce](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Se nella colonna a destra viene visualizzato **PIANO DI AGGIORNAMENTO**, questa area di lavoro usa una sottoscrizione gratuita. Eseguire l'aggiornamento al piano Standard per usare questa funzionalità. Se nella colonna a destra viene visualizzato **RICHIEDE L'AGGIORNAMENTO** aggiornare [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per usare questa funzionalità. Per altre informazioni sul piano tariffario, vedere i prezzi del Centro sicurezza di Azure. 
    > 
3. Se sono presenti più aree di lavoro da analizzare, classificare l'indagine in ordine di priorità in base alla colonna **Indirizzi IP dannosi**. Mostra il numero attuale di indirizzi IP dannosi nell'area di lavoro. Selezionare l'area di lavoro da usare per visualizzare il dashboard **Intelligence per le minacce**.

    ![Informazioni di intelligence sulle minacce](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Questo dashboard è suddiviso in quattro riquadri:

    a.  **Tipi di minacce**. Offre un riepilogo dei tipi di minacce rilevati nell'area di lavoro selezionata.

    b.  **Paese di origine**. Aggrega la quantità di traffico in base alla località di origine.

    c.  **Posizione minaccia**. Consente di identificare le località nel mondo che attualmente comunicano con l'ambiente. Nella mappa visualizzata le frecce arancioni (in ingresso) e rosse (in uscita) identificano le direzioni del traffico. Se si seleziona una di queste frecce, vengono visualizzati il tipo di minaccia e la direzione del traffico.

    d.  **Dettagli minaccia**. Mostra altri dettagli sulla minaccia selezionata nella mappa.

Indipendentemente dal riquadro di opzione selezionato, il dashboard visualizzato è basato sulla query di ricerca log. L'unica differenza è costituita dal tipo di query e dal risultato.

### <a name="threat-types"></a>Tipi di minacce
Selezionare il riquadro **Tipi di minacce** per aprire il dashboard **Ricerca log**. Le opzioni relative ai filtri vengono visualizzate a sinistra e i risultati della query a destra.

![Ricerca log](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Il risultato della query mostra le minacce ordinate per nome. È possibile usare il riquadro sinistro per selezionare l'attributo da filtrare. Per visualizzare, ad esempio, solo le minacce attualmente connesse ai computer, in **STATO SESSIONE** selezionare **Connesso** > **Applica**.

![Stato della sessione](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Per le VM di Azure, nel dashboard **Intelligence per le minacce** vengono visualizzati solo i dati di rete che passano attraverso l'agente. Nell'intelligence per le minacce vengono usati anche i tipi di dati seguenti:

- Dati CEF (Type=CommonSecurityLog)
- Dati in transito (Type= WireData)
- Log di IIS (Type=W3CIISLog)
- Windows Firewall (Type=WindowsFirewall)
- Eventi DNS (Type=DnsEvents)


## <a name="see-also"></a>Vedere anche
In questo articolo si è appreso come usare la funzionalità di intelligence per le minacce del Centro sicurezza per identificare le attività sospette. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

