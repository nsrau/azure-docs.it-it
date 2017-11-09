---
title: "La funzionalità e concetti nello Stack di Azure | Documenti Microsoft"
description: "Informazioni sulle funzionalità e i concetti principali in Azure Stack."
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: fd16748e1369b8abcab38ce1945f72c681c344b8
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Funzionalità e concetti principali in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Le descrizioni delle funzionalità e i termini riportati di seguito possono risultare utili a chi non si ha familiarità con Microsoft Azure Stack.

## <a name="personas"></a>Utenti tipo
Esistono due tipi di utenti per Microsoft Azure Stack, l'operatore cloud (provider) e del tenant (consumer).

* Oggetto **operatore cloud** dello Stack di Azure di configurare e gestire offerte, dei piani, servizi, quote e prezzi per fornire le risorse per i tenant.  Gli operatori di cloud consente inoltre di gestire la capacità e di rispondono agli avvisi.  
* Oggetto **tenant** (detto anche un utente) utilizza i servizi che offre l'amministratore del cloud. Il tenant può effettuare il provisioning, monitorare e gestire i servizi che ha sottoscritto, ad esempio App Web, Archiviazione e Macchine virtuali.

## <a name="portal"></a>di Microsoft Azure
I metodi principali dell'interazione con Microsoft Azure Stack sono il portale dell'amministratore, un portale per gli utenti e PowerShell.

I portali di Azure Stack ognuno sono supportati da istanze separate di gestione risorse di Azure.  Un operatore di cloud utilizza il portale dell'amministratore per gestire Azure Stack e, ad esempio creare offerte di tenant.  Portale per gli utenti (detto anche il portale tenant) offre un'esperienza self-service per l'utilizzo delle risorse cloud, macchine virtuali, gli account di archiviazione e le applicazioni Web. Per ulteriori informazioni, vedere [tramite i portali di amministratore e utente di Azure Stack](azure-stack-manage-portals.md).

## <a name="identity"></a>Identità 
Stack di Azure Usa Azure Active Directory (AAD) o Active Directory Federation Services (ADFS) come provider di identità.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory è il provider di identità basato sul cloud e multi-tenant di Microsoft.  La maggior parte degli scenari ibridi usare Azure Active Directory come archivio identità.

### <a name="active-directory-federation-services"></a>Active Directory Federation Services
È possibile scegliere di utilizzare Active Directory Federation Services (ADFS) per le distribuzioni disconnesse dello Stack di Azure.  Stack di Azure, i provider di risorse e altre applicazioni di lavoro simili a quelle con AD FS come avviene con Azure Active Directory. Stack di Azure include la propria istanza di AD FS e Active Directory e un'API Graph di Active Directory. Kit di sviluppo dello Stack Azure supporta i seguenti scenari di AD FS:

- Accedere alla distribuzione tramite ADFS.
- Creare una macchina virtuale con i segreti nell'insieme di credenziali chiave
- Creare un insieme di credenziali per l'archiviazione o l'accesso a informazioni riservate
- Creare ruoli RBAC personalizzati nella sottoscrizione
- Assegnare utenti a ruoli RBAC sulle risorse
- Creare ruoli del sistema RBAC tramite Azure PowerShell
- Accedere come un utente tramite Azure PowerShell
- Creare servizio entità utilizzano per accedere a Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Le aree, servizi, dei piani, offerte e sottoscrizioni
Nello Stack di Azure, servizi vengono recapitati ai tenant utilizzando le aree, sottoscrizioni, offerte e piani. I tenant possono sottoscrivere più offerte. Le offerte possono includere uno o più piani, che a loro volta possono includere uno o più servizi.

![](media/azure-stack-key-features/image4.png)

Gerarchia di esempio delle offerte sottoscritte da un tenant, ognuna con piani e servizi diversi.

### <a name="regions"></a>Regioni
Aree di Azure Stack sono un elemento di base della scala e la gestione. Un'organizzazione può avere più aree con le risorse disponibili in ogni area. Aree possono avere diversi servizi disponibili. Nel Kit di sviluppo dello Stack di Azure, è supportata solo una singola area e viene automaticamente denominata *locale*.

