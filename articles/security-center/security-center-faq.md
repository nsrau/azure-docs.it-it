---
title: Domande frequenti sul Centro sicurezza di Azure | Documentazione Microsoft
description: Queste FAQ rispondono alle domande sul Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2018
ms.author: terrylan
ms.openlocfilehash: 2bbd0a8be891bd472cdc631a1f8dc79471d66a77
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Domande frequenti sul Centro sicurezza di Azure
Queste FAQ rispondono alle domande sul Centro sicurezza di Azure, un servizio che consente di prevenire, rilevare e rispondere alle minacce con visibilità e controllo maggiori sulla sicurezza delle risorse di Microsoft Azure.

> [!NOTE]
> A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Domande generali
### <a name="what-is-azure-security-center"></a>Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

### <a name="how-do-i-get-azure-security-center"></a>In che modo è possibile accedere al Centro sicurezza di Azure?
Il Centro sicurezza di Azure viene abilitato con la sottoscrizione di Microsoft Azure ed è accessibile dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/). ([accedere al portale](https://portal.azure.com), selezionare **Sfoglia** e scorrere fino a **Centro sicurezza**).  

## <a name="billing"></a>Fatturazione
### <a name="how-does-billing-work-for-azure-security-center"></a>Come funziona la fatturazione per il Centro sicurezza di Azure?
Il Centro sicurezza è disponibile in due livelli:

Il **livello gratuito** permette di conoscere lo stato di protezione delle risorse di Azure, i criteri di sicurezza di base, i consigli sulla sicurezza e l'aspetto di integrazione con i prodotti e i servizi dei partner.

Il **livello standard** aggiunge funzionalità avanzate per il rilevamento delle minacce, tra cui intelligence per le minacce, analisi del comportamento, rilevamento delle anomalie, eventi imprevisti per la sicurezza e report di valutazione delle minacce. Il livello Standard è gratuito per i primi 60 giorni. Se scegli di continuare a usare il servizio dopo questi 60 giorni, l'uso del servizio verrà addebitato automaticamente.  Per eseguire l'aggiornamento, selezionare il [piano tariffario](https://docs.microsoft.com/azure/security-center/security-center-pricing) nei criteri di sicurezza.

## <a name="permissions"></a>Autorizzazioni
Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md), con [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](security-center-permissions.md) (Autorizzazioni in Centro sicurezza di Azure).

## <a name="data-collection"></a>Raccolta dei dati
Il Centro sicurezza raccoglie i dati dalle macchine virtuali per valutarne lo stato della sicurezza, indicare raccomandazioni sulla sicurezza e segnalare le minacce. La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. È inoltre possibile abilitare la raccolta dei dati nei criteri del Centro sicurezza.

### <a name="how-do-i-disable-data-collection"></a>Come si disabilita la raccolta dati?
Se si usa il livello gratuito del Centro sicurezza di Azure, è possibile disabilitare la raccolta dati dalle macchine virtuali in qualsiasi momento. La raccolta dati è richiesta per le sottoscrizioni a livello Standard. È possibile disabilitare la raccolta dei dati per una sottoscrizione in Criteri di sicurezza. ([accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, quindi **Centro sicurezza** e infine **Criteri**).  Quando si seleziona una sottoscrizione, si apre un nuovo pannello in cui è possibile disattivare la **raccolta di dati**.

### <a name="how-do-i-enable-data-collection"></a>Come si abilita la raccolta dati?
È possibile abilitare la raccolta dei dati per la sottoscrizione nei criteri di sicurezza. Per abilitare la raccolta di dati. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, quindi **Centro sicurezza** e infine **Criteri**. Impostare **Raccolta dati** su **On** (Attivo).

### <a name="what-happens-when-data-collection-is-enabled"></a>Cosa accade quando si abilita la raccolta dati?
Dopo aver abilitato la raccolta dati nei criteri di sicurezza, si esegue il provisioning automatico di Microsoft Monitoring Agent in tutte le macchine virtuali supportate, nuove ed esistenti, distribuite nella sottoscrizione.

L'agente abilita l'evento di creazione di processi 4688 e il campo *CommandLine* all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza. Per informazioni sui dettagli registrati per ogni nuovo processo, vedere i [campi descrizione in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L'agente inoltre raccoglie gli eventi 4688 creati nella macchina virtuale e li archivia nella ricerca.

Quando il Centro sicurezza rileva attività sospette nella macchina virtuale, il cliente riceve una notifica tramite posta elettronica se sono state fornite le [informazioni sul contatto di sicurezza](security-center-provide-security-contact-details.md). Viene visualizzato un avviso anche nel dashboard degli avvisi di sicurezza del Centro sicurezza.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>L'agente di monitoraggio compromettere le prestazioni dei server?
L'agente e usa una quantità nominale delle risorse di sistema e dovrebbe avere un impatto minimo sulle prestazioni. Per altre informazioni sull'agente, sull'estensione e sull'impatto sulle prestazioni, vedere la [guida alla pianificazione e alla gestione](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Dove vengono archiviati i dati?
I dati raccolti dall'agente vengono archiviati in un'area di lavoro di Log Analytics esistente associata alla sottoscrizione o in una nuova area di lavoro. Per altre informazioni, vedere [Sicurezza dei dati](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Utilizzo del Centro sicurezza di Azure
### <a name="what-is-a-security-policy"></a>Cosa sono i criteri di sicurezza?
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza di Azure è possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ciascuna sottoscrizione.

I criteri di sicurezza abilitati nel Centro sicurezza di Azure determinano il monitoraggio e i suggerimenti per la sicurezza. Per ulteriori informazioni sui criteri di sicurezza, vedere [Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Chi può modificare i criteri di sicurezza?
Per modificare i criteri di sicurezza, è necessario essere Amministratore della protezione, proprietario o collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Che cos'è un suggerimento per la sicurezza?
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando vengono identificate potenziali vulnerabilità di sicurezza, vengono creati i suggerimenti. Tali suggerimenti illustrano in dettaglio il processo di configurazione dei controlli necessari. Alcuni esempi:

* Provisioning di antimalware per identificare e rimuovere il software dannoso
* Configurazione dei [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) e delle regole per controllare il traffico verso le macchine virtuali
* Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
* Distribuzione degli aggiornamenti di sistema mancanti
* Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Qui vengono visualizzati solo i suggerimenti abilitati in Criteri di sicurezza.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Come è possibile visualizzare lo stato di sicurezza corrente delle risorse Azure?
Il pannello **Security Center Overview** (Panoramica del Centro sicurezza) mostra la situazione generale relativamente alla sicurezza dell'ambiente ripartito per calcolo, rete, archiviazioni e dati e applicazioni. Ciascun tipo di risorsa presenta un indicatore che visualizza l'eventuale rilevamento di potenziali vulnerabilità di sicurezza. Facendo clic su ogni sezione viene visualizzato un elenco di problemi di sicurezza identificati dal Centro sicurezza, insieme a un inventario delle risorse nella sottoscrizione.

### <a name="what-triggers-a-security-alert"></a>Che cosa attiva un avviso di sicurezza?
Il Centro sicurezza di Azure raccoglie, analizza e unisce automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

* Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti
* Malware avanzato rilevato mediante i report degli errori di Windows
* Attacchi di forza bruta contro le macchine virtuali
* Avvisi di sicurezza da soluzioni di sicurezza integrata dei partner, ad esempio antimalware o Web application firewall

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual è la differenza tra le minacce rilevate e le minacce segnalate da Microsoft Security Response Center e dal Centro sicurezza di Azure?
Microsoft Security Response Center (MSRC) esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi. Se MSRC rileva che un'entità illegale o non autorizzata ha ottenuto l'accesso a dati del cliente o che l'uso di Azure da parte del cliente non è conforme ai criteri d'uso, un responsabile della sicurezza segnala il problema al cliente. Come notifica viene in genere inviato un messaggio di posta elettronica ai contatti per la sicurezza specificati nel Centro sicurezza di Azure oppure al proprietario della sottoscrizione di Azure se non è specificato nessun contatto per la sicurezza.

Il Centro sicurezza PC è un servizio di Azure che esegue il monitoraggio continuo dell'ambiente Azure del cliente e applica metodi di analisi per rilevare un'ampia gamma di attività potenzialmente dannose. I rilevamenti vengono visualizzati come di avvisi di sicurezza nel dashboard del Centro sicurezza PC.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quali risorse di Azure vengono monitorate dal Centro sicurezza di Azure?
Il Centro sicurezza di Azure monitora le risorse di Azure seguenti:

* Macchine virtuali (VM) (inclusi i [Servizi cloud](../cloud-services/cloud-services-choose-me.md))
* Reti virtuali di Azure
* Servizio di SQL Azure
* Account di archiviazione di Azure
* App Web di Azure in un [ambiente del servizio app](../app-service/environment/intro.md)
* Soluzioni partner integrate con la sottoscrizione di Azure, ad esempio un Web application firewall, nelle VM e nell'ambiente del servizio app

## <a name="virtual-machines"></a>Macchine virtuali
### <a name="what-types-of-virtual-machines-are-supported"></a>Quali tipi di macchine virtuali sono supportati?
Il monitoraggio e le indicazioni sono disponibili per le macchine virtuali create usando i [modelli di distribuzione classica e Resource Manager](../azure-classic-rm.md).

Per l'elenco delle piattaforme supportate vedere [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Perché il Centro sicurezza di Azure non riconosce la soluzione antimalware in esecuzione nella VM Azure?
Il Centro sicurezza di Azure dispone di visibilità sull'antimalware installato tramite le estensioni di Azure. Ad esempio, il Centro sicurezza non è in grado di rilevare l'antimalware che è stato preinstallato in un'immagine fornita dall'utente o è stato installato nelle macchine virtuali mediante processi dell'utente (ad esempio sistemi di gestione della configurazione).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Perché viene visualizzato il messaggio "Dati di analisi mancanti" per la VM?
Questo messaggio viene visualizzato quando non sono presenti dati di analisi per una macchina virtuale. Dopo l'abilitazione della raccolta dei dati nel Centro sicurezza di Azure, l'analisi dei dati da popolare può richiedere tempo, in genere meno di un'ora. Dopo il popolamento iniziale dei dati di analisi, è possibile che si riceva questo messaggio perché non è presente alcun dato di analisi o non sono presenti dati di analisi recenti. Le analisi non saranno popolate per le macchine virtuali con stato arrestato. Questo messaggio potrebbe essere visualizzato anche se i dati di analisi non sono stati inseriti di recente, in conformità ai criteri di conservazione per l'agente Windows, che ha un valore predefinito di 30 giorni.

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Con quale frequenza il Centro sicurezza esegue l'analisi alla ricerca di vulnerabilità del sistema operativo, aggiornamenti del sistema e problemi di protezione degli endpoint?
La latenza con cui il Centro sicurezza esegue l'analisi alla ricerca di vulnerabilità, aggiornamenti e problemi è riportata di seguito:

- Configurazioni di sicurezza del sistema operativo: i dati vengono aggiornati entro 48 ore
- Aggiornamenti di sistema: i dati vengono aggiornati entro 24 ore
- Problemi di protezione degli endpoint: i dati vengono aggiornati entro 8 ore

In genere il Centro sicurezza esegue l'analisi alla ricerca di nuovi dati ogni ora. I valori di latenza sopra riportati fanno riferimento a uno scenario pessimistico, ovvero nel caso in cui non sia disponibile un'analisi recente o l'analisi abbia avuto esito negativo.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Perché viene visualizzato il messaggio "L'agente di macchine virtuali non è presente?"
Per abilitare la raccolta dei dati, l'agente di macchine virtuali deve essere installato nelle VM. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. Per altre informazioni su come installare l'agente di maccine virtuali in altre VM, vedere il post di blog [Estensioni e agente di macchine virtuali](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
