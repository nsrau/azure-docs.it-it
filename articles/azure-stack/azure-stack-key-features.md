---
title: Chiave funzionalità e concetti di Azure Stack | Microsoft Docs
description: Informazioni sulle funzionalità e i concetti principali in Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: 16c908dabd313cd9d64ce5be9b7d0299423a7675
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344686"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Funzionalità e concetti principali in Azure Stack
Le descrizioni delle funzionalità e i termini riportati di seguito possono risultare utili a chi non si ha familiarità con Microsoft Azure Stack.

## <a name="personas"></a>Utenti tipo
Esistono due tipi di utenti per Microsoft Azure Stack, l'operatore cloud (provider) e il tenant (consumer).

* Oggetto **operatore cloud** possono configurare Azure Stack e gestire le offerte, piani, servizi, quote e prezzi per fornire le risorse per i tenant.  Gli operatori cloud anche la capacità di gestire e rispondono agli avvisi.  
* Oggetto **tenant** (detta anche un utente) utilizza i servizi che offre l'amministratore del cloud. Il tenant può effettuare il provisioning, monitorare e gestire i servizi che ha sottoscritto, ad esempio App Web, Archiviazione e Macchine virtuali.

## <a name="portal"></a>Portale
I metodi principali per l'interazione con Microsoft Azure Stack sono il portale per gli amministratori, portale per gli utenti e PowerShell.

I portali di Azure Stack sono ognuno supportati da istanze separate di Azure Resource Manager.  Un operatore cloud Usa il portale dell'amministratore per la gestione di Azure Stack e, ad esempio, creare offerte di tenant.  Il portale utenti (detto anche il portale tenant) offre un'esperienza self-service per l'utilizzo delle risorse cloud, come le macchine virtuali, gli account di archiviazione e le app Web. Per altre informazioni, vedere [tramite i portali di amministratore e utente di Azure Stack](azure-stack-manage-portals.md).

## <a name="identity"></a>Identità 
Stack di Azure Usa Azure Active Directory (AAD) o Active Directory Federation Services (ADFS) come provider di identità.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory è il provider di identità basato sul cloud e multi-tenant di Microsoft.  La maggior parte degli scenari ibridi usano Azure Active Directory come archivio identità.

### <a name="active-directory-federation-services"></a>Active Directory Federation Services
È possibile scegliere di utilizzare Active Directory Federation Services (ADFS) per le distribuzioni non connessione di Azure Stack.  Azure Stack, i provider di risorse e altre applicazioni funzionano molto simile a quello con AD FS come avviene con Azure Active Directory. Lo Stack di Azure include una propria istanza di AD FS e Active Directory e un'API Graph di Active Directory. Azure Stack Development Kit supporta i seguenti scenari di AD FS:

- Accedi per la distribuzione tramite ADFS.
- Creare una macchina virtuale con i segreti in Key Vault
- Creare un insieme di credenziali per l'archiviazione o l'accesso ai segreti
- Creare ruoli personalizzati nella sottoscrizione
- Assegnare utenti ai ruoli RBAC sulle risorse
- Creare i ruoli RBAC a livello di sistema tramite Azure PowerShell
- Accedere come un utente tramite Azure PowerShell
- Crea servizio entità usarle per accedere ad Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Le aree, servizi, piani, offerte e sottoscrizioni
In Azure Stack, i servizi vengono forniti ai tenant che usano le aree, le sottoscrizioni, offerte e piani. I tenant possono sottoscrivere più offerte. Le offerte possono includere uno o più piani, che a loro volta possono includere uno o più servizi.

![](media/azure-stack-key-features/image4.png)

Gerarchia di esempio delle offerte sottoscritte da un tenant, ognuna con piani e servizi diversi.

### <a name="regions"></a>Regioni
Aree di Azure Stack sono un elemento di base di scalabilità e gestione. Un'organizzazione può avere più aree con le risorse disponibili in ogni area. Le aree potrebbero anche essere disponibili le offerte di servizio diverso. In Azure Stack Development Kit, è supportata solo una singola area e viene automaticamente denominata *locale*.

