---
title: Proteggere i dati personali con il Centro sicurezza di Azure| Microsoft Docs
description: Usare il Centro sicurezza per proteggere i dati personali. Questa funzionalità può essere utile per assicurare la conformità al Regolamento generale sulla protezione dei dati (RGPD).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 118752a030a1c300306aa3e05571bb9b624a2c20
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Proteggere i dati personali da violazioni e attacchi: Centro sicurezza di Azure

Questo articolo illustra come usare il Centro sicurezza di Azure per proteggere i dati personali da violazioni e attacchi. Le informazioni qui incluse saranno utili per assicurare la conformità al Regolamento generale sulla protezione dei dati (RGPD).

## <a name="scenario"></a>Scenario 

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare tale iniziativa, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito.

La società usa Microsoft Azure per archiviare i dati aziendali nel cloud. Questi includono informazioni personali come nomi, indirizzi, numeri di telefono e dati delle carte di credito, oltre a informazioni relative alle risorse umane quali:

- Indirizzi
- Numeri di telefono
- Codici fiscali
- Informazioni sanitarie

La compagnia di viaggi in crociera gestisce anche un ampio database di clienti che partecipano al programma fedeltà. I dipendenti aziendali accedono alla rete dagli uffici remoti della società, mentre agenti di viaggio in tutto il mondo hanno accesso ad alcune risorse aziendali.
I dati personali viaggiano attraverso la rete tra queste posizioni e il data center di Microsoft.

## <a name="problem-statement"></a>Presentazione del problema

La società teme eventuali attacchi alle risorse di Azure e intende impedire l'esposizione dei dati personali di clienti e dipendenti a persone non autorizzate. Vuole ottenere informazioni sulla prevenzione e sulla risposta/correzione per questi eventi, oltre a un modo efficace per monitorare costantemente la sicurezza delle risorse cloud.
È necessaria una solida linea di difesa dagli attuali utenti malintenzionati, particolarmente sofisticati e organizzati.

## <a name="company-goal"></a>Obiettivo dell'azienda

Uno degli obiettivi dell'azienda è garantire la privacy dei dati personali di dipendenti e clienti, proteggendo tali dati dalle minacce. Uno degli obiettivi dell'azienda è rispondere immediatamente a eventuali segnali di violazione per ridurre l'impatto. È necessario un modo per valutare lo stato corrente della sicurezza e identificare le configurazioni vulnerabili per adottare misure correttive.

## <a name="solutions"></a>Soluzioni

Il Centro sicurezza di Microsoft Azure offre una soluzione integrata di monitoraggio della sicurezza e gestione dei criteri. Offre funzionalità efficaci e facili da usare per la prevenzione e il rilevamento delle minacce e la relativa risposta.

### <a name="prevention"></a>Prevenzione

Il Centro sicurezza di Azure consente di impedire le violazioni tramite l'impostazione di criteri di sicurezza, l'accesso Just-in-Time e l'implementazione delle raccomandazioni sulla sicurezza.

I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. L'accesso Just-in-Time può essere usato per bloccare il traffico in ingresso nelle macchine virtuali di Azure, riducendo l'esposizione agli attacchi. Le raccomandazioni sulla sicurezza vengono create dal Centro sicurezza di Azure dopo l'analisi dello stato di sicurezza delle risorse di Azure.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Come impostare i criteri di sicurezza nel Centro sicurezza di Azure

È possibile configurare criteri di sicurezza per ogni sottoscrizione. Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore di tale sottoscrizione. Nel portale di Azure eseguire questa procedura:

1. Selezionare **Criteri** nel dashboard del Centro sicurezza di Azure.

2. Selezionare la sottoscrizione in cui abilitare i criteri.

3. Scegliere **Criteri di prevenzione** per configurare criteri per ogni sottoscrizione. Impostare **Raccogli dati dalle macchine virtuali** su **Sì**.

