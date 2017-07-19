---
title: Procedure consigliate per le aziende che passano ad Azure | Microsoft Docs
description: Viene descritto uno scaffold che le organizzazioni possono usare per garantire un ambiente protetto e gestibile.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: 8692f37e-4d33-4100-b472-a8da37ce628f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: rodend;karlku;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: ce4e19738a66629c7dd587da50f71f6675c159e8
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni
Le organizzazioni stanno adottando sempre di più il cloud pubblico per la sua agilità e la sua flessibilità. Usano i punti di forza del cloud per generare ricavi o ottimizzare le risorse per l'azienda. Microsoft Azure fornisce numerosi servizi che le organizzazioni possono assemblare come blocchi predefiniti per affrontare le esigenze di una vasta gamma di applicazioni e carichi di lavoro. 

Tuttavia, spesso è difficile capire da dove iniziare. Dopo avere deciso di usare Azure, generalmente sorgono alcune domande:

* "Come si soddisfano i requisiti legali soddisfare i requisiti legali per la sovranità dei dati in determinati paesi?"
* "Come è possibile garantire che un utente non apporti inavvertitamente modifiche a un sistema critico?"
* "Come è possibile determinare cosa è supportato da ogni risorsa, in modo da poterlo giustificare e fatturare con precisione?"

La prospettiva di una sottoscrizione vuota e senza barriere .è scoraggiante Questo spazio vuoto può ostacolare la migrazione ad Azure.

In questo articolo viene fornito un punto di partenza per i professionisti tecnici, in modo da aiutarli a soddisfare le esigenze in termini di governance e bilanciarle con la richiesta di agilità. Viene introdotto il concetto di un scaffold enterprise per guidare le organizzazioni nell'implementazione e nella gestione delle loro sottoscrizioni Azure. 

## <a name="need-for-governance"></a>Esigenza di governance
Durante il passaggio ad Azure, è necessario affrontare in anticipo l'argomento della governance per garantire il corretto uso del cloud all'interno dell'organizzazione. Purtroppo, il tempo necessario e le procedure per creare un sistema di governance completo implicano che alcuni gruppi aziendali si rivolgano direttamente ai fornitori senza coinvolgere l'IT aziendale. Se le risorse non vengono gestite correttamente, questo approccio rischia di lasciare l'azienda in balia delle vulnerabilità. Le caratteristiche del cloud pubblico, cioè agilità, flessibilità e prezzi in base al consumo, sono importanti per i gruppi aziendali che devono soddisfare rapidamente le richieste dei clienti (interni ed esterni). Tuttavia, l'IT aziendale deve assicurarsi che i dati e i sistemi siano protetti in modo efficace.

In uno scenario reale, lo scaffolding viene usato per creare la base della struttura. Lo scaffold descrive la struttura generale e fornisce punti di ancoraggio per il montaggio di sistemi permanenti. Un scaffold enterprise è lo stesso: un set di controlli flessibili e funzionalità di Azure che forniscono la struttura per l'ambiente e punti di ancoraggio per servizi basati su cloud pubblico. Fornisce ai generatori (IT e gruppi aziendali) una base per creare e collegare nuovi servizi.

Lo scaffold si basa su procedure raccolte attraverso le varie interazioni con i client di diverse dimensioni. Questi client spaziano da organizzazioni di piccole dimensioni che sviluppano soluzioni nel cloud ad aziende presenti nell'elenco Fortune 500 e fornitori di software indipendenti che stanno eseguendo la migrazione e sviluppando soluzioni nel cloud. Lo scaffold enterprise è costruito appositamente in modo da essere flessibile e supportare i carichi di lavoro IT tradizionali e flessibili; ad esempio, gli sviluppatori che creano applicazioni software come un servizio (SaaS) in base alle funzionalità di Azure.

