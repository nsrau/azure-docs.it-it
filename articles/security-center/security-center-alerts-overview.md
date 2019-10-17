---
title: Avvisi di sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: In questo argomento vengono illustrati gli avvisi di sicurezza e i diversi tipi disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: memildin
ms.openlocfilehash: 69b81417e541bd6853e02065e8cee08e3e04b4a2
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433888"
---
# <a name="security-alerts-in-azure-security-center"></a>Avvisi di sicurezza nel Centro sicurezza di Azure

Nel centro sicurezza di Azure sono disponibili diversi avvisi per molti tipi di risorse diversi. Il Centro sicurezza genera avvisi per le risorse distribuite in Azure e anche per le risorse distribuite in ambienti locali e cloud ibridi.

Il piano Standard del Centro sicurezza di Azure include funzionalità di rilevamento avanzate. È disponibile una versione di valutazione gratuita. È possibile eseguire l'aggiornamento dal piano tariffario selezionato in [Criteri di sicurezza](security-center-pricing.md). Per altre informazioni sui prezzi, vedere la [pagina del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/) .

## Risposta alle minacce <a name="respond-threats"></a> attuali

Negli ultimi 20 anni sono state introdotte modifiche significative nel panorama delle minacce . In passato, le aziende dovevano in genere preoccuparsi solo del danneggiamento del sito Web da parte di singoli utenti malintenzionati, interessati soprattutto a "provare cosa potevano fare". Oggi gli utenti malintenzionati sono molto più sofisticati e organizzati. Hanno spesso obiettivi finanziari e strategici specifici, inoltre hanno a disposizione più risorse, perché possono essere finanziati da stati o da organizzazioni criminali.

Queste mutevoli realtà hanno portato a un livello di professionalità senza precedenti nella classifica degli utenti malintenzionati. Non sono più interessati al danneggiamento del Web. Sono ora interessati a sottrarre informazioni, account finanziari e dati privati, che possono essere usati per generare denaro sul mercato aperto o per sfruttare una particolare posizione aziendale, politica o militare. Ancora più preoccupanti di quelli con un obiettivo finanziario sono gli utenti malintenzionati che violano le reti per danneggiare l'infrastruttura e le persone.

Per rispondere a questa situazione, le organizzazioni distribuiscono spesso varie soluzioni specifiche, concentrandosi sulla difesa del perimetro o degli endpoint dell'organizzazione attraverso la ricerca delle firme di attacchi noti. Queste soluzioni tendono a generare un numero elevato di avvisi con un basso livello di affidabilità, che richiedono l'intervento di un analista di sicurezza per la valutazione e l'analisi. La maggior parte delle organizzazioni non ha il tempo e le competenze che servono per rispondere a questi avvisi, quindi molti rimangono senza risposta.  

