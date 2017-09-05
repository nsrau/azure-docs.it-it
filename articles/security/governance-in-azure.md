---
title: Governance in Azure | Microsoft Docs
description: "Informazioni sui servizi di calcolo basati sul cloud che includono un'ampia gamma di istanze e servizi di calcolo con scalabilità automatica per soddisfare le esigenze dell'applicazione o dell'organizzazione."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/01/2017
ms.author: TomSh
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: f3fbca281dbbfca7ff9392d003429719c589afbd
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---

# <a name="governance-in-azure"></a>Governance in Azure

La sicurezza è la priorità principale nel cloud ed è importante che l'utente trovi informazioni accurate e tempestive sulla sicurezza di Azure. Uno dei motivi migliori per usare Azure per le proprie applicazioni e i propri servizi consiste nella possibilità di sfruttare una vasta gamma di strumenti e funzionalità per la sicurezza. Questi strumenti e queste funzionalità consentono di creare soluzioni sicure sulla piattaforma Azure protetta.

Questo articolo, "Governance in Azure", è stato scritto per offrire una migliore comprensione dei numerosi controlli di governance implementati in Microsoft Azure, sia dal punto di vista dei clienti che di Microsoft Operations. Offre un quadro completo sulle funzionalità di governance disponibili in Microsoft Azure.

## <a name="azure-platform"></a>Piattaforma Azure

Azure è una piattaforma del servizio cloud pubblico che supporta un'ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi. Può eseguire contenitori Linux con integrazione Dockers; creare app con JavaScript, Python, .NET, PHP, Java e Node.js; creare backend per dispositivi iOS, Android e Windows. I servizi cloud pubblici di Azure supportano le stesse tecnologie già considerate affidabili e usate da milioni di sviluppatori e professionisti IT.

Quando si usano risorse IT di un provider di servizi cloud pubblici, o si migrano tali risorse, ci si affida alla capacità dell'organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli forniti dalla stessa per gestire la sicurezza delle proprie risorse basate su cloud.

L'infrastruttura di Azure è progettata, dalla struttura alle applicazioni, per ospitare contemporaneamente milioni di clienti e offre una base affidabile che consente alle aziende di soddisfare le loro esigenze di sicurezza. Inoltre, Azure offre molte opzioni di sicurezza e la possibilità di controllarle per poter personalizzare la sicurezza in base ai requisiti specifici delle distribuzioni delle singole aziende.

Questo documento spiega come usare le funzionalità Governance di Azure per soddisfare questi requisiti.

## <a name="abstract"></a>Sunto

La governance cloud di Microsoft Azure offre un approccio di controllo e consulenza integrato per la revisione e la consulenza sull'uso della piattaforma Azure da parte delle organizzazioni. La governance cloud di Microsoft Azure si riferisce ai processi decisionali e ai criteri che influenzano la pianificazione, l'architettura, l'acquisizione, la distribuzione, il funzionamento e la gestione di un cloud computing.

Per creare un piano di governance cloud per Microsoft Azure, è necessario esaminare approfonditamente le persone, i processi e le tecnologie in uso, quindi creare framework per consentano all'IT di supportare più facilmente e coerentemente le esigenze aziendali, offrendo al tempo stesso agli utenti la flessibilità di usare le straordinarie funzionalità di Microsoft Azure.

Questo documento spiega come ottenere una governance di alto livello per le proprie risorse IT in Microsoft Azure. Questo documento può essere utile per comprendere meglio le funzionalità di sicurezza e governance incorporate in Microsoft Azure.

I principali problemi di governance esaminati in questo documento sono i seguenti:

- Implementazione di criteri, processi e procedure in linea con gli obiettivi dell'organizzazione.

- Protezione e conformità continua con gli standard dell'organizzazione

- Invio di avvisi e monitoraggio

## <a name="implementation-of-policies-processes-and-procedures"></a>Implementazione di criteri, processi e procedure 

La direzione ha stabilito ruoli e responsabilità per controllare l'implementazione dei criteri di sicurezza delle informazioni e la continuità operativa in Azure. Gestione di Microsoft Azure è responsabile del controllo delle procedure di sicurezza e della continuità nell'ambito dei rispettivi team (compresi quelli di terze parti), ed è tenuta a contribuire alla conformità con i criteri, i processi e gli standard di sicurezza.

Questi sono i fattori che si sono evoluti:

- Provisioning degli account

- Controlli delle sottoscrizioni

- Controlli degli accessi in base al ruolo

- Gestione delle risorse

- Rilevamento delle risorse

- Controllo delle risorse critiche

- Accesso API alle informazioni di fatturazione

- Controlli delle reti

## <a name="account-provisioning"></a>Provisioning degli account

Definire una gerarchia degli account è un passaggio fondamentale per usare e strutturare i servizi di Azure in ambito aziendale e rappresenta la struttura di governance principale. I clienti con un contratto Enterprise possono ulteriormente suddividere l'ambiente in reparti, account e anche sottoscrizioni.