Lo scaffold enterprise deve essere la base di ogni nuova sottoscrizione all'interno di Azure. Consente agli amministratori di verificare che i carichi di lavoro soddisfino i requisiti minimi di governance di un'organizzazione senza impedire che i gruppi e gli sviluppatori aziendali soddisfino i propri obiettivi.

> [!IMPORTANT]
> La governance è fondamentale per il successo di Azure. In questo articolo è descritta l'implementazione tecnica di uno scaffold enterprise, tuttavia sono illustrati solo il processo più ampio e le relazioni tra i componenti. La governance dei criteri scorre dall'alto verso il basso e dipende dai risultati che l'azienda desidera ottenere. Naturalmente, la creazione di un modello di governance per Azure include rappresentanti dell'IT, ma soprattutto deve prevedere una significativa rappresentanza di leader di gruppo aziendali e gestione di rischi e sicurezza. Fondamentalmente, uno scaffold enterprise consiste nella riduzione dei rischi aziendali per facilitare la missione e gli obiettivi dell'organizzazione.
> 
> 

Nell'immagine seguente vengono descritti i componenti dello scaffold. Le fondamenta sono rappresentate da un saldo piano per reparti, account e sottoscrizioni. Le colonne sono i criteri di Resource Manager e saldi standard di denominazione. Il resto del scaffold proviene dalle principali funzionalità di Azure e dalle funzionalità che garantiscono un ambiente protetto e gestibile.

![componenti dello scaffold](./media/resource-manager-subscription-governance/components.png)

> [!NOTE]
> Azure è cresciuto rapidamente rispetto alla sua introduzione, nel 2008. Questa crescita ha obbligato i team di tecnici Microsoft a ripensare il loro approccio alla gestione e alla distribuzione dei servizi. Il modello di Azure Resource Manager è stato introdotto nel 2014 per sostituire il modello di distribuzione classica. Resource Manager consente alle organizzazioni di distribuire, organizzare e controllare le risorse di Azure con maggiore facilità. Resource Manager include la parallelizzazione quando si creano risorse per una distribuzione più veloce di soluzioni complesse e interdipendenti. Include anche il controllo granulare degli accessi e la possibilità di contrassegnare le risorse con metadati. Microsoft consiglia di creare tutte le risorse tramite il modello di Resource Manager. Lo scaffold enterprise è stato progettato appositamente per il modello di Resource Manager.
> 
> 

## <a name="define-your-hierarchy"></a>Definire la gerarchia
Alle fondamenta dello scaffold sta l'iscrizione enterprise ad Azure insieme al portale aziendale. L'iscrizione enterprise definisce la forma e l'uso dei servizi Azure all'interno di una società e la struttura di governance di base. All'interno del contratto enterprise, i clienti possono suddividere ulteriormente l'ambiente in reparti, account e, infine, sottoscrizioni. Una sottoscrizione Azure è l'unità di base che contiene tutte le risorse. Definisce anche diversi limiti all'interno di Azure, ad esempio il numero di memorie centrali, risorse e così via.

![gerarchia](./media/resource-manager-subscription-governance/agreement.png)

Ogni azienda è diversa e la gerarchia nell'immagine precedente consente una notevole flessibilità nell'organizzazione di Azure all'interno della società. Prima di implementare le linee guida contenute in questo documento, è necessario modellare la gerarchia e comprendere l'impatto su fatturazione, accesso alle risorse e complessità.

I tre modelli comuni per le iscrizioni ad Azure sono:

* Il modello **funzionale**
  
    ![functional](./media/resource-manager-subscription-governance/functional.png)
* Il modello **business unit** 
  
    ![business](./media/resource-manager-subscription-governance/business.png)
* Il modello **geografico**
  
    ![geografico](./media/resource-manager-subscription-governance/geographic.png)

Lo scaffold viene applicato a livello di sottoscrizione per estendere i requisiti di governance dell'organizzazione nella sottoscrizione.