### <a name="services"></a>Services
Microsoft Azure Stack consente ai provider di offrire una vasta gamma di servizi e applicazioni, ad esempio le macchine virtuali, SQL Server database, SharePoint, Exchange e altro ancora.

### <a name="plans"></a>Piani
I piani sono raggruppamenti di uno o più servizi. Il provider crea i piani da offrire ai tenant. A loro volta, i tenant sottoscrivono le offerte per usare i piani e i servizi inclusi.

Ogni servizio aggiunto a un piano può essere configurato con impostazioni delle quote, che permettono di gestire la capacità del cloud. Le quote possono includere restrizioni, ad esempio limiti a livello della CPU, della RAM o della macchina virtuale, che vengono applicate per ogni sottoscrizione utente. Le quote possono essere differenziate in base alla località. Ad esempio, un piano che contiene i servizi di calcolo da un'area A potrebbe avere una quota di due macchine virtuali, 4 GB di RAM e 10 core CPU.

Quando si crea un'offerta, l'amministratore del servizio può includere un **piano base**. Questi piani base sono inclusi per impostazione predefinita, quando un tenant sottoscrive l'offerta. Quando un utente si iscrive (e la creazione della sottoscrizione), l'utente può accedere a tutti i provider di risorse specificato in tali piani base (con le quote corrispondenti).

L'amministratore del servizio può includere nell'offerta anche **piani aggiuntivi** . I piani aggiuntivi non sono inclusi per impostazione predefinita nella sottoscrizione. Componente aggiuntivo piani sono piani aggiuntivi (quote) disponibili in un'offerta che è possibile aggiungere un proprietario della sottoscrizione per le sottoscrizioni.

### <a name="offers"></a>Offerte
Le offerte sono gruppi di uno o più piani che i provider propongono ai tenant per l'acquisto e la sottoscrizione. Ad esempio, offerta alfa può includere un piano che contiene un set di servizi di calcolo e il piano B che contiene un set di servizi di archiviazione e rete.

Ogni offerta include un set di piani base e gli amministratori del servizio possono creare piani aggiuntivi che i tenant possono aggiungere alla sottoscrizione.

### <a name="subscriptions"></a>Sottoscrizioni
Una sottoscrizione è come tenant acquistare le offerte. Una sottoscrizione è una combinazione di un tenant con un'offerta. Un tenant può avere più offerte sottoscritte. Ogni sottoscrizione si applica a sola offerta. Le sottoscrizioni del tenant stabilire quali piani/servizi sono accessibili.

Le sottoscrizioni consentono ai provider di organizzare e accedere alle risorse cloud e servizi.

Per l'amministratore, viene creata una sottoscrizione di Provider predefinito durante la distribuzione. Questa sottoscrizione è utilizzabile per gestire Azure Stack, distribuire ulteriormente i provider di risorse e creare i piani e offerte per i tenant. Consigliabile non usarla per eseguire le applicazioni e carichi di lavoro. A partire dalla versione 1804, due sottoscrizioni aggiuntive integrano sottoscrizione Provider predefinito. una sottoscrizione di analisi e di una sottoscrizione di consumo. Queste aggiunte semplificano separando la gestione dell'infrastruttura di base, i provider di risorse aggiuntive e i carichi di lavoro.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Usando Azure Resource Manager, per lavorare con le risorse di infrastruttura in un modello dichiarativo, basato su modello.   Fornisce un'unica interfaccia che è possibile usare per distribuire e gestire i componenti della soluzione. Per altre informazioni e istruzioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Gruppi di risorse
Gruppi di risorse sono raccolte di risorse, servizi e applicazioni, e ogni risorsa dispone di un tipo, ad esempio le macchine virtuali, reti virtuali, gli indirizzi IP pubblici, gli account di archiviazione e siti Web. Ogni risorsa deve essere in un gruppo di risorse e gruppi di risorse di ricevere assistenza in modo logico organizzare le risorse, ad esempio dal carico di lavoro o il percorso.  In Microsoft Azure Stack le risorse come i piani e le offerte vengono gestite anche nei gruppi di risorse.