4. Nelle opzioni **Criteri di prevenzione** selezionare **Sì** per abilitare le raccomandazioni sulla sicurezza da usare per la sottoscrizione.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Per altre istruzioni dettagliate e la spiegazione di ogni raccomandazione che può essere abilitata, vedere [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Come configurare l'accesso Just-in-Time

Quando è abilitata la funzionalità Just-in-Time, il Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola del gruppo di sicurezza di rete. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Per usare l'accesso Just-in-Time seguire questa procedura:

1. Nel pannello del Centro sicurezza di Azure selezionare il **riquadro Accesso Just-In-Time alla VM**.

2. Selezionare la scheda **Consigliati**.

3. In **Macchine virtuali** selezionare le macchine virtuali da abilitare. Verrà visualizzato un segno di spunta accanto a una macchina virtuale. 
4. Selezionare **Abilita JIT** in VM.
5. Selezionare **Salva**.

Verranno quindi visualizzate le porte predefinite che il Centro sicurezza di Azure consiglia di abilitare per la funzionalità Just-In-Time. È anche possibile aggiungere e configurare una nuova porta per cui abilitare la soluzione Just-In-Time. Il riquadro **Accesso Just-In-Time alla VM** nel Centro sicurezza visualizza il numero di macchine virtuali configurate per l'accesso Just-In-Time. Visualizza anche il numero di richieste di accesso approvate eseguite nell'ultima settimana.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Per istruzioni su questa operazione e altre informazioni sull'accesso Just-In-Time, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Come implementare le raccomandazioni sulla sicurezza del Centro sicurezza di Azure

Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli necessari. 
1. Selezionare il riquadro **Raccomandazioni** nel pannello del Centro sicurezza di Azure.

2. Le raccomandazioni vengono visualizzate sotto forma di tabella, in cui ogni riga rappresenta una raccomandazione.

3. Per filtrare le raccomandazioni, selezionare **Filtro** e selezionare i valori di gravità e stato da visualizzare.

4. Per ignorare una raccomandazione non applicabile, fare clic e selezionare **Ignora**.

5. Valutare la raccomandazione da applicare per prima.

6. Applicare le raccomandazioni in ordine di priorità.

Per un elenco delle possibili raccomandazioni e le procedure dettagliate per l'applicazione di ognuna di esse, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

### <a name="detection-and-response"></a>Rilevamento e risposta

Il rilevamento e la risposta vanno di pari passo perché si vuole ovviamente rispondere nel minor tempo possibile dopo aver rilevato una minaccia.
Il sistema di rilevamento delle minacce del Centro sicurezza di Azure funziona tramite la raccolta automatica di informazioni sulla sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connessi. Il Centro sicurezza di Azure può aggiornare rapidamente gli algoritmi di rilevamento a fronte del rilascio di exploit nuovi e sofisticati da parte di utenti malintenzionati. Per altre informazioni sul funzionamento del rilevamento delle minacce nel Centro sicurezza di Azure, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Come gestire e rispondere agli avvisi di sicurezza

Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, oltre alle informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco. Per gestire gli avvisi di sicurezza, seguire questa procedura:

1. Selezionare il riquadro **Avvisi di sicurezza** nel dashboard del Centro sicurezza di Azure. Verranno visualizzati i dettagli di ogni avviso.

2. Per filtrare gli avvisi in base alla data, allo stato e alla gravità, selezionare **Filtro** e quindi i valori da visualizzare.

3. Per rispondere a un avviso selezionarlo, verificare le informazioni e quindi selezionare la risorsa che ha subito l'attacco.

4. Nel campo **Descrizione** verranno visualizzate informazioni dettagliate, tra cui la correzione consigliata.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Per altre informazioni dettagliate sulla risposta agli avvisi di sicurezza, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

Per agevolare l'analisi degli avvisi di sicurezza, l'azienda può integrare gli avvisi del Centro sicurezza di Azure con la propria soluzione SIEM, usando l'[integrazione dei log di Azure](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Come gestire gli eventi imprevisti per la sicurezza

In Centro sicurezza di Azure, un evento imprevisto per la sicurezza è un'aggregazione di tutti gli avvisi relativi a una risorsa corrispondenti a modelli di catena di attacco. Un evento imprevisto riporta un elenco degli avvisi correlati, che consente di ottenere altre informazioni su ogni occorrenza. Gli eventi imprevisti vengono visualizzati nel riquadro e nel pannello Avvisi di sicurezza.

Per esaminare e gestire gli eventi imprevisti per la sicurezza, seguire questa procedura:

1. Selezionare il riquadro **Avvisi di sicurezza**. In caso di rilevamento, l'evento imprevisto per la sicurezza verrà visualizzato nel grafico degli avvisi di sicurezza, con un'icona diversa da quella degli altri avvisi.

2. Selezionare l'evento imprevisto per la sicurezza per visualizzare altri dettagli. Le altre informazioni includono descrizione completa, gravità, stato corrente, risorsa che ha subito l'attacco e misure correttive per l'evento imprevisto, oltre agli avvisi inclusi nell'evento.

È possibile filtrare per visualizzare **solo gli eventi imprevisti**, **solo gli avvisi** o **entrambi**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Come accedere al report di intelligence per le minacce

Per identificare le minacce, il Centro sicurezza di Azure analizza informazioni raccolte da più origini. Per consentire ai team che gestiscono la risposta agli eventi imprevisti di analizzare e correggere le minacce, il Centro sicurezza include un report di intelligence per le minacce contenente informazioni sulla minaccia rilevata.

Il Centro sicurezza rende disponibili tre tipi di report per le minacce, che possono variare a seconda dell'attacco.
I report disponibili sono:

- Report sui gruppi di attività: fornisce approfondimenti sugli utenti malintenzionati e relativi obiettivi e strategie.

- Report sulle campagne: si concentra sui dettagli di specifiche campagne di attacco.

- Report riepilogativo delle minacce: tratta tutti gli elementi presenti nei due report precedenti.

Questo tipo di informazioni è molto utile nel corso del processo di risposta agli eventi imprevisti, in cui è in corso un'analisi per identificare l'origine dell'attacco, le motivazioni dell'utente malintenzionato e le azioni da eseguire per attenuare il problema in futuro.

1. Per accedere al report di intelligence per le minacce, seguire questa procedura:

2. Selezionare il riquadro **Avvisi di sicurezza** nel dashboard del Centro sicurezza di Azure.

3. Selezionare l'avviso di sicurezza per il quale si vogliono ottenere altre informazioni.

4. Nel campo **Report** fare clic sul collegamento al report di intelligence per le minacce.

5. Si aprirà un file PDF che è possibile scaricare.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Per altre informazioni sul report di intelligence per le minacce del Centro sicurezza di Azure, vedere [Report di intelligence per le minacce generato dal Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-threat-report).

### <a name="assessment"></a>Valutazione

Per semplificare la verifica e la valutazione del comportamento di sicurezza, il Centro sicurezza di Azure mette a disposizione la valutazione integrata della vulnerabilità con agenti cloud Qualys nell'ambito del componente delle raccomandazioni per le macchine virtuali.

L'agente Qualys invia i dati di vulnerabilità alla piattaforma di gestione Qualys che, a sua volta, ritrasmette i dati di vulnerabilità e monitoraggio dello stato al Centro sicurezza di Azure. La raccomandazione relativa all'aggiunta di una soluzione di valutazione della vulnerabilità viene visualizzata nel pannello **Raccomandazioni** del dashboard del Centro sicurezza di Azure.

Dopo l'installazione della soluzione di valutazione della vulnerabilità nella VM di destinazione, il Centro sicurezza analizza la VM per rilevare e identificare le vulnerabilità del sistema e delle applicazioni. I problemi rilevati vengono visualizzati nell'opzione **Raccomandazioni sulle macchine virtuali**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Come implementare una soluzione di valutazione della vulnerabilità 

Se in una macchina virtuale non è già distribuita una soluzione di valutazione della vulnerabilità, il Centro sicurezza ne raccomanda l'installazione.

1. Nel pannello **Raccomandazioni** del dashboard del Centro sicurezza di Azure selezionare **Aggiungi una soluzione di valutazione della vulnerabilità**.

2. Selezionare le VM in cui si vuole installare la soluzione di valutazione della vulnerabilità.

3. Fare clic su **Installa su [numero di] VM.**

4. Selezionare una soluzione partner in Azure Marketplace oppure selezionare **Qualys** in **Usa la soluzione esistente**.

5. È possibile attivare o disattivare le impostazioni di aggiornamento automatico nel pannello **Soluzioni partner**.

Per altre informazioni su come implementare una soluzione di valutazione della vulnerabilità, vedere [Valutazione della vulnerabilità nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations).

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva per il Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Introduzione al Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Integrazione degli avvisi del Centro sicurezza di Azure con l'integrazione dei log di Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Ottimizzare il Centro sicurezza di Azure con la valutazione integrata della vulnerabilità](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
