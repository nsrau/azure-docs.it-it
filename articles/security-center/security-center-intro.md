---
title: Introduzione al Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: bd0e517845b9cfcbe6090dff8d656edcca782c83
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126293"
---
# <a name="what-is-azure-security-center"></a>Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. Con il Centro sicurezza, è possibile applicare i criteri di sicurezza sui carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi.

Perché usare il Centro sicurezza?

- **Gestione centralizzata dei criteri**: garantisce la conformità con i requisiti di sicurezza normativi o aziendali tramite la gestione centralizzata dei criteri di sicurezza in tutti i carichi di lavoro cloud ibridi.
- **Continua valutazione della sicurezza**: monitora il comportamento di sicurezza dei computer, delle reti, dei servizi di archiviazione e dati e delle applicazioni per individuare potenziali problemi di sicurezza.
- **Consigli operativi**: corregge le vulnerabilità della sicurezza prima che possano essere sfruttate dagli utenti malintenzionati con indicazioni di sicurezza con priorità e operative.
- **Avvisi ed eventi imprevisti classificati in ordine di priorità**: si concentra per prima cosa sulle minacce principali con avvisi di sicurezza ed eventi imprevisti classificati in ordine di priorità.
- **Difese cloud avanzate**: riducono le minacce con l'accesso JIT (Just-in-Time) alle porte di gestione e i controlli applicazioni adattivi in esecuzione nelle macchine virtuali.
- **Soluzioni di sicurezza integrate**: raccoglie, esegue ricerche e analizza i dati di sicurezza da diverse origini, incluse le soluzioni partner connesse.

La **panoramica del Centro sicurezza** offre una visualizzazione rapida del comportamento di sicurezza dei carichi di lavoro Azure e non Azure, consentendo all'utente di individuare e valutare la sicurezza dei carichi di lavoro e identificare e ridurre i rischi. Il dashboard predefinito fornisce informazioni dettagliate immediate sugli avvisi di sicurezza e sulle vulnerabilità che richiedono attenzione.

![Panoramica][1]

## <a name="centralized-policy-management"></a>Gestione centralizzata dei criteri
La sezione **Criteri e conformità** (illustrata sopra) fornisce informazioni rapide sulla copertura della sottoscrizione, la conformità ai criteri e il comportamento di sicurezza.

### <a name="subscription-coverage"></a>Copertura della sottoscrizione
Questa sezione visualizza il numero totale di sottoscrizioni a cui si ha accesso (in lettura o scrittura) e il livello di copertura del Centro sicurezza (Standard o Gratuito) in cui è in esecuzione una sottoscrizione:

- **Con copertura (Standard)**: le sottoscrizioni con copertura vengono eseguite al livello massimo di protezione offerto dal Centro sicurezza
- **Con copertura (Gratuito)**: le sottoscrizioni con copertura vengono eseguite al livello di protezione gratuito e limitato offerto dal Centro sicurezza
- **Senza copertura**: le sottoscrizioni in questo stato non sono monitorate dal Centro sicurezza

Selezionando il grafico viene aperta la finestra **Copertura**. Se si seleziona una scheda (**Senza copertura**, **Copertura Basic** o **Copertura Standard**), viene visualizzato un elenco di sottoscrizioni con il relativo stato. Se si seleziona una sottoscrizione in una di queste schede, vengono visualizzate ulteriori informazioni sulla sottoscrizione. Queste informazioni consentono di identificare il proprietario della sottoscrizione e di contattarlo per abilitare il Centro sicurezza o per aumentare la copertura della sottoscrizione.

![Centro sicurezza - Copertura][9]

### <a name="policy-compliance"></a>Conformità ai criteri
La conformità ai criteri viene determinata dai fattori di conformità di tutti i criteri assegnati. Il punteggio di conformità complessivo per un gruppo di gestione, una sottoscrizione o un'area di lavoro è la media ponderata delle assegnazioni. La media ponderata si scompone nel numero di criteri in una singola assegnazione e nel numero di risorse a cui si applica l'assegnazione.

Ad esempio, se la sottoscrizione dispone di due macchine virtuali e un'iniziativa con cinque criteri assegnati, sono presenti dieci valutazioni nella sottoscrizione. Se una delle macchine virtuali non è conforme a due dei criteri, il punteggio di conformità complessivo dell'assegnazione della sottoscrizione è pari all'80%.

Questa sezione visualizza il rapporto di conformità complessivo e le sottoscrizioni con il punteggio di conformità più basso. Selezionando **Mostrare la conformità dei criteri dell'ambiente** viene aperta la finestra **Gestione dei criteri**. **Gestione dei criteri** mostra la struttura gerarchica dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro. In questa finestra è possibile gestire i criteri di sicurezza scegliendo una sottoscrizione o un gruppo di gestione.

