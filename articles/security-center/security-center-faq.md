<properties
   pageTitle="Domande frequenti sul Centro sicurezza di Azure | Microsoft Azure"
   description="Queste FAQ rispondono alle domande sul Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="terrylan"/>

# Domande frequenti sul Centro sicurezza di Azure

Queste FAQ rispondono alle domande sul Centro sicurezza di Azure, un servizio che consente di prevenire, rilevare e rispondere alle minacce con maggiore visibilità e controllo sulla sicurezza delle risorse di Microsoft Azure.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Domande generali

### Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

### In che modo è possibile accedere al Centro sicurezza di Azure?
Il Centro sicurezza di Azure viene abilitato con la sottoscrizione di Microsoft Azure ed è accessibile dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/). ([Accedere al portale](https://portal.azure.com), selezionare **Sfoglia** e scorrere fino a **Centro sicurezza**).

## Fatturazione

### Come funziona la fatturazione per il Centro sicurezza di Azure?
Per informazioni, vedere la pagina relativa ai [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

## Raccolta dei dati

Il Centro sicurezza raccoglie i dati dalle macchine virtuali per valutarne lo stato della sicurezza, fornire raccomandazioni sulla sicurezza e avvisare delle minacce. La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. La raccolta dati è consigliata, ma è possibile rifiutare esplicitamente [disattivandola](#how-do-i-disable-data-collection) nei criteri del Centro di sicurezza.

### Come si disabilita la raccolta dati?

È possibile disabilitare la **raccolta dati** per una sottoscrizione in Criteri di sicurezza in qualunque momento. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Esplora**, **Centro sicurezza** e **Criteri di sicurezza**. Quando si seleziona una sottoscrizione, si apre un nuovo pannello in cui è possibile disattivare la **raccolta dati**. Selezionare l'opzione **Elimina agenti** nella barra multifunzione superiore per rimuovere gli agenti dalle macchine virtuali esistenti.

> [AZURE.NOTE] I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma è necessario selezionare una sottoscrizione per disattivare la raccolta dati.

### Come si abilita la raccolta dati?
È possibile abilitare la raccolta dei dati per le sottoscrizioni di Azure in Criteri di sicurezza. Per abilitare la raccolta dati, [accedere al portale di Azure](https://portal.azure.com), selezionare **Esplora**, **Centro sicurezza** e quindi **Criteri di sicurezza**. Impostare **Raccolta dei dati** su **Attiva** e configurare gli account di archiviazione in cui raccogliere i dati (vedere la domanda "[Dove vengono archiviati i dati?](#where-is-my-data-stored)"). Quando viene abilitata l'opzione **Raccolta dei dati**, le informazioni sulla configurazione e sugli eventi di sicurezza vengono raccolte automaticamente da tutte le macchine virtuali supportate nella sottoscrizione.

> [AZURE.NOTE] I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma la configurazione della raccolta dei dati viene eseguita solo a livello di sottoscrizione.

### Cosa accade quando si abilita la raccolta dati?
La raccolta dei dati viene abilitata tramite l'agente di monitoraggio di Azure e l'estensione per il monitoraggio della sicurezza di Azure. L'estensione per il monitoraggio della sicurezza di Azure esegue l'analisi delle varie configurazioni relative alla sicurezza e le invia alle tracce di [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Il sistema operativo crea anche le voci del registro eventi. L'agente di monitoraggio di Azure legge le voci del registro eventi ed ETW le traccia e le copia nell'account di archiviazione per l'analisi. Si tratta dell'account di archiviazione configurato in Criteri di sicurezza. Per ulteriori informazioni sull'account di archiviazione, vedere la domanda "[Dove vengono archiviati i dati?](#where-is-my-data-stored)"

### L'agente di monitoraggio o l'estensione di monitoraggio di sicurezza influiscono sulle prestazioni dei server?
L'agente e l'estensione utilizzano una quantità nominale delle risorse di sistema e dovrebbero avere un impatto minimo sulle prestazioni.

### Dove vengono archiviati i dati?
Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Ciò semplifica la conservazione dei dati nella stessa area geografica per scopi di sovranità dei dati e di privacy. In Criteri di sicurezza, selezionare l'account di archiviazione per una sottoscrizione. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Esplora**, **Centro sicurezza** e **Criteri di sicurezza**. Quando si fa clic su una sottoscrizione, viene aperto un nuovo pannello. Selezionare **Scegliere gli account di archiviazione** per selezionare un'area.

> [AZURE.NOTE] I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma la selezione di un'area per l'account di archiviazione viene eseguita solo a livello di sottoscrizione.

Per ulteriori informazioni sull'archiviazione di Azure e i relativi account di archiviazione, vedere [Documentazione di archiviazione](https://azure.microsoft.com/documentation/services/storage/) e [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

## Utilizzo del Centro sicurezza di Azure

### Cosa sono i criteri di sicurezza?
I criteri di sicurezza definiscono il set di controlli consigliato per le risorse all'interno della sottoscrizione o di un gruppo di risorse specificato. Nel Centro sicurezza di Azure è possibile definire i criteri per i gruppi di risorse le sottoscrizioni di Azure in base ai requisiti di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ciascuna sottoscrizione.

Ad esempio, le risorse utilizzate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle applicazioni di produzione. In modo analogo, le applicazioni con dati regolamentati come le informazioni personali possono richiedere un maggiore livello di sicurezza. I criteri di sicurezza abilitati nel Centro sicurezza di Azure determineranno il monitoraggio e i suggerimenti per la sicurezza. Per ulteriori informazioni sui criteri di sicurezza, vedere [Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).

> [AZURE.NOTE] In caso di conflitto tra criteri definiti a livello di sottoscrizione e quelli a livello di gruppo di risorse, i criteri a livello di gruppo di risorse avranno la precedenza.

### Chi può modificare i criteri di sicurezza?
I criteri di sicurezza vengono configurati per ogni sottoscrizione o gruppo di risorse. Per modificare i criteri di sicurezza a livello di sottoscrizione o di gruppo di risorse, è necessario essere un proprietario o un collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).

### Che cos'è un suggerimento per la sicurezza?
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando vengono identificate potenziali vulnerabilità di sicurezza, vengono creati i suggerimenti. Tali suggerimenti illustrano in dettaglio il processo di configurazione dei controlli necessari. Alcuni esempi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso
- Configurazione dei [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) e delle regole per controllare il traffico verso le macchine virtuali
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

### Qual è la differenza tra le minacce rilevate e le minacce segnalate da Microsoft Security Response Center e dal Centro sicurezza di Azure?
Microsoft Security Response Center (MSRC) esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi. Se MSRC rileva che un'entità illegale o non autorizzata ha ottenuto l'accesso a dati del cliente o che l'uso di Azure da parte del cliente non è conforme ai criteri d'uso, un responsabile della sicurezza segnala il problema al cliente. In genere la notifica avviene con l'invio di un messaggio di posta elettronica al o ai contatti per la sicurezza specificati nel Centro sicurezza di Azure oppure al proprietario della sottoscrizione Azure se non è specificato nessun contatto per la sicurezza.

Il Centro sicurezza PC è un servizio di Azure che esegue il monitoraggio continuo dell'ambiente Azure del cliente e applica metodi di analisi per rilevare un'ampia gamma di attività potenzialmente dannose. I rilevamenti vengono visualizzati come di avvisi di sicurezza nel dashboard del Centro sicurezza PC.

### Come avviene la gestione delle autorizzazioni nel Centro sicurezza di Azure?
Il Centro sicurezza di Azure supporta l'accesso in base al ruolo. Per ulteriori informazioni sul controllo di accesso in base al ruolo (RBAC) in Azure, vedere [Controllo di accesso in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Quando un utente apre il Centro sicurezza di Azure, verranno visualizzati solo i suggerimenti e gli avvisi che riguardano le risorse a cui l'utente può accedere. Di conseguenza, gli utenti visualizzeranno solo le voci relative alle risorse di cui rivestono il ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse a cui appartengono tali risorse.

Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore della sottoscrizione.

## Macchine virtuali

### Quali tipi di macchine virtuali saranno supportati?
Il monitoraggio dello stato della sicurezza e indicazioni in merito sono disponibili per le macchine virtuali create usando i [modelli di distribuzione classica e Resource Manager](../azure-classic-rm.md), incluse le macchine virtuali che fanno parte di cluster di Azure Service Fabric.

Macchine virtuali di Windows supportate:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Macchine virtuali di Linux supportate:

- Versioni di Ubuntu 12.04, 14.04, 15.10, 16.04
- Versioni di Debian 7, 8
- Versioni di CentOS 6.*, 7.*
- Versioni di Red Hat Enterprise Linux (RHEL) 6.*, 7.*
- Versioni di SUSE Linux Enterprise Server (SLES) 11.*, 12.*

Sono supportate anche macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli Web e di lavoro dei servizi cloud in esecuzione negli slot di produzione. Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md).

<!---HONumber=AcomDC_0713_2016-->