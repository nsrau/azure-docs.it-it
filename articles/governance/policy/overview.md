---
title: Panoramica dei criteri di Azure
description: Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire definizioni di criteri nell'ambiente Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 11384d1bbffb675bd322469d129464f58a48bb6b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311832"
---
# <a name="what-is-azure-policy"></a>Informazioni su Criteri di Azure

La governance garantisce che l'organizzazione sia in grado di conseguire i propri obiettivi tramite un uso efficace ed efficiente delle risorse IT, stabilendo con chiarezza gli obiettivi aziendali e i progetti IT.

Se l'azienda affronta una serie di problemi IT importanti che sembrano non risolversi mai,
Una valida governance IT include la pianificazione delle iniziative e la definizione delle priorità a livello strategico, per contribuire a gestire e prevenire i problemi. Criteri di Azure offre le funzionalità necessarie a riguardo.

Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire criteri. Questi criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda. Criteri di Azure soddisfa questa esigenza valutando le risorse per la mancata conformità con i criteri assegnati. Si possono avere, ad esempio, criteri che consentono solo determinate dimensioni di SKU per le macchine virtuali nell'ambiente. Dopo aver implementato questi criteri, le risorse nuove ed esistenti vengono valutate per la conformità. Con il tipo corretto di criteri, le risorse esistenti possono essere rese conformi. Più avanti in questa documentazione verranno forniti maggiori dettagli su come creare ed implementare i criteri con Criteri di Azure.

> [!IMPORTANT]
> La valutazione della conformità di Criteri di Azure è ora disponibile per tutte le assegnazioni indipendentemente dal piano tariffario. Se le assegnazioni non mostrano i dati di conformità, assicurarsi che la sottoscrizione sia registrata con il provider di risorse Microsoft.PolicyInsights.

## <a name="how-is-it-different-from-rbac"></a>Quali sono le differenze rispetto al controllo degli accessi in base al ruolo?

Esistono differenze importanti tra i criteri e il controllo degli accessi in base al ruoli (RBAC). Quest'ultimo è incentrato sulle azioni dell'utente in ambiti diversi. Un utente potrebbe essere aggiunto al ruolo di collaboratore per un gruppo di risorse, in modo che possa apportare modifiche a tale gruppo di risorse. Il servizio Criteri è incentrato sulle proprietà delle risorse durante la distribuzione e per le risorse già esistenti. Il servizio controlla proprietà come tipi o posizioni delle risorse. A differenza del controllo degli accessi in base al ruolo, il servizio Criteri rappresenta un sistema con autorizzazioni predefinite e negazione esplicita.

### <a name="rbac-permissions-in-azure-policy"></a>Autorizzazioni del controllo degli accessi in base al ruolo in Criteri di Azure

Criteri di Azure dispone di diverse autorizzazioni, note come operazioni, in due provider di risorse:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Molti ruoli predefiniti concedono autorizzazioni alle risorse di Criteri di Azure. Il ruolo **Collaboratore ai criteri delle risorse (anteprima)** include la maggior parte delle operazioni di Criteri di Azure. **Proprietario** dispone invece di diritti completi. Sia il ruolo **Collaboratore** che il ruolo **Lettore** possono effettuare tutte le operazioni di lettura relative ai Criteri, ma **Collaboratore** può anche attivare la correzione.

Se nessuno dei ruoli predefiniti dispone delle autorizzazioni necessarie, creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definizione di criteri

Il processo di creazione e implementazione dei criteri in Criteri di Azure ha inizio con la creazione di una definizione di criteri. Ogni definizione di criteri include condizioni da cui ne dipende l'applicazione. C'è anche un effetto definito associato, che viene applicato se le condizioni sono soddisfatte.

In Criteri di Azure sono disponibili diversi criteri predefiniti. Ad esempio: 

- **Richiedere SQL Server 12.0**: Verifica che tutte le istanze di SQL Server usino la versione 12.0. L'effetto consiste nel rifiutare tutti i server che non soddisfano tali criteri.
- **SKU degli account di archiviazione consentiti**: Determina se un account di archiviazione in distribuzione si trova all'interno di un set di dimensioni di SKU. L'effetto consiste nel rifiutare tutti gli account di archiviazione che non rispettano il set di dimensioni di SKU definite.
- **Tipi di risorsa consentiti**: Definisce i tipi di risorsa che è possibile distribuire. L'effetto consiste nel rifiutare tutte le risorse non incluse nell'elenco definito.
- **Aree consentite**: Limita le aree disponibili per le nuove risorse. L'effetto viene usato per imporre i requisiti di conformità geografica.
- **SKU delle macchine virtuali consentiti**: Specifica un set di SKU di macchine virtuali che è possibile distribuire.
- **Applica tag e relativo valore predefinito**: Applica un tag obbligatorio e il relativo valore predefinito quando non viene specificato nella richiesta di distribuzione.
- **Imponi tag e relativo valore**: Impone un tag obbligatorio e il relativo valore a una risorsa.
- **Tipi di risorsa non consentiti**: Impedisce la distribuzione di un tipo di risorsa.

