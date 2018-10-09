---
title: Panoramica di Azure Blueprint
description: Azure Blueprint è un servizio disponibile in Azure usato per creare, definire e distribuire artefatti nell'ambiente Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c7ffbe86407bc776870890e5b4151556f572832e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957514"
---
# <a name="what-is-azure-blueprints"></a>Informazioni su Azure Blueprint

Così come un progetto consente a un ingegnere o un architetto di tracciare i parametri di progettazione, Azure Blueprint consente agli architetti cloud e al dipartimento IT centrale di definire un set ripetibile di risorse di Azure che implementa ed è conforme a standard, criteri e requisiti di un'organizzazione. Azure Blueprint consente ai team di sviluppo di eseguire rapidamente il provisioning e di realizzare nuovi ambienti sapendo che vengono creati in conformità con l'organizzazione e che contengono un set di componenti integrati, ad esempio reti, per velocizzare lo sviluppo e il recapito.

I progetti costituiscono un metodo dichiarativo per orchestrare la distribuzione di più modelli di risorse e altri artefatti, ad esempio:

- Assegnazioni di ruoli
- Assegnazioni di criteri
- Modelli di Gestione risorse di Azure
- Gruppi di risorse

## <a name="how-it-is-different-from-resource-manager-templates"></a>In cosa differisce dai modelli di Resource Manager

Azure Blueprint è concepito per facilitare la _configurazione dell'ambiente_, spesso costituito da un set di gruppi di risorse, criteri e assegnazioni di ruolo, oltre che dalle distribuzioni dei modelli di Resource Manager. Un progetto è un pacchetto che riunisce ognuno di questi tipi di _artefatti_ e consente di comporre e indicare la versione di tale pacchetto, anche tramite una pipeline CI/CD. In definitiva, ognuno viene assegnato a una sottoscrizione in una singola operazione che può essere controllata e monitorata.

Quasi tutto ciò che si desidera includere per la distribuzione nei progetti può essere eseguito con un modello di Resource Manager. Tuttavia, un modello di Resource Manager è un documento che non esiste in modo nativo in Azure: ognuno viene archiviato in locale o nel controllo del codice sorgente. Il modello viene usato per le distribuzioni di una o più risorse di Azure, ma dopo la distribuzione di tali risorse viene persa la connessione e la relazione con il modello utilizzato.

Con i progetti, la relazione tra la definizione di progetto (_cosa_ distribuire) e l'assegnazione di progetto (_cosa è stato_ distribuito) viene mantenuta. Questa connessione consente di migliorare monitoraggio e controllo delle distribuzioni, di eseguire l'aggiornamento simultaneo di più sottoscrizioni soggette allo stesso progetto e molto altro.

Non è necessario scegliere tra un modello di Resource Manager e un progetto. Ogni progetto può essere costituito da zero o più _artefatti_ dei modelli di Resource Manager. Ciò significa che il lavoro richiesto in precedenza per sviluppare e gestire una libreria di modelli di Resource Manager può essere svolto nei progetti.

## <a name="how-it-is-different-from-azure-policy"></a>In cosa differisce dai Criteri di Azure

Un progetto è un pacchetto o un contenitore per la composizione di specifici set di standard, criteri e requisiti correlati all'implementazione dei servizi cloud, della sicurezza e della progettazione di Azure, e che può essere riutilizzato per garantire coerenza e conformità.

I [criteri](../policy/overview.md) costituiscono un sistema predefinito di autorizzazione e negazione esplicita che mira alle proprietà delle risorse durante la distribuzione e viene usato per le risorse già esistenti. I criteri offrono supporto per la governance IT assicurando che le risorse all'interno di una sottoscrizione siano conformi a standard e requisiti.

L'inclusione di criteri in un progetto consente non solo di creare la corretta regola o progettazione durante l'assegnazione del progetto, ma assicura anche che nell'ambiente vengano eseguite solo le modifiche approvate o previste, in modo da assicurare la conformità continua con la finalità del progetto.

I criteri possono essere inclusi nella definizione di un progetto come i vari _artefatti_. In Azure Blueprint è inoltre supportato l'uso di parametri con criteri e iniziative.

## <a name="blueprint-definition"></a>Definizione di progetto

Un progetto è costituito da _artefatti_. Azure Blueprint supporta attualmente le seguenti risorse come artefatti:

|Risorsa  | Opzioni della gerarchia| DESCRIZIONE  |
|---------|---------|---------|
|Gruppi di risorse     | Sottoscrizione | Creare un nuovo gruppo di risorse per l'uso da parte di altri artefatti nel progetto.  Questi gruppi di risorse segnaposto consentono di organizzare le risorse strutturandole esattamente nel modo desiderato e forniscono un limitatore di ambito per i criteri e gli artefatti di assegnazione dei ruoli inclusi, nonché per i modelli di Azure Resource Manager.         |
|Modello di Azure Resource Manager      | Gruppo di risorse | Questi modelli possono essere usati per comporre ambienti complessi quali una farm di SharePoint, la configurazione dello stato di Automazione di Azure o un'area di lavoro di Log Analytics. |
|Assegnazione dei criteri     | Sottoscrizione, gruppo di risorse | Consente di assegnare criteri o iniziative al gruppo di gestione o alla sottoscrizione cui il progetto è assegnato. I criteri o iniziative devono rientrare nell'ambito del progetto (nel gruppo di gestione del progetto o sotto). Se i criteri o iniziative includono dei parametri, questi possono essere assegnati al momento della creazione del progetto o durante l'assegnazione dello stesso.       |
|Assegnazione di ruolo   | Sottoscrizione, gruppo di risorse | Aggiungere un utente o gruppo esistente a un ruolo predefinito per assicurarsi che gli utenti corretti possano sempre accedere correttamente alle risorse. Le assegnazioni di ruolo possono essere definite per l'intera sottoscrizione o annidate in un gruppo di risorse specifiche incluso nel progetto. |

### <a name="blueprints-and-management-groups"></a>Progetti e gruppi di gestione

Durante la creazione di una definizione di progetto, viene definita la posizione di salvataggio del progetto. Attualmente, i progetti possono essere salvati solo in un [gruppo di gestione](../management-groups/overview.md) a cui si ha accesso come **collaboratore**. Il progetto sarà quindi disponibile per l'assegnazione a qualsiasi figlio (gruppo di gestione o sottoscrizione) di tale gruppo di gestione.

> [!IMPORTANT]
> Se non si dispone dell'accesso a nessun gruppo di gestione esistente o configurato, il caricamento dell'elenco delle definizioni di progetto mostrerà che nessun gruppo è disponibile. Facendo clic su **Ambito** si aprirà una finestra con un avviso riguardante il recupero dei gruppi di gestione. Per risolvere questo problema, assicurarsi che la sottoscrizione a cui si ha accesso faccia parte del [gruppo di gestione](../management-groups/overview.md).

### <a name="blueprint-parameters"></a>Parametri di progetto

Azure Blueprint può passare i parametri a criteri o iniziative oppure a un modello di Azure Resource Manager.
Quando un _artefatto_ viene aggiunto a un progetto, l'autore può decidere se fornire un valore definito per ogni assegnazione di progetto o se consentire a ogni assegnazione di progetto di fornire un valore in fase di assegnazione. Questa flessibilità consente di definire un valore predeterminato per tutti gli usi del progetto o per consentire che tale decisione venga presa al momento dell'assegnazione.

> [!NOTE]
> Un progetto può avere i propri parametri, ma al momento questi possono essere creati solo se un progetto è generato dall'API REST anziché tramite il portale.

Per altre informazioni, vedere [Parametri di progetto](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Pubblicazione di progetti

Quando si crea un progetto per la prima volta, questo viene considerato in modalità **bozza**. Quando è pronto per l'assegnazione, deve essere **pubblicato**. La pubblicazione richiede la definizione di una stringa **versione** (lettere, numeri e trattini con una lunghezza massima di 20 caratteri) accompagnata da **note di modifica** facoltative.
La stringa **versione** consente di distinguere il progetto dalle modifiche future apportate allo stesso e di assegnare ciascuna versione. Questo significa anche che è possibile assegnare diverse **versioni** dello stesso progetto alla stessa sottoscrizione. Quando il progetto subisce ulteriori modifiche, la **versione** **pubblicata** esiste ancora, e si aggiunge alle **modifiche non pubblicate**. Una volta ultimate le modifiche, il progetto aggiornato viene **pubblicato** con una nuova **versione** univoca e può essere ora assegnato.

## <a name="blueprint-assignment"></a>Assegnazione progetto

Ciascuna **versione** **pubblicata** di un progetto può essere assegnata a una sottoscrizione esistente. Nel portale, il progetto imposterà la **versione** **pubblicata** più di recente come predefinita. Se sono presenti parametri dell'artefatto (o parametri di progetto), questi verranno definiti durante il processo di assegnazione.

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

Queste autorizzazioni sono incluse nel ruolo **proprietario** e, fatta eccezione per le autorizzazioni di assegnazione di progetto, sono incluse anche nei ruoli **collaboratore**. Se questi ruoli predefiniti non soddisfano specifiche esigenze di sicurezza, è consigliabile creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Per poter abilitare la distribuzione, l'entità servizio per Azure Blueprint richiede il ruolo **proprietario** nella sottoscrizione assegnata. Se si usa il portale, questo ruolo viene automaticamente concesso e revocato per la distribuzione. Se si usa l'API REST, questo ruolo deve essere concesso manualmente, ma viene comunque revocato automaticamente al termine della distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un progetto - Portale](create-blueprint-portal.md)
- [Creare un progetto - API REST](create-blueprint-rest-api.md)