### <a name="services"></a>Services
Stack di Microsoft Azure consente ai provider di fornire un'ampia gamma di servizi e applicazioni, ad esempio macchine virtuali, SQL Server database, SharePoint, Exchange e altro ancora.

### <a name="plans"></a>Piani
I piani sono raggruppamenti di uno o più servizi. Il provider crea i piani da offrire ai tenant. A loro volta, i tenant sottoscrivono le offerte per usare i piani e i servizi inclusi.

Ogni servizio aggiunto a un piano può essere configurato con impostazioni delle quote, che permettono di gestire la capacità del cloud. Le quote possono includere restrizioni, ad esempio limiti a livello della CPU, della RAM o della macchina virtuale, che vengono applicate per ogni sottoscrizione utente. Le quote possono essere differenziate in base alla località. Ad esempio, un piano che contiene i servizi di calcolo da un'area potrebbe avere una quota di due macchine virtuali, 4 GB di RAM e 10 core di CPU.

Quando si crea un'offerta, l'amministratore del servizio può includere un **piano base**. Questi piani di base vengono incluse per impostazione predefinita, quando un tenant sottoscrive l'offerta. Quando un utente effettua la sottoscrizione e viene creata la sottoscrizione, l'utente ha accesso a tutti i provider di risorse specificato in questi piani di base (con le quote corrispondenti).

L'amministratore del servizio può includere nell'offerta anche **piani aggiuntivi** . I piani aggiuntivi non sono inclusi per impostazione predefinita nella sottoscrizione. Piani di componente aggiuntivo sono piani aggiuntivi (quote) disponibili in un'offerta che è possibile aggiungere un proprietario della sottoscrizione per le sottoscrizioni.

### <a name="offers"></a>Offerte
Le offerte sono gruppi di uno o più piani che i provider propongono ai tenant per l'acquisto e la sottoscrizione. Ad esempio, offrire alfa può contenere un piano che contiene un set di servizi di calcolo e pianificare B, che contiene un set di servizi di archiviazione e rete.

Ogni offerta include un set di piani base e gli amministratori del servizio possono creare piani aggiuntivi che i tenant possono aggiungere alla sottoscrizione.

### <a name="subscriptions"></a>Sottoscrizioni
Una sottoscrizione è come tenant acquistano le offerte. Una sottoscrizione è una combinazione di un tenant con un'offerta. Un tenant può disporre di sottoscrizioni per le offerte più. Ogni sottoscrizione si applica a una sola offerta. Le sottoscrizioni del tenant determinano quale possono accedere i piani e servizi.

Le sottoscrizioni consentono di provider di organizzare e accedere alle risorse cloud e servizi.

Per l'amministratore, viene creata una sottoscrizione di Provider predefinito durante la distribuzione. Questa sottoscrizione è utilizzabile per gestire Azure Stack, distribuire ulteriormente i provider di risorse e creare i piani e le offerte per i tenant. E non deve essere utilizzato per eseguire applicazioni e i carichi di lavoro di cliente. 

