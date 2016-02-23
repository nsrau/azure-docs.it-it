<properties
   pageTitle="Domande frequenti sul Centro sicurezza di Azure | Microsoft Azure"
   description="Queste FAQ rispondono alle domande sul Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Domande frequenti sul Centro sicurezza di Azure

Questo articolo risponde alle domande frequenti sul Centro sicurezza di Azure.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Domande generali

### Che cos'è il Centro sicurezza?
 Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

### In che modo è possibile accedere al Centro sicurezza?
 Il Centro sicurezza viene abilitato con la sottoscrizione di Microsoft Azure ed è accessibile dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/). [Accedere al portale](https://portal.azure.com), selezionare **Sfoglia** e scorrere fino a **Centro sicurezza**. È possibile visualizzare subito alcuni suggerimenti per la sicurezza nel dashboard. Ciò avviene poiché il servizio consente di valutare lo stato di sicurezza di alcuni controlli in base alla relativa configurazione in Azure. Per accedere al set completo di funzionalità di monitoraggio di sicurezza, suggerimenti e avvisi, è necessario [abilitare la raccolta dei dati](#data-collection).

## Fatturazione

### Come funziona la fatturazione per il Centro sicurezza?
Per informazioni, vedere la pagina relativa ai [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

## Raccolta dei dati

### Come è possibile abilitare la raccolta dei dati?<a name=data-collection></a>
È possibile abilitare la raccolta dei dati per le sottoscrizioni di Azure nei criteri di sicurezza. Per abilitare la raccolta dei dati, [accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, **Centro sicurezza** e quindi **Criteri di sicurezza**. Impostare **Raccolta dati** su **Sì** e configurare gli account di archiviazione in cui raccogliere i dati. Vedere in proposito la domanda "[Dove vengono archiviati i dati?](#where-is-my-data-stored)". Quando viene abilitata l'opzione **Raccolta dei dati**, le informazioni sulla configurazione e sugli eventi di sicurezza vengono raccolte automaticamente da tutte le macchine virtuali supportate nella sottoscrizione.

### Cosa accade quando si abilita la raccolta dei dati?
La raccolta dei dati viene abilitata tramite l'agente di monitoraggio di Azure e l'estensione per il monitoraggio della sicurezza di Azure. L'estensione per il monitoraggio di sicurezza di Azure esegue l'analisi di varie configurazioni di sicurezza pertinenti e le registra come eventi nelle tracce di [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW).

Il sistema operativo genera eventi del registro eventi in tutte le attività. L'agente di monitoraggio della sicurezza di Azure legge le voci del registro eventi e le tracce ETW e quindi le copia nell'account di archiviazione per l'analisi. Si tratta dell'account di archiviazione configurato nei criteri di sicurezza. Per altre informazioni sull'account di archiviazione, vedere la domanda "[Dove vengono archiviati i dati?](#where-is-my-data-stored)"

### L'agente di monitoraggio o l'estensione per il monitoraggio di sicurezza influiscono sulle prestazioni dei server?
L'agente e l'estensione utilizzano una quantità nominale delle risorse di sistema e dovrebbero avere un impatto minimo sulle prestazioni.

### In che modo è possibile eseguire il rollback se non si intende più abilitare la raccolta dei dati?
La raccolta dei dati per una sottoscrizione può essere disabilitata nei criteri di sicurezza. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, **Centro sicurezza** e quindi **Criteri di sicurezza**. Quando si fa clic su una sottoscrizione, si apre un nuovo pannello in cui è possibile disabilitare la raccolta dei dati. Selezionare l'opzione **Elimina agenti** nella barra multifunzione superiore per rimuovere gli agenti dalle macchine virtuali esistenti.

### Dove vengono archiviati i dati?<a name=where-is-my-data-stored></a>
Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Ciò semplifica la conservazione dei dati nella stessa area geografica per scopi di sovranità dei dati e di privacy.

L'account di archiviazione per una sottoscrizione può essere selezionato nei criteri di sicurezza. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, **Centro sicurezza** e quindi **Criteri di sicurezza**. Quando si fa clic su una sottoscrizione, viene aperto un nuovo pannello. Fare clic su **Seleziona account di archiviazione** per selezionare un'area. I dati raccolti vengono isolati logicamente da quelli di altri clienti per motivi di sicurezza.

Per altre informazioni su Archiviazione di Azure e sugli account di archiviazione, vedere la pagina relativa alla [documentazione di Archiviazione](https://azure.microsoft.com/documentation/services/storage/) e [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

## Uso del Centro sicurezza

### Cosa sono i criteri di sicurezza?
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza è possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza della società, al tipo di applicazioni usate e al livello di riservatezza dei dati in ogni sottoscrizione.

Ad esempio, le risorse usate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle applicazioni di produzione. In modo analogo, le applicazioni con dati regolamentati come le informazioni personali possono richiedere un maggiore livello di sicurezza. I criteri di sicurezza abilitati nel Centro sicurezza determinano il monitoraggio e i suggerimenti per la sicurezza. Per ulteriori informazioni sui criteri di sicurezza, vedere [Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).

### Chi può modificare i criteri di sicurezza?
I criteri di sicurezza vengono configurati per ciascuna sottoscrizione. Per modificare i criteri di sicurezza, è necessario essere il proprietario o un collaboratore della relativa sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).

### Che cos'è un suggerimento per la sicurezza?
 Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Tali suggerimenti illustrano in dettaglio il processo di configurazione dei controlli necessari. Alcuni esempi:

- Provisioning di programmi antimalware per identificare e rimuovere il software dannoso
- Configurazione dei [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) e delle regole per controllare il traffico verso le macchine virtuali
- Provisioning di Web application firewall per la difesa da attacchi diretti alle applicazioni Web
- Distribuzione degli aggiornamenti di sistema mancanti
- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Qui vengono visualizzati solo i suggerimenti abilitati nei criteri di sicurezza.

### Come è possibile visualizzare lo stato di sicurezza corrente delle risorse Azure?
Il riquadro **Integrità delle risorse** nel pannello **Centro sicurezza** visualizza il comportamento di sicurezza complessivo dell'ambiente suddiviso per macchine virtuali, applicazioni Web e altre risorse. Ogni risorsa presenta un indicatore che mostra se sono state rilevate potenziali vulnerabilità di sicurezza. Facendo clic sul riquadro **Integrità delle risorse**, è possibile visualizzare le risorse e identificare le aree che richiedono attenzione o potrebbero presentare problemi.

### Che cosa attiva un avviso di sicurezza?
 Il Centro sicurezza raccoglie, analizza e combina automaticamente i dati di log provenienti dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall. Quando il Centro sicurezza rileva minacce, viene generato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

- Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti.
- Malware avanzato rilevato tramite Segnalazione errori Windows.
- Attacchi di forza bruta contro le macchine virtuali.
- Avvisi di sicurezza da soluzioni di sicurezza integrate dei partner, ad esempio programmi antimalware o Web application firewall.

### Come avviene la gestione delle autorizzazioni nel Centro sicurezza?
 Il Centro sicurezza supporta l'accesso in base al ruolo. Per altre informazioni sul controllo degli accessi in base al ruolo in Azure, vedere [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

Quando si apre il Centro sicurezza, vengono visualizzati unicamente suggerimenti e avvisi correlati alle risorse a cui si ha accesso. Ciò significa che gli utenti visualizzeranno solo le voci relative alle risorse di cui rivestono il ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse a cui appartengono tali risorse.

Per modificare i criteri di sicurezza, è necessario essere il proprietario o un collaboratore della relativa sottoscrizione.

### Quali tipi di macchine virtuali sono supportati?
Sono supportate le macchine virtuali [classiche e di Gestione risorse](../azure-classic-rm.md), comprese quelle che fanno parte dei cluster di Azure Service Fabric.

I suggerimenti relativi all'elenco di controllo di accesso sono attualmente validi per le macchine virtuali classiche. Le regole del gruppo di sicurezza di rete e i suggerimenti per l'installazione di Web application firewall sono attualmente validi solo per le macchine virtuali di Gestione risorse.

### Sono supportate le macchine virtuali Linux?
Il Centro sicurezza di Azure offre il monitoraggio della baseline per le macchine virtuali Linux (solo per le versioni 12.04, 14.04, 14.10 e 15.04 di Ubuntu). In futuro, saranno disponibili ulteriori funzioni di monitoraggio dello stato di sicurezza e per la raccolta/analisi dei dati, nonché il supporto di altre distribuzioni Linux.

<!---HONumber=AcomDC_0218_2016-->