![Gestione dei criteri][10]

Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. Nel Centro sicurezza i criteri vengono definiti e adattati al tipo di carico di lavoro o alla riservatezza dei dati, determinando quali controlli vengono monitorati e consigliati dal Centro sicurezza. È possibile modificare i criteri di sicurezza nel Centro sicurezza facendo clic su un gruppo di gestione o una sottoscrizione. Si può anche usare Criteri di Azure per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri ai vari gruppi di gestione.

Selezionare **Modifica impostazioni** per modificare le impostazioni del Centro sicurezza seguenti a livello di sottoscrizione, gruppo di gestione, gruppo di risorse o area di lavoro:

- **Raccolta di dati**: determina il provisioning dell'agente e le impostazioni di sicurezza della [raccolta di dati](security-center-enable-data-collection.md).
- **Notifiche tramite posta elettronica**: determina i contatti di sicurezza e le impostazioni di [notifica tramite posta elettronica](security-center-provide-security-contact-details.md).
- **Piano tariffario**: definisce la selezione del livello Gratuito o Standard del [piano tariffario](security-center-pricing.md). Il piano scelto determina le funzionalità del Centro sicurezza disponibili per le risorse nell'ambito.
- **Modifica le configurazioni di sicurezza**: consente di visualizzare e modificare le configurazioni del sistema operativo valutate dal Centro sicurezza per identificare le potenziali vulnerabilità della sicurezza.