## <a name="azure-resource-manager"></a>Gestione risorse di Azure
Tramite Gestione risorse di Azure, per lavorare con le risorse di infrastruttura in un modello dichiarativo, basato su modello.   Fornisce una singola interfaccia che è possibile utilizzare per distribuire e gestire i componenti della soluzione. Per ulteriori informazioni e istruzioni, vedere il [Panoramica di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Gruppi di risorse
Gruppi di risorse sono raccolte di risorse, servizi e applicazioni, e ogni risorsa ha un tipo, ad esempio macchine virtuali, reti virtuali, gli indirizzi IP pubblici, gli account di archiviazione e i siti Web. Ogni risorsa deve essere in un gruppo di risorse e permettono di gruppi di risorse in modo logico organizzare le risorse, ad esempio, dal carico di lavoro o il percorso.  In Microsoft Azure Stack le risorse come i piani e le offerte vengono gestite anche nei gruppi di risorse.
 
### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure
Con Gestione risorse di Azure, è possibile creare un modello che definisce la distribuzione e configurazione dell'applicazione (in formato JSON). Questo modello è noto come modello di Gestione risorse di Azure e permette di definire la distribuzione in modo dichiarativo. Utilizzando un modello, è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente.

## <a name="resource-providers-rps"></a>Provider di risorse (RPs)
I provider di risorse sono servizi Web su cui si basano tutti i servizi PaaS e IaaS basati su Azure. Gestione risorse di Azure si basa su diversi RPs per fornire accesso ai servizi.

Esistono quattro RPs fondamentali: rete, archiviazione, calcolo e KeyVault. Ognuno di questi provider di risorse consente di configurare e controllare le rispettive risorse. Gli amministratori del servizio è anche possono aggiungere nuovi provider di risorse personalizzato.

### <a name="compute-rp"></a>Provider di risorse di calcolo
Il provider di risorse di calcolo consente ai tenant di Azure Stack di creare le proprie macchine virtuali. Il provider di risorse di calcolo include la possibilità di creare macchine virtuali ed estensioni della macchina virtuale. Il servizio di estensione della macchina virtuale consente di fornire funzionalità IaaS per macchine virtuali Linux e Windows.  Ad esempio, è possibile utilizzare il CRP per effettuare il provisioning di una macchina virtuale Linux ed eseguire script Bash durante la distribuzione per configurare la macchina virtuale.

### <a name="network-rp"></a>Provider di risorse di rete
Il Provider di risorse di rete (NRP) offre una serie di funzionalità SDN Software Defined Networking () e virtualizzazione di funzione di rete (NFV) per il cloud privato.  Per creare risorse come software carico bilanciamento del carico, gli indirizzi IP pubblici, gruppi di sicurezza, le reti virtuali, è possibile utilizzare il NRP.

### <a name="storage-rp"></a>Provider di risorse di archiviazione
Il componente di archiviazione offre quattro servizi di archiviazione Azure coerenti: blob, tabella, coda e la gestione degli account. Offre anche un servizio di amministrazione del cloud di archiviazione per semplificare l'amministrazione dei servizi di archiviazione coerenti con Azure da parte del provider di servizi. Archiviazione di Azure permette di archiviare e recuperare in modo flessibile grandi quantità di dati non strutturati, ad esempio documenti e file multimediali usando i BLOB di Azure e dati strutturati basati su NoSQL usando le tabelle di Azure. Per ulteriori informazioni sull'archiviazione di Azure, vedere [Introduzione all'archiviazione di Microsoft Azure](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Archiviazione BLOB
Nell'archivio BLOB sono archiviati i set di dati. Un BLOB può essere qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. Nell'archivio tabelle sono archiviati set di dati strutturati. L'archiviazione delle tabelle utilizza un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati. L'archiviazione di accodamento offre un sistema di messaggistica affidabile per l'elaborazione del flusso di lavoro e per la comunicazione tra i componenti dei servizi cloud.

Ogni blob sono organizzati in un contenitore. I contenitori sono inoltre una soluzione utile per assegnare criteri di sicurezza a gruppi di oggetti. Un account di archiviazione può contenere un numero qualsiasi di contenitori e un contenitore può contenere un numero qualsiasi di BLOB, fino a che non viene raggiunto il limite di capacità di 500 TB dell'account di archiviazione. Gli archivi BLOB offrono tre tipi di BLOB: i BLOB in blocchi , i BLOB di accodamento e i BLOB di pagine (dischi). I BLOB in blocchi sono ottimizzati per lo streaming e l'archiviazione di oggetti cloud e sono una soluzione adatta per l'archiviazione di documenti, file multimediali, backup e così via. I BLOB di accodamento sono simili ai BLOB in blocchi, ma ottimizzati per le operazioni di Accodamento. Un BLOB di accodamento può essere aggiornato solo aggiungendo un nuovo blocco alla fine. I BLOB di accodamento sono una buona scelta per scenari quali la registrazione, dove i nuovi dati devono essere scritti solo fino alla fine del BLOB. BLOB di pagine sono ottimizzati per che rappresentano i dischi di IaaS e supporto casuale scrive e può essere fino a 1 TB. Un disco IaaS collegato a una rete di macchine virtuali Azure è un disco rigido virtuale archiviato come BLOB di pagine.

#### <a name="table-storage"></a>Archiviazione tabelle
L'archivio tabelle è l'archivio chiave-attributo NoSQL di Microsoft. Ha una struttura priva di schema che lo rende diverso dai database relazionali tradizionali. L'assenza di uno schema negli archivi dati permette di adattare facilmente i dati con il variare delle esigenze dell'applicazione. L'archiviazione tabelle è semplice da usare e consente di velocizzare lo sviluppo e la creazione di applicazioni. Archiviazione tabelle è un archivio chiave-attributo, ovvero ogni valore di una tabella è archiviato con un nome di proprietà tipizzato che può essere usato per filtrare i dati e specificare i criteri di selezione. Una raccolta di proprietà con i rispettivi valori costituisce un'entità. Dal momento che l'archivio tabelle è privo di schema, due entità nella stessa tabella possono contenere raccolte diverse di proprietà, che a loro volta possono essere di tipi diversi. È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio. In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

#### <a name="queue-storage"></a>Archiviazione di accodamento
L'archivio code di Azure fornisce la messaggistica cloud tra i componenti dell'applicazione. Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archivio code fornisce la messaggistica asincrona per la comunicazione tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Archiviazione code supporta anche la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

### <a name="keyvault"></a>Insieme di credenziali delle chiavi
Il RP KeyVault fornisce la gestione e controllo dei segreti, ad esempio le password e certificati. Ad esempio, un tenant può utilizzare il RP KeyVault per fornire la password amministratore o chiavi durante la distribuzione della macchina virtuale.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo può essere usato per concedere l'accesso al sistema a utenti autorizzati, gruppi e servizi tramite l'assegnazione di ruoli a livello di sottoscrizione, gruppo di risorse o singola risorsa. Ogni ruolo definisce il livello di accesso di un utente, un gruppo o un servizio rispetto alle risorse di Microsoft Azure Stack.

RBAC Azure dispone di tre ruoli di base che si applicano a tutti i tipi di risorsa: proprietario, collaboratore e lettore. Proprietario dispone dell'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Collaboratore può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti. Lettore può solo visualizzare le risorse di Azure esistenti. Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore di macchina virtuale consente la creazione e la gestione delle macchine virtuali, ma non consente la gestione della rete virtuale o della subnet a cui la macchina virtuale si connette.

## <a name="usage-data"></a>Dati di utilizzo
Microsoft Azure Stack raccoglie e aggrega i dati di utilizzo per tutti i provider di risorse e trasmette a Azure per l'elaborazione da Azure commerce. I dati di utilizzo raccolti nello Stack di Azure possono essere visualizzati mediante un'API REST. Sono disponibili un'API del tenant coerente con Azure e API del provider e del provider delegato che permettono di ottenere dati di utilizzo di tutte le sottoscrizioni del tenant. Questi dati possono essere integrati con un servizio o uno strumento esterno di fatturazione o chargeback. Dopo l'utilizzo è stato elaborato dal commercio Azure, possono essere visualizzato nel portale di fatturazione di Azure.

## <a name="in-development-build-of-azure-stack-development-kit"></a>Build di sviluppo del Kit di sviluppo di Azure Stack
Le compilazioni in sviluppo consentono adottato valutare la versione più recente del Kit di sviluppo dello Stack di Azure. Si tratta di compilazioni incrementali in base alla versione principale più recente. Mentre le versioni principali continuerà a essere rilasciata ogni pochi mesi, le compilazioni in sviluppo rilascerà in modo intermittente tra le versioni principali.

Le compilazioni in sviluppo fornirà i vantaggi seguenti:
- Correzioni di bug
- Nuove funzionalità
- Altri miglioramenti

## <a name="next-steps"></a>Passaggi successivi
[Prerequisiti per la distribuzione di Azure Stack](azure-stack-deploy.md)