A differenza [Azure](../azure-resource-manager/resource-group-move-resources.md), è possibile spostare le risorse tra gruppi di risorse. Quando si visualizzano le proprietà di una risorsa o gruppo di risorse nel portale di amministrazione di Azure Stack, il *spostare* pulsante è inattivo e non è disponibile. 
 
### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure
Con Azure Resource Manager, è possibile creare un modello (in formato JSON) che definisce la distribuzione e configurazione dell'applicazione. Questo modello è noto come modello di Gestione risorse di Azure e permette di definire la distribuzione in modo dichiarativo. Utilizzando un modello, è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente.

## <a name="resource-providers-rps"></a>Provider di risorse (RPs)
I provider di risorse sono servizi Web su cui si basano tutti i servizi PaaS e IaaS basati su Azure. Azure Resource Manager si basa su diversi provider di risorse per fornire l'accesso ai servizi.

Esistono quattro RPs fondamentali: rete, calcolo e archiviazione Key Vault. Ognuno di questi provider di risorse consente di configurare e controllare le rispettive risorse. Gli amministratori del servizio è anche possono aggiungere nuovi provider di risorse personalizzati.

### <a name="compute-rp"></a>Provider di risorse di calcolo
Il provider di risorse di calcolo consente ai tenant di Azure Stack di creare le proprie macchine virtuali. Il provider di risorse di calcolo include la possibilità di creare macchine virtuali ed estensioni della macchina virtuale. Il servizio di estensione della macchina virtuale consente di fornire funzionalità IaaS per macchine virtuali Linux e Windows.  Ad esempio, è possibile usare CRP per effettuare il provisioning di una macchina virtuale Linux ed eseguire gli script di Bash durante la distribuzione per configurare la macchina virtuale.

### <a name="network-rp"></a>Provider di risorse di rete
Il Provider di risorse rete (NRP) offre una serie di funzionalità di rete SDN (Software Defined) e rete funzione virtualizzazione (NFV) per il cloud privato.  Per creare risorse quali gruppi di sicurezza di rete personali mediante servizi di bilanciamento, gli indirizzi IP pubblici, carico di software, reti virtuali, è possibile utilizzare provider NRP.

### <a name="storage-rp"></a>Provider di risorse di archiviazione
Il provider di risorse di archiviazione offre quattro servizi di archiviazione coerenti con Azure: blob, tabella, coda e la gestione degli account. Offre anche un servizio di amministrazione del cloud di archiviazione per semplificare l'amministrazione dei servizi di archiviazione coerenti con Azure da parte del provider di servizi. Archiviazione di Azure permette di archiviare e recuperare in modo flessibile grandi quantità di dati non strutturati, ad esempio documenti e file multimediali usando i BLOB di Azure e dati strutturati basati su NoSQL usando le tabelle di Azure. Per altre informazioni su archiviazione di Azure, vedere [Introduzione ad archiviazione di Microsoft Azure](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Archiviazione BLOB
Nell'archivio BLOB sono archiviati i set di dati. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. Nell'archivio tabelle sono archiviati set di dati strutturati. L'archiviazione delle tabelle utilizza un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati. L'archiviazione di accodamento offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra i componenti dei servizi cloud.

Ogni blob è organizzato in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere un numero qualsiasi di contenitori e un contenitore può contenere qualsiasi numero di BLOB, fino al limite di 500 TB di capacità dell'account di archiviazione. Gli archivi BLOB offrono tre tipi di BLOB: i BLOB in blocchi , i BLOB di accodamento e i BLOB di pagine (dischi). I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono una soluzione adatta per l'archiviazione di documenti, file multimediali, backup e così via. I BLOB di accodamento sono simili ai BLOB in blocchi, ma ottimizzati per le operazioni di Accodamento. Un BLOB di accodamento può essere aggiornato solo aggiungendo un nuovo blocco alla fine. I BLOB di accodamento sono una buona scelta per scenari quali la registrazione, dove i nuovi dati devono essere scritti solo fino alla fine del BLOB. I BLOB di pagine sono ottimizzati per la rappresentazione di dischi IaaS e supporto casuali scrive e può estendersi fino a 1 TB. Un disco IaaS collegato a una rete di macchine virtuali Azure è un disco rigido virtuale archiviato come BLOB di pagine.

