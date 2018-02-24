---
title: Introduzione al Centro sicurezza di Azure | Microsoft Docs
description: "Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-security-center"></a>Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. Con il Centro sicurezza, è possibile applicare i criteri di sicurezza sui carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi.

Perché usare il Centro sicurezza?

- **Gestione centralizzata dei criteri**: garantisce la conformità con i requisiti di sicurezza normativi o aziendali tramite la gestione centralizzata dei criteri di sicurezza in tutti i carichi di lavoro cloud ibridi.
- **Continua valutazione della sicurezza**: monitora la sicurezza dei computer, delle reti, dei servizi di archiviazione e dati e delle applicazioni per individuare potenziali problemi di sicurezza.
- **Consigli operativi**: corregge le vulnerabilità della sicurezza prima che possano essere sfruttate dagli utenti malintenzionati con indicazioni di sicurezza con priorità e operative.
- **Difese cloud avanzate**: riduce le minacce con l'accesso JIT (Just-in-Time) alle porte di gestione e l'inserimento nell'elenco degli elementi consentiti per controllare le applicazioni in esecuzione nelle macchine virtuali.
- **Avvisi ed eventi imprevisti classificati in ordine di priorità**: si concentra per prima cosa sulle minacce principali con avvisi di sicurezza ed eventi imprevisti classificati in ordine di priorità.
- **Soluzioni di sicurezza integrate**: raccoglie, esegue ricerche e analizza i dati di sicurezza da diverse origini, incluse le soluzioni partner connesse.

La **panoramica del Centro sicurezza** offre una visualizzazione rapida del comportamento di sicurezza dei carichi di lavoro Azure e non Azure, consentendo all'utente di individuare e valutare la sicurezza dei carichi di lavoro e identificare e ridurre i rischi. Il dashboard predefinito fornisce informazioni dettagliate immediate sugli avvisi di sicurezza e sulle vulnerabilità che richiedono attenzione.

![Panoramica][1]

## <a name="centralized-policy-management"></a>Gestione centralizzata dei criteri
Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. Nel Centro sicurezza vengono definiti i criteri e vengono adattati al tipo di carico di lavoro o alla riservatezza dei dati.

I criteri del Centro sicurezza includono i componenti seguenti:

- **Raccolta di dati**: determina il provisioning dell'agente e le impostazioni di sicurezza della [raccolta di dati](security-center-enable-data-collection.md).
- **Criteri di sicurezza**: determina i controlli monitorati e consigliati dal Centro sicurezza mediante la modifica dei [criteri di sicurezza](security-center-policies.md).
- **Notifiche tramite posta elettronica**: determina i contatti di sicurezza e le impostazioni di [notifica tramite posta elettronica](security-center-provide-security-contact-details.md).
- **Piano tariffario**: definisce la selezione del livello Gratuito o Standard del [piano tariffario](security-center-pricing.md). Il piano scelto determina le funzionalità del Centro sicurezza disponibili per le risorse nell'ambito.

![Criteri di sicurezza][2]

Per altre informazioni, vedere [Panoramica dei criteri di sicurezza](security-center-policies-overview.md).

## <a name="continuous-security-assessment"></a>Valutazione continua della sicurezza
Il Centro sicurezza analizza lo stato di sicurezza delle risorse di calcolo, delle reti virtuali, dei servizi di archiviazione e dati e delle applicazioni. La valutazione continua consente di individuare potenziali problemi di sicurezza, ad esempio i sistemi con aggiornamenti della sicurezza mancanti o con porte di rete esposte. Selezionare un riquadro nella sezione Prevenzione per visualizzare altre informazioni, incluso un elenco delle risorse e delle potenziali vulnerabilità identificate.

![Monitoraggio dell'integrità della sicurezza][3]

Per altre informazioni, vedere [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Consigli operativi
Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure e non di Azure per identificare le potenziali vulnerabilità di sicurezza. Un elenco di raccomandazioni di sicurezza classificate in ordine di priorità descrive il processo di gestione dei problemi di sicurezza.

![Raccomandazioni][4]

Per altre informazioni, vedere [Gestione delle raccomandazioni di sicurezza](security-center-recommendations.md).

## <a name="just-in-time-vm-access"></a>Accesso Just-In-Time alla VM
Riduce la superficie di attacco alla rete con l'accesso JIT (Just In Time) controllato alle porte di gestione nelle macchine virtuali di Azure per ridurre drasticamente l'esposizione ad attacchi di forza bruta e ad altri attacchi di rete.

![Accesso Just-In-Time alla VM][5]

Specificare le regole per la modalità con cui gli utenti possono connettersi alle macchine virtuali. Quando necessario, può essere richiesto l'accesso dal Centro sicurezza o tramite PowerShell. Fino a quando la richiesta è conforme alle regole, l'accesso viene concesso automaticamente per il tempo richiesto.

Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time) ](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Controlli delle applicazioni adattivi
Consentono di bloccare malware e altre applicazioni indesiderate applicando i consigli per l'inserimento nell'elenco elementi consentiti adattati ai carichi di lavoro specifici di Azure e basati su tecnologia per l'apprendimento automatico.

![Controlli delle applicazioni adattivi][6]

Rivedere e fare clic per applicare le regole consigliate per l'inserimento delle applicazioni nell'elenco elementi consentiti generate dal Centro sicurezza o modificare le regole già configurate.

Per altre informazioni, vedere [Controlli adattivi della applicazioni](security-center-adaptive-application.md).

## <a name="prioritized-alerts-and-incidents"></a>Avvisi ed eventi imprevisti classificati in ordine di priorità
Il Centro sicurezza usa l'analisi avanzata e l'intelligence per le minacce globali per rilevare attacchi in arrivo e attività post-violazione. Gli avvisi vengono classificati in ordine di priorità e raggruppati in eventi imprevisti, che consentono di concentrare l'attenzione per prima cosa sulle minacce più critiche. È possibile creare anche avvisi di sicurezza personalizzati.

![Avvisi ed eventi imprevisti classificati in ordine di priorità][7]

È possibile valutare rapidamente l'ambito e l'impatto di un attacco con un'esperienza interattiva di analisi visiva e usare query predefinite o ad hoc per un'esplorazione più approfondita dei dati di sicurezza.

Per altre informazioni, vedere [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md).

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
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
