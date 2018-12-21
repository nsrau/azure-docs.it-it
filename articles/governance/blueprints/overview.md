---
title: Panoramica di Azure Blueprint
description: Azure Blueprint è un servizio disponibile in Azure usato per creare, definire e distribuire artefatti nell'ambiente Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/05/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: f1ebbc10109563b771c5417a0449efec12138526
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967692"
---
# <a name="what-is-azure-blueprints"></a>Informazioni su Azure Blueprint

Così come un progetto consente a un ingegnere o un architetto di tracciare i parametri di progettazione, Azure Blueprint consente agli architetti cloud e ai gruppi centrali del reparto IT di definire un set ripetibile di risorse di Azure che implementa ed è conforme a standard, criteri e requisiti di un'organizzazione. Azure Blueprint consente ai team di sviluppo di creare e realizzare rapidamente nuovi ambienti sapendo che vengono creati in conformità con l'organizzazione con un set di componenti integrati, ad esempio reti, per velocizzare lo sviluppo e il recapito.

I progetti costituiscono un metodo dichiarativo per orchestrare la distribuzione di vari modelli di risorse e altri artefatti, ad esempio:

- Assegnazioni di ruoli
- Assegnazioni di criteri
- Modelli di Gestione risorse di Azure
- Gruppi di risorse

Il servizio Azure Blueprints è supportato da [Azure Cosmos DB](../../cosmos-db/introduction.md) distribuito a livello globale.
Gli oggetti del progetto vengono replicati in più aree di Azure. Questa replica fornisce bassa latenza, disponibilità elevata e accesso coerente agli oggetti del progetto, indipendentemente dall'area in cui Blueprints distribuisce le risorse.

## <a name="how-its-different-from-resource-manager-templates"></a>In cosa differisce dai modelli di Resource Manager

Il servizio è progettato per facilitare la _configurazione dell'ambiente_. Questa configurazione spesso è costituita da un set di gruppi di risorse, criteri, assegnazioni di ruolo e distribuzioni dei modelli di Resource Manager. Un progetto è un pacchetto che riunisce ognuno di questi tipi di _artefatti_ e consente di comporre e indicare la versione di tale pacchetto, anche tramite una pipeline CI/CD. In definitiva, ognuno viene assegnato a una sottoscrizione in una singola operazione che può essere controllata e monitorata.

Quasi tutto ciò che si desidera includere per la distribuzione nei progetti può essere eseguito con un modello di Resource Manager. Tuttavia, un modello di Resource Manager è un documento che non esiste in modo nativo in Azure: ognuno viene archiviato in locale o nel controllo del codice sorgente. Il modello viene usato per le distribuzioni di una o più risorse di Azure, ma dopo la distribuzione di tali risorse non c'è una connessione o relazione attiva con il modello.

Con i progetti, la relazione tra la definizione di progetto (_cosa_ distribuire) e l'assegnazione di progetto (_cosa è stato_ distribuito) viene mantenuta. Questa connessione supporta un migliore monitoraggio e controllo delle distribuzioni. Blueprint può anche aggiornare più sottoscrizioni contemporaneamente regolate dallo stesso progetto.

Non è necessario scegliere tra un modello di Resource Manager e un progetto. Ogni progetto può essere costituito da zero o più _artefatti_ dei modelli di Resource Manager. Ciò significa che il lavoro richiesto in precedenza per sviluppare e gestire una libreria di modelli di Resource Manager può essere riusato nei progetti.

## <a name="how-its-different-from-azure-policy"></a>In cosa differisce dai Criteri di Azure

Un progetto è un pacchetto o un contenitore per la composizione di specifici set di standard, criteri e requisiti correlati all'implementazione dei servizi cloud, della sicurezza e della progettazione di Azure, che può essere riusato per garantire coerenza e conformità.