#### <a name="table-storage"></a>Archiviazione tabelle
L'archivio tabelle è l'archivio chiave-attributo NoSQL di Microsoft. Ha una struttura priva di schema che lo rende diverso dai database relazionali tradizionali. L'assenza di uno schema negli archivi dati permette di adattare facilmente i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni. Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato che può essere usato per filtrare i dati e specificare i criteri di selezione. Una raccolta di proprietà con i rispettivi valori costituisce un'entità. Dal momento che l'archivio tabelle è privo di schema, due entità nella stessa tabella possono contenere raccolte diverse di proprietà, che a loro volta possono essere di tipi diversi. È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio. In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

#### <a name="queue-storage"></a>Archiviazione code
L'archivio code di Azure fornisce la messaggistica cloud tra i componenti dell'applicazione. Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archivio code fornisce la messaggistica asincrona per la comunicazione tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Archiviazione code supporta anche la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

### <a name="keyvault"></a>Insieme di credenziali delle chiavi
RP KeyVault fornisce la gestione e il controllo dei segreti, ad esempio le password e certificati. Ad esempio, un tenant può usare RP KeyVault per specificare chiavi o le password dell'amministratore durante la distribuzione della macchina virtuale.

## <a name="high-availability-for-azure-stack"></a>Disponibilità elevata per Azure Stack
*Si applica a: Azure Stack 1802 o versioni successive*

Per ottenere disponibilità elevata di un sistema di produzione di più macchine Virtuali in Azure, macchine virtuali vengono inserite in un set di disponibilità che queste ultime vengono distribuite tra più domini di errore e domini di aggiornamento. In questo modo [macchine virtuali distribuite nei set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) sono fisicamente isolati tra loro in rack di server separato per consentire la resilienza di errore come illustrato nel diagramma seguente:

  ![Disponibilità elevata in Azure Stack](media/azure-stack-key-features/high-availability.png)

### <a name="availability-sets-in-azure-stack"></a>Set di disponibilità in Azure Stack
Mentre l'infrastruttura di Azure Stack è ancora resiliente agli errori, la tecnologia sottostante (clustering di failover) ancora comporta tempi di inattività per le macchine virtuali in un server fisico interessati se è presente un errore hardware. Azure Stack supporta un set di disponibilità con un massimo di tre domini di errore siano coerenti con Azure.

- **Domini di errore**. Le macchine virtuali inserite in un set di disponibilità è fisicamente isolate una da altra per la distribuzione di in modo più uniforme possibile in più domini di errore (nodi di Azure Stack). Se si verifica un errore hardware, le macchine virtuali dal dominio di errore vengono riavviate in altri domini di errore, ma, se possibile, mantenute in domini di errore da altre macchine virtuali nello stesso set di disponibilità. Quando l'hardware ritorna in linea, le macchine virtuali saranno ribilanciate per mantenere la disponibilità elevata. 
 
- **Domini di aggiornamento**. Domini di aggiornamento sono un altro concetto di Azure che fornisce elevata disponibilità nei set di disponibilità. Un dominio di aggiornamento è un gruppo logico di hardware sottostante che può essere sottoposto a manutenzione contemporaneamente. Macchine virtuali presenti nello stesso dominio di aggiornamento verranno riavviate contemporaneamente durante la manutenzione pianificata. Quando i tenant creano VM all'interno di un set di disponibilità, la piattaforma Azure distribuisce automaticamente le macchine virtuali in questi domini di aggiornamento. In Azure Stack le macchine virtuali sono in tempo reale la migrazione tra gli altri host nel cluster online prima dell'aggiornamento dell'host sottostante. Perché durante un aggiornamento host è presente alcun tempo di inattività di tenant, la funzionalità del dominio di aggiornamento in Azure Stack è presente solo per la compatibilità dei modelli di Azure. 

