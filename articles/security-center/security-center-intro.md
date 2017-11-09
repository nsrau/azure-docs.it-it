---
title: Introduzione al Centro sicurezza di Azure | Documentazione Microsoft
description: "Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 21415af0d449d639d000e07afdb4de3680a64774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-security-center"></a>Introduzione al Centro sicurezza di Azure
Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento.

## <a name="what-is-azure-security-center"></a>Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud.  Offre visibilità e controllo su carichi di lavoro cloud ibridi, difese attive che riducono l'esposizione a minacce e il rilevamento intelligente che consente di tenere il passo con gli attacchi informatici in rapida evoluzione.

La panoramica del Centro sicurezza offre una visualizzazione rapida del comportamento di sicurezza dei carichi di lavoro Azure e non Azure, consentendo all'utente di individuare e valutare la sicurezza dei carichi di lavoro e identificare e ridurre i rischi.

![Panoramica](./media/security-center-intro/security-center-intro-fig1.png)

## <a name="why-use-security-center"></a>Perché usare il Centro sicurezza?

**Visibilità e controllo unificati**

- **Informazioni sullo stato della sicurezza nei carichi di lavoro ibridi**. Gestione della protezione in tutti i carichi di lavoro cloud, locali, di Azure e di altre piattaforme cloud, in un'unica console. I dashboard predefiniti offrono informazioni dettagliate immediate sui problemi di sicurezza che richiedono attenzione.
- **Visibilità sui carichi di lavoro cloud**. Restare al passo con carichi di lavoro cloud che cambiano rapidamente. Individuare e caricare automaticamente le nuove risorse create nelle sottoscrizioni di Azure.
- **Gestione dei centralizzata**. Garantisce la conformità con i requisiti di sicurezza normativi o aziendali tramite la gestione centralizzata dei criteri di sicurezza in tutti i carichi di lavoro cloud ibridi.
- **Dati di sicurezza da più origini**. Consente di raccogliere, eseguire ricerche e analizzare i dati di sicurezza da diverse origini, inclusi soluzioni partner connesse quali firewall e altre servizi Microsoft. 
- **Integrazione con flussi di lavoro di sicurezza esistente**. Consente di accedere, integrare e analizzare le informazioni di sicurezza usando le API REST per collegare i processi e gli strumenti esistenti.
- **Dichiarazione di conformità**. Consente di usare i dati di sicurezza e le informazioni dettagliate per dimostrare la conformità e generare facilmente prove per gli auditor.

**Prevenzione delle minacce adattiva**

- **Valutazione continua della sicurezza**. Consente di monitorare la sicurezza dei computer, delle reti e dei servizi di Azure usando centinaia di valutazioni della sicurezza predefinite oppure creare valutazioni personalizzate. Consente di identificare software e configurazioni vulnerabili agli attacchi.
- **Consigli operativi**. Consente di correggere le vulnerabilità di sicurezza prima che possano essere sfruttati dagli utenti malintenzionati con indicazioni di sicurezza operativi con priorità e playbooks di automazione incorporati.
- **Controlli delle applicazioni adattivi**. Consentono di bloccare malware e altre applicazioni indesiderate applicando i consigli per l'inserimento nell'elenco elementi consentiti adattati ai carichi di lavoro specifici di Azure e basati su tecnologia per l'apprendimento automatico. 
- **Sicurezza dall'accesso alla rete**. Riduce la superficie di attacco alla rete con l'accesso JIT (Just-In-Time) controllato alle porte di gestione nelle macchine virtuali di Azure per ridurre drasticamente l'esposizione ad attacchi di forza bruta e ad altri attacchi di rete.

**Rilevamento delle minacce e risposta intelligente**

- **Intelligence per le minacce più estesa del settore**. Sfrutta Microsoft Intelligent Security Graph, che usa miliardi di segnali provenienti da sistemi e dai servizi Microsoft di tutto il mondo per identificare minacce nuove e in continua evoluzione.
- **Rilevamento avanzato delle minacce**. Usa i vantaggi delle analisi comportamentali predefinite e di apprendimento automatico per identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione.
- **Avvisi ed eventi imprevisti classificati in ordine di priorità**. Si concentra sulle minacce principali innanzitutto con avvisi di sicurezza con priorità che eseguono il mapping di tipi diversi di avviso in una campagna di attacco singolo. Consente di creare anche gli avvisi di sicurezza personalizzati.
- **Analisi semplificata**. Consente di valutare rapidamente l'ambito e l'impatto di un attacco con un'esperienza di visualizzazione interattiva. Usa query predefinite o ad hoc per un'esplorazione più approfondita dei dati di sicurezza. 
- **Intelligence per le minacce contestuali**. Consente di visualizzare l'origine dell'attacco in una mappa mondiale interattiva. Usa report di intelligence per le minacce predefiniti per acquisire informazioni importanti sulle tecniche e gli obiettivi di attori dannosi noti.

## <a name="get-started"></a>Attività iniziali
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Il Centro sicurezza viene abilitato con la sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). 

[Introduzione al Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started) fornisce una rapida descrizione dei componenti di monitoraggio della sicurezza e gestione dei criteri del Centro sicurezza. 


## <a name="next-steps"></a>Passaggi successivi
Questo documento ha illustrato il Centro sicurezza, le funzionalità principali e come iniziare a usarlo. Per altre informazioni, vedere le risorse seguenti:

* [Guida operativa e alla pianificazione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni su come ottimizzare l'uso del Centro sicurezza in base ai requisiti di sicurezza e al modello di gestione cloud dell'organizzazione.
* [Impostazione dei criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-recommendations): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure e non di Azure.
* [Monitoraggio dell'integrità della sicurezza](https://docs.microsoft.com/azure/security-center/security-center-monitoring): informazioni su come monitorare l'integrità delle risorse di Azure e non di Azure.
* [Gestione e risposta agli avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner](https://docs.microsoft.com/azure/security-center/security-center-partner-solutions): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-faq): leggere le domande frequenti sull'uso del Centro sicurezza.


