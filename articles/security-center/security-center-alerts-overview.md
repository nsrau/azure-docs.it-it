---
title: Avvisi di sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: In questo argomento vengono illustrati gli avvisi di sicurezza e i diversi tipi disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/25/2019
ms.author: v-mohabe
ms.openlocfilehash: 1a3ce29750351d6037b376ebb93eb73e141c0727
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047574"
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

* **Intelligence per le minacce integrata**: Cerca gli attori cattivi noti sfruttando l'Intelligence per le minacce globale da prodotti e servizi Microsoft, da Microsoft Digital Crimes Unit (UCD), Microsoft Security Response Center (MSRC) e feed esterni.
* **Analisi del comportamento**: Vengono applicati schemi noti per individuare comportamenti dannosi.
* **Rilevamento anomalie**: Usa la tecnica di profilatura statistica per creare una baseline cronologica. Genera avvisi sulle deviazioni dalle baseline stabilite che risultano conformi a un potenziale vettore di attacco .

Gli argomenti seguenti illustrano ogni analisi in modo più dettagliato.

### <a name="integrated-threat-intelligence"></a>Intelligence per le minacce integrata

Microsoft vanta un'enorme quantità di dati di intelligence per le minacce globali. Il flusso di dati di telemetria proviene da più origini, ad esempio Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC). I ricercatori ricevono anche informazioni di intelligence per le minacce condivise tra i principali provider di servizi cloud e sottoscrivono i feed di terze parti di intelligence per le minacce. Il Centro sicurezza di Azure usa queste informazioni per avvisare gli utenti nel caso di minacce provenienti da attori dannosi noti.

### <a name="behavioral-analytics"></a>Analisi del comportamento

L'analisi del comportamento è una tecnica che analizza e confronta i dati con una raccolta di modelli noti. Tuttavia, questi modelli non sono semplici firme. Sono determinati usando algoritmi di Machine Learning complessi applicati a set di dati di grandi dimensioni. Sono anche definiti tramite l'attento esame di comportamenti dannosi da parte di analisti esperti. Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi dei log delle macchine virtuali, dei dispositivi di rete virtuale, dell'infrastruttura, nonché dei dump di arresto anomalo del sistema e di altre origini.

Esiste inoltre una correlazione con altri segnali per verificare la presenza di elementi a riprova di una campagna su larga scala. La correlazione consente di identificare gli eventi che risultano coerenti con gli indicatori di violazione stabiliti. 

### <a name="anomaly-detection"></a>Rilevamento anomalie

Il Centro sicurezza di Azure usa inoltre il rilevamento anomalie per identificare le minacce. A differenza dell'analisi del comportamento, che dipende da modelli noti derivati da set di dati di grandi dimensioni, il rilevamento anomalie è più "personalizzato" e incentrato sulle baseline specifiche delle distribuzioni. Le tecniche di apprendimento automatico vengono applicate per determinare la normale attività per le distribuzioni dei clienti e quindi vengono generate regole per definire le condizioni degli outlier che possono rappresentare un evento di sicurezza.

## <a name="continuous-monitoring-and-assessments"></a>Monitoraggio e valutazioni continui

Il Centro sicurezza di Azure è in grado di sfruttare la ricerca di sicurezza e i team di data science in Microsoft che monitorano costantemente le modifiche nel panorama delle minacce. Sono incluse le iniziative seguenti:

* **Monitoraggio dell'intelligence per le minacce**: l'intelligence per le minacce include meccanismi, indicatori, implicazioni e consigli utili sulle minacce esistenti o emergenti. Queste informazioni sono condivise nella community sulla sicurezza e Microsoft monitora costantemente i feed di intelligence per le minacce da origini interne ed esterne.
* **Condivisione dei segnali**: le informazioni dettagliate dai team della sicurezza nell'ampio portfolio di servizi, server e dispositivi endpoint client locali e cloud di Microsoft vengono condivise e analizzate.
* **Specialisti della sicurezza Microsoft**: collaborazione continua con i team Microsoft che operano in ambiti di sicurezza specializzati, ad esempio analisi forense e rilevamento di attacchi Web.
* **Ottimizzazione del rilevamento**: gli algoritmi vengono eseguiti su set di dati reali dei clienti e ricercatori che si occupano di sicurezza collaborano con i clienti per convalidare i risultati. Per perfezionare gli algoritmi di Machine Learning vengono usati veri e falsi positivi.

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

Un evento imprevisto di sicurezza è una raccolta di avvisi correlati, anziché elencare singolarmente ogni avviso. Il Centro sicurezza usa la correlazione degli avvisi [cloud](security-center-alerts-cloud-smart.md) per correlare avvisi diversi e segnali di fedeltà bassa in eventi imprevisti della sicurezza.

Con gli eventi imprevisti, il Centro sicurezza offre un'unica visualizzazione di una campagna di attacco e di tutti gli avvisi correlati. Questa visualizzazione consente di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate. Per ulteriori informazioni, vedere [correlazione tra Smart Alert cloud](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati i diversi tipi di avvisi disponibili nel centro sicurezza. Per altre informazioni, vedere:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Domande frequenti sul Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)