### <a name="upgrade-scenarios"></a>Scenari di aggiornamento 
Le macchine virtuali nel set di disponibilità che sono stati creati prima versione di Azure Stack 1802 viene assegnato un numero predefinito di domini di errore e aggiornamento (1 e 1 rispettivamente). Per ottenere disponibilità elevata per le macchine virtuali in questi set di disponibilità esistente, è necessario innanzitutto eliminare le macchine virtuali esistenti e quindi ridistribuirli in un nuovo set di disponibilità con i conteggi di dominio di errore e di aggiornamento corretti come descritto in [modifica il set di disponibilità per una macchina virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Per set di scalabilità di macchine virtuali, un set di disponibilità è stato creato internamente con un numero di dominio predefinito aggiornamento e di dominio di errore (3 e 5 rispettivamente). Conteggi di qualsiasi set di scalabilità di macchine virtuali creati prima dell'aggiornamento 1802 disponibilità verrà inserita in un set di disponibilità con il dominio di errore e di aggiornamento predefinito (1 e 1 rispettivamente). Per aggiornare queste istanze di set di scalabilità di macchine virtuali per ottenere la diffusione più recente, scalare orizzontalmente il set di scalabilità di macchine virtuali per il numero di istanze che erano presenti prima dell'aggiornamento 1802 e quindi eliminare le istanze meno recenti di set di scalabilità di macchine virtuali. 

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo può essere usato per concedere l'accesso al sistema a utenti autorizzati, gruppi e servizi tramite l'assegnazione di ruoli a livello di sottoscrizione, gruppo di risorse o singola risorsa. Ogni ruolo definisce il livello di accesso di un utente, un gruppo o un servizio rispetto alle risorse di Microsoft Azure Stack.

Azure RBAC dispone di tre ruoli di base che si applicano a tutti i tipi di risorse: proprietario, collaboratore e lettore. Proprietario dispone dell'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Collaboratore può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti. Lettore può solo visualizzare le risorse di Azure esistenti. Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore di macchina virtuale consente la creazione e la gestione delle macchine virtuali, ma non consente la gestione della rete virtuale o della subnet a cui la macchina virtuale si connette.

## <a name="usage-data"></a>Dati di utilizzo
Microsoft Azure Stack raccoglie e aggrega i dati di utilizzo in tutti i provider di risorse e lo trasmette in Azure per l'elaborazione dal commerce di Azure. I dati di utilizzo raccolti in Azure Stack possono essere visualizzati tramite un'API REST. Sono disponibili un'API del tenant coerente con Azure e API del provider e del provider delegato che permettono di ottenere dati di utilizzo di tutte le sottoscrizioni del tenant. Questi dati possono essere integrati con un servizio o uno strumento esterno di fatturazione o chargeback. Una volta che utilizzo è stato elaborato dal commercio Azure, possono essere visualizzato nel portale di fatturazione di Azure.

## <a name="in-development-build-of-azure-stack-development-kit"></a>Build di sviluppo di Azure Stack Development Kit
Le build di sviluppo consentono primi adottanti valutare la versione più recente di Azure Stack Development Kit. Sono infatti le compilazioni incrementali in base alla versione principale più recente. Anche se le versioni principali continueranno a essere rilasciate ogni pochi mesi, le compilazioni di sviluppo verranno rilasciato in modo intermittente tra le versioni principali.

Le build di sviluppo fornirà i vantaggi seguenti:
- Correzioni di bug
- Nuove funzionalità
- Altri miglioramenti

## <a name="next-steps"></a>Passaggi successivi
[Nozioni fondamentali sull'amministrazione](azure-stack-manage-basics.md)

