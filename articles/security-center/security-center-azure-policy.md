---
title: I criteri di sicurezza del Centro sicurezza di Azure possono essere impostati singolarmente o come parte dei criteri di Azure | Microsoft Docs
description: Questo documento descrive come impostare i criteri nel Centro sicurezza di Azure o nei Criteri di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: 0b38c6895421b43d6f80e0c34cc23b379a673559
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261945"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Impostazione dei criteri di sicurezza nel Centro sicurezza o nei Criteri di Azure

Questo articolo illustra come configurare i criteri di sicurezza nel Centro sicurezza di Azure. I criteri del Centro sicurezza di Azure si integrano con i criteri di Azure, in modo da poterli impostare nel Centro sicurezza in una sottoscrizione specifica o nei [Criteri di Azure](../azure-policy/azure-policy-introduction.md), che consentono di impostare criteri per gruppi di gestione e in più sottoscrizioni.

## <a name="what-are-security-policies"></a>Informazioni sui criteri di sicurezza
Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. Nel Centro sicurezza di Azure è possibile definire criteri per le sottoscrizioni di Azure e adattarli al tipo di carico di lavoro o alla riservatezza dei dati. Ad esempio, le applicazioni che usano dati regolamentati come le informazioni personali possono richiedere un livello di sicurezza maggiore di quello degli altri carichi di lavoro. Per impostare un criterio nelle sottoscrizioni o nei gruppi di gestione, impostarle nei [Criteri di Azure](../azure-policy/azure-policy-introduction.md).

> [!NOTE]
> Se sono stati configurati in precedenza i criteri di sicurezza su una sottoscrizione che fa parte di un gruppo di gestione o ha più assegnazioni di criteri, questi criteri vengono visualizzati in grigio nel Centro sicurezza, in modo da poter gestire i criteri a livello di gruppo di gestione tramite la pagina Criteri di Azure. 

## <a name="how-security-policies-work"></a>Funzionamento dei criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare i criteri nel Centro sicurezza o usare Criteri di Azure per eseguire le operazioni seguenti:
- Creare nuove definizioni dei criteri.
- Assegnare i criteri ai gruppi di gestione e alle sottoscrizioni, che possono rappresentare un'intera organizzazione o una business unit all'interno dell'organizzazione.
- Monitorare la conformità ai criteri.

Per altre informazioni su Criteri di Azure, vedere [Creare e gestire i criteri per applicare la conformità](../azure-policy/create-manage-policy.md).

Un criterio di Azure è costituito dai componenti seguenti:

- Un **criterio** è una regola
- Un'**iniziativa** è una raccolta di criteri
- Un'**assegnazione** è un'applicazione di un'iniziativa o di un criterio a un ambito specifico (gruppo di gestione, sottoscrizione o gruppo di risorse)

Una risorsa viene valutata in base ai criteri ad essa assegnati e riceve un rapporto di conformità in base al numero di criteri a cui la risorsa è conforme.

## <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza
Il Centro sicurezza usa il controllo degli accessi in base al ruolo, che fornisce ruoli predefiniti assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- Ruolo con autorizzazioni di lettura per la sicurezza: l'utente con questo ruolo dispone dei diritti di visualizzazione per il Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non può apportare modifiche.
- Amministratore della sicurezza: l'utente con questo ruolo dispone degli stessi diritti di visualizzazione del ruolo con autorizzazioni di lettura per la sicurezza, ma può anche aggiornare i criteri di sicurezza e ignorare raccomandazioni e avvisi.

## <a name="edit-security-policies"></a>Modificare i criteri di sicurezza
È possibile modificare i criteri di sicurezza predefiniti per ogni sottoscrizione e gruppo di gestione di Azure nel Centro sicurezza. Per modificare i criteri di sicurezza, è necessario essere un proprietario, un collaboratore o un amministratore della sicurezza della sottoscrizione o del gruppo di gestione che la include. Per visualizzare i criteri di sicurezza nel Centro sicurezza:

> [!NOTE]
> Eventuali criteri impostati su una sottoscrizione che fa parte di un gruppo di gestione o ha più assegnazioni di criteri, verranno visualizzati come disattivati nel Centro sicurezza. È possibile modificare questi criteri in [Criteri di Azure](../azure-policy/azure-policy-introduction.md). 

1. Nel dashboard **Centro sicurezza** in **CRITERI E CONFORMITÀ** selezionare **Criteri di sicurezza**. Verrà visualizzato **Gestione dei criteri**.

    ![Riquadro Gestione dei criteri](./media/security-center-azure-policy/security-center-policies-fig10.png)

  Gestione dei criteri permette di visualizzare il numero dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro, oltre alla struttura dei gruppi di gestione.

  > [!NOTE]
  > Il dashboard Centro sicurezza potrebbe mostrare in **Copertura della sottoscrizione** un numero di sottoscrizioni maggiore rispetto a quello indicato in **Gestione dei criteri**. In Copertura della sottoscrizione è riportato il numero di sottoscrizioni di tipo Standard, Gratuito e “senza copertura”. Le sottoscrizioni “senza copertura” non hanno abilitato il Centro sicurezza e non vengono visualizzate in **Gestione dei criteri**.
  >
  >

  Le colonne nella tabella mostrano:

 - Assegnazione dell'iniziativa relativa ai criteri: iniziative e criteri predefiniti del Centro sicurezza assegnati a una sottoscrizione o a un gruppo di gestione.
 - Conformità: punteggio di conformità complessiva per un gruppo di gestione, una sottoscrizione o un'area di lavoro. Il punteggio è la media ponderata delle assegnazioni. La media ponderata si scompone nel numero di criteri in una singola assegnazione e nel numero di risorse a cui si applica l'assegnazione.

 Ad esempio, se la sottoscrizione dispone di due macchine virtuali e un'iniziativa con cinque criteri assegnati, sono presenti 10 valutazioni nella sottoscrizione. Se una delle macchine virtuali non è conforme a due dei criteri, il punteggio di conformità complessiva dell'assegnazione della sottoscrizione è pari all'80%.

 - Copertura: identifica il piano tariffario, Gratuito o Standard, in cui viene eseguito il gruppo di gestione, la sottoscrizione o l'area di lavoro.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
 - Impostazioni: per le sottoscrizioni è disponibile il collegamento **Modifica impostazioni**. Selezionando **Modifica impostazioni**, è possibile aggiornare le impostazioni della sottoscrizione, ad esempio raccolta dati, piano tariffario e notifiche di posta elettronica.

2. Selezionare la sottoscrizione o il gruppo di gestione per cui abilitare un criterio di sicurezza. Viene visualizzata la finestra **Criteri di sicurezza**.

3.  In **Criteri di sicurezza** selezionare i controlli per cui si vuole che il Centro sicurezza esegua il monitoraggio e fornisca raccomandazioni selezionando **Sì**.  Selezionare **No** se non si vuole che il Centro sicurezza esegua il monitoraggio di tale controllo.

    ![Componenti dei criteri](./media/security-center-azure-policy/security-policy.png)

4. Selezionare **Salva**.

## <a name="available-security-policy-definitions"></a>Definizioni di criteri di sicurezza disponibili

Per informazioni sulle definizioni dei criteri disponibili nel criterio di sicurezza predefinito, vedere la tabella seguente:

| Criterio | Operazione eseguita dal criterio abilitato |
| --- | --- |
| Aggiornamenti del sistema |Recupera un elenco giornaliero degli aggiornamenti della sicurezza e critici da Windows Update o da Windows Server Update Services. L'elenco recuperato dipende dal servizio configurato per le macchine virtuali e consiglia di applicare gli aggiornamenti mancanti. Per i sistemi Linux, il criterio usa il sistema di gestione pacchetti fornito dalla distribuzione per determinare per quali pacchetti sono disponibili aggiornamenti. Controlla anche la presenza di aggiornamenti critici e della sicurezza dalle macchine virtuali di [Servizi cloud di Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurazioni di sicurezza |Analizza giornalmente le configurazioni del sistema operativo per determinare i problemi che potrebbero rendere vulnerabile agli attacchi la macchina virtuale. Il criterio consiglia anche le modifiche alla configurazione necessarie per risolvere queste vulnerabilità. Per altre informazioni sulle configurazioni specifiche sottoposte a monitoraggio, vedere l'[elenco di baseline consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Al momento, Windows Server 2016 non è completamente supportato.) |
| Endpoint Protection |Consiglia di configurare Endpoint Protection per tutte le macchine virtuali di Windows, per identificare e rimuovere virus, spyware e altro software dannoso. |
| Crittografia del disco |Suggerisce di abilitare la crittografia dischi in tutte le macchine virtuali per migliorare la protezione dei dati inattivi. |
| Gruppi di sicurezza di rete |Consiglia di configurare [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per controllare il traffico in ingresso e in uscita nelle VM che hanno endpoint pubblici. I gruppi di sicurezza di rete configurati per una subnet vengono ereditati da tutte le interfacce di rete della macchina virtuale, se non diversamente specificato. Oltre a controllare che sia stato configurato un gruppo di sicurezza di rete, questo criterio valuta le regole di sicurezza in ingresso per identificare le regole che consentono il traffico in ingresso. |
| Web application firewall |Consiglia di configurare un Web application firewall nelle macchine virtuali quando una delle due condizioni seguenti è vera: <ul><li>Viene usato un [IP pubblico a livello di istanza](../virtual-network/virtual-networks-instance-level-public-ip.md) e le regole di sicurezza in ingresso per il gruppo di sicurezza di rete associato vengono configurate in modo da consentire l'accesso alla porta 80/443.</li><li>Viene usato un IP con carico bilanciato e le regole NAT (Network Address Translation) in ingresso e di bilanciamento del carico associate sono configurate per consentire l'accesso alla porta 80/443. Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](../load-balancer/load-balancer-arm.md).</li> |
| Firewall di nuova generazione |Estende le protezioni di rete oltre i gruppi di sicurezza di rete predefiniti di Azure. Il Centro sicurezza individua le distribuzioni per cui è consigliabile un firewall di nuova generazione ed è quindi possibile configurare un'appliance virtuale. |
| Controllo e rilevamento delle minacce SQL |Consiglia l'abilitazione del controllo dell'accesso al database di Azure per la conformità e il rilevamento avanzato delle minacce, per scopi di analisi. |
| Crittografia SQL |Consiglia l'abilitazione della crittografia dati inattivi per il database SQL di Azure, i backup associati e file di log delle transazioni. Anche se i dati vengono violati, non sono leggibili. |
| Valutazione della vulnerabilità |Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| Crittografia di archiviazione |Questa funzionalità è attualmente disponibile per archivi BLOB di Azure e File di Azure. Dopo l'abilitazione della Crittografia del servizio di archiviazione vengono crittografati solo i nuovi dati, mentre i file già esistenti nell'account di archiviazione rimangono non crittografati. |
| Accesso alla rete JIT |Quando è abilitato l'accesso alla rete Just-in-Time, il Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola del gruppo di sicurezza di rete. Selezionare le porte nella macchina virtuale per cui bloccare il traffico in ingresso. Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |

## <a name="management-groups"></a>Gruppi di gestione
Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicati i criteri di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente i criteri applicati al gruppo di gestione. A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice". Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice permette l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Per configurare i gruppi di gestione per l'uso con Centro sicurezza di Azure, seguire le istruzioni nell'articolo [Gain tenant-wide visibility for Azure Security Center](security-center-management-groups.md) (Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure). 

> [!NOTE]
> È importante comprendere la gerarchia dei gruppi di gestione e delle sottoscrizioni. Vedere [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) per altre informazioni sui gruppi di gestione, la gestione radice e l'accesso ai gruppi di gestione.
>
>


## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni sulla pianificazione e considerazioni di progettazione per il Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitorare le soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Gain tenant-wide visibility for Azure Security Center](security-center-management-groups.md) (Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure): informazioni su come configurare i gruppi di gestione per il Centro sicurezza di Azure. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

Per altre informazioni su Criteri di Azure, vedere [Informazioni su Criteri di Azure](../azure-policy/azure-policy-introduction.md).