## <a name="naming-standards"></a>Standard di denominazione
Il primo pilastro dello scaffolding è lo standard di denominazione. Standard di denominazione ben progettati consentono di identificare le risorse nel portale, in una fattura e all'interno degli script. È molto probabile che si disponga già di standard di denominazione per un'infrastruttura locale. Quando si aggiunge Azure all'ambiente, è opportuno estendere tali standard di denominazione alle risorse di Azure. Uno standard di denominazione consente una più efficace gestione dell'ambiente a tutti i livelli.

> [!TIP]
> Per le convenzioni di denominazione:
> * Esaminare [Patterns and Practices guidance (Linee guida su modelli e procedure)](../guidance/guidance-naming-conventions.md) e adottarle dove possibile. Queste linee guida consentono di stabilire uno standard di denominazione significativo.
> * Usare il sistema camelCasing per i nomi delle risorse (ad esempio myResourceGroup e vnetNetworkName). Nota: esistono determinate risorse, ad esempio gli account di archiviazione, in cui l'unica opzione consiste nell'usare lettere minuscole (e nessun altro carattere speciale).
> * È consigliabile usare i criteri di Azure Resource Manager (descritti nella sezione successiva) per applicare gli standard di denominazione.
> 
> I suggerimenti precedenti consentono di implementare una convenzione di denominazione coerente.

## <a name="policies-and-auditing"></a>Criteri e controllo
Il secondo pilastro dello scaffold comporta la creazione di [criteri di Azure Resource Manager](resource-manager-policy.md) e il [controllo del log attività](resource-group-audit.md). I criteri di Resource Manager consentono di gestire i rischi in Azure. È possibile definire criteri in grado di garantire la sovranità dei dati attraverso la limitazione, l'applicazione o il controllo di determinate azioni. 

* Per impostazione predefinita, i criteri sono un sistema **allow**. Le azioni vengono controllate attraverso la definizione e l'assegnazione di criteri a risorse che negano o controllano le azioni sulle risorse.
* I criteri sono descritti dalle relative definizioni in un linguaggio di definizione dei criteri (condizioni if-then).
* I criteri vengono creati attraverso file con formattazione JSON (Javascript Object Notation). Dopo la definizione, un criterio viene assegnato a un determinato ambito: sottoscrizione, gruppo di risorse o risorsa.

I criteri hanno diverse azioni che consentono un approccio più granulare agli scenari. Le azioni sono:

* **Nega**: blocca la richiesta di risorse
* **Controlla**: consente la richiesta ma aggiunge una riga al log attività da usare per fornire avvisi o attivare runbook
* **Aggiungi**: aggiunge informazioni specificate alla risorsa. Ad esempio, se non è presente un tag "CostCenter" in una risorsa, aggiungere il tag con un valore predefinito.

### <a name="common-uses-of-resource-manager-policies"></a>Uso comune dei criteri di Resource Manager
I criteri di Azure Resource Manager sono un potente strumento del toolkit di Azure. Consentono di evitare costi imprevisti, per identificare un centro di costo per le risorse tramite l'assegnazione di tag e per verificare che siano soddisfatti i requisiti di conformità. Quando i criteri vengono combinati con le funzionalità di controllo incorporate, è possibile personalizzare soluzioni complesse e flessibile. I criteri consentono alle società di fornire controlli per i carichi di lavoro di tipo "IT tradizionale" e "Agile", come lo sviluppo delle applicazioni dei clienti. I modelli più diffusi che vediamo per i criteri sono:

* **Conformità geografica/sovranità dei dati**: Azure fornisce le aree geografiche di tutto il mondo. Le aziende spesso desiderano sapere dove vengono create le risorse (per garantire la sovranità dei dati oppure semplicemente assicurarsi che le risorse vengano create vicino ai clienti finali delle risorse).
* **Gestione dei costi**: una sottoscrizione Azure può contenere risorse di diversi tipi e scalabilità. Le aziende spesso desiderano garantire che le sottoscrizioni standard evitino di usare risorse inutilmente grandi, che possono costare centinaia di dollari al mese o anche di più.
* **Governance predefinita attraverso tag obbligatori**: i tag obbligatori sono fra le funzionalità più comuni e più richieste. Grazie ai criteri di Azure Resource Manager, le aziende possono assicurarsi che a una risorsa venga applicato il tag corretto. I tag più comuni sono: reparto, proprietario della risorsa e tipo ambiente (ad esempio, produzione, test e sviluppo)

**esempi**

Sottoscrizione di tipo "IT tradizionale" per le applicazioni line of business

* Applicare tag relativi a reparto e proprietario in tutte le risorse
* Limitare la creazione di risorse all'area dell'America del Nord
* Limitare la possibilità di creare HDInsight e VM G-Series

Ambiente di tipo "Agile" per una business unit che crea applicazioni cloud

* Per soddisfare requisiti di sovranità dei dati, consentire la creazione di risorse SOLO in un'area geografica specifica.
* Applicare i tag di ambiente in tutte le risorse. Se una risorsa viene creata senza tag, aggiungere il tag **Ambiente: sconosciuto** alla risorsa.
* Controllare quando vengono create risorse al di fuori dell'America del Nord ma non impedirle.
* Controllare quando vengono create risorse dal costo elevato.