Inoltre, gli utenti malintenzionati hanno sviluppato i loro metodi per sovvertire molte difese basate sulla firma e [adattarsi agli ambienti cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Per identificare le minacce emergenti più rapidamente e accelerare le operazioni di rilevamento e risposta, sono quindi necessari nuovi approcci.

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza

Gli avvisi sono le notifiche generate dal centro sicurezza quando rileva le minacce sulle risorse. Il Centro sicurezza assegna le priorità ed elenca gli avvisi, insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza fornisce anche indicazioni su come correggere un attacco.

## In che modo il Centro sicurezza rileva le minacce? <a name="detect-threats"> </a>

I ricercatori Microsoft nell'ambito della sicurezza sono costantemente impegnati nella ricerca delle minacce. Grazie alla presenza globale di Microsoft nel cloud e in locale, l'utente ha accesso a un ampio set di dati di telemetria. La raccolta estesa e diversificata di set di impostazioni consente di individuare nuovi modelli di attacco e tendenze nei propri prodotti aziendali e consumer locali, oltre ai relativi Servizi online. Di conseguenza, il Centro sicurezza può aggiornare rapidamente gli algoritmi di rilevamento a fronte del rilascio di exploit nuovi e sofisticati da parte di utenti malintenzionati. Questo approccio consente di tenere il passo con un ambiente caratterizzato da minacce in rapida evoluzione.

Per rilevare le minacce reali e ridurre i falsi positivi, il Centro sicurezza raccoglie, analizza e integra i dati di log dalle risorse di Azure e dalla rete. Funziona anche con soluzioni partner connesse, ad esempio soluzioni di protezione endpoint e firewall. Il Centro sicurezza analizza queste informazioni, correlando spesso le informazioni provenienti da più origini, per identificare le minacce.

![Raccolta dati e presentazione del Centro sicurezza](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. I progressi tecnologici in ambito Big Data e [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) vengono sfruttati per valutare gli eventi nell'intera l'infrastruttura cloud, rilevando minacce che sarebbe impossibile identificare con approcci manuali e stimando l'evoluzione degli attacchi. Queste analisi della sicurezza includono:

* **Intelligence per le minacce integrata**: cerca gli attori cattivi noti sfruttando l'Intelligence per le minacce globale da prodotti e servizi Microsoft, da Microsoft Digital Crimes Unit (UCD), Microsoft Security Response Center (MSRC) e feed esterni.
* **Analisi del comportamento**: applica i criteri noti per individuare comportamenti dannosi.
* **Rilevamento anomalie**: usa la tecnica di profilatura statistica per creare una baseline cronologica. Genera avvisi sulle deviazioni dalle baseline stabilite che risultano conformi a un potenziale vettore di attacco .

Le sezioni seguenti illustrano ogni analisi in modo più dettagliato.

### <a name="integrated-threat-intelligence"></a>Intelligence per le minacce integrata

Microsoft vanta un'enorme quantità di dati di intelligence per le minacce globali. Il flusso di dati di telemetria proviene da più origini, ad esempio Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC). I ricercatori ricevono anche informazioni di intelligence per le minacce condivise tra i principali provider di servizi cloud e i feed di terze parti. Il Centro sicurezza di Azure usa queste informazioni per avvisare gli utenti nel caso di minacce provenienti da attori dannosi noti.

### <a name="behavioral-analytics"></a>Analisi comportamentali

L'analisi del comportamento è una tecnica che analizza e confronta i dati con una raccolta di modelli noti. Tuttavia, questi modelli non sono semplici firme. Sono determinati usando algoritmi di Machine Learning complessi applicati a set di dati di grandi dimensioni. Sono anche definiti tramite l'attento esame di comportamenti dannosi da parte di analisti esperti. Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi dei log delle macchine virtuali, dei dispositivi di rete virtuale, dell'infrastruttura, nonché dei dump di arresto anomalo del sistema e di altre origini.

Inoltre, c'è la correlazione con altri segnali per verificare la presenza di evidenze di una campagna diffusa. La correlazione consente di identificare gli eventi che risultano coerenti con gli indicatori di violazione stabiliti. 

### <a name="anomaly-detection"></a>Rilevazione delle anomalie

Il Centro sicurezza di Azure usa inoltre il rilevamento anomalie per identificare le minacce. A differenza dell'analisi del comportamento, che dipende da modelli noti derivati da set di dati di grandi dimensioni, il rilevamento anomalie è più "personalizzato" e incentrato sulle baseline specifiche delle distribuzioni. Le tecniche di apprendimento automatico vengono applicate per determinare la normale attività per le distribuzioni dei clienti e quindi vengono generate regole per definire le condizioni degli outlier che possono rappresentare un evento di sicurezza.

## <a name="how-are-alerts-classified"></a>In che modo vengono classificati gli avvisi?

Il Centro sicurezza assegna una gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

> [!NOTE]
> La gravità degli avvisi viene visualizzata in modo diverso nel portale e nell'API REST. Le differenze sono indicate nell'elenco seguente.

* **Elevato:** È molto probabile che la risorsa venga compromessa. È consigliabile controllarla immediatamente. Il Centro sicurezza ha una certezza elevata sia delle finalità dannose che delle conclusioni usate per inviare l'avviso, ad esempio un avviso che rileva l'esecuzione di uno strumento dannoso noto, come Mimikatz, uno strumento comune usato per il furto di credenziali.
* **Media (bassa nell'API REST)** : si tratta probabilmente di un'attività sospetta che può indicare la compromissione di una risorsa.
La fiducia del Centro sicurezza nell'analisi o nella conclusione è media e la certezza delle finalità dannose è da media a elevata. Si tratta in genere di rilevamenti basati sulle anomalie o su Machine Learning. Ad esempio, un tentativo di accesso da un percorso anomalo.
* **Bassa (Informazioni nell'API REST)** : potrebbe trattarsi di un falso positivo o di un attacco bloccato.
   * Il Centro sicurezza non è del tutto certo che l'intento sia dannoso e che l'attività possa essere inoffensiva. La cancellazione di un log, ad esempio, è un'azione che può verificarsi quando un utente malintenzionato cerca di nascondere le proprie tracce, ma in molti casi è un'operazione di routine eseguita dagli amministratori.
   * Il Centro sicurezza non segnala in genere quando sono stati bloccati attacchi, a meno che non si tratti di un caso interessante che si consiglia di esaminare. 
* **Informativo (invisibile nell'API REST)** : gli avvisi informativi verranno visualizzati solo quando si esegue il drill-down di un evento imprevisto relativo alla sicurezza oppure se si usa l'API REST con un ID avviso specifico. Un evento imprevisto è in genere costituito da più avvisi, alcuni dei quali sono di per sé semplicemente informativi, ma nel contesto degli altri avvisi potrebbero richiedere un'analisi più approfondita. 
 

## <a name="continuous-monitoring-and-assessments"></a>Monitoraggio e valutazioni continui

Il Centro sicurezza di Azure è in grado di sfruttare la ricerca di sicurezza e i team di data science in Microsoft che monitorano costantemente le modifiche nel panorama delle minacce. Sono incluse le iniziative seguenti:

* **Monitoraggio dell'intelligence per le minacce**: questo tipo di intelligence include meccanismi, indicatori, implicazioni e consigli utili sulle minacce esistenti o emergenti. Queste informazioni sono condivise nella community sulla sicurezza e Microsoft monitora costantemente i feed di intelligence per le minacce da origini interne ed esterne.
* **Condivisione dei segnali**: le informazioni dettagliate dai team della sicurezza nell'ampio portfolio di servizi, server e dispositivi endpoint client locali e cloud di Microsoft vengono condivise e analizzate.
* **Specialisti della sicurezza Microsoft**: in contatto costante con i team Microsoft che operano in ambiti di sicurezza specializzati, ad esempio analisi scientifiche e rilevamento di attacchi Web.
* **Ottimizzazione del rilevamento**: gli algoritmi vengono eseguiti su set di dati reali del cliente e ricercatori dedicati alla sicurezza collaborano con i clienti per convalidare i risultati. Per perfezionare gli algoritmi di Machine Learning vengono usati veri e falsi positivi.

Questi sforzi combinati convergono in rilevamenti nuovi e migliorati, da cui è possibile trarre vantaggio immediatamente, senza che sia richiesta alcuna azione.

## Tipi <a name="security-alert-types"></a> di avviso di sicurezza

Gli argomenti seguenti illustrano i diversi avvisi in base ai tipi di risorse:

* [Avvisi server e VM IaaS](security-center-alerts-iaas.md)
* [Avvisi di calcolo nativi](security-center-alerts-compute.md)
* [Avvisi di servizi dati](security-center-alerts-data-services.md)

Gli argomenti seguenti illustrano come il Centro sicurezza usa i diversi dati di telemetria raccolti dall'integrazione con l'infrastruttura di Azure, in modo da applicare livelli di protezione aggiuntivi per le risorse distribuite in Azure:

* [Avvisi del livello di servizio](security-center-alerts-service-layer.md)
* [Integrazione con i prodotti di sicurezza di Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Che cosa sono gli eventi imprevisti della sicurezza?

Un evento imprevisto di sicurezza è una raccolta di avvisi correlati, anziché elencare singolarmente ogni avviso. Il Centro sicurezza usa la [correlazione](security-center-alerts-cloud-smart.md) degli avvisi cloud per correlare avvisi diversi e segnali di fedeltà bassa in eventi imprevisti della sicurezza.

Con gli eventi imprevisti, il Centro sicurezza offre un'unica visualizzazione di una campagna di attacco e di tutti gli avvisi correlati. Questa visualizzazione consente di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate. Per ulteriori informazioni, vedere [correlazione tra Smart Alert cloud](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati i diversi tipi di avvisi disponibili nel centro sicurezza. Per scoprire di più, vedi:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Domande frequenti sul Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)