Per implementare queste definizioni di criteri (predefinite e personalizzate), sarà necessario assegnarle. Tutti questi criteri possono essere assegnati tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

La valutazione dei criteri avviene con diverse azioni, ad esempio l'assegnazione o l'aggiornamento dei criteri. Per un elenco completo, vedere [Trigger di valutazione dei criteri](./how-to/get-compliance-data.md#evaluation-triggers).

Per altre informazioni sulle strutture delle definizioni dei criteri, vedere [Struttura delle definizioni di criteri](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Assegnazione di criteri

Un'assegnazione di criteri è una definizione di criteri che è stata assegnata per l'implementazione in un ambito specifico. L'ambito può spaziare da un [gruppo di gestione](../management-groups/overview.md) a un gruppo di risorse. Il termine *ambito* fa riferimento a tutti i gruppi di risorse, le sottoscrizioni o i gruppi di gestione a cui è assegnata la definizione di criteri. Le assegnazioni dei criteri vengono ereditate da tutte le risorse figlio. Questa progettazione significa che i criteri applicati a un gruppo di risorse vengono applicati a tutte le risorse presenti nel gruppo. È tuttavia possibile escludere un sottoambito dall'assegnazione dei criteri.

Nell'ambito della sottoscrizione è ad esempio possibile assegnare criteri che non permettono la creazione di risorse di rete. È possibile escludere un gruppo di risorse nella sottoscrizione che è pensato per l'infrastruttura di rete. e consentire quindi l'accesso a questo gruppo specifico a utenti fidati in grado di creare risorse di rete.

In un altro esempio è possibile che si voglia assegnare un criterio dell'elenco elementi consentiti relativo al tipo di risorsa a livello di gruppo di gestione. Assegnare quindi un criterio più permissivo, consentendo più tipi di risorse, in un gruppo di gestione figlio o anche direttamente nelle sottoscrizioni. Questo esempio tuttavia non funzionerebbe perché un criterio è un sistema di rifiuto esplicito. È invece necessario escludere il gruppo di gestione figlio o la sottoscrizione dall'assegnazione del criterio a livello di gruppo di gestione. Assegnare quindi il criterio più permissivo a livello di gruppo di gestione figlio o di sottoscrizione. Se eventuali criteri provocano il rifiuto di una risorsa, l'unico modo per consentire la risorsa consiste nel modificare il criterio che la rifiuta.

Per altre informazioni sull'impostazione delle definizioni e delle assegnazioni di criteri tramite portale, consultare [Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente di Azure](assign-policy-portal.md). Sono inoltre disponibili i passaggi per [PowerShell](assign-policy-powershell.md) e [Interfaccia della riga di comando di Azure](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parametri dei criteri

I parametri dei criteri consentono di semplificarne la gestione riducendo il numero di definizioni di criteri che è necessario creare. È possibile definire parametri mentre si crea una definizione di criteri per renderla più generica. La definizione di criteri può successivamente essere usata anche per altri scenari. È sufficiente, a tal fine, passare valori diversi durante l'assegnazione della definizione di criteri. Si può ad esempio specificare un set di località per una sottoscrizione.

I parametri vengono definiti quando si crea una definizione di criteri. Quando viene definito un parametro, vengono assegnati un nome e facoltativamente un valore. È ad esempio possibile definire un parametro per i criteri denominato *location* (posizione) e assegnargli valori diversi durante l'assegnazione dei criteri, ad esempio *EastUS* o *WestUS*.

Per altre informazioni sui parametri dei criteri, vedere [Struttura definizioni - Parametri](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definizione di iniziativa

Una definizione di iniziativa è una raccolta di definizioni di criteri ottimizzate per il raggiungimento di un singolo obiettivo globale. Le definizioni di iniziativa semplificano la gestione e l'assegnazione delle definizioni di criteri, in quanto raggruppano un set di criteri in un unico elemento. Ad esempio, è possibile creare un'iniziativa denominata **Abilita monitoraggio nel Centro sicurezza di Azure** con l'obiettivo di monitorare tutte le raccomandazioni sulla sicurezza disponibili nel Centro sicurezza di Azure.

In questa iniziativa saranno incluse definizioni di criteri come le seguenti:

- **Monitora database SQL non crittografato nel Centro sicurezza**, per il monitoraggio dei server e dei database SQL non crittografati.
- **Monitora vulnerabilità del sistema operativo nel Centro sicurezza**, per il monitoraggio dei server che non soddisfano la baseline configurata.
- **Monitora Endpoint Protection mancante nel Centro sicurezza**, per il monitoraggio dei server in cui non è installato un agente di Endpoint Protection.

## <a name="initiative-assignment"></a>Assegnazione di iniziativa

Analogamente a un'assegnazione di criteri, un'assegnazione di iniziativa è una definizione di iniziativa assegnata a un ambito specifico. Le assegnazioni di iniziativa riducono l'esigenza di creare diverse definizioni di iniziativa per ogni ambito. L'ambito può anche comprendere da un gruppo di gestione a un gruppo di risorse.

Ogni iniziativa è assegnabile a diversi ambiti. Un'iniziativa può essere assegnata sia a **subscriptionA** che a **subscriptionB**.

## <a name="initiative-parameters"></a>Parametri delle iniziative

Analogamente ai parametri dei criteri, i parametri delle iniziative consentono di semplificarne la gestione riducendo la ridondanza. I parametri delle iniziative sono parametri usati dalle definizioni di criteri incluse nell'iniziativa.

Si consideri ad esempio uno scenario con una definizione di iniziativa, **initiativeC**, che include le definizioni di criteri **policyA** e **policyB**, per ognuna delle quali è previsto un diverso tipo di parametro:

| Criterio | Nome del parametro |Tipo di parametro  |Note |
|---|---|---|---|
| policyA | allowedLocations | array  |Questo parametro prevede come valore un elenco di stringhe, perché il parametro è stato definito come di tipo matrice |
| policyB | allowedSingleLocation |stringa |Questo parametro prevede come valore una parola, perché il parametro è stato definito come di tipo stringa |

In questo scenario, per la definizione dei parametri dell'iniziativa **initiativeC** sono disponibili tre opzioni:

- Usare i parametri delle definizioni di criteri incluse nell'iniziativa: In questo esempio *allowedLocations* e *allowedSingleLocation* diventano i parametri dell'iniziativa **initiativeC**.
- Specificare valori per i parametri delle definizioni di criteri incluse nella definizione di iniziativa. In questo esempio, è possibile specificare un elenco di località per il **parametro allowedLocations di policyA** e il **parametro allowedSingleLocation di policyB**. È anche possibile specificare i valori quando si assegna l'iniziativa.
- Specificare un elenco di opzioni di *valore* utilizzabili durante l'assegnazione dell'iniziativa. Quando si assegna l'iniziativa, i parametri ereditati dalle definizioni di criteri incluse nell'iniziativa possono contenere solo valori presenti nell'elenco specificato.

Durante la creazione di opzioni di valori in una definizione di iniziativa, non è possibile a immettere un valore diverso durante l'assegnazione dell'iniziativa perché non è incluso nell'elenco.

## <a name="maximum-count-of-policy-objects"></a>Numero massimo di oggetti di criteri

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Raccomandazioni per la gestione dei criteri

Ecco alcuni consigli e suggerimenti da tenere presenti:

- Iniziare con un effetto di controllo anziché un effetto di negazione per tenere traccia dell'impatto della definizione dei criteri per le risorse nell'ambiente. Se sono già presenti script per aumentare automaticamente le prestazioni delle applicazioni, l'impostazione di un effetto di rifiuto potrebbe impedire tali attività di automazione già implementate.

- Tenere presente le gerarchie organizzative quando si creano definizioni e assegnazioni. È consigliabile creare definizioni a livelli più generici, ad esempio il gruppo di gestione o a livello di sottoscrizione. Quindi, creare l'assegnazione al livello figlio successivo. Se si crea una definizione in un gruppo di gestione, l'assegnazione può essere limitata a una sottoscrizione o un gruppo di risorse all'interno di tale gruppo.

- È consigliabile creare e assegnare definizioni di iniziativa anche se si definisce un singolo criterio.
Ad esempio, si dispone della definizione del criterio *policyDefA* e la si crea nella definizione di iniziativa *initiativeDefC*. Se si crea un'altra definizione di criteri in un secondo momento per *policyDefB* con obiettivi simili a *policyDefA*, è possibile aggiungerla sotto *initiativeDefC* e tenerne traccia congiuntamente.

- Dopo aver creato un'assegnazione di iniziativa, le definizioni di criteri aggiunte all'iniziativa diventano anch'esse parte di tale assegnazione.

- Quando viene valutata un'assegnazione di iniziativa, vengono valutati anche tutti i criteri inclusi nell'iniziativa. Se è necessario convalidare i criteri singolarmente, è preferibile non includerli in un'iniziativa.

## <a name="video-overview"></a>Video introduttivo

La panoramica di Criteri di Azure riportata di seguito è tratta da Build 2018. Per scaricare le diapositive o il video, visitare [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Governance dell'ambiente di Azure tramite Criteri di Azure) su Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i passaggi successivi consigliati dopo questa panoramica di Criteri di Azure e di alcuni dei concetti chiave:

- [Assegnare una definizione di criteri con il portale](assign-policy-portal.md)
- [Assegnare una definizione di criteri con l'interfaccia della riga di comando di Azure](assign-policy-azurecli.md)
- [Assegnare una definizione di criteri con PowerShell](assign-policy-powershell.md)
- Scoprire le caratteristiche di un gruppo di gestione con [Organizzare le risorse con i gruppi di gestione di Azure](..//management-groups/overview.md)
- Visualizzare [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Governance dell'ambiente di Azure tramite Criteri di Azure) su Channel 9