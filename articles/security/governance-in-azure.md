---
title: Governance in Azure | Microsoft Docs
description: Informazioni su servizi di calcolo basati sul cloud che possono aumentare o diminuire in modo da soddisfare le esigenze dell'applicazione o dell'organizzazione.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970524"
---
# <a name="governance-in-azure"></a>Governance in Azure

Azure offre molte opzioni di sicurezza e la possibilità di controllarle in modo che sia possibile soddisfare i requisiti univoci delle distribuzioni dell'organizzazione.

La governance cloud di Azure si riferisce ai processi decisionali e ai criteri che influenzano la pianificazione, l'architettura, l'acquisizione, la distribuzione, il funzionamento e la gestione del cloud computing. La governance cloud di Azure offre anche un approccio di controllo e consulenza integrato per la revisione e la consulenza sull'uso della piattaforma Azure da parte delle organizzazioni. 

Per creare un piano per la governance cloud di Azure, è necessario eseguire un'analisi dettagliata di persone, tecnologie e processi coinvolti. È possibile quindi creare framework che semplificano al reparto IT il supporto uniforme delle esigenze aziendali, offrendo contemporaneamente agli utenti la flessibilità necessaria per usare le funzionalità di Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implementazione di criteri, processi e standard 

I dirigenti stabiliscono ruoli e responsabilità per controllare l'implementazione di criteri di sicurezza delle informazioni e la continuità operativa in Azure. La gestione di Azure prevede invece la supervisione delle procedure di sicurezza e continuità nei team relativi (incluse terze parti) e facilita anche il rispetto della conformità con i criteri, i processi e gli standard di sicurezza.

### <a name="account-provisioning"></a>Provisioning degli account

La definizione di una gerarchia di account è un passaggio fondamentale per usare e strutturare i servizi di Azure in una società e rappresenta la struttura di governance principale. I clienti che dispongono di un Enterprise Agreement (EA) possono suddividere l'ambiente in reparti, account e sottoscrizioni.

