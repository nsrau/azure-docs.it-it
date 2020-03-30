---
title: Avvisi di sicurezza nel Centro sicurezza di Azure - Documenti Microsoft
description: Questo argomento illustra gli avvisi di sicurezza e i diversi tipi disponibili nel Centro sicurezza di Azure.This topic explains what security alerts are, and the different types available in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415860"
---
# <a name="security-alerts-in-azure-security-center"></a>Avvisi di sicurezza nel Centro sicurezza di Azure

Nel Centro sicurezza di Azure sono disponibili diversi avvisi per molti tipi di risorse diversi. Il Centro sicurezza genera avvisi per le risorse distribuite in Azure e anche per le risorse distribuite in ambienti cloud locali e ibridi.

Security alerts are triggered by advanced detections and are available only in the Standard Tier of Azure Security Center. È disponibile una versione di valutazione gratuita. È possibile eseguire l'aggiornamento dal piano tariffario selezionato in [Criteri di sicurezza](security-center-pricing.md). Per altre informazioni sui prezzi, vedere la [pagina del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/) .

## <a name="responding-to-todays-threats"></a>Rispondere alle minacce <a name="respond-threats"> </a> odierne

Negli ultimi 20 anni sono state introdotte modifiche significative nel panorama delle minacce . In passato, le aziende in genere dovevano preoccuparsi solo di defacement del sito web da parte di singoli aggressori che erano per lo più interessati a vedere "cosa potevano fare". Gli aggressori di oggi sono molto più sofisticati e organizzati. Hanno spesso obiettivi finanziari e strategici specifici, inoltre hanno a disposizione più risorse, perché possono essere finanziati da stati o da organizzazioni criminali.

Queste realtà mutevoli hanno portato ad un livello senza precedenti di professionalità nei ranghi degli attaccanti. Non sono più interessati al danneggiamento del Web. Ora sono interessati a rubare informazioni, conti finanziari e dati privati - tutti che possono utilizzare per generare denaro sul mercato aperto o per sfruttare una particolare posizione commerciale, politica o militare. Ancora più preoccupanti di quelli con un obiettivo finanziario sono gli utenti malintenzionati che violano le reti per danneggiare l'infrastruttura e le persone.

Per rispondere a questa situazione, le organizzazioni distribuiscono spesso varie soluzioni specifiche, concentrandosi sulla difesa del perimetro o degli endpoint dell'organizzazione attraverso la ricerca delle firme di attacchi noti. Queste soluzioni tendono a generare un numero elevato di avvisi con un basso livello di affidabilità, che richiedono l'intervento di un analista di sicurezza per la valutazione e l'analisi. La maggior parte delle organizzazioni non ha il tempo e le competenze che servono per rispondere a questi avvisi, quindi molti rimangono senza risposta.  

Inoltre, gli aggressori hanno evoluto i loro metodi per sovvertire molte difese basate sulle firme e [adattarsi agli ambienti cloud.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) Per identificare le minacce emergenti più rapidamente e accelerare le operazioni di rilevamento e risposta, sono quindi necessari nuovi approcci.

## <a name="what-are-security-alerts-and-security-incidents"></a>Cosa sono gli avvisi di sicurezza e gli incidenti di sicurezza? 

**Gli avvisi** sono le notifiche generate dal Centro sicurezza quando rileva le minacce nelle risorse. Il Centro sicurezza assegna priorità ed elenca gli avvisi, insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza offre anche raccomandazioni per la risoluzione di un attacco.

**Un incidente di sicurezza** è una raccolta di avvisi correlati, anziché elencare ogni avviso singolarmente. Il Centro sicurezza utilizza [Cloud Smart Alert Correlation](security-center-alerts-cloud-smart.md) per correlare diversi avvisi e segnali a bassa fedeltà in incidenti di sicurezza.

Utilizzando gli eventi imprevisti, centro sicurezza offre una visualizzazione singola di una campagna di attacco e di tutti gli avvisi correlati. Questa visualizzazione consente di comprendere rapidamente le azioni eseguite dall'utente malintenzionato e le risorse interessate. Per ulteriori informazioni, consultate [Correlazione degli avvisi intelligenti cloud](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>In che modo il Centro sicurezza rileva le minacce? <a name="detect-threats"> </a>