Per altre informazioni, vedere [Integrare i criteri di sicurezza del Centro sicurezza con Criteri di Azure](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>Gestire e regolamentare il comportamento di sicurezza
Sul lato destro del dashboard in **Criteri e conformità** sono disponibili informazioni dettagliate su cui è possibile agire immediatamente per migliorare il comportamento di sicurezza generale. Alcuni esempi:

- Definire e assegnare i criteri del Centro sicurezza per verificare e tenere traccia della conformità agli standard di sicurezza
- Rendere disponibili gli avvisi di sicurezza del Centro sicurezza a un connettore di informazioni di sicurezza e gestione degli eventi
- Conformità ai criteri nel tempo

## <a name="continuous-security-assessment"></a>Valutazione continua della sicurezza
La sezione Protezione della sicurezza delle risorse in **Centro sicurezza - Panoramica** consente di controllare rapidamente lo stato di protezione della sicurezza delle risorse, visualizzando il numero di problemi identificati e lo stato di sicurezza di ogni tipo di risorsa. La valutazione continua consente di individuare potenziali problemi di sicurezza, ad esempio i sistemi con aggiornamenti della sicurezza mancanti o con porte di rete esposte.

### <a name="secure-score"></a>Punteggio di sicurezza
Il punteggio di sicurezza del Centro sicurezza di Azure esamina le raccomandazioni sulla sicurezza e assegna loro una priorità, in modo da sapere quali raccomandazioni seguire per prime e trovare più facilmente le vulnerabilità per la sicurezza più gravi per esaminarle per prime. Il punteggio di sicurezza è uno strumento di misurazione che consente di rafforzare il livello di sicurezza per ottenere un carico di lavoro sicuro. Per altre informazioni, vedere [Punteggio di sicurezza nel Centro sicurezza di Azure](security-center-secure-score.md).

### <a name="health-monitoring"></a>Monitoraggio dell’integrità
Se si seleziona una risorsa in **Monitoraggio dell'integrità delle risorse**, viene visualizzato un elenco di risorse e delle vulnerabilità eventualmente identificate. I tipi di risorsa sono: Risorse di calcolo e app, Rete, Dati e archiviazione e Identità e accesso.

In questo caso è stato selezionato **Risorse di calcolo e app**. Nella sezione **Calcolo**, sono disponibili quattro schede:

- **Panoramica**: monitoraggio e raccomandazioni identificati dal Centro sicurezza.
- **VM e computer**: elenco di VM, computer e il relativo stato di sicurezza.
- **Servizi cloud**: elenco dei ruoli Web e di lavoro monitorati dal Centro sicurezza.
- **Servizi app (anteprima)**: elenco delle applicazioni Web e degli ambienti del Servizio app e relativo stato di sicurezza corrente.

![Monitoraggio dell'integrità della sicurezza][3]

Per altre informazioni, vedere [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Consigli operativi
Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure e non di Azure per identificare le potenziali vulnerabilità di sicurezza. Se si seleziona **Raccomandazioni** in **Risorse**, viene visualizzato un elenco di raccomandazioni di sicurezza classificate in ordine di priorità che facilitano il processo di risoluzione dei problemi di sicurezza.

![Consigli][4]

Per altre informazioni, vedere [Gestione delle raccomandazioni di sicurezza](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>Consigli più frequenti
Sul lato destro del dashboard, in **Risorse**, è disponibile un elenco dei consigli più frequenti esistenti per il maggior numero di risorse. Questi consigli evidenziano l'area su cui occorre concentrare l'attenzione. Selezionando la freccia a destra viene visualizzato il consiglio con l'impatto più elevato.

![Consigli più frequenti][11]

Si tratta del consiglio con il maggiore impatto presente nell'ambiente. Applicandolo si otterrà il massimo livello di conformità. In questo esempio il consiglio è "Applica la crittografia del disco". Selezionando **Migliorare la conformità** viene visualizzata una descrizione del consiglio e un elenco delle risorse interessate.

![Applica la crittografia del disco][12]

## <a name="threat-protection"></a>Protezione dalle minacce
Questa area fornisce visibilità sugli avvisi di sicurezza rilevati sulle risorse e sul livello di gravità di tali avvisi.

### <a name="prioritized-alerts-and-incidents"></a>Avvisi ed eventi imprevisti classificati in ordine di priorità
Il Centro sicurezza usa l'analisi avanzata e l'intelligence per le minacce globali per rilevare attacchi in arrivo e attività post-violazione. Gli avvisi vengono classificati in ordine di priorità e raggruppati in eventi imprevisti, che consentono di concentrare l'attenzione per prima cosa sulle minacce più critiche. È possibile creare anche avvisi di sicurezza personalizzati.

Se si seleziona **Avvisi di sicurezza per gravità** o **Avvisi di sicurezza nel tempo**, vengono visualizzate informazioni dettagliate sugli avvisi.

![Avvisi ed eventi imprevisti classificati in ordine di priorità][7]

È possibile valutare rapidamente l'ambito e l'impatto di un attacco con un'esperienza interattiva di analisi visiva e usare query predefinite o ad hoc per un'esplorazione più approfondita dei dati di sicurezza.

Per altre informazioni, vedere [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md).

Sul lato destro del dashboard sono disponibili informazioni utili per assegnare la massima priorità agli avvisi da gestire per primi e per identificare le vulnerabilità più comuni dell'ambiente:

- **Risorse con il maggior numero di attacchi**: risorse specifiche con il numero più elevato di avvisi
- **Avvisi più frequenti**: tipi di avvisi che interessano il maggior numero di risorse

## <a name="just-in-time-vm-access"></a>Accesso Just-In-Time alla VM
Riduce la superficie di attacco alla rete con l'accesso JIT (Just In Time) controllato alle porte di gestione nelle macchine virtuali di Azure per ridurre drasticamente l'esposizione ad attacchi di forza bruta e ad altri attacchi di rete.

![Accesso Just-In-Time alla VM][5]

Specificare le regole per la modalità con cui gli utenti possono connettersi alle macchine virtuali. Quando necessario, può essere richiesto l'accesso dal Centro sicurezza o tramite PowerShell. Fino a quando la richiesta è conforme alle regole, l'accesso viene concesso automaticamente per il tempo richiesto.

Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Controlli delle applicazioni adattivi
Consentono di bloccare malware e altre applicazioni indesiderate applicando i consigli per l'inserimento nell'elenco elementi consentiti adattati ai carichi di lavoro specifici di Azure e basati su tecnologia per l'apprendimento automatico.

![Controlli delle applicazioni adattivi][6]

Rivedere e fare clic per applicare le regole consigliate per l'inserimento delle applicazioni nell'elenco elementi consentiti generate dal Centro sicurezza o modificare le regole già configurate.

Per altre informazioni, vedere [Controlli delle applicazioni adattivi nel Centro sicurezza di Azure](security-center-adaptive-application.md).

## <a name="integrate-your-security-solutions"></a>Integrare le soluzioni di sicurezza
Nel Centro sicurezza è possibile raccogliere, eseguire ricerche e analizzare i dati di sicurezza da diverse origini, inclusi soluzioni partner connesse quali firewall e altre servizi Microsoft.

![Integrare le soluzioni di sicurezza][8]

Per altre informazioni, vedere [Integrare soluzioni di sicurezza](security-center-partner-integration.md).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Il Piano tariffario Gratuito del Centro sicurezza di Azure viene abilitato con la sottoscrizione di Azure. Per sfruttare i vantaggi della gestione della sicurezza avanzata e la funzionalità di rilevamento minacce, è necessario eseguire l'aggiornamento al piano tariffario Standard. Il livello Standard è gratuito per i primi 60 giorni. Per altre informazioni, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).
- Se si è pronti per abilitare il livello Standard del Centro sicurezza, la [Guida introduttiva per il Centro sicurezza di Azure](security-center-get-started.md) illustra i passaggi da eseguire.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