![Provisioning degli account](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Se non si ha un contratto Enterprise, è possibile usare i [tag di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a livello di sottoscrizione per definire la gerarchia. Una sottoscrizione Azure è l'unità di base che contiene tutte le risorse. Definisce anche diversi limiti all'interno di Azure, ad esempio il numero di memorie centrali, risorse e così via. Le sottoscrizioni possono contenere [gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), che a loro volta contengono risorse. I principi di [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) si applicano solo a questi tre livelli.

Ogni azienda è diversa e la definizione di una gerarchia con i tag di Azure, in caso di clienti non Enterprise, permette di organizzare l'uso di Azure nell'azienda con grande flessibilità. Prima di distribuire risorse in Microsoft Azure, è opportuno modellare la gerarchia e comprendere il suo impatto sulla fatturazione, l'accesso alle risorse e la complessità.

## <a name="subscription-controls"></a>Controlli delle sottoscrizioni

La sottoscrizione permette di controllare come l'uso delle risorse viene riportato e fatturato. È possibile impostare sottoscrizioni in modo che fatturazione e pagamento siano separati. Come spiegato in precedenza, un account Azure può avere più sottoscrizioni. È possibile usare le sottoscrizioni per determinare l'uso delle risorse di Azure da parte di più reparti di un'azienda.

Ad esempio, nel caso in cui l'azienda abbia reparti IT, risorse umane e marketing e questi reparti abbiano più progetti in corso. Le risorse di Azure, come le macchine virtuali, possono essere fatturate in base all'uso da parte dei singoli reparti. In questo modo è possibile controllare le finanze di ciascun reparto.

Le sottoscrizioni di Azure definiscono tre parametri:

- Un ID sottoscrittore univoco

- Una sede di fatturazione

- Set di risorse disponibili

Per un singolo utente, il set includerebbe un ID account Microsoft, un numero di carta di credito e la suite completa dei servizi di Azure, benché Microsoft applichi limiti di consumo in base al tipo di sottoscrizione.

Le gerarchie di registrazione di Azure definiscono come sono strutturati i servizi nell'ambito di un contratto Enterprise. Enterprise Portal consente ai clienti di dividere l'accesso alle risorse di Azure associate a un contratto Enterprise in base a gerarchie flessibili, personalizzabili in base alle esigenze specifiche dell'azienda. Il modello gerarchico deve corrispondere alla struttura gestionale e geografica dell'organizzazione in modo che la fatturazione e l'accesso alle risorse possano essere correttamente contabilizzati.

I tre modelli di alto livello sono funzionale, business unit e geografico, quando si usano i reparti come costrutto amministrativo per i raggruppamenti degli account. All'interno di ciascun reparto, è possibile assegnare agli account sottoscrizioni, che creano silos per la fatturazioni e molti limiti chiave in Azure (ad esempio, numero di macchine virtuali, account di archiviazione, ecc.).

![Controlli delle sottoscrizioni](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Per le organizzazioni con un contratto Enterprise, le sottoscrizioni di Azure seguono una gerarchia a quattro livelli:

- Amministratore della registrazione Enterprise

- Amministratore del reparto

- Proprietario dell'account

- Amministratore del servizio

Questa gerarchia disciplina quanto segue:

- Rapporto di fatturazione

- Amministrazione account

- Controllo degli accessi in base al ruolo (RBAC) agli elementi grafici

- Confini/Limiti

- Confini

  - Uso e fatturazione (scheda tariffa in base al numero di offerte)

  - Limiti

  - Rete virtuale

- Connesso a 1 AAD (1 ADD può essere associato a più sottoscrizioni)

- Associato a un account di registrazione Enterprise

## <a name="role-based-access-controls"></a>Controlli degli accessi in base al ruolo

Quando Azure è stato originariamente rilasciato, i controlli degli accessi a una sottoscrizione erano elementari: Amministratore o Coamministratore. L'accesso a una sottoscrizione nel modello classico implicava l'accesso a tutte le risorse del portale. Questa mancanza di un controllo granulare ha portato alla proliferazione di sottoscrizioni per fornire un livello ragionevole di controllo degli accessi per un'iscrizione ad Azure.

![Controlli degli accessi in base al ruolo](./media/governance-in-azure/security-governance-in-azure-fig3.png)

La proliferazione di sottoscrizioni non è più necessaria. Con il controllo degli accessi in base al ruolo, è possibile assegnare ruoli standard agli utenti (ad esempio tipi di ruoli comuni come "lettore" e "scrittore"). È inoltre possibile definire ruoli personalizzati.

[Controllo degli accessi in base al ruolo di Azure (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) consente una gestione specifica degli accessi per Azure. L'uso del Controllo degli accessi in base al ruolo permette di concedere agli utenti solo il livello di accesso necessario per lavorare. Le aziende orientate sulla sicurezza devono concedere ai propri dipendenti la quantità esatta di autorizzazioni di cui necessitano. Un numero eccessivo di autorizzazioni espone un account a utenti malintenzionati. Un numero insufficiente di autorizzazioni impedisce ai dipendenti di svolgere efficientemente il proprio lavoro. Il Controllo degli accessi in base al ruolo di Azure (RBAC) aiuta a risolvere questo problema offrendo la gestione specifica degli accessi per Azure. RBAC aiuta a separare i compiti all'interno del team e a concedere agli utenti l'accesso solo nella misura necessaria per consentire loro di svolgere il lavoro. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni.

Ad esempio, usare il Controllo degli accessi in base al ruolo per consentire a un dipendente di gestire le macchine virtuali in una sottoscrizione, mentre un altro utente può gestire i database SQL della stessa sottoscrizione.

Il Controllo degli accessi in base al ruolo di Azure include di tre ruoli di base che si applicano a tutti i tipi di risorsa:

- **Proprietario** ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.

- **Collaboratore** può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri.

- **Lettore** può visualizzare le risorse di Azure esistenti.

Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore Macchina virtuale consente all'utente di creare e gestire macchine virtuali, ma non concede l'accesso alla rete virtuale o alla subnet a cui la macchina virtuale si connette.

[Controllo degli accessi in base al ruolo: ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) elenca i ruoli disponibili in Azure. Specifica le operazioni e l'ambito che ogni ruolo predefinito concede agli utenti.

Concedere l'accesso assegnando i ruoli Controllo degli accessi in base al ruolo appropriati a utenti, gruppi e applicazioni in un ambito specifico. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Un ruolo assegnato a un ambito padre concede anche l'accesso agli elementi figlio contenuti al suo interno.

Ad esempio, un utente con accesso a un gruppo di risorse può gestire tutte le risorse che contiene, come siti Web, macchine virtuali e subnet.

Il Controllo degli accessi in base al ruolo di Azure supporta solo operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Azure Resource Manager. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, è possibile autorizzare un utente a gestire gli account di archiviazione, ma non i BLOB e le tabelle all'interno di un account di archiviazione. Analogamente, può essere gestito un database SQL, ma non le tabelle in esso contenute.

Per altri dettagli sulla gestione degli accessi, vedere l'articolo relativo al [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).

In Controllo degli accessi in base al ruolo (RBAC), è possibile anche [creare un ruolo personalizzato](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) se nessuno dei ruoli incorporati soddisfa le necessità specifiche di accesso dell'azienda. I ruoli personalizzati possono essere creati usando [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [l'interfaccia della riga di comando di Azure (CLI)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) e la [API REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Analogamente ai ruoli predefiniti, i ruoli personalizzati possono essere assegnati a utenti, gruppi e applicazioni nell'ambito della sottoscrizione, del gruppo di risorse e delle risorse.

In ogni sottoscrizione è possibile concedere fino a 2000 assegnazioni di ruolo.

## <a name="resource-management"></a>Resource management

In origine, Azure metteva a disposizione solo il modello di distribuzione classica. In questo modello ogni risorsa era indipendente, non era possibile raggruppare le risorse correlate. Era invece necessario verificare manualmente quali risorse componessero la soluzione o l'applicazione e ricordare di gestirle in un approccio coordinato.

Per distribuire una soluzione era necessario creare ogni risorsa singolarmente con il portale classico oppure creare uno script che distribuisse tutte le risorse nell'ordine corretto. Per eliminare una soluzione era necessario eliminare ogni risorsa singolarmente. Non era semplice applicare e aggiornare i criteri di controllo di accesso per le risorse correlate. Non era infine possibile applicare i tag alle risorse per etichettarle con i termini che consentono di monitorare le risorse e gestire la fatturazione.

Nel 2014, Azure ha introdotto Resource Manager e il nuovo concetto di gruppo di risorse. Un gruppo di risorse è un contenitore di risorse che condividono un ciclo di vita comune. Il modello di distribuzione di Resource Manager offre diversi vantaggi:

- È possibile distribuire, gestire e monitorare tutti i servizi per la soluzione come un gruppo, anziché gestire singolarmente questi servizi.

- È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

- È possibile applicare il controllo di accesso a tutte le risorse nel gruppo di risorse e tali criteri vengono applicati automaticamente quando si aggiungono nuove risorse al gruppo di risorse.

- È possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.

- È possibile usare JavaScript Object Notation (JSON) per definire l'infrastruttura per la soluzione. Il file JSON è noto come modello di Resource Manager.

- È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.

![Gestione delle risorse](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Resource Manager consente di inserire le risorse in gruppi significativi per gestione, fatturazione o affinità naturale. Come accennato in precedenza, Azure offre due modelli di distribuzione. Nel [modello Classico](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) precedente, l'unità di base di gestione era la sottoscrizione. Era difficile suddividere le risorse all'interno di una sottoscrizione e questo comportava la creazione di un numero elevato di sottoscrizioni. Con il modello di Resource Manager, sono stati introdotti i gruppi di risorse.

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. [Il gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) può includere tutte le risorse per la soluzione o solo quelle risorse che si desidera gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione.

Per altri suggerimenti sui modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analizza le dipendenze per far sì che le risorse vengano create nell'ordine corretto. Se una risorsa si basa sul valore di un'altra risorsa, ad esempio una macchina virtuale che richiede un account di archiviazione per i dischi, impostare una dipendenza.

>[!Note]
>Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. È ad esempio possibile aggiungere una risorsa alla soluzione e quindi aggiungere regole di configurazione per le risorse già distribuite. Se il modello specifica la creazione di una risorsa, ma la risorsa esiste già, Azure Resource Manager esegue un aggiornamento anziché creare un nuovo asset. Gestione risorse di Azure aggiorna l'asset esistente allo stesso stato di quelli nuovi.

Resource Manager offre estensioni per gli scenari in cui sono necessarie altre operazioni, come installare software che non è incluso nell'installazione.

## <a name="resource-tracking"></a>Rilevamento delle risorse

Man mano che gli utenti all'interno dell'organizzazione aggiungono risorse alla sottoscrizione, diventa sempre più importante associare le risorse al reparto, all'ambiente e ai clienti giusti. È possibile allegare metadati alle risorse tramite i tag. È possibile usare i [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) per fornire informazioni sulla risorsa o sul proprietario. I tag consentono non solo di aggregare e raggruppare le risorse in vari modi, ma anche di usare questi dati per il chargeback.

Usare i tag quando si ha un insieme complesso di gruppi di risorse e risorse ed è necessario visualizzare tali risorse nel modo più razionale. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto.

Senza tag è possibile che gli utenti dell'organizzazione creino più risorse che possono risultare difficili da identificare e gestire in un secondo momento. Ad esempio si può voler eliminare tutte le risorse di un progetto. Se alle risorse non stati assegnati tag per il progetto, è necessario ricercarle manualmente. L'assegnazione di tag può essere un modo importante per ridurre i costi non necessari nella sottoscrizione.

Non è necessario che le risorse si trovino nello stesso gruppo di risorse per condividere un tag. È possibile creare una propria tassonomia di tag per assicurarsi che tutti gli utenti dell'organizzazione utilizzino tag comuni anziché applichino inavvertitamente tag leggermente diversi (ad esempio "dept" anziché "department").

I criteri delle risorse consentono di creare regole standard per l'organizzazione. È possibile creare criteri che garantiscono che le risorse vengano contrassegnate con i valori appropriati.

> [!Note]
> Per altre informazioni, vedere [Applicare criteri delle risorse per i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags).

È anche possibile visualizzare le risorse con tag tramite il portale di Azure.

Il [report di uso](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) della sottoscrizione include valori e nomi di tag per suddividere i costi per tag.

> [!Note]
> Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

Ai tag si applicano le limitazioni seguenti:

- Ogni risorsa o gruppo di risorse può avere un massimo di 15 coppie chiave-valore di tag. Questa limitazione si applica solo ai tag applicati direttamente al gruppo di risorse o alla risorsa. Un gruppo di risorse può contenere più risorse ognuna con 15 coppie chiave-valore di tag.

- Il nome del tag non può superare i 512 caratteri.

- Il valore del tag non può superare i 256 caratteri.

- I tag applicati al gruppo di risorse non vengono ereditati dalle risorse in tale gruppo di risorse.

Se si devono associare più di 15 valori a una risorsa, usare una stringa JSON come valore di tag. La stringa JSON può contenere diversi valori applicati a una singola chiave di tag.

### <a name="tags-and-billing"></a>Tag e fatturazione

I tag consentono di raggruppare i dati di fatturazione. Ad esempio, se si eseguono più macchine virtuali per organizzazioni diverse, usare i tag per raggruppare l'utilizzo in base al centro di costo. È inoltre possibile utilizzare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'utilizzo di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

Le informazioni sui tag possono essere recuperate tramite l' [API di utilizzo della risorsa di Azure e della Rate Card](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) o il file di utilizzo con valori delimitati da virgole (CSV). Il file di utilizzo può essere scaricato dal [portale degli account di Azure](https://account.windowsazure.com/) o dal [portale EA](https://ea.azure.com/).

>[!Note]
> Per altre informazioni sull'accesso a livello di codice alle informazioni sulla fatturazione, vedere [Ottenere informazioni dettagliate sul consumo delle risorse di Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Per le operazioni API REST, vedere [Riferimento API REST alla fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando si scarica il file CSV sull'uso per i servizi che supportano tag con fatturazione, i tag vengono visualizzati nella colonna Tag.

## <a name="critical-resource-controls"></a>Controlli delle risorse critiche

Man mano che l'organizzazione aggiunge servizi di base alla sottoscrizione, diventa sempre più importante garantire che tali servizi siano disponibili per evitare l'interruzione delle attività aziendali. I [blocchi per le risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) consentono di limitare le operazioni sulle risorse di valore elevato in cui la modifica o l'eliminazione potrebbe avere un impatto significativo sulle applicazioni o sull'infrastruttura del cloud. È possibile applicare blocchi a una sottoscrizione, a un gruppo di risorse o a una risorsa. Generalmente, i blocchi vengono applicati a risorse fondamentali come reti virtuali, gateway e account di archiviazione.

I blocchi di risorse attualmente supportano due valori: CanNotDelete e ReadOnly. CanNotDelete significa che gli utenti con i diritti appropriati possono continuare a leggere o a modificare una risorsa ma non possono eliminarla. ReadOnly significa che gli utenti autorizzati non possono eliminare o modificare una risorsa.

I blocchi di Resource Manager si applicano solo alle operazioni che vengono effettuate a livello di gestione, che sono costituite da operazioni inviate a <https://management.azure.com>. I blocchi non limitano il modo in cui le risorse eseguono le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Un blocco di sola lettura in un database SQL impedisce ad esempio l'eliminazione o la modifica del database, ma non impedisce la creazione, l'aggiornamento o l'eliminazione dei dati nel database.

Applicare **ReadOnly** può causare risultati imprevisti perché alcune operazioni che sembrano di sola lettura possono richiedere ulteriori operazioni. Ad esempio, l'inserimento di un blocco **ReadOnly** in un account di archiviazione impedisce a tutti gli utenti di ottenere un elenco delle chiavi. L'operazione di elenco delle chiavi viene gestita tramite una richiesta POST, perché le chiavi restituite sono disponibili per operazioni di scrittura.

![Controllo delle risorse critiche](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Per fare un altro esempio: assegnare un blocco ReadOnly a una risorsa Servizio app impedisce a Visual Studio Server Explorer di visualizzare i file della risorsa perché questa interazione richiede l'accesso in scrittura.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione consentono di applicare una restrizione a tutti gli utenti e i ruoli. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo degli accessi in base al ruolo nel Portale di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dal padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

Per creare o eliminare blocchi di gestione, è necessario avere l'accesso alle azioni Microsoft.Authorization/ _o Microsoft.Authorization/locks/_. Dei ruoli predefiniti, solo **Proprietario** e **Amministratore Accesso utenti** garantiscono tali azioni.

## <a name="api-access-to-billing-information"></a>Accesso API alle informazioni di fatturazione

Usare le API di fatturazione di Azure per raccogliere e immettere i dati di uso e delle risorse negli strumenti di analisi dei dati scelti. Le API di utilizzo delle risorse di Azure e RateCard possono aiutare a prevedere e gestire i costi con precisione. Le API vengono implementate come provider di risorse, nell’ambito della famiglia di API esposte da Azure Resource Manager.

### <a name="azure-resource-usage-api-preview"></a>API di uso delle risorse di Azure (anteprima)

Per ottenere una stima dei dati di consumo di Azure usare l'[API di uso delle risorse](https://msdn.microsoft.com/library/azure/mt219003) di Azure. L'API include:

- **Controllo degli accessi in base al ruolo di Azure** - Configurare i criteri di accesso nel [portale di Azure](https://portal.azure.com/) o tramite i [cmdlet PowerShell di Azure](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati sull'uso della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore.

- **Aggregazioni orarie o giornaliere** : i chiamanti possono specificare se desiderano i dati di utilizzo di Azure in intervalli orari o giornalieri. L’impostazione predefinita è rappresentata dagli intervalli giornalieri.

- **Metadati dell'istanza (includono i tag delle risorse)** – Ottenere dettagli a livello di istanza come l'URI completo della risorsa (/subscriptions/{subscription-id} /..), le informazioni sul gruppo di risorse e i tag delle risorse. Tali metadati aiutano gli utenti ad allocare l’utilizzo in modo deterministico e programmatico in base ai tag, per casi di utilizzo come l’addebito delle tariffe.

- **Metadati delle risorse**: dettagli delle risorse, ad esempio il nome, la categoria e la sottocategoria del misuratore, l'unità e l'area, per fornire ai chiamanti una migliore comprensione delle risorse utilizzate. Stiamo inoltre lavorando per allineare la terminologia dei metadati delle risorse nel portale di Azure, il CSV di utilizzo di Azure, il CSV di fatturazione EA e altre esperienze pubbliche, per consentire agli utenti di correlare i dati delle diverse esperienze.

- **Utilizzo per tutti i tipi di offerte** : i dati di utilizzo sono accessibili per tutti i tipi di offerta, tra cui il pagamento in base al consumo, MSDN, l’impegno monetario, il credito monetari ed EA.

**API RateCard delle risorse di Azure (anteprima)**

Usare l'API RateCard delle risorse di Azure per ottenere l'elenco delle risorse di Azure disponibili e una stima delle informazioni di prezzo di ognuna di esse. L'API include:

- **Controllo degli accessi in base al ruolo di Azure** - Configurare i criteri di accesso nel portale di Azure o tramite i cmdlet PowerShell di Azure per specificare quali utenti o applicazioni possono accedere ai dati RateCard. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Aggiungere il chiamante al ruolo Lettore, Proprietario o Collaboratore per ottenere l'accesso ai dati di utilizzo per una determinata sottoscrizione di Azure.

- **Supporto delle offerte con pagamento in base al consumo, MSDN, impegno monetario e credito monetario (EA non supportato)** : questa API fornisce informazioni sulle tariffe a livello di offerta di Azure. Il chiamante di questa API deve passare le informazioni sull'offerta per dettagli e tariffe delle risorse. Dal momento che le offerte EA hanno tariffe personalizzate in base alla registrazione, al momento non è possibile fornire le tariffe EA. Di seguito sono illustrati alcuni scenari resi possibili con la combinazione di API di utilizzo e API RateCard:

- **Spesa di Azure durante il mese**: usare le API di utilizzo e RateCard per ottenere informazioni più approfondite sulle spese mensili legate al cloud. È possibile analizzare i bucket di utilizzo orari e giornalieri e le stime di addebito.

- **Impostare avvisi**: usare le API di utilizzo e RateCard per ottenere una stima dei consumi e degli addebiti legati al cloud e per impostare avvisi basati sulle risorse o sui costi.

- **Previsione delle fatture**: è possibile ottenere le stime sui consumi e sulle spese per il cloud e applicare algoritmi di Machine Learning per prevedere l’importo della fattura al termine del periodo di fatturazione.

- **Analisi dei costi prima del consumo**: usare l'API RateCard per prevedere l'importo della fattura per l'utilizzo quando si spostano i carichi di lavoro in Azure. Se si dispone di carichi di lavoro esistenti in altri cloud o nei cloud privati, è possibile inoltre eseguire il mapping dell'utilizzo con le tariffe di Azure per ottenere una stima più accurata della spesa stimata di Azure. Questa stima permette di girare un'offerta, confrontare vari tipi di offerte oltre al Pagamento al consumo, come impegni monetari e crediti monetari. L'API, inoltre, offre la possibilità di visualizzare le differenze di costo per area geografica e consente di eseguire una simulazione dei costi per facilitare le decisioni legate alla distribuzione.

- **Analisi di simulazione** - È possibile determinare se sia più conveniente eseguire carichi di lavoro in un'altra regione o in un'altra configurazione della risorsa di Azure. I costi delle risorse di Azure possono variare in base all'area di Azure in uso.

- È inoltre possibile determinare se un altro tipo di offerta di Azure offre una tariffa migliore per una risorsa di Azure.

## <a name="networking-controls"></a>Controlli della rete

È possibile accedere alle risorse dall'interno (nella rete di un'azienda) o dall'esterno (tramite Internet). Può capitare che gli utenti interni all'organizzazione inseriscano involontariamente le risorse nel punto sbagliato e le rendano vulnerabili all'accesso di utenti malintenzionati. Come per locale/dispositivi, le aziende devono aggiungere controlli appropriati per accertarsi che gli utenti di Azure prendano le decisioni giuste.

![Controlli delle reti](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Per la governance delle sottoscrizioni, vengono identificate le risorse principali che garantiscono il controllo di base degli accessi. Le risorse principali sono:

### <a name="network-connectivity"></a>Connettività di rete

[Reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sono oggetti contenitori per le subnet. Sebbene non sia strettamente necessario, vengono spesso usate per la connessione delle applicazioni alle risorse aziendali interne. Il servizio Rete virtuale di Azure consente di connettere tra loro le risorse di Azure in modo sicuro con reti virtuali.

Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È anche possibile connettere le reti virtuali alla rete locale.

Di seguito sono descritte le funzionalità delle reti virtuali di Azure:

- **Isolamento**: le reti virtuali sono isolate le une dalle altre. È possibile creare reti virtuali separate per sviluppo, test e produzione che usano gli stessi blocchi di indirizzi CIDR. È altrimenti possibile creare più reti virtuali che usano blocchi di indirizzi CIDR diversi e connettere tra loro le reti. Una rete virtuale può essere segmentata in più subnet. Azure offre la risoluzione dei nomi interna per le VM e le istanze del ruolo Servizi cloud connesse a una rete virtuale. Facoltativamente, è possibile configurare una rete virtuale in modo da usare server DNS personalizzati anziché la risoluzione dei nomi interna di Azure.

- **Connettività Internet**: per impostazione predefinita, tutte le macchine virtuali (VM) di Azure e le istanze dei ruoli Servizi cloud collegati a reti virtuali hanno accesso a Internet. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario.

- **Connettività delle risorse di Azure**: le risorse di Azure, come Servizi cloud e macchine virtuali possono essere collegati alla stessa rete virtuale. Le risorse possono essere connesse tra loro usando indirizzi IP privati, anche se si trovano in subnet diverse. Azure offre il routing predefinito tra subnet, reti virtuali e reti locali, quindi non è necessario configurare e gestire le route.

- **Connettività rete virtuale**: le reti virtuali possono essere collegate le une alle altre, e consentono alle risorse connesse a qualsiasi VNet di comunicare con qualsiasi risorsa su un'altra rete virtuale.

- **Connettività locale**: le reti virtuali possono essere collegate a reti locali stabilendo una connessione di rete privata tra la rete del cliente e Azure, oppure tramite una connessione VPN da sito a sito su Internet.

- **Filtraggio del traffico**: il traffico di rete delle istanze dei ruoli VM e Servizi cloud può essere filtrato in ingresso e in uscita in base all'indirizzo IP e la porta di origine, l'indirizzo IP e la porta di destinazione, e il protocollo.

- **Routing**: è possibile anche ignorare il routing predefinito di Azure configurando route personalizzate oppure usando le route BGP attraverso un gateway di rete.

## <a name="network-access-controls"></a>Controlli degli accessi di rete

I [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sono come un firewall e forniscono regole per le interazioni tra le risorse in rete. Forniscono un controllo granulare su come (o se) una subnet (o una macchina virtuale) può connettersi a Internet o ad altre subnet della stessa rete virtuale.

Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a reti virtuali di Azure. I gruppi di sicurezza di rete possono essere associati a subnet, singole VM (distribuzione classica) o singole interfacce di rete collegate a VM (Resource Manager).

Quando un gruppo di sicurezza di rete è associato a una subnet, le regole si applicano a tutte le risorse connesse alla subnet. Il traffico può essere ulteriormente limitato associando un gruppo di sicurezza di rete anche a una VM o un'interfaccia di rete.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Sicurezza e conformità continua con gli standard aziendali

Ogni azienda ha esigenze diverse e ciascuna di loro può ottenere vantaggi diversi dalle soluzioni cloud. Tuttavia, tutti i tipi di clienti hanno le stesse preoccupazioni di base sul trasferimento delle risorse nel cloud. Vogliono mantenere il controllo dei loro dati, vogliono che i loro dati vengano protetti e mantenuti riservati, e al tempo vogliono trasparenza e conformità.

Ciò che i clienti chiedono ai provider di servizi cloud è:

- **Proteggere i loro dati** : benché i responsabili IT sappiano che il cloud può fornire una maggiore protezione dei dati e un miglior controllo amministrativo, temono che la migrazione nel cloud li esponga molto di più ai pirati informatici delle loro attuali soluzioni interne.

- **Mantenere riservati i dati**: i servizi cloud pongono straordinarie sfide in termini di privacy alle aziende. Benché siano interessate al cloud come soluzione per ridurre i costi d'infrastruttura e migliorare la loro flessibilità, le aziende si preoccupano di non poter controllare dove si trovino i dati, chi vi ha accesso e come vengono usati.

- **Controllo diretto**: anche se le aziende si avvalgono del cloud per distribuire soluzioni più innovative, hanno paura di perdere il controllo dei loro dati. Le recenti rivelazioni di agenzie governative relative all'accesso di dati riservati di clienti, sia per vie extragiudiziali che giudiziali, hanno reso molti CIO diffidenti nei confronti dell'idea di archiviare i dati nel cloud.

- **Promuovere la trasparenza**: sebbene la sicurezza, la privacy e il controllo siano importanti per i responsabili decisionali, questi desiderano anche verificare indipendentemente come vengono archiviati i dati, come vi si accede e come vengono protetti.

- **Mantenere la conformità**: via via che le aziende ampliano l'uso delle tecnologie cloud, la complessità e l'ambito degli standard e dei regolamenti continuano ad evolversi. Le aziende hanno bisogno di sapere in che modo saranno soddisfatti i loro standard di conformità e che la conformità si evolverà di pari passo con i regolamenti.

## <a name="security-configuration-monitoring-and-alerting"></a>Configurazione della sicurezza, monitoraggio e invio di avvisi

I sottoscrittori di Azure possono gestire i propri ambienti cloud da più dispositivi, tra cui workstation di gestione, PC per sviluppatori e dispositivi di utenti finali con privilegi elevati con autorizzazioni specifiche per le attività. In alcuni casi, le funzioni amministrative vengono effettuate tramite console basate sul Web come il portale di Azure. In altri casi è possibile che vengano usate connessioni dirette ad Azure da sistemi locali su reti private virtuali (VPN), Servizi terminal, protocolli applicativi client oppure, a livello di codice, l'API Gestione dei servizi di Azure (SMAPI). Gli endpoint client possono essere inoltre aggiunti a un dominio o isolati e non gestiti, ad esempio tablet o smartphone.

Anche se le funzionalità multiple di accesso e gestione offrono una vasta gamma di opzioni, questa variabilità può aggiungere rischi significativi a una distribuzione cloud, complicando la gestione, la verifica e il controllo delle azioni amministrative. Questa variabilità potrebbe introdurre anche minacce alla sicurezza tramite accesso non regolamentato agli endpoint client usati per la gestione dei servizi cloud. L'uso di workstation generiche o personali per lo sviluppo e la gestione dell'infrastruttura genera vettori di minaccia imprevedibili, come l'esplorazione del Web (ad esempio attacchi di tipo watering hole) o la posta elettronica (ad esempio "ingegneria sociale" e phishing).

Il monitoraggio, la registrazione e il controllo forniscono una base per la verifica e la comprensione delle attività amministrative, ma è possibile che non si riesca a controllare tutte le azioni in modo dettagliato a causa della quantità di dati generata. Il controllo dell'efficacia dei criteri di gestione è tuttavia una procedura consigliata.

La governance di sicurezza di Azure da AD DS GPO per controllare tutte le interfacce Windows degli amministratori, come la condivisione dei file. Includere le workstation di gestione nei processi di controllo, monitoraggio e registrazione. Tenere traccia di tutti gli accessi e gli utilizzi di amministratori e sviluppatori.

### <a name="azure-security-center"></a>Centro di sicurezza di Azure

Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornisce un punto di vista centralizzato dello stato della sicurezza delle risorse nelle sottoscrizioni e indicazioni che aiutano a evitare di compromettere le risorse. Può consentire criteri più granulari (ad esempio, l'applicazione di criteri a gruppi di risorse specifici che consentono all'azienda di adattare il loro modo di reagire ai rischi che stanno affrontando).

![Centro sicurezza di Azure](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Il Centro sicurezza offre un monitoraggio di sicurezza integrato e gestione dei criteri per le sottoscrizioni di Azure, aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate ed è compatibile con un ampio ecosistema di soluzioni di sicurezza. Appena si abilitano i [criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies) per le risorse di una sottoscrizione, Centro sicurezza analizza la sicurezza delle risorse per identificare le possibili vulnerabilità. Le informazioni sulla configurazione di rete sono disponibili immediatamente.

Centro sicurezza di Azure rappresenta una combinazione tra l'analisi delle procedure consigliate e la gestione dei criteri di sicurezza per tutte le risorse incluse nella sottoscrizione di Azure. Questo potente e intuitivo strumento permette ai team responsabili della sicurezza e ai funzionari dei rischi di prevenire, rilevare e rispondere alle minacce di sicurezza, poiché raccoglie e analizza automaticamente i dati di sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner come i programmi anti-malware e i firewall.

Inoltre, Centro sicurezza di Azure applica analisi avanzate, tra cui l'apprendimento automatico e l'analisi comportamentale, usando al tempo stessi i dati sulle minacce globali acquisite dai prodotti e servizi Microsoft, da Digital Crimes Unit (DCU) di Microsoft, da Security Response Center (MSRC) e feed esterni. La [governance di sicurezza](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) può essere applicata in modo esteso a livello di sottoscrizione oppure limitata a requisiti specifici e granulari, applicati a singole risorse mediante la definizione di criteri.

Infine, il Centro sicurezza di Azure analizza l'integrità della sicurezza delle risorse in base ai criteri definiti e usa queste informazioni per offrire dashboard e avvisi approfonditi per eventi come il rilevamento di malware o tentativi di connessione da parte di IP dannosi.

>[!Note]
> Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo sull'[implementazione delle raccomandazioni sulla sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Il Centro sicurezza raccoglie i dati dalle macchine virtuali per valutarne lo stato della sicurezza, indicare raccomandazioni sulla sicurezza e segnalare le minacce. La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. La raccolta dei dati è consigliata, ma è possibile rifiutare esplicitamente [disattivandola](https://docs.microsoft.com/azure/security-center/security-center-faq) nei criteri del Centro sicurezza.

Infine, il Centro sicurezza di Azure è una piattaforma aperta che consente ai partner Microsoft e ai fornitori di software indipendenti di creare un software che si inserisce nel Centro sicurezza di Azure per migliorarne le funzionalità.

Il Centro sicurezza di Azure monitora le risorse di Azure seguenti:

- Macchine virtuali (VM) (compresi servizi cloud)

- Reti virtuali di Azure

- Servizio di SQL Azure

- Soluzioni dei partner integrate nella sottoscrizione di Azure come un web application firewall nelle macchine virtuali e nell'[ambiente del servizio app](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="operations-management-suite"></a>Operations Management Suite

Il [programma di governance](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) e per la sicurezza delle informazioni del team di sviluppo e assistenza di OMS supporta requisiti interni ed è conforme alle leggi ed ai regolamenti, come descritto in [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e [Microsoft Trust Center Compliance (Conformità di Microsoft Trust Center)](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Vengono anche fornite informazioni su come OMS stabilisce i requisiti di sicurezza, identifica i controlli di sicurezza, e gestisce e monitora i rischi. Ogni anno viene effettuata una revisione di criteri, standard, procedure e linee guida.

Ciascun membro del team di sviluppo di OMS riceve una formazione formale sulla sicurezza delle applicazioni. Internamente, viene usato un sistema di controllo della versione per lo sviluppo di software, che protegge ogni singolo progetto software.

Microsoft si avvale di un team per la sicurezza e conformità che supervisiona e valuta tutti i servizi in Microsoft. Il team è composto da addetti alla sicurezza delle informazioni che non sono associati ai reparti tecnici dove si sviluppa OMS. Gli addetti alla sicurezza dispongono della propria catena di gestione ed eseguono valutazioni indipendenti di prodotti e servizi per garantirne la sicurezza e la conformità.

Operations Management Suite (OMS) è una raccolta di servizi di gestione progettati nel cloud sin dall'inizio. Anziché distribuire e gestire le risorse localmente, i componenti OMS sono ospitati integralmente in Azure. La configurazione è minima ed è possibile essere operativi in davvero pochi minuti.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Anche se i servizi OMS vengono eseguiti nel cloud, non significa che non siano in grado di gestire efficacemente l'ambiente locale.

Se si installa un agente in un qualsiasi computer Windows o Linux nel data center, invierà i dati a Log Analytics dove potranno essere analizzati insieme agli altri dati raccolti dal cloud o dai servizi locali. Usare Backup di Azure e Azure Site Recovery per sfruttare il cloud per il backup e l'elevata disponibilità per le risorse locali.

I runbook nel cloud non possono generalmente accedere alle risorse locali, ma è possibile installare un agente in uno o più computer per ospitare i runbook del data center. Quando si avvia un runbook, si specifica solo se lo si vuole eseguire nel cloud o in un ruolo di lavoro locale.

Le funzionalità principali di OMS vengono fornite da un set di servizi eseguiti in Azure. Ogni servizio fornisce una funzione di gestione specifica ed è possibile combinare i servizi per realizzare scenari di gestione diversi.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Operation Manager di Azure estende le sue funzionalità offrendo soluzioni di gestione. Le [soluzioni di gestione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) sono set pre-confezionati di logica che implementano uno scenario di gestione usando uno o più servizi OMS.

![Operation Manager di Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Sono disponibili diverse soluzioni Microsoft e dei partner che è possibile aggiungere facilmente alla sottoscrizione di Azure per aumentare il valore dell'investimento in OMS.

I partner possono creare le proprie soluzioni per supportare le applicazioni e i servizi e offrirle agli utenti tramite Azure Marketplace o i modelli di avvio rapido.

## <a name="performance-alerting-and-monitoring"></a>Invio di avvisi e monitoraggio delle prestazioni

### <a name="alerting"></a>Creazione di avvisi

Gli avvisi sono un metodo per monitorare le metriche, gli eventi e i log delle risorse di Azure e ricevere una notifica quando una condizione specificata viene soddisfatta.

**Avvisi nei diversi servizi di Azure**

Gli avvisi sono disponibili in diversi servizi, tra cui:

- Application Insights: abilita i test Web e gli avvisi sulle metriche.

>[!Note]
> Vedere [Impostare gli avvisi in Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) e [Monitorare la disponibilità e la velocità di risposta dei siti Web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Log Analytics (Operations Management Suite): permette il routing dei registri delle attività e diagnostici a Log Analytics. Operations Management Suite consente gli avvisi relativi alla metrica, ai log e altri tipi di avviso.

>[!Note]
> Per altre informazioni, vedere Avvisi in [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Monitoraggio di Azure: permette di generare avvisi sia in base a valori metrici che in base agli eventi del registro attività. È possibile usare l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/dn931943.aspx) per gestire gli avvisi.

>[!Note]
> Per altre informazioni, vedere [Uso del portale di Azure, di PowerShell o dell'interfaccia della riga di comando per creare avvisi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitoraggio

I problemi di prestazioni nell'app cloud possono avere un impatto sull'azienda. Con più componenti interconnessi e versioni frequenti, possono verificarsi in qualsiasi momento riduzioni delle prestazioni. Se si sta sviluppando un'app, gli utenti scopriranno problemi che non vengono generalmente rilevati durante i test. È consigliabile conoscere questi problemi immediatamente e avere gli strumenti per diagnosticarli e risolverli. Microsoft Azure offre diversi strumenti per identificare i problemi.

**Come monitorare le app cloud di Azure**

Per il monitoraggio delle applicazioni e dei servizi di Azure sono disponibili diversi strumenti, le cui funzionalità in parte si sovrappongono. Ciò è dovuto in parte a motivi storici e in parte alla commistione tra sviluppo e funzionamento di un'applicazione.

Di seguito sono riportati gli strumenti principali:

- **Monitoraggio di Azure** è lo strumento di base per il monitoraggio dei servizi eseguiti in Azure. Offre dati a livello di infrastruttura sulla velocità effettiva di un servizio e l'ambiente circostante. Se si gestiscono tutte le app in Azure e si deve decidere se aumentare o ridurre le risorse, Monitoraggio di Azure offre un utile punto di partenza.

- **Application Insights** può essere usato per lo sviluppo e come soluzione di monitoraggio di produzione. Installa un pacchetto nell'app, consentendo di vedere ciò che accade dall'interno. I dati includono i tempi di risposta delle dipendenze, le tracce delle eccezioni, gli snapshot per il debug e i profili di esecuzione. Vengono offerti strumenti intelligenti avanzati per analizzare tutti questi dati di telemetria ed eseguire così il debug di un'app e comprendere come viene usata dagli utenti. È possibile determinare se un picco nei tempi di risposta sia causato da un problema in un'app o da un problema esterno di allocazione delle risorse. In caso di errore nell'app, se si usa Visual Studio è possibile passare direttamente alle righe di codice problematiche per correggerlo.

- **Log Analytics** è destinato a chi deve ottimizzare le prestazioni e pianificare la manutenzione delle applicazioni eseguite in produzione. È basato su Azure e raccoglie e aggrega dati da diverse origini, anche se con un ritardo di 10-15 minuti. Offre una soluzione di gestione IT olistica per l'infrastruttura Azure, locale e basata sul cloud di terze parti (ad esempio, Amazon Web Services). Fornisce strumenti più avanzati per analizzare i dati in più origini, consente query complesse su tutti i log e può generare avvisi proattivi in condizioni specificate. È anche possibile raccogliere dati personalizzati nel repository centrale per l'esecuzione di query e la visualizzazione.

- **System Center Operations Manager (SCOM)** è destinato alla gestione e al monitoraggio di installazioni cloud di grandi dimensioni. Può essere già noto come strumento di gestione per cloud locali basati su Windows Sever e Hyper-V, ma può anche essere integrato con app Azure e gestirle. Tra l'altro, può installare Application Insights in app attive esistenti. L'eventuale inattività di un'app viene segnalata in pochi secondi.


## <a name="next-steps"></a>Passaggi successivi

- [Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Esempi di implementazione della governance delle sottoscrizioni di Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/).