I [criteri](../policy/overview.md) costituiscono un sistema predefinito di autorizzazione e negazione esplicita che mira alle proprietà delle risorse durante la distribuzione e viene usato per le risorse già esistenti. I criteri supportano la governance cloud verificando che le risorse all'interno di una sottoscrizione siano conformi a standard e requisiti.

L'inclusione di criteri in un progetto consente non solo di creare il criterio o la progettazione corretta durante l'assegnazione del progetto, ma assicura anche che nell'ambiente vengano eseguite solo le modifiche approvate o previste, in modo da garantire la conformità continua con la finalità del progetto.

I criteri possono essere inclusi nella definizione di un progetto come i vari _artefatti_. In Azure Blueprint è inoltre supportato l'uso di parametri con criteri e iniziative.

## <a name="blueprint-definition"></a>Definizione di progetto

Un progetto è costituito da _artefatti_. Azure Blueprint supporta attualmente le seguenti risorse come artefatti:

|Risorsa  | Opzioni della gerarchia| Descrizione  |
|---------|---------|---------|
|Gruppi di risorse     | Sottoscrizione | Creare un nuovo gruppo di risorse per l'uso da parte di altri artefatti nel progetto.  Questi gruppi di risorse segnaposto consentono di organizzare le risorse strutturandole esattamente nel modo desiderato e forniscono un limitatore di ambito per i criteri e gli artefatti di assegnazione dei ruoli inclusi, nonché per i modelli di Azure Resource Manager.         |
|Modello di Azure Resource Manager      | Sottoscrizione, gruppo di risorse | I modelli vengono usati per creare ambienti complessi. Esempi di ambienti complessi sono: una farm di SharePoint, la configurazione dello stato di Automazione di Azure o un'area di lavoro di Log Analytics. |
|Assegnazione dei criteri     | Sottoscrizione, gruppo di risorse | Consente di assegnare criteri o iniziative alla sottoscrizione a cui è assegnato il progetto. I criteri o iniziative devono rientrare nell'ambito del progetto (nel gruppo di gestione del progetto o sotto). Se i criteri o le iniziative includono dei parametri, questi vengono assegnati al momento della creazione del progetto o durante l'assegnazione dello stesso.       |
|Assegnazione di ruolo   | Sottoscrizione, gruppo di risorse | Aggiungere un utente o gruppo esistente a un ruolo predefinito per assicurarsi che gli utenti corretti possano sempre accedere correttamente alle risorse. Le assegnazioni di ruolo possono essere definite per l'intera sottoscrizione o annidate in un gruppo di risorse specifiche incluso nel progetto. |

### <a name="blueprints-and-management-groups"></a>Progetti e gruppi di gestione

Durante la creazione di una definizione di progetto, viene definita la posizione di salvataggio del progetto. Attualmente, i progetti possono essere salvati solo in un [gruppo di gestione](../management-groups/overview.md) a cui si ha accesso come **collaboratore**. Il progetto è disponibile per l'assegnazione a qualsiasi sottoscrizione figlio del gruppo di gestione.

> [!IMPORTANT]
> Se non si ha accesso a un gruppo di gestione esistente o configurato, il caricamento dell'elenco delle definizioni di progetto mostrerà che nessun gruppo è disponibile. Facendo clic su **Ambito** si aprirà una finestra con un avviso relativo al recupero dei gruppi di gestione. Per risolvere questo problema, assicurarsi che la sottoscrizione a cui si ha accesso faccia parte del [gruppo di gestione](../management-groups/overview.md).

### <a name="blueprint-parameters"></a>Parametri di progetto

Azure Blueprint può passare i parametri a criteri o iniziative oppure a un modello di Azure Resource Manager.
Quando si aggiunge un _artefatto_ a un progetto, l'autore decide se fornire un valore definito per ogni assegnazione di progetto o se consentire a ogni assegnazione di progetto di fornire un valore in fase di assegnazione. Questa flessibilità consente di definire un valore predeterminato per tutti gli usi del progetto o per consentire che tale decisione venga presa al momento dell'assegnazione.

