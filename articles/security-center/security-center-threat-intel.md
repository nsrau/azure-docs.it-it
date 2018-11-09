---
title: Intelligence per le minacce e mappa degli avvisi di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare la mappa degli avvisi di sicurezza e la funzionalità di intelligence per le minacce del Centro sicurezza di Azure per identificare le potenziali minacce nelle VM e nei computer.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: c41cb48e61b4c2bd1dc81e508c8218fad688bbaf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241720"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Mappa degli avvisi di sicurezza e intelligence per le minacce
Questo articolo illustra come usare la mappa degli avvisi di sicurezza e la mappa dell'intelligence per le minacce basata su eventi di sicurezza del Centro sicurezza di Azure per risolvere i problemi relativi alla sicurezza.

## <a name="how-the-security-alerts-map-works"></a>Funzionamento della mappa degli avvisi di sicurezza
Il Centro sicurezza offre una mappa che consente di identificare le minacce alla sicurezza per l'ambiente. È ad esempio possibile determinare se un computer è parte di una botnet e qual è la provenienza di una minaccia. I computer possono diventare nodi di una botnet quando utenti malintenzionati installano illecitamente malware che interagisce con il comando e il controllo della botnet all'insaputa dell'utente. 

Per creare questa mappa, il Centro sicurezza usa dati provenienti da più origini nell'ambito di Microsoft. Questi dati vengono usati per identificare le potenziali minacce all'ambiente. 

Uno dei passaggi del [processo di risposta agli eventi imprevisti per la sicurezza](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) consiste nell'identificare la gravità della compromissione dei sistemi. In questa fase è consigliabile eseguire queste attività:

- Determinare la natura dell'attacco.
- Determinare il punto di origine dell'attacco.
- Determinare l'intento dell'attacco. Si tratta di un attacco casuale o di un attacco mirato ad acquisire informazioni specifiche dell'organizzazione?
- Identificare i sistemi che sono stati compromessi.
- Identificare i file a cui è stato eseguito l'accesso e determinarne il grado di riservatezza.

È possibile usare la mappa degli avvisi di sicurezza del Centro sicurezza per semplificare queste attività.

## <a name="access-the-security-alerts-map"></a>Accesso alla mappa degli avvisi di sicurezza
Per visualizzare le minacce correnti nell'ambiente, aprire la mappa degli avvisi di sicurezza:

1. Aprire il dashboard **Centro sicurezza**.
2. Nel riquadro sinistro selezionare **Mappa degli avvisi di sicurezza** in **Protezione dalle minacce**. Si apre la mappa.
3. Per ottenere altre informazioni sull'avviso e ricevere la procedura di correzione, fare clic sul punto di avviso sulla mappa e seguire le istruzioni. 
 
La mappa degli avvisi di sicurezza è costituita da avvisi. Questi avvisi si basano su attività per le quali le comunicazioni di rete sono state associate a un indirizzo IP risolto, indipendentemente dal fatto che tale indirizzo sia un indirizzo IP notoriamente rischioso (ad esempio, un cryptominer noto) o che non sia riconosciuto in precedenza come rischioso. La mappa fornisce avvisi in tutte le sottoscrizioni selezionate in precedenza in Azure. 

Gli avvisi presenti nella mappa vengono visualizzati in base alla posizione geografica di cui è stata rilevata la provenienza e sono colorati in base alla gravità. 
    ![Informazioni di intelligence per le minacce](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Visualizzazione del dashboard di intelligence per le minacce basata su eventi
Per visualizzare la mappa dell'intelligence per le minacce basata su eventi di sicurezza non elaborati, è possibile seguire questa procedura. In questa mappa vengono visualizzati solo gli eventi che riguardano un indirizzo IP considerato come rischioso, ad esempio l'indirizzo IP di una botnet nota.

1. Aprire il dashboard **Centro sicurezza**.

1. Nel riquadro sinistro selezionare **Mappa degli avvisi di sicurezza** in **Protezione dalle minacce**. Si apre la mappa.
2. Nell'angolo in alto a destra fare clic su **Vai alla mappa degli eventi di sicurezza**.
3. Selezionare l'area di lavoro di cui si vuole visualizzare il dashboard.
4. Nella parte superiore della mappa, selezionare **Visualizza Intelligence per le minacce classico**. Verrà visualizzato il dashboard **Intelligence per le minacce**.

   > [!NOTE]
   > Se nella colonna a destra viene visualizzato **PIANO DI AGGIORNAMENTO**, questa area di lavoro usa una sottoscrizione gratuita. Eseguire l'aggiornamento al piano Standard per usare questa funzionalità. Se nella colonna a destra viene visualizzato **RICHIEDE L'AGGIORNAMENTO** aggiornare [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per usare questa funzionalità. Per altre informazioni sul piano tariffario, vedere i prezzi del Centro sicurezza di Azure.
   >
5. Se sono presenti più aree di lavoro da analizzare, classificare l'indagine in ordine di priorità in base alla colonna **Indirizzi IP dannosi**. Mostra il numero attuale di indirizzi IP dannosi nell'area di lavoro. Selezionare l'area di lavoro da usare per visualizzare il dashboard **Intelligence per le minacce**.

    ![Informazioni di intelligence sulle minacce](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Questo dashboard è suddiviso in quattro riquadri:

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
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