> [!TIP]
> L'uso più comune di criteri di Resource Manager nelle organizzazioni consiste nel controllare *dove* è possibile creare le risorse e *quali* tipi di risorse è possibile creare. Oltre a fornire controlli su *dove* e *quali* tipi, molte aziende usano criteri per garantire che le risorse abbiano i metadati appropriati per la fatturazione del consumo. Si consiglia di applicare criteri a livello di sottoscrizione per:
> 
> * Conformità dei dati/sovranità dei dati
> * Gestione dei costi
> * Tag obbligatori (in base alle esigenze aziendali, ad esempio BillTo, proprietario dell'applicazione)
> 
> È possibile applicare altri criteri a livelli inferiori di ambito.
> 
> 

### <a name="audit---what-happened"></a>Controllo: cos'è successo?
Per visualizzare il funzionamento dell'ambiente, è necessario controllare l'attività dell'utente. La maggior parte dei tipi di risorse all'interno di Azure crea log di diagnostica che è possibile analizzare tramite uno strumento di log o in Azure Operations Management Suite. È possibile raccogliere log attività tra più sottoscrizioni per fornire una visualizzazione a livello di reparto o di azienda. I record di controllo sono allo stesso tempo un importante strumento di diagnostica e un meccanismo fondamentale per attivare gli eventi nell'ambiente di Azure.

I log attività da distribuzioni Resource Manager consentono di determinare le **operazioni** che hanno avuto luogo e chi le ha eseguite. I log attività possono essere raccolti e aggregati tramite  strumenti quali Log Analytics.

## <a name="resource-tags"></a>Tag delle risorse
Man mano che gli utenti all'interno dell'organizzazione aggiungono risorse alla sottoscrizione, diventa sempre più importante associare le risorse al reparto, all'ambiente e ai clienti giusti. È possibile allegare metadati alle risorse tramite [tag](resource-group-using-tags.md). Usare tag per fornire informazioni su risorsa o proprietario. I tag consentono non solo di aggregare e raggruppare le risorse in vari modi, ma anche di usare i dati a scopo di chargeback. È possibile assegnare tag alle risorse con un massimo di 15 coppie di chiave:valore. 

I tag delle risorse sono flessibili e dovrebbero essere allegati alla maggior parte delle risorse. Esempi di tag di risorse comuni sono:

* BillTo
* Reparto (o Business unit)
* Ambiente (Produzione, Fase, Sviluppo)
* Livello (Livello Web, Livello di applicazione)
* Proprietario dell'applicazione
* ProjectName

![tags](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Per altri esempi di tag, vedere [Recommended Naming Conventions for Azure Resources](../guidance/guidance-naming-conventions.md) (Convenzioni di denominazione consigliate per le risorse di Azure).

> [!TIP]
> Si consiglia di creare un criterio che ordini l'assegnazione di tag per:
> 
> * Gruppi di risorse
> * Archiviazione
> * Macchine virtuali
> * Ambienti di servizi delle applicazioni/server Web
> 
> Questa strategia di assegnazione dei tag identifica all'interno delle sottoscrizioni quali metadati servono per azienda, finanze, sicurezza, gestione dei rischi e gestione globale dell'ambiente. 

## <a name="resource-group"></a>Resource group
Resource Manager consente di inserire le risorse in gruppi significativi per gestione, fatturazione o affinità naturale. Come accennato in precedenza, Azure offre due modelli di distribuzione. Nel modello di distribuzione classica, quello precedente, l'unità di base per la gestione era la sottoscrizione. Era difficile suddividere le risorse all'interno di una sottoscrizione e questo comportava la creazione di un numero elevato di sottoscrizioni. Con il modello di Resource Manager, sono stati introdotti i gruppi di risorse. I gruppi di risorse sono contenitori di risorse che hanno un ciclo di vita comune o condividono un attributo, ad esempio "tutti i server SQL" o "Applicazione A".

I gruppi di risorse non possono essere contenuti in un altro gruppo di risorse e le risorse possono appartenere a un solo gruppo di risorse. Determinate azioni possono essere applicate a tutte le risorse di un gruppo di risorse. Ad esempio, l'eliminazione di un gruppo di risorse comporta la rimozione di tutte le risorse all'interno del gruppo di risorse. In genere, un'intera applicazione o il sistema correlato vengono posizionati nello stesso gruppo di risorse. Ad esempio, un'applicazione a tre livelli chiamata applicazione Web di Contoso conterrebbe il server Web, il server applicazioni e il server SQL nello stesso gruppo di risorse.

> [!TIP]
> La modalità di organizzazione dei gruppi di risorse può variare da carichi di lavoro di tipo "IT tradizionale" a carichi di lavoro di tipo "IT agile":
> 
> * I carichi di lavoro di tipo "IT tradizionale" sono più comunemente raggruppati in base agli elementi all'interno di uno stesso ciclo di vita, ad esempio un'applicazione. Il raggruppamento in base all'applicazione consente la gestione di singole applicazioni.
> * I carichi di lavoro di tipo "IT agile" tendono a concentrarsi su applicazioni cloud orientate ai clienti esterni. I gruppi di risorse devono riflettere i livelli di distribuzione (ad esempio livello Web, un livello di app) e la gestione.
> 
> Conoscere il carico di lavoro aiuta a sviluppare una strategia di gruppo di risorse.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Probabilmente ci si sta domandando chi deve avere accesso alle risorse e come è possibile controllare tale accesso. È fondamentale consentire o impedire l'accesso al Portale di Azure e controllare l'accesso alle risorse del portale. 

Quando Azure è stato originariamente rilasciato, i controlli degli accessi a una sottoscrizione erano elementari: Amministratore o Coamministratore. L'accesso a una sottoscrizione nel modello classico implicava l'accesso a tutte le risorse del portale. Questa mancanza di un controllo granulare ha portato alla proliferazione di sottoscrizioni per fornire un livello ragionevole di controllo degli accessi per un'iscrizione ad Azure.

La proliferazione di sottoscrizioni non è più necessaria. Con il controllo degli accessi in base al ruolo, è possibile assegnare ruoli standard agli utenti (ad esempio tipi di ruoli comuni come "lettore" e "scrittore"). È inoltre possibile definire ruoli personalizzati.

> [!TIP]
> Per implementare il controllo degli accessi in base al ruolo:
> * Connettere l'archivio identità aziendale (più comunemente detto Active Directory) ad Azure Active Directory tramite lo strumento AD Connect.
> * Controllare chi è l'amministratore/il coamministratore di una sottoscrizione tramite un'identità gestita. **Non** assegnare il ruolo di amministratore o coamministratore a un nuovo proprietario di sottoscrizione. Usare invece i ruoli RBAC per fornire i diritti di **proprietario** a un gruppo o a un singolo utente.
> * Aggiungere gli utenti di Azure a un gruppo (ad esempio, Proprietari dell'applicazione X) in Active Directory. Utilizzare il gruppo sincronizzato per fornire ai membri del gruppo i diritti appropriati per gestire il gruppo di risorse contenente l'applicazione.
> * Seguire il principio di concedere il **privilegio minimo** necessario per svolgere il lavoro previsto. Ad esempio:
>   * Gruppo di distribuzione: un gruppo che può solo distribuire risorse.
>   * Gestione delle macchine virtuali: un gruppo che può riavviare le VM (per le operazioni)
> 
> Questi suggerimenti aiutano a gestire l'accesso utente nella sottoscrizione.

## <a name="azure-resource-locks"></a>Blocchi per le risorse di Azure
Man mano che l'organizzazione aggiunge servizi di base alla sottoscrizione, diventa sempre più importante garantire che tali servizi siano disponibili per evitare l'interruzione delle attività aziendali. I [blocchi per le risorse](resource-group-lock-resources.md) consentono di limitare le operazioni sulle risorse di valore elevato in cui la modifica o l'eliminazione potrebbe avere un impatto significativo sulle applicazioni o sull'infrastruttura del cloud. È possibile applicare blocchi a una sottoscrizione, a un gruppo di risorse o a una risorsa. Generalmente, i blocchi vengono applicati a risorse fondamentali come reti virtuali, gateway e account di archiviazione. 

I blocchi di risorse attualmente supportano due valori: CanNotDelete e ReadOnly. CanNotDelete significa che gli utenti con i diritti appropriati possono continuare a leggere o a modificare una risorsa ma non possono eliminarla. ReadOnly significa che gli utenti autorizzati non possono eliminare o modificare una risorsa.

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`.
Fra i ruoli predefiniti, solo a Proprietario e Amministratore Accesso utenti sono concesse tali azioni.

> [!TIP]
> Le opzioni di rete di base devono essere protette con blocchi. L'eliminazione accidentale di una VPN gateway da sito a sito sarebbe disastrosa per una sottoscrizione di Azure. Azure non consente di eliminare una rete virtuale in uso, tuttavia sarebbe una precauzione utile applicare più restrizioni. 
> 
> * Rete virtuale: CanNotDelete
> * Gruppo di sicurezza di rete: CanNotDelete
> * Criteri: CanNotDelete
> 
> Anche i criteri sono fondamentali per la manutenzione dei controlli appropriati. Si consiglia di applicare un blocco **CanNotDelete** ai criteri in uso.

## <a name="core-networking-resources"></a>Risorse di rete di base
È possibile accedere alle risorse dall'interno (nella rete di un'azienda) o dall'esterno (tramite Internet). Può capitare che gli utenti interni all'organizzazione inseriscano involontariamente le risorse nel punto sbagliato e le rendano vulnerabili all'accesso di utenti malintenzionati. Come con i dispositivi locali, le aziende devono aggiungere i controlli appropriati per assicurarsi che gli utenti di Azure prendano le giuste decisioni. Per la governance delle sottoscrizioni, vengono identificate le risorse principali che garantiscono il controllo di base degli accessi. Le risorse principali sono:

* **Reti virtuali**, cioè oggetti contenitore per le subnet. Sebbene non sia strettamente necessario, vengono spesso usate per la connessione delle applicazioni alle risorse aziendali interne.
* **Gruppi di sicurezza di rete**, che sono simili a un firewall e specificano le regole con cui una risorsa può comunicare sulla rete. Forniscono un controllo granulare su come (o se) una subnet (o una macchina virtuale) può connettersi a Internet o ad altre subnet della stessa rete virtuale.

![rete di base](./media/resource-manager-subscription-governance/core-network.png)

> [!TIP]
> Per le reti:
> * Creare reti virtuali dedicate ai carichi di lavoro orientati all'esterno o all'interno. Questo approccio riduce il rischio di posizionare inavvertitamente in uno spazio esterno le macchine virtuali destinate ai carichi di lavoro interni.
> * Configurare gruppi di sicurezza di rete per limitare l'accesso. È necessario almeno bloccare l'accesso a Internet da reti virtuali interne e l'accesso alla rete aziendale da reti virtuali esterne.
> 
> Questi suggerimenti aiutano a implementare risorse di rete sicure.

### <a name="automation"></a>Automazione
La gestione singola delle risorse è sia dispendiosa in termini di tempo che potenzialmente soggetta a errori per certe operazioni. Azure offre varie funzionalità di automazione tra cui Automazione di Azure, App per la logica e Funzioni di Azure. [Automazione di Azure](../automation/automation-intro.md) consente agli amministratori di creare e definire i runbook per gestire attività comuni nella gestione delle risorse. I runbook vengono creati attraverso un edito di codice PowerShell o un editor grafico. È possibile creare flussi di lavoro complessi in più fasi. Automazione di Azure è spesso usata per gestire attività comuni quali l'arresto di risorse inutilizzate o la creazione di risorse in risposta a un trigger specifico senza bisogno dell'intervento umano.

> [!TIP]
> Per l'automazione:
> * Creare un account di Automazione di Azure ed esaminare i runbook disponibili (sia grafici che della riga di comando), che è possibile trovare in [Runbook Gallery (Raccolta di runbook)](../automation/automation-runbook-gallery.md).
> * Importare e personalizzare i runbook chiave per l'uso personale.
> 
> Uno scenario comune è la possibilità di avviare/arrestare macchine virtuali in una pianificazione. Alcuni runbook di esempio disponibili nella raccolta consentono sia di gestire questo scenario che di spiegare come espanderlo.
> 
> 

## <a name="azure-security-center"></a>Centro sicurezza di Azure
Forse uno dei principali ostacoli per l'adozione del cloud è rappresentato dalle preoccupazioni legate alla sicurezza. I responsabili della gestione dei costi dell'IT e i reparti della sicurezza devono assicurarsi che le risorse in Azure siano al sicuro. 

Il [Centro sicurezza di Azure](../security-center/security-center-intro.md) fornisce un punto di vista centralizzato dello stato della sicurezza delle risorse nelle sottoscrizioni e indicazioni che aiutano a evitare di compromettere le risorse. Può consentire criteri più granulari (ad esempio, l'applicazione di criteri a gruppi di risorse specifici che consentono all'azienda di adattare il loro modo di reagire ai rischi che stanno affrontando). Infine, il Centro sicurezza di Azure è una piattaforma aperta che consente ai partner Microsoft e ai fornitori di software indipendenti di creare un software che si inserisce nel Centro sicurezza di Azure per migliorarne le funzionalità. 

> [!TIP]
> Il Centro sicurezza di Azure è abilitato per impostazione predefinita in ogni sottoscrizione. Tuttavia, è necessario abilitare la raccolta di dati dalle macchine virtuali per consentire al Centro sicurezza di Azure di installare l'agente e avviare la raccolta di dati.
> 
> ![Raccolta dei dati](./media/resource-manager-subscription-governance/data-collection.png)
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Ora che sono state acquisite informazioni sulla governance delle sottoscrizioni, è il momento di vedere l'applicazione pratica di questi consigli. Vedere [Examples of implementing Azure subscription governance](resource-manager-subscription-examples.md) (Esempi di implementazione della governance delle sottoscrizioni di Azure).