> [!NOTE]
> Un progetto può avere i propri parametri, ma al momento questi possono essere creati solo se un progetto è generato dall'API REST anziché tramite il portale.

Per altre informazioni, vedere [Parametri di progetto](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Pubblicazione di progetti

Quando si crea un progetto per la prima volta, questo viene considerato in modalità **bozza**. Quando è pronto per l'assegnazione, deve essere **pubblicato**. La pubblicazione richiede la definizione di una stringa **versione** (lettere, numeri e trattini con una lunghezza massima di 20 caratteri) insieme a **note di modifica** facoltative. La stringa **versione** consente di distinguere il progetto dalle modifiche future apportate allo stesso e di assegnare ciascuna versione. Questo controllo delle versioni significa anche che è possibile assegnare diverse **versioni** dello stesso progetto alla stessa sottoscrizione. Quando al progetto vengono apportate ulteriori modifiche, la **versione** **pubblicata** esiste ancora, e si aggiunge alle **modifiche non pubblicate**. Una volta ultimate le modifiche, il progetto aggiornato viene **pubblicato** con una nuova **versione** univoca e può essere ora assegnato.

## <a name="blueprint-assignment"></a>Assegnazione progetto

Ciascuna **versione** **pubblicata** di un progetto può essere assegnata a una sottoscrizione esistente. Nel portale il progetto imposta la **versione** **pubblicata** più di recente come predefinita. Se sono presenti parametri dell'artefatto (o parametri di progetto), questi vengono definiti durante il processo di assegnazione.

## <a name="permissions-in-azure-blueprints"></a>Autorizzazioni in Azure Blueprint

Per usare i progetti è necessario essere autorizzati tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Per creare i progetti l'account necessita delle seguenti autorizzazioni:

- `Microsoft.Blueprint/blueprints/write` - Creare una definizione di progetto
- `Microsoft.Blueprint/blueprints/artifacts/write` - Creare artefatti su una definizione di progetto
- `Microsoft.Blueprint/blueprints/versions/write` - Pubblicare un progetto

Per eliminare i progetti l'account necessita delle seguenti autorizzazioni:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Dato che le definizioni di progetto vengono create in un gruppo di gestione, le autorizzazioni per la definizione di progetto devono essere concesse nell'ambito di un gruppo di gestione o essere ereditate nell'ambito di un gruppo di gestione.

Per assegnare o annullare l'assegnazione di un progetto l'account necessita delle seguenti autorizzazioni:

- `Microsoft.Blueprint/blueprintAssignments/write` - Assegnare un progetto
- `Microsoft.Blueprint/blueprintAssignments/delete` - Annullare l'assegnazione di un progetto

> [!NOTE]
> Dato che le assegnazioni di progetto vengono create in una sottoscrizione, le autorizzazioni per assegnare o annullare l'assegnazione devono essere concesse nell'ambito della sottoscrizione o essere ereditate nell'ambito della sottoscrizione.

Tutte le autorizzazioni precedenti sono incluse nel ruolo **Proprietario**. Il ruolo **Collaboratore** dispone delle autorizzazioni per creare ed eliminare progetti, ma non delle autorizzazioni per l'assegnazione di progetti. Se questi ruoli predefiniti non soddisfano specifiche esigenze di sicurezza, provare a creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Per poter abilitare la distribuzione, l'entità servizio per Azure Blueprint richiede il ruolo **proprietario** nella sottoscrizione assegnata. Se si usa il portale, questo ruolo viene automaticamente concesso e revocato per la distribuzione. Se si usa l'API REST, questo ruolo deve essere concesso manualmente, ma viene comunque revocato automaticamente al termine della distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un progetto - Portale](create-blueprint-portal.md)
- [Creare un progetto - API REST](create-blueprint-rest-api.md)