![Provisioning degli account](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Se non si dispone di un Enterprise Agreement, usare i [tag di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a livello della sottoscrizione per definire la gerarchia. Una sottoscrizione di Azure è l'unità di base che contiene tutte le risorse e definisce anche diversi limiti in Azure, ad esempio di memorie centrali e di risorse. Le sottoscrizioni possono contenere [gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), che a loro volta contengono risorse. I principi di [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) si applicano a questi tre livelli.

Ogni azienda è diversa. Per le aziende di dimensioni ridotte, la gerarchia dell'uso di tag di Azure offre una flessibilità nell'organizzazione di Azure. Prima di distribuire risorse in Azure, è necessario modellare una gerarchia e comprenderne l'impatto sulla fatturazione, sull'accesso alle risorse e sulla complessità.

### <a name="subscription-controls"></a>Controlli delle sottoscrizioni

Le sottoscrizioni determinano il modo in cui l'uso delle risorse viene segnalato e fatturato. È possibile impostare sottoscrizioni per la fatturazione e il pagamento separati. A un account Azure possono essere associate più sottoscrizioni. È possibile usare le sottoscrizioni per determinare l'uso delle risorse di Azure da parte di più reparti di un'azienda.

Si prenda ad esempio il caso in cui in un'azienda sono presenti reparti IT, risorse umane e marketing con più progetti in corso. L'azienda può basare la fatturazione sull'uso delle risorse di Azure, come le macchine virtuali, da parte delle risorse di Azure per controllare le finanze di ciascun reparto.

Le sottoscrizioni di Azure definiscono tre parametri:

- ID sottoscrittore univoco

- Sede di fatturazione

- Set di risorse disponibili

Per un singolo utente, tali parametri includono un ID account Microsoft, un numero di carta di credito e la serie completa di servizi di Azure. Microsoft applica limiti di consumo a seconda del tipo di sottoscrizione.

Le gerarchie di registrazione di Azure definiscono come sono strutturati i servizi nell'ambito di un contratto Enterprise. Il portale per gli Enterprise Agreement consente ai clienti di dividere l'accesso alle risorse di Azure associato a un contratto Enterprise Agreement secondo gerarchie flessibili che possono essere personalizzate in base alle esigenze dell'organizzazione. Il modello gerarchico deve corrispondere alla struttura gestionale e geografica dell'organizzazione per tenere conto della fatturazione e dell'accesso alle risorse associati.

I tre modelli di gerarchia ad alto livello sono il modello funzionale e geografico e di Business Unit. I reparti sono una struttura amministrativa per i raggruppamenti di account. In ogni reparto è possibile assegnare agli account sottoscrizioni, che creano silo per la fatturazioni e molti limiti chiave in Azure (ad esempio numero di macchine virtuali e account di archiviazione).

![Controlli delle sottoscrizioni](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Per le organizzazioni con un contratto Enterprise, le sottoscrizioni di Azure seguono una gerarchia a quattro livelli:

1. Amministratore della registrazione Enterprise

2. Amministratore del reparto

3. Proprietario dell'account

4. Amministratore del servizio

Questa gerarchia disciplina quanto segue:

- Rapporto di fatturazione.

- Amministrazione account.

- Accesso alle risorse tramite il controllo degli accessi in base al ruolo.

- Limiti:

  - Uso e fatturazione (scheda tariffa in base al numero di offerte)

  - Limiti

  - Rete virtuale

- Collegamento ad Azure Active Directory (Azure AD). Un'istanza di Azure AD può essere associata a più sottoscrizioni.

- Associazione a un account di registrazione aziendale.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) consente di gestire in modo dettagliato l'accesso alle risorse in Azure. L'uso del controllo degli accessi in base al ruolo permette di concedere agli utenti solo il livello di accesso necessario per lavorare. Le aziende devono concedere ai dipendenti solo le autorizzazione strettamente necessarie per lavorare. Un numero eccessivo di autorizzazioni espone un account a utenti malintenzionati. Un numero di autorizzazioni insufficiente impedisce ai dipendenti di svolgere il proprio lavoro in modo efficiente. 

Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni. È possibile ad esempio usare il controllo degli accessi in base al ruolo per consentire a un dipendente di gestire le macchine virtuali in una sottoscrizione e a un altro dipendente di gestire i database SQL nella stessa sottoscrizione.

Per concedere l'accesso, assegnare ruoli a utenti, gruppi o applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Un ruolo assegnato a un ambito padre concede anche l'accesso agli elementi figlio contenuti al suo interno. Un utente con accesso a un gruppo di risorse, ad esempio, può gestire tutte le risorse che contiene, come siti Web, macchine virtuali e subnet. In ogni sottoscrizione è possibile creare fino a 2000 assegnazioni di ruolo.

Un ruolo è una raccolta di autorizzazioni e il controllo degli accessi in base al ruolo dispone di diversi ruoli predefiniti. I ruoli predefiniti seguenti si applicano a tutti i tipi di risorse:

- **Proprietario**: ha accesso completo a tutte le risorse, incluso il diritto di delegare l'accesso ad altri utenti.

- **Collaboratore**: può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri.

- **Lettore**: può visualizzare tutte le risorse di Azure.

Gli altri ruoli predefiniti consentono la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore Macchina virtuale consente all'utente di creare e gestire macchine virtuali, Per un elenco di tutti i ruoli predefiniti e le relative operazioni, vedere [Ruoli predefiniti in Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Il controllo degli accessi in base al ruolo supporta operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Azure Resource Manager. Nella maggior parte dei casi il controllo degli accessi in base al ruolo non è in grado di autorizzare le operazioni a livello di dati per le risorse di Azure. Per informazioni sull'estensione del controllo degli accessi in base al ruolo alle operazioni sui dati, vedere [Informazioni sulle definizioni del ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Se i ruoli predefiniti non soddisfano le esigenze di accesso specifiche, è possibile [creare un ruolo personalizzato](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Analogamente ai ruoli predefiniti, i ruoli personalizzati possono essere assegnati a utenti, gruppi e applicazioni nell'ambito della sottoscrizione, del gruppo di risorse e delle risorse. È possibile creare ruoli personalizzati tramite [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) e l'[API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Resource management

In Azure sono disponibili due modelli di distribuzione, il modello [classico](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) e Azure Resource Manager.

Nel modello classico ogni risorsa esiste in modo indipendente e non è possibile raggruppare le risorse correlate. È necessario verificare manualmente quali risorse compongono la soluzione o l'applicazione e ricordare di gestirle in un approccio coordinato. L'unità di base della gestione è la sottoscrizione. È difficile suddividere le risorse all'interno di una sottoscrizione e ciò comporta la creazione di un numero elevato di sottoscrizioni.

Il modello di distribuzione Azure Resource Manager include il concetto di un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Un gruppo di risorse è un contenitore di risorse che condividono un ciclo di vita comune. Tale gruppo può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

Il modello di distribuzione di Resource Manager offre diversi vantaggi:

- È possibile distribuire, gestire e monitorare tutti i servizi per la soluzione come un gruppo, anziché gestire singolarmente questi servizi.

- È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

- È possibile applicare il controllo di accesso a tutte le risorse di un gruppo. Tali criteri vengono applicati automaticamente quando si aggiungono nuove risorse al gruppo.

- È possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.

- È possibile usare JavaScript Object Notation (JSON) per definire l'infrastruttura per la soluzione. Il file JSON è noto come modello di Resource Manager.

- È possibile definire le dipendenze tra risorse in modo che vengano distribuite nell'ordine corretto.

![Gestione risorse](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Per altri suggerimenti sui modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analizza le dipendenze per garantire che le risorse vengono create nell'ordine corretto. Se una risorsa si basa sul valore di un'altra risorsa, ad esempio una macchina virtuale che richiede un account di archiviazione per i dischi, [impostare una dipendenza](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) nel modello.

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. È ad esempio possibile aggiungere una risorsa alla soluzione e quindi aggiungere regole di configurazione per le risorse già distribuite. Se il modello specifica la creazione di una risorsa, ma la risorsa esiste già, Azure Resource Manager esegue un aggiornamento anziché creare un nuovo asset. Azure Resource Manager aggiorna l'asset esistente in base allo stesso di quelli nuovi.

Azure Resource Manager offre anche estensioni per gli scenari in cui sono necessarie altre operazioni, ad esempio l'installazione di software non incluso.

### <a name="resource-tracking"></a>Rilevamento delle risorse

Con l'aggiunta di risorse alla sottoscrizione da parte degli utenti dell'organizzazione, diventa più importante associare le risorse al reparto, all'ambiente e ai clienti giusti. È possibile allegare metadati alle risorse tramite [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Usare tag per fornire informazioni su risorsa o proprietario. I tag consentono non solo di aggregare e raggruppare le risorse in modi diversi, ma anche di usare questi dati per operazioni di chargeback.

Usare i tag quando è presente una raccolta complessa di risorse e di gruppi di risorse ed è necessario visualizzare tali risorse nel modo più razionale. È possibile ad esempio contrassegnare le risorse che svolgono un ruolo analogo nell'organizzazione o che appartengono allo stesso reparto.

Senza l'uso dei tag è possibile che gli utenti dell'organizzazione creino più risorse che possono risultare difficili da identificare e gestire in un secondo momento. Si supponga che sia necessario eliminare tutte le risorse per un progetto. Se alle risorse non stati assegnati tag per il progetto, è necessario ricercarle manualmente. L'assegnazione di tag può essere un modo importante per ridurre i costi non necessari nella sottoscrizione.

Per condividere un tag, non è necessario che le risorse si trovino nello stesso gruppo di risorse. È possibile creare una propria tassonomia di tag per garantire che tutti gli utenti dell'organizzazione usino tag comuni anziché applicare inavvertitamente tag leggermente diversi (ad esempio "dept" anziché "department").

I criteri delle risorse consentono di creare regole standard per l'organizzazione. È possibile creare criteri che garantiscono che le risorse vengano contrassegnate con i valori appropriati.

È anche possibile visualizzare le risorse con tag tramite il portale di Azure. Il [report sull'uso](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) per la sottoscrizione include nomi e valori di tag, pertanto, è possibile suddividere i costi in base ai tag.

Per altre informazioni sui tag, vedere [Iniziativa relativa ai criteri dei tag di fatturazione](../azure-policy/scripts/billing-tags-policy-init.md).

Ai tag si applicano le limitazioni seguenti:

- Ogni risorsa o gruppo di risorse può avere un massimo di 15 coppie chiave-valore di tag. Questa limitazione si applica solo ai tag applicati direttamente al gruppo di risorse o alla risorsa. Un gruppo di risorse può contenere più risorse ognuna con 15 coppie chiave-valore di tag.

- Il nome del tag non può superare i 512 caratteri.

- Il valore del tag non può superare i 256 caratteri.

- I tag applicati al gruppo di risorse non vengono ereditati dalle risorse in tale gruppo di risorse.

Se si devono associare più di 15 valori a una risorsa, usare una stringa JSON come valore di tag. La stringa JSON può contenere diversi valori applicati a una singola chiave di tag.

#### <a name="tags-for-billing"></a>Tag per la fatturazione

I tag consentono di raggruppare i dati di fatturazione. Se sono in esecuzione più macchine virtuali per organizzazioni diverse, usare i tag per raggrupparne l'uso in base al centro di costo. È anche possibile usare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'uso di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

Le informazioni sui tag possono essere recuperate tramite l' [API di utilizzo della risorsa di Azure e della Rate Card](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) o il file di utilizzo con valori delimitati da virgole (CSV). Il file di uso può essere scaricato dal [portale degli account di Azure](https://account.windowsazure.com/) o dal [portale EA](https://ea.azure.com/).

Per altre informazioni sull'accesso a livello di codice alle informazioni sulla fatturazione, vedere [Ottenere informazioni dettagliate sul consumo delle risorse di Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Per le operazioni API REST, vedere [Riferimento API REST alla fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando si scarica il file CSV sull'uso per i servizi che supportano tag con fatturazione, i tag vengono visualizzati nella colonna Tag.

### <a name="critical-resource-controls"></a>Controlli delle risorse critiche

Con l'aggiunta di servizi di base alla sottoscrizione da parte dell'organizzazione, diventa più importante garantire che tali servizi siano disponibili per evitare l'interruzione delle attività aziendali. I [blocchi per le risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) consentono di limitare le operazioni sulle risorse di valore elevato in cui la modifica o l'eliminazione potrebbe avere un impatto significativo sulle applicazioni o sull'infrastruttura del cloud. È possibile applicare blocchi a una sottoscrizione, a un gruppo di risorse o a una risorsa. Generalmente, i blocchi vengono applicati a risorse fondamentali come reti virtuali, gateway e account di archiviazione.

I blocchi di risorse attualmente supportano due valori: **CanNotDelete** e **ReadOnly**. **CanNotDelete** indica che gli utenti con i diritti appropriati possono continuare a leggere o a modificare una risorsa, ma non possono eliminarla. **ReadOnly** indica che gli utenti autorizzati non possono eliminare né modificare una risorsa.

I blocchi di risorse si applicano solo alle operazioni del piano di gestione, costituito da operazioni inviate a <https://management.azure.com>. I blocchi non limitano il modo in cui le risorse eseguono le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Un blocco **ReadOnly** in un database SQL impedisce ad esempio l'eliminazione o la modifica del database, ma non impedisce la creazione, l'aggiornamento o l'eliminazione dei dati nel database.

Applicare **ReadOnly** può causare risultati imprevisti perché alcune operazioni che sembrano di sola lettura possono richiedere ulteriori operazioni. Ad esempio, l'inserimento di un blocco **ReadOnly** in un account di archiviazione impedisce a tutti gli utenti di ottenere un elenco delle chiavi. L'operazione di elenco delle chiavi viene gestita tramite una richiesta POST, perché le chiavi restituite sono disponibili per operazioni di scrittura.

![Controlli delle risorse critiche](./media/governance-in-azure/security-governance-in-azure-fig5.png)

L'assegnazione di un blocco **ReadOnly** a una risorsa Servizio app di Azure impedisce ad esempio a Visual Studio Server Explorer di visualizzare i file della risorsa perché questa interazione richiede l'accesso in scrittura.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione consentono di applicare una restrizione a tutti gli utenti e i ruoli. Per altre informazioni sull'impostazione delle autorizzazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dall'elemento padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

Per creare o eliminare blocchi di gestione, è necessario disporre dell'accesso alle azioni Microsoft.Authorization/ o Microsoft.Authorization/locks/. Fra i ruoli predefiniti, solo a Proprietario e Amministratore Accesso utenti sono concesse tali azioni.

### <a name="api-access-to-billing-information"></a>Accesso API alle informazioni di fatturazione

Usare le API di fatturazione di Azure per raccogliere e immettere i dati di uso e delle risorse negli strumenti di analisi dei dati scelti. Le API di utilizzo delle risorse di Azure e RateCard possono aiutare a prevedere e gestire i costi con precisione. Le API vengono implementate come provider di risorse nell’ambito della famiglia di API esposte da Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>API di uso delle risorse di Azure (anteprima)

Per ottenere una stima dei dati di consumo di Azure usare l'[API di uso delle risorse](https://msdn.microsoft.com/library/azure/mt219003) di Azure. L'API include:

- **Controllo degli accessi in base al ruolo di Azure**: configurare i propri criteri di accesso nel [portale di Azure](https://portal.azure.com/) o tramite i [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono avere accesso ai dati di uso della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore.

- **Aggregazioni orarie o giornaliere**: i chiamanti possono specificare se vogliono i dati di uso di Azure in incrementi orari o giornalieri. L’impostazione predefinita è rappresentata dagli intervalli giornalieri.

- **Metadati dell'istanza (includono i tag delle risorse)**: ottenere dettagli a livello di istanza come URI completo della risorsa (/subscriptions/{subscription-id} /..), informazioni sul gruppo di risorse e tag delle risorse. Tali metadati consentono agli utenti di allocare l'uso in modo deterministico e programmatico in base ai tag, per casi di uso come l'addebito delle tariffe.

- **Metadati delle risorse**: dettagli delle risorse, ad esempio il nome, la categoria e la sottocategoria del misuratore, l'unità e l'area, per fornire ai chiamanti una migliore comprensione delle risorse usate. Lavoriamo anche per allineare la terminologia dei metadati delle risorse nel portale di Azure, nel file CSV di uso di Azure e di fatturazione EA e in altre esperienze pubbliche, per consentire agli utenti di correlare i dati delle diverse esperienze.

- **Uso per tutti i tipi di offerte**: i dati di uso sono accessibili per tutti i tipi di offerta, tra cui il pagamento in base al consumo, MSDN, l’impegno monetario, il credito monetario ed EA.

#### <a name="resource-ratecard-api"></a>API RateCard delle risorse

Usare l'API RateCard delle risorse di Azure per ottenere l'elenco delle risorse di Azure disponibili e una stima delle informazioni di prezzo di ognuna di esse. L'API include:

- **Controllo degli accessi in base al ruolo**: configurare i criteri di accesso nel portale di Azure o tramite i cmdlet di Microsoft Azure PowerShell per specificare quali utenti o applicazioni possono accedere ai dati RateCard. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Aggiungere il chiamante al ruolo Lettore, Proprietario o Collaboratore per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure.

- **Supporto delle offerte con pagamento in base al consumo, MSDN, impegno monetario e credito monetario (EA non supportato)**: questa API fornisce informazioni sulle tariffe a livello di offerta di Azure. Il chiamante di questa API deve passare le informazioni sull'offerta per dettagli e tariffe delle risorse. Enterprise Agreement non è attualmente supportato perché alle offerte EA sono associate tariffe personalizzate in base alla registrazione. 

#### <a name="scenarios"></a>Scenari

La combinazione delle API RateCard e di uso rende possibili gli scenari seguenti:

- **Informazioni sulla spesa per Azure durante il mese**: usare le API RateCard e di uso per ottenere informazioni più approfondite sulla spesa per il cloud mensile. È possibile analizzare l'uso orario e giornaliero e stimare i costi.

- **Impostazione di avvisi**: usare le API RateCard e di uso per ottenere una stima dei consumi e degli addebiti legati al cloud e per impostare avvisi basati sulle risorse o sui costi.

- **Previsione delle fatture**: è possibile ottenere le stime sui consumi e sulle spese per il cloud e applicare algoritmi di apprendimento automatico per prevedere l’importo della fattura al termine del periodo di fatturazione.

- **Analisi dei costi prima del consumo**: usare l'API RateCard per prevedere l'importo della fattura per l'uso quando si spostano i carichi di lavoro in Azure. Se si dispone di carichi di lavoro esistenti in altri cloud o nei cloud privati, è possibile inoltre eseguire il mapping dell'utilizzo con le tariffe di Azure per ottenere una stima più accurata della spesa stimata di Azure. Questa stima permette di modificare un'offerta e di confrontare vari tipi di offerte oltre al pagamento in base al consumo, ad esempio impegni monetari e crediti monetari.

- **Analisi di simulazione**: è possibile determinare se sia più conveniente eseguire carichi di lavoro in un'altra area o in un'altra configurazione della risorsa di Azure. I costi delle risorse di Azure possono variare in base all'area di Azure. È inoltre possibile determinare se un altro tipo di offerta di Azure offre una tariffa migliore per una risorsa di Azure.

### <a name="networking-controls"></a>Controlli della rete

È possibile accedere alle risorse dall'interno (nella rete di un'azienda) o dall'esterno (tramite Internet). In alcuni casi può accadere che gli utenti interni all'organizzazione inseriscano involontariamente le risorse nel punto sbagliato e le rendano vulnerabili all'accesso di utenti malintenzionati. Come per i dispositivi locali, le aziende devono aggiungere controlli appropriati per accertarsi che gli utenti di Azure prendano le decisioni giuste.

![Controlli della rete](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Per la governance delle sottoscrizioni, il controllo di accesso di base viene implementato dalle risorse seguenti.

#### <a name="network-connectivity"></a>Connettività di rete

[Reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), cioè oggetti contenitore per le subnet. Sebbene non sia strettamente necessario, una rete virtuale viene spesso usata per la connessione di applicazioni alle risorse aziendali interne. Il servizio Rete virtuale di Azure consente di connettere tra loro le risorse di Azure in modo sicuro con reti virtuali.

Una rete virtuale è una rappresentazione della rete dell'utente nel cloud. Una rete virtuale è un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È anche possibile connettere reti virtuali alla rete locale.

Di seguito sono descritte le funzionalità delle reti virtuali di Azure.

- **Isolamento**: le reti virtuali sono isolate una dall'altra. È possibile creare reti virtuali separate per sviluppo, test e produzione che usano gli stessi blocchi di indirizzi CIDR. In alternativa, è possibile creare più reti virtuali che usano blocchi di indirizzi CIDR diversi e connettere tra loro le reti. Una rete virtuale può essere segmentata in più subnet. Azure offre la risoluzione dei nomi interni per istanze del ruolo macchine virtuali e Servizi cloud di Azure connesse a una rete virtuale. Facoltativamente, è possibile configurare una rete virtuale in modo da usare server DNS personalizzati anziché la risoluzione dei nomi interna di Azure.

- **Connettività Internet**: tutte le istanze del ruolo macchine virtuali e Servizi cloud connesse a una rete virtuale hanno accesso a Internet per impostazione predefinita. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario.

- **Connettività delle risorse di Azure**: è possibile connettere risorse di Azure, ad esempio Servizi cloud e macchine virtuali, alla stessa rete virtuale. Le risorse possono essere connesse tra loro usando indirizzi IP privati, anche se si trovano in subnet diverse. In Azure è disponibile il routing predefinito tra subnet, reti virtuali connesse e reti locali, quindi non è necessario configurare e gestire le route.

- **Connettività di reti virtuali**: è possibile connettere reti virtuali tra loro. Le risorse connesse a una rete virtuale possono quindi comunicare con qualsiasi risorsa in qualsiasi altra rete virtuale.

- **Connettività locale**: è possibile connettere reti virtuali a reti locali tramite connessioni di rete privata tra la rete e Azure o tramite una connessione di rete privata virtuale (VPN) da sito a sito in internet.

- **Filtro del traffico**: è possibile filtrare il traffico di rete (in ingresso e in uscita) per macchine virtuali e Servizi cloud in base all'indirizzo IP e alla porta di origine e di destinazione e al protocollo.

- **Routing**: facoltativamente, è possibile sostituire il routing predefinito di Azure configurando route personalizzate o usando route BGP attraverso un gateway di rete.

#### <a name="network-access-controls"></a>Controlli di accesso alla rete

I [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) agiscono come un firewall e offrono regole per le interazioni tra le risorse presenti nella rete. Tali gruppi implementano un controllo granulare sul modo in cui una subnet (o una macchina virtuale) può connettersi a Internet o ad altre subnet della stessa rete virtuale.

Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a reti virtuali di Azure. I gruppi di sicurezza di rete possono essere associati a subnet, a macchine virtuali singole (distribuzione classica) oppure a singole interfacce di rete collegate a macchine virtuali (Azure Resource Manager).

Quando un gruppo di sicurezza di rete è associato a una subnet, le regole si applicano a tutte le risorse connesse alla subnet stessa. È possibile limitare ulteriormente il traffico associando un gruppo di sicurezza di rete a una macchina virtuale oppure a una scheda di interfaccia di rete.

## <a name="security-and-compliance-with-organizational-standards"></a>Sicurezza e conformità agli standard aziendali

Ogni azienda ha esigenze diverse e ognuna può ottenere vantaggi diversi dalle soluzioni cloud. Tuttavia, tutti i tipi di clienti hanno le stesse preoccupazioni di base sul trasferimento delle risorse nel cloud. Ciò che i clienti chiedono ai provider di servizi cloud è:

- **Protezione dei dati**: i responsabili IT riconoscono che il cloud può offrire maggiore sicurezza e controllo amministrativo in relazione ai dati. ma sono comunque preoccupati dal fatto che la migrazione al cloud li renda più vulnerabili agli attacchi di pirati informatici rispetto alle soluzioni in locale.

- **Riservatezza dei dati**: i servizi cloud pongono sfide impegnative in termini di privacy. Benché siano interessate al cloud come soluzione per ridurre i costi d'infrastruttura e migliorare la loro flessibilità, le aziende si preoccupano di non poter controllare dove si trovino i dati, chi vi ha accesso e come vengono usati.

- **Controllo diretto**: anche se le aziende si avvalgono del cloud per distribuire soluzioni più innovative, hanno paura di perdere il controllo dei propri dati. Le recenti rivelazioni di agenzie governative relative all'accesso di dati riservati di clienti, sia per vie extragiudiziali che giudiziali, hanno reso molti CIO diffidenti nei confronti dell'idea di archiviare i dati nel cloud.

- **Promozione della trasparenza**: i decision maker aziendali sono consapevoli dell'importanza di fattori come sicurezza, privacy e controllo, ma vogliono anche la possibilità di verificare in modo indipendente le modalità di archiviazione, accesso e protezione dei dati.

- **Mantenimento della conformità**: con l'aumento dell'uso delle tecnologie cloud da parte delle aziende, la complessità e l'ambito degli standard e dei regolamenti continuano a evolvere. Le aziende devono essere certe che i propri standard di conformità vengono rispettati.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Configurazione di sicurezza per monitoraggio, registrazione e controllo

I sottoscrittori di Azure possono gestire i propri ambienti cloud in più dispositivi, ad esempio workstation di gestione, PC per sviluppatori e dispositivi di utenti finali con privilegi e con autorizzazioni specifiche per le attività. 

In alcuni casi, le funzioni amministrative vengono effettuate tramite console basate sul Web come il portale di Azure. In altri casi è possibile che vengano usate connessioni dirette ad Azure da sistemi locali su reti private virtuali, Servizi terminal, protocolli applicativi client oppure, a livello di codice, l'API Gestione dei servizi di Azure (SMAPI). Gli endpoint client possono essere anche aggiunti a un dominio o isolati e non gestiti, come tablet o smartphone.

Questa variabilità può aggiungere rischi significativi a una distribuzione cloud. Il monitoraggio, il controllo e la gestione delle operazioni amministrative possono infatti risultare difficili. Questa variabilità può introdurre anche minacce alla sicurezza tramite l'accesso non regolamentato agli endpoint client usati per la gestione dei servizi cloud. L'uso di workstation generiche o personali per lo sviluppo e la gestione dell'infrastruttura genera vettori di minaccia imprevedibili, come l'esplorazione del Web (ad esempio attacchi di tipo watering hole) o la posta elettronica (ad esempio "ingegneria sociale" e phishing).

Monitoraggio, registrazione e controllo rappresentano una base per il rilevamento e la comprensione delle attività amministrative. Il controllo di tutte le azioni in modo dettagliato può non essere sempre fattibile a causa della quantità di dati generati, ma il controllo dell'efficacia dei criteri di gestione è comunque una procedura consigliata.

La governance in termini di sicurezza di Azure in oggetto Criteri di gruppo di Azure Active Directory Domain Services (AD DS) consente di controllare tutte le interfacce Windows degli amministratori, ad esempio la condivisione file. Sono incluse le workstation di gestione nei processi di monitoraggio, registrazione e controllo. Tenere traccia di tutti gli accessi e gli utilizzi di amministratori e sviluppatori.

### <a name="azure-security-center"></a>Centro sicurezza di Azure

[Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) offre una visualizzazione centralizzata dello stato di sicurezza delle risorse nelle sottoscrizioni. Fornisce anche indicazioni che aiutano a impedire la compromissione di risorse e può consentire l'uso di criteri più dettagliati (ad esempio l'applicazione di criteri a gruppi di risorse specifici che consentono all'azienda di adeguare il modo di reagire ai rischi che affronta).

![Centro sicurezza di Azure](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Il Centro sicurezza offre un monitoraggio di sicurezza integrato e gestione dei criteri per le sottoscrizioni di Azure, aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate ed è compatibile con un ampio ecosistema di soluzioni di sicurezza. Appena si abilitano i [criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies) per le risorse di una sottoscrizione, Centro sicurezza analizza la sicurezza delle risorse per identificare le possibili vulnerabilità. Le informazioni sulla configurazione di rete sono disponibili immediatamente.

Centro sicurezza di Azure rappresenta una combinazione tra l'analisi delle procedure consigliate e la gestione dei criteri di sicurezza per tutte le risorse incluse nella sottoscrizione di Azure. Si tratta di uno strumento che permette ai team responsabili della sicurezza e ai funzionari dei rischi di prevenire, rilevare e rispondere alle minacce di sicurezza, poiché raccoglie e analizza automaticamente i dati di sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner come i programmi anti-malware e i firewall.

Centro sicurezza di Azure applica anche funzioni di analisi avanzate, ad esempio apprendimento automatico e analisi del comportamento. Vengono usate le informazioni sulle minacce globali di prodotti e servizi Microsoft, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC), nonché di feed esterni. È possibile applicare opzioni di [governance di sicurezza](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) su vasta scala a livello di sottoscrizione. In alternativa, è possibile adeguare la governance a requisiti specifici e applicarla a singole risorse tramite la definizione di criteri.

Il Centro sicurezza di Azure analizza infine l'integrità della sicurezza delle risorse in base ai criteri definiti e usa queste informazioni per offrire dashboard e avvisi approfonditi per eventi come il rilevamento di malware o tentativi di connessione da parte di IP dannosi. Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Il Centro sicurezza di Azure monitora le risorse di Azure seguenti:

- Macchine virtuali (inclusi i servizi cloud)

- Reti virtuali

- Database SQL

- Soluzioni partner integrate con la sottoscrizione di Azure, ad esempio un web application firewall nelle macchine virtuali e nell'[ambiente del servizio app](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. È consigliabile mantenere abilitata la raccolta dei dati, ma è possibile [disabilitarla](https://docs.microsoft.com/azure/security-center/security-center-faq) nei criteri del Centro sicurezza.

### <a name="log-analytics"></a>Log Analytics

Il [programma di governance](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) e di sicurezza della informazioni del team di servizio e di sviluppo del software Azure Log Analytics supporta i requisiti aziendali. Il programma è conforme alle leggi e alle normative, come descritto in [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e [conformità in Centro protezione Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Qui viene descritto anche il modo in cui Log Analytics stabilisce i requisiti di sicurezza, identifica i controlli di sicurezza e gestisce e controlla i rischi. Una volta all'anno il team esamina i criteri, gli standard, le procedure e le linee guida.

Ciascun membro del team di sviluppo di Log Analytics riceve una formazione formale sulla sicurezza delle applicazioni. Un sistema di controllo della versione consente di proteggere ogni singolo progetto software in fase di sviluppo.

Microsoft si avvale di un team per la sicurezza e conformità che supervisiona e valuta tutti i servizi in Microsoft. Il team è composto da addetti alla sicurezza delle informazioni non associati ai reparti tecnici dove si sviluppa Log Analytics. Gli addetti alla sicurezza hanno una propria catena di gestione ed eseguono valutazioni indipendenti di prodotti e servizi per garantire sicurezza e conformità.

Le funzionalità principali di Log Analytics vengono fornite da un set di servizi eseguiti in Azure. Ogni servizio fornisce una funzione di gestione specifica ed è possibile combinare i servizi per realizzare scenari di gestione diversi.

![Servizi di Azure per la gestione](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Questi servizi di gestione sono stati progettati nel cloud. Sono completamente ospitati in Azure, in modo da non comportare la distribuzione e la gestione delle risorse locali. La configurazione è minima e consentono di essere operativi in pochi minuti.

È sufficiente installare un agente in un computer Windows o Linux nel proprio data center per inviare i dati a Log Analytics. L'analisi può essere eseguita con tutti gli altri dati raccolti dai servizi cloud o in locale. Usare Backup di Azure e Azure Site Recovery per sfruttare il cloud per il backup e l'elevata disponibilità per le risorse locali.

![Servizi di gestione nel dashboard di Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

I runbook nel cloud non possono generalmente accedere alle risorse locali, ma è possibile installare un agente in uno o più computer per ospitare i runbook del data center. Quando si avvia un runbook, si specifica se lo si vuole eseguire nel cloud o in un ruolo di lavoro locale.

Sono disponibili diverse soluzioni Microsoft e dei partner che è possibile aggiungere alla sottoscrizione di Azure per aumentare il valore dell'investimento in Log Analytics. Azure offre ad esempio [soluzioni di gestione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions), ovvero pacchetti di logica predefiniti che implementano uno scenario di gestione usando uno o più servizi di gestione.

![Raccolta di soluzioni di gestione in Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

I partner possono creare le proprie soluzioni per supportare le applicazioni e i servizi e fornirle agli utenti tramite Azure Marketplace o i modelli di avvio rapido.

## <a name="performance-alerting-and-monitoring"></a>Invio di avvisi e monitoraggio delle prestazioni

### <a name="alerting"></a>Creazione di avvisi

Gli avvisi consentono di monitorare metriche di risorse di Azure, eventi o log e inviano una notifica quando viene soddisfatta una condizione specificata.

Gli avvisi sono disponibili in tutti i servizi, tra cui i seguenti:

- **Azure Application Insights**: abilita gli avvisi per le metriche e i test Web. Per altre informazioni, vedere [Impostare gli avvisi in Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) e [Monitorare la disponibilità e la velocità di risposta dei siti Web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics**: abilita il routing dei log di attività e diagnostica in Log Analytics. Consente anche di implementare avvisi per le metriche e i log e di altro tipo. Per altre informazioni, vedere [Avvisi in Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Monitoraggio di Azure**: abilita gli avvisi basati su valori della metrica e sugli eventi del log attività. È possibile usare l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/dn931943.aspx) per gestire gli avvisi. Per altre informazioni, vedere [Uso del portale di Azure, di PowerShell o dell'interfaccia della riga di comando per creare avvisi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitoraggio

I problemi di prestazioni nell'app cloud possono avere un impatto negativo su un'azienda. Con più componenti interconnessi e versioni frequenti, possono verificarsi in qualsiasi momento riduzioni delle prestazioni. Quando si sviluppa un'app, gli utenti in genere individuano problemi non trovati in fase di test. È consigliabile rilevare tali problemi immediatamente e disporre degli strumenti per diagnosticarli e risolverli.

Per il monitoraggio delle applicazioni e dei servizi di Azure sono disponibili diversi strumenti, le cui funzionalità in parte si sovrappongono. Ciò è dovuto in parte al confine non sempre chiaro tra sviluppo e funzionamento di un'applicazione.

Di seguito sono riportati gli strumenti principali:

- **Monitoraggio di Azure** è uno strumento di base per il monitoraggio dei servizi eseguiti in Azure. Offre dati a livello di infrastruttura sulla velocità effettiva di un servizio e l'ambiente circostante. Se si gestiscono tutte le app in Azure e si decide se aumentare o ridurre le risorse, Monitoraggio di Azure consente di iniziare a operare.

- **Application Insights** può essere usato per lo sviluppo e come soluzione di monitoraggio di produzione. Tale strumento installa un pacchetto nell'app, consentendo di capire in modo più dettagliato ciò che accade. I dati includono i tempi di risposta delle dipendenze, le tracce delle eccezioni, gli snapshot per il debug e i profili di esecuzione. Vengono offerti strumenti per analizzare tutti questi dati di telemetria ed eseguire così il debug di un'app per comprendere come viene usata dagli utenti. È possibile determinare se un picco nei tempi di risposta sia causato da un problema in un'app o da un problema esterno di allocazione delle risorse. In caso di errore nell'app, se si usa Visual Studio è possibile passare direttamente alle righe di codice problematiche per correggerlo.

- **Log Analytics** è destinato a chi deve ottimizzare le prestazioni e pianificare la manutenzione delle applicazioni eseguite in produzione. Lo strumento raccoglie e aggrega dati da diverse origini, con un ritardo di 10-15 minuti. Offre una soluzione di gestione IT olistica per l'infrastruttura Azure, locale e basata sul cloud di terze parti (ad esempio, Amazon Web Services). Fornisce strumenti per analizzare i dati in più origini, consente query complesse su tutti i log e può generare avvisi proattivi in condizioni specifiche. È anche possibile raccogliere dati personalizzati nel repository centrale e quindi eseguire una query sui dati e visualizzarli.

- **System Center Operations Manager** consente di gestire e monitorare installazioni cloud di grandi dimensioni. Può essere già noto come strumento di gestione per cloud locali basati su Windows Sever e Hyper-V, ma può anche essere integrato con app Azure e gestirle. Tra l'altro, può installare Application Insights in app attive esistenti. Se un'app si arresta, Operations Manager lo indica in pochi secondi.


## <a name="next-steps"></a>Passaggi successivi

- [Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Esempi di implementazione della governance delle sottoscrizioni di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/)