I ricercatori Microsoft nell'ambito della sicurezza sono costantemente impegnati nella ricerca delle minacce. A causa della presenza globale di Microsoft nel cloud e in locale, hanno accesso a un ampio set di dati di telemetria. La vasta e diversificata raccolta di set di dati consente di scoprire nuovi modelli di attacco e tendenze nei suoi prodotti consumer e enterprise locali, nonché nei suoi servizi online. Di conseguenza, il Centro sicurezza può aggiornare rapidamente gli algoritmi di rilevamento a fronte del rilascio di exploit nuovi e sofisticati da parte di utenti malintenzionati. Questo approccio consente di tenere il passo con un ambiente caratterizzato da minacce in rapida evoluzione.

Per rilevare le minacce reali e ridurre i falsi positivi, il Centro sicurezza raccoglie, analizza e integra i dati di log dalle risorse di Azure e dalla rete. Funziona anche con soluzioni partner connessi, come soluzioni di protezione da firewall ed endpoint. Il Centro sicurezza analizza queste informazioni, spesso correlando le informazioni provenienti da più origini, per identificare le minacce.

![Raccolta dati e presentazione del Centro sicurezza](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. I progressi tecnologici in ambito Big Data e [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) vengono sfruttati per valutare gli eventi nell'intera l'infrastruttura cloud, rilevando minacce che sarebbe impossibile identificare con approcci manuali e stimando l'evoluzione degli attacchi. Queste analisi della sicurezza includono:

* **Intelligenza integrata**delle minacce : Microsoft ha un'immensa quantità di informazioni sulle minacce globali. Il flusso di dati di telemetria proviene da più origini, ad esempio Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC). I ricercatori ricevono anche informazioni di intelligence sulle minacce che vengono condivise tra i principali fornitori di servizi cloud e feed da altre terze parti. Il Centro sicurezza di Azure usa queste informazioni per avvisare gli utenti nel caso di minacce provenienti da attori dannosi noti.

* **Analisi comportamentale**: L'analisi comportamentale è una tecnica che analizza e confronta i dati con una raccolta di modelli noti. Tuttavia, questi modelli non sono semplici firme. Sono determinati usando algoritmi di Machine Learning complessi applicati a set di dati di grandi dimensioni. Sono anche definiti tramite l'attento esame di comportamenti dannosi da parte di analisti esperti. Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi dei log delle macchine virtuali, dei dispositivi di rete virtuale, dell'infrastruttura, nonché dei dump di arresto anomalo del sistema e di altre origini.

* **Rilevamento**anomalie: il Centro sicurezza di Azure usa anche il rilevamento delle anomalie per identificare le minacce. A differenza dell'analisi comportamentale (che dipende da modelli noti derivati da set di dati di grandi dimensioni), il rilevamento delle anomalie è più "personalizzato" e si concentra sulle linee di base specifiche per le distribuzioni. Le tecniche di apprendimento automatico vengono applicate per determinare la normale attività per le distribuzioni dei clienti e quindi vengono generate regole per definire le condizioni degli outlier che possono rappresentare un evento di sicurezza.

## <a name="how-are-alerts-classified"></a>Come vengono classificati gli avvisi?

Il Centro sicurezza assegna una gravità agli avvisi, per consentire di assegnare una priorità all'ordine di assistenza a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile raggiungerlo immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

> [!NOTE]
> La gravità degli avvisi viene visualizzata in modo diverso nel portale e nelle versioni dell'API REST antedate 01-01-2019.Alert severity is displayed differently in the portal and versions of the REST API that predate 01-01-2019. Se usi una versione precedente dell'API, esegui l'aggiornamento per l'esperienza coerente descritta di seguito.

- **Alto:** È molto probabile che la risorsa venga compromessa. È consigliabile controllarla immediatamente. Il Centro sicurezza ha una certezza elevata sia delle finalità dannose che delle conclusioni usate per inviare l'avviso, ad esempio un avviso che rileva l'esecuzione di uno strumento dannoso noto, come Mimikatz, uno strumento comune usato per il furto di credenziali.
- **Medio:** Si tratta probabilmente di un'attività sospetta può indicare che una risorsa è compromessa.
La fiducia del Centro sicurezza nell'analita o nella ricerca è media e la fiducia dell'intento dannoso è medio-alta. Questi sarebbero in genere l'apprendimento automatico o rilevamenti basati su anomalie. Ad esempio, un tentativo di accesso da una posizione anomala.
- **Basso:** Questo potrebbe essere un positivo benigno o un attacco bloccato.
   * Il Centro sicurezza non è del tutto certo che l'intento sia dannoso e che l'attività possa essere inoffensiva. La cancellazione di un log, ad esempio, è un'azione che può verificarsi quando un utente malintenzionato cerca di nascondere le proprie tracce, ma in molti casi è un'operazione di routine eseguita dagli amministratori.
   * Il Centro sicurezza di solito non ti dice quando gli attacchi sono stati bloccati, a meno che non sia un caso interessante che ti consigliamo di esaminare. 
- **Informativo:** Gli avvisi informativi verranno visualizzati solo quando si esegue il drill-down in un evento imprevisto di sicurezza o se si usa l'API REST con un ID avviso specifico. Un evento imprevisto è in genere costituito da più avvisi, alcuni dei quali sono di per sé semplicemente informativi, ma nel contesto degli altri avvisi potrebbero richiedere un'analisi più approfondita. 

## <a name="continuous-monitoring-and-assessments"></a>Monitoraggio e valutazioni continui

Il Centro sicurezza di Azure trae vantaggio dall'avere team di ricerca e analisi scientifica dei dati di sicurezza in tutta Microsoft che monitorano continuamente le modifiche nel panorama delle minacce. Sono incluse le iniziative seguenti:

* **Monitoraggio dell'intelligence**delle minacce: l'intelligence sulle minacce include meccanismi, indicatori, implicazioni e consigli utili sulle minacce esistenti o emergenti. Queste informazioni sono condivise nella community sulla sicurezza e Microsoft monitora costantemente i feed di intelligence per le minacce da origini interne ed esterne.
* **Condivisione dei**segnali: vengono condivise e analizzate le informazioni dettagliate dei team di sicurezza nell'ampio portafoglio di servizi, server e dispositivi endpoint client cloud e locali di Microsoft.
* **Specialisti della sicurezza Microsoft**: in contatto costante con i team Microsoft che operano in ambiti di sicurezza specializzati, ad esempio analisi scientifiche e rilevamento di attacchi Web.
* **Ottimizzazione del rilevamento**: gli algoritmi vengono eseguiti su set di dati reali del cliente e ricercatori dedicati alla sicurezza collaborano con i clienti per convalidare i risultati. Per perfezionare gli algoritmi di Machine Learning vengono usati veri e falsi positivi.

Questi sforzi combinati culminano in rilevamenti nuovi e migliorati, di cui puoi beneficiare istantaneamente: non c'è nessuna azione da intraprendere.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi i diversi tipi di avvisi disponibili nel Centro sicurezza. Per altre informazioni, vedere:

* [Protezione dalle minacce nel Centro sicurezza](threat-protection.md) di Azure- Per una breve descrizione delle origini degli avvisi di sicurezza visualizzati dal Centro sicurezza di Azure 
* **Avvisi di sicurezza nel log attività** di Azure: oltre a essere disponibili nel portale di Azure o a livello di codice, gli avvisi e gli eventi imprevisti di sicurezza vengono controllati come eventi nel log attività di Azure.Security alerts in Azure Activity Log - Oltre a essere disponibile nel portale di Azure o a livello di codice, gli avvisi e gli eventi imprevisti di sicurezza vengono controllati come eventi nel log attività di Azure.Security alerts in [Azure Activity Log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)- Oltre a essere disponibile in the Azure portal or programmatically, Security alerts and incident Per altre informazioni sullo schema degli eventi, vedere Avvisi di sicurezza nel log attività di [AzureFor](https://go.microsoft.com/fwlink/?linkid=2114113) more information on the event schema, see Security Alerts in Azure Activity log

