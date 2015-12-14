<properties
   pageTitle="Domande frequenti (FAQ) sul Centro sicurezza di Azure | Microsoft Azure"
   description="Queste FAQ rispondono alle domande sul Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Domande frequenti (FAQ) sul Centro sicurezza di Azure

Queste FAQ rispondono alle domande sul Centro sicurezza di Azure, un servizio che consente di prevenire, rilevare e rispondere alle minacce con maggiore visibilità e controllo sulla sicurezza delle risorse di Microsoft Azure.

> [AZURE.NOTE]Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Domande generali

### Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza di Azure aiuta a prevenire, rilevare e rispondere alle minacce con maggiore visibilità e controllo sulla sicurezza delle risorse di Microsoft Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

### In che modo è possibile accedere al Centro sicurezza di Azure?
Il Centro sicurezza di Azure viene abilitato con la sottoscrizione di Microsoft Azure ed è accessibile dal [portale di anteprima di Microsoft Azure](http://azure.microsoft.com/features/azure-portal/). ([Accedere al portale di anteprima di Azure](https://ms.portal.azure.com/), selezionare **Sfoglia** e scorrere fino a **Centro sicurezza**). È possibile visualizzare subito alcuni suggerimenti per la sicurezza nel dashboard. Ciò avviene poiché il servizio consente di valutare lo stato di sicurezza di alcuni controlli in base alla relativa configurazione in Azure. Per attivare il set completo delle funzionalità di monitoraggio di sicurezza, dei suggerimenti e degli avvisi, è necessario [abilitare la raccolta dati](#data-collection).

## Fatturazione

### Come funziona la fatturazione per il Centro sicurezza di Azure?
Per informazioni, vedere [Prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/).

## Raccolta dei dati

### Come è possibile abilitare la raccolta dei dati?<a name=data-collection></a>
È possibile abilitare la raccolta dei dati per le sottoscrizioni di Azure in Criteri di sicurezza. Per abilitare la raccolta dei dati, [accedere al portale di anteprima di Azure](https://ms.portal.azure.com/), selezionare **Sfoglia**, selezionare **Centro sicurezza** e selezionare **Criteri di sicurezza**. Impostare **Raccolta dei dati** su **Attiva** e configurare gli account di archiviazione in cui raccogliere i dati (vedere la domanda "[Dove vengono archiviati i dati?](#where-is-my-data-stored)"). Quando viene abilitata l'opzione **Raccolta dei dati**, le informazioni sulla configurazione e sugli eventi di sicurezza vengono raccolte automaticamente da tutte le macchine virtuali supportate nella sottoscrizione.

### Cosa accade quando si abilita la raccolta dei dati?
La raccolta dei dati viene abilitata tramite l'agente di monitoraggio di Azure e l'estensione per il monitoraggio della sicurezza di Azure. L'estensione per il monitoraggio della sicurezza di Azure esegue l'analisi delle varie configurazioni pertinenti per la sicurezza e ne genera gli eventi nelle tracce di [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Inoltre, il sistema operativo genera eventi del relativo registro in qualsiasi attività. L'agente di monitoraggio di Azure legge le voci del registro eventi ed ETW le traccia e le copia nell'account di archiviazione per l'analisi. Si tratta dell'account di archiviazione configurato in Criteri di sicurezza. Per ulteriori informazioni sull'account di archiviazione, vedere la domanda "[Dove vengono archiviati i dati?](#where-is-my-data-stored)"

### L'agente di monitoraggio o l'estensione di monitoraggio di sicurezza influiscono sulle prestazioni dei server?
L'agente e l'estensione utilizzano una quantità nominale delle risorse di sistema e dovrebbero avere un impatto minimo sulle prestazioni.

### In che modo è possibile eseguire il rollback se non si intende più abilitare la raccolta dei dati?
È possibile disabilitare la **raccolta dei dati** per una sottoscrizione in Criteri di sicurezza. ([Accedere al portale di anteprima di Azure](https://ms.portal.azure.com/), selezionare **Sfoglia**, selezionare **Centro sicurezza** e selezionare **Criteri di sicurezza**). Quando si fa clic su una sottoscrizione, si apre un nuovo pannello consente di disattivare la raccolta dei dati. Selezionare l'opzione **Elimina agenti** nella barra multifunzione superiore per rimuovere gli agenti dalle macchine virtuali esistenti.

### Dove vengono archiviati i dati?<a name=where-is-my-data-stored></a>
Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Ciò semplifica la conservazione dei dati nella stessa area geografica per scopi di sovranità dei dati e di privacy. In Criteri di sicurezza, selezionare l'account di archiviazione per una sottoscrizione. ([Accedere al portale di anteprima di Azure](https://ms.portal.azure.com/), selezionare **Sfoglia**, selezionare **Centro sicurezza** e selezionare **Criteri di sicurezza**). Quando si fa clic su una sottoscrizione, viene aperto un nuovo pannello. Fare clic su **Seleziona account di archiviazione** per selezionare un'area. I dati raccolti vengono isolati logicamente da quelli di altri clienti per motivi di sicurezza.

Per ulteriori informazioni sull'archiviazione di Azure e i relativi account di archiviazione, vedere [Documentazione di archiviazione](https://azure.microsoft.com/documentation/services/storage/) e [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md).

## Utilizzo del Centro sicurezza di Azure

### Cosa sono i criteri di sicurezza?
I criteri di sicurezza definiscono il set di controlli consigliato per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza di Azure, è possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ciascuna sottoscrizione.

Ad esempio, le risorse utilizzate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle applicazioni di produzione. In modo analogo, le applicazioni con dati regolamentati come le informazioni personali possono richiedere un maggiore livello di sicurezza. I criteri di sicurezza abilitati nel Centro sicurezza di Azure determineranno il monitoraggio e i suggerimenti per la sicurezza. Per ulteriori informazioni sui criteri di sicurezza, vedere [Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).

### Chi può modificare i criteri di sicurezza?
I criteri di sicurezza vengono configurati per ciascuna sottoscrizione. Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).

### Che cos'è un suggerimento per la sicurezza?
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando vengono identificate potenziali vulnerabilità di sicurezza, vengono creati i suggerimenti. Tali suggerimenti illustrano in dettaglio il processo di configurazione dei controlli necessari. Alcuni esempi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso
- Configurazione dei [gruppi di sicurezza di rete](virtual-networks-nsg.md) e delle regole per controllare il traffico verso le macchine virtuali
- Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
- Distribuzione degli aggiornamenti di sistema mancanti
- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Qui vengono visualizzati solo i suggerimenti abilitati in Criteri di sicurezza.

### Come è possibile visualizzare lo stato di sicurezza corrente delle risorse Azure?
Il riquadro **Integrità delle risorse** nel pannello **Centro sicurezza** visualizza il comportamento di sicurezza complessivo dell'ambiente per macchine virtuali, applicazioni Web e altre risorse. Ciascuna risorsa presenta un indicatore che visualizza l'eventuale rilevamento di potenziali vulnerabilità di sicurezza. Facendo clic sul riquadro Integrità delle risorse, è possibile visualizzare le risorse e individuare le aree che richiedono interventi o in cui potrebbero essere presenti problemi.

### Che cosa attiva un avviso di sicurezza?
Il Centro sicurezza di Azure raccoglie, analizza e unisce automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

- Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti
- Malware avanzato rilevato mediante i report degli errori di Windows
- Attacchi di forza bruta contro le macchine virtuali
- Avvisi di sicurezza da soluzioni di sicurezza integrata dei partner, ad esempio antimalware o Web application firewall

### Come avviene la gestione delle autorizzazioni nel Centro sicurezza di Azure?
Il Centro sicurezza di Azure supporta l'accesso in base al ruolo. Per ulteriori informazioni sul controllo di accesso in base al ruolo (RBAC) in Azure, vedere [Controllo di accesso in base al ruolo di Azure Active Directory](role-based-access-control-configure.md).

Quando un utente apre il Centro sicurezza di Azure, verranno visualizzati solo i suggerimenti e gli avvisi che riguardano le risorse a cui l'utente può accedere. Di conseguenza, gli utenti visualizzeranno solo le voci relative alle risorse di cui rivestono il ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse a cui appartengono tali risorse.

Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore della sottoscrizione.

### Quali tipi di macchine virtuali saranno supportati?
Sono supportate le macchine virtuali [classiche e di Gestione risorse](azure-classic-rm.md), comprese quelle che fanno parte dei cluster Service Fabric di Azure.

I suggerimenti sull'elenco di controllo di accesso sono attualmente validi per le macchine virtuali classiche. I gruppi di sicurezza di rete e i suggerimenti per l'installazione di Web Application Firewall sono attualmente validi solo per le macchine virtuali di Gestione risorse.

### Sono supportate le macchine virtuali Linux?
Il Centro sicurezza di Azure offre il monitoraggio della baseline per le macchine virtuali Linux (solo Ubuntu). In futuro, saranno disponibili ulteriori funzioni di monitoraggio dello stato di sicurezza e per la raccolta/analisi dei dati, nonché il supporto di altre distribuzioni Linux.

<!---HONumber=AcomDC_1203_2015-->