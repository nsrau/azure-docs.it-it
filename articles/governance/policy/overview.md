---
title: Panoramica dei criteri di Azure
description: Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire definizioni di criteri nell'ambiente Azure.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 4ec09c8a38e22fc14980422bfe9a80a2bf3edda4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097371"
---
# <a name="what-is-azure-policy"></a>Informazioni su Criteri di Azure

Criteri di Azure consente di imporre standard aziendali e di valutare la conformità su larga scala. Il dashboard di conformità fornisce una visualizzazione aggregata per valutare lo stato complessivo dell'ambiente, con la possibilità di eseguire il drill-down con granularità per risorsa e per criterio. Consente inoltre di ottenere la conformità delle risorse tramite la correzione in blocco per le risorse esistenti e la correzione automatica per le nuove risorse.

I casi d'uso comuni per Criteri di Azure includono l'implementazione della governance per la coerenza delle risorse, la conformità alle normative, la sicurezza, i costi e la gestione. Le definizioni dei criteri per questi casi d'uso comuni sono già disponibili nell'ambiente Azure come predefinite per iniziare facilmente a usare il servizio.

## <a name="overview"></a>Panoramica

Criteri di Azure valuta le risorse in Azure confrontando le proprietà di tali risorse con le regole business. Queste regole business, descritte in [formato JSON](./concepts/definition-structure.md), sono note come [definizioni dei criteri](#policy-definition). Per semplificare la gestione, è possibile raggruppare diverse regole business per formare un'[iniziativa dei criteri](#initiative-definition) (talvolta denominata _set di criteri_). Una volta formate le regole business, la definizione dei criteri o l'iniziativa viene [assegnata](#assignments) a qualsiasi ambito di risorse supportate da Azure, ad esempio [gruppi di gestione](../management-groups/overview.md), sottoscrizioni, [gruppi di risorse](../../azure-resource-manager/management/overview.md#resource-groups) o singole risorse. L'assegnazione si applica a tutte le risorse all'interno dell'[ambito](../../azure-resource-manager/management/overview.md#understand-scope) di tale assegnazione.
Se necessario, è possibile escludere gli ambiti secondari.

Criteri di Azure usa un [formato JSON](./concepts/definition-structure.md) per formare la logica usata dalla valutazione per determinare se una risorsa è conforme o meno. Le definizioni includono i metadati e la regola dei criteri. La regola definita può usare funzioni, parametri, operatori logici, condizioni e [alias](./concepts/definition-structure.md#aliases) delle proprietà per corrispondere esattamente allo scenario desiderato. La regola dei criteri determina quali risorse nell'ambito dell'assegnazione vengono valutate.

### <a name="understand-evaluation-outcomes"></a>Informazioni sui risultati della valutazione

Le risorse vengono valutate in momenti specifici durante il ciclo di vita delle risorse, il ciclo di vita dell'assegnazione dei criteri e per la valutazione periodica della conformità. Di seguito sono riportati gli orari o gli eventi che determinano la valutazione di una risorsa:

- Una risorsa viene creata, aggiornata o eliminata in un ambito con un'assegnazione di criteri.
- Un criterio o un'iniziativa è stata appena assegnata a un ambito.
- Un criterio o un'iniziativa già assegnata a un ambito viene aggiornata.
- Durante il ciclo di valutazione della conformità standard, che viene eseguito una volta ogni 24 ore.

Per informazioni dettagliate su quando e come viene eseguita la valutazione dei criteri, vedere [Trigger di valutazione](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Controllare la risposta a una valutazione

Le regole business per la gestione delle risorse non conformi variano notevolmente tra le organizzazioni. Le risposte della piattaforma circa una risorsa non conforme includono:

- Negare la modifica della risorsa
- Registrare la modifica alla risorsa
- Modificare la risorsa prima della modifica
- Modificare la risorsa dopo la modifica
- Distribuire risorse conformi correlate

Criteri di Azure consente di attivare queste risposte tramite l'applicazione di [effetti](./concepts/effects.md). Gli effetti vengono impostati nella parte della **regola dei criteri** della [definizione di criteri](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Correggere le risorse non conformi

Sebbene questi effetti influiscano principalmente su una risorsa quando la risorsa viene creata o aggiornata, Criteri di Azure supporta anche la gestione delle risorse non conformi esistenti senza la necessità di modificare la risorsa. Per altre informazioni su come rendere conformi le risorse esistenti, vedere [Correzione delle risorse](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Video introduttivo

La panoramica di Criteri di Azure riportata di seguito è tratta da Build 2018. Per scaricare le diapositive o il video, visitare [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Governance dell'ambiente di Azure tramite Criteri di Azure) su Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Introduzione

### <a name="azure-policy-and-rbac"></a>Criteri di Azure e controllo degli accessi in base al ruolo

Esistono alcune differenze importanti tra Criteri di Azure e il controllo degli accessi in base al ruolo (RBAC). Criteri di Azure valuta lo stato esaminando le proprietà delle risorse rappresentate in Resource Manager e le proprietà di alcuni provider di risorse. Criteri di Azure non limita le azioni, denominate anche _operazioni_. Criteri di Azure garantisce che lo stato delle risorse sia conforme alle regole business senza preoccuparsi dell'utente che ha apportato la modifica o chi dispone delle autorizzazioni per apportare una modifica.

Il Controllo degli accessi in base al ruolo è incentrato sulle [azioni](../../role-based-access-control/resource-provider-operations.md) dell'utente in ambiti diversi. Se è necessario il controllo di un'azione, il Controllo degli accessi in base al ruolo è lo strumento corretto da usare. Anche se un utente è autorizzato a eseguire un'azione, se il risultato è una risorsa non conforme Criteri di Azure blocca la creazione o l'aggiornamento.

La combinazione del Controllo degli accessi in base al ruolo e Criteri di Azure fornisce il controllo completo dell'ambito in Azure.

### <a name="rbac-permissions-in-azure-policy"></a>Autorizzazioni del controllo degli accessi in base al ruolo in Criteri di Azure

Criteri di Azure dispone di diverse autorizzazioni, note come operazioni, in due provider di risorse:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Molti ruoli predefiniti concedono autorizzazioni alle risorse di Criteri di Azure. Il ruolo **Collaboratore ai criteri delle risorse** include la maggior parte delle operazioni di Criteri di Azure. **Proprietario** dispone invece di diritti completi. I ruoli **Collaboratore** e **lettore** hanno accesso a tutte le operazioni di Criteri di Azure _in lettura_. Il ruolo **Collaboratore** può attivare la correzione delle risorse, ma non _creare_ definizioni o assegnazioni.

Se nessuno dei ruoli predefiniti dispone delle autorizzazioni necessarie, creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> L'identità gestita di un'assegnazione dei criteri **deployIfNotExists** richiede autorizzazioni sufficienti per creare o aggiornare le risorse incluse nel modello. Per altre informazioni, vedere [Configurare le definizioni dei criteri per la correzione](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Risorse coperte da Criteri di Azure

Criteri di Azure valuta tutte le risorse in Azure. Per alcuni provider di risorse, ad esempio [Configurazione guest](./concepts/guest-configuration.md), [servizio Azure Kubernetes](../../aks/intro-kubernetes.md) e [Azure Key Vault](../../key-vault/key-vault-overview.md), esiste un'integrazione più profonda per la gestione di impostazioni e oggetti. Per altre informazioni, vedere [Modalità dei provider di risorse](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Raccomandazioni per la gestione dei criteri

Ecco alcuni consigli e suggerimenti da tenere presenti:

- Iniziare con un effetto di controllo anziché un effetto di negazione per tenere traccia dell'impatto della definizione dei criteri per le risorse nell'ambiente. Se sono già presenti script per aumentare automaticamente le prestazioni delle applicazioni, l'impostazione di un effetto di rifiuto potrebbe impedire tali attività di automazione già implementate.

- Tenere presente le gerarchie organizzative quando si creano definizioni e assegnazioni. È consigliabile creare definizioni a livelli più generici, ad esempio il gruppo di gestione o a livello di sottoscrizione. Quindi, creare l'assegnazione al livello figlio successivo. Se si crea una definizione in un gruppo di gestione, l'assegnazione può essere limitata a una sottoscrizione o un gruppo di risorse all'interno di tale gruppo.

- È consigliabile creare e assegnare definizioni di iniziativa anche se si definisce un singolo criterio.
  Ad esempio, si dispone della definizione del criterio _policyDefA_ e la si crea nella definizione di iniziativa _initiativeDefC_. Se si crea un'altra definizione di criteri in un secondo momento per _policyDefB_ con obiettivi simili a _policyDefA_, è possibile aggiungerla sotto _initiativeDefC_ e tenerne traccia congiuntamente.

- Dopo aver creato un'assegnazione di iniziativa, le definizioni di criteri aggiunte all'iniziativa diventano anch'esse parte di tale assegnazione.

- Quando viene valutata un'assegnazione di iniziativa, vengono valutati anche tutti i criteri inclusi nell'iniziativa.
  Se è necessario convalidare i criteri singolarmente, è preferibile non includerli in un'iniziativa.

## <a name="azure-policy-objects"></a>Oggetti Criteri di Azure

### <a name="policy-definition"></a>Definizione di criteri

Il processo di creazione e implementazione dei criteri in Criteri di Azure ha inizio con la creazione di una definizione di criteri. Ogni definizione di criteri include condizioni da cui ne dipende l'applicazione. C'è anche un effetto definito associato, che viene applicato se le condizioni sono soddisfatte.

In Criteri di Azure sono disponibili diversi criteri predefiniti. Ad esempio:

- **SKU account di archiviazione consentiti** (Deny): Determina se un account di archiviazione in distribuzione si trova all'interno di un set di dimensioni di SKU. L'effetto consiste nel rifiutare tutti gli account di archiviazione che non rispettano il set di dimensioni di SKU definite.
- **Tipi di risorse consentiti** (Deny): Definisce i tipi di risorsa che è possibile distribuire. L'effetto consiste nel rifiutare tutte le risorse non incluse nell'elenco definito.
- **Località consentite** (Deny): Limita le aree disponibili per le nuove risorse. L'effetto viene usato per imporre i requisiti di conformità geografica.
- **SKU di macchine virtuali consentiti** (Deny): Specifica un set di SKU di macchine virtuali che è possibile distribuire.
- **Aggiungi un tag alle risorse** (Modify): Applica un tag obbligatorio e il relativo valore predefinito quando non viene specificato nella richiesta di distribuzione.
- **Aggiungi tag e relativo valore predefinito** (Append): Impone un tag obbligatorio e il relativo valore a una risorsa.
- **Tipi di risorse non consentiti** (Deny): Impedisce la distribuzione di un tipo di risorsa.

Per implementare queste definizioni di criteri (predefinite e personalizzate), sarà necessario assegnarle. Tutti questi criteri possono essere assegnati tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

La valutazione dei criteri avviene con diverse azioni, ad esempio l'assegnazione o l'aggiornamento dei criteri. Per un elenco completo, vedere [Trigger di valutazione dei criteri](./how-to/get-compliance-data.md#evaluation-triggers).

Per altre informazioni sulle strutture delle definizioni dei criteri, vedere [Struttura delle definizioni di criteri](./concepts/definition-structure.md).

I parametri dei criteri consentono di semplificarne la gestione riducendo il numero di definizioni di criteri che è necessario creare. È possibile definire parametri mentre si crea una definizione di criteri per renderla più generica. La definizione di criteri può successivamente essere usata anche per altri scenari. È sufficiente, a tal fine, passare valori diversi durante l'assegnazione della definizione di criteri. Si può ad esempio specificare un set di località per una sottoscrizione.

I parametri vengono definiti quando si crea una definizione di criteri. Quando viene definito un parametro, vengono assegnati un nome e facoltativamente un valore. È ad esempio possibile definire un parametro per i criteri denominato _location_ (posizione) e assegnargli valori diversi durante l'assegnazione dei criteri, ad esempio _EastUS_ o _WestUS_.

Per altre informazioni sui parametri dei criteri, vedere [Struttura definizioni - Parametri](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definizione di iniziativa

Una definizione di iniziativa è una raccolta di definizioni di criteri ottimizzate per il raggiungimento di un singolo obiettivo globale. Le definizioni di iniziativa semplificano la gestione e l'assegnazione delle definizioni di criteri, in quanto raggruppano un set di criteri in un unico elemento. Ad esempio, è possibile creare un'iniziativa denominata **Abilita monitoraggio nel Centro sicurezza di Azure** con l'obiettivo di monitorare tutte le raccomandazioni sulla sicurezza disponibili nel Centro sicurezza di Azure.

> [!NOTE]
> L'SDK, ad esempio l'interfaccia della riga di comando di Azure e Azure PowerShell, usa proprietà e parametri denominati **PolicySet** per fare riferimento alle iniziative.

In questa iniziativa saranno incluse definizioni di criteri come le seguenti:

- **Monitora database SQL non crittografato nel Centro sicurezza**, per il monitoraggio dei server e dei database SQL non crittografati.
- **Monitora vulnerabilità del sistema operativo nel Centro sicurezza**, per il monitoraggio dei server che non soddisfano la baseline configurata.
- **Monitora Endpoint Protection mancante nel Centro sicurezza**, per il monitoraggio dei server in cui non è installato un agente di Endpoint Protection.

Analogamente ai parametri dei criteri, i parametri delle iniziative consentono di semplificarne la gestione riducendo la ridondanza. I parametri delle iniziative sono parametri usati dalle definizioni di criteri incluse nell'iniziativa.

Si consideri ad esempio uno scenario con una definizione di iniziativa, **initiativeC**, che include le definizioni di criteri **policyA** e **policyB**, per ognuna delle quali è previsto un diverso tipo di parametro:

| Policy | Nome del parametro |Tipo di parametro  |Note |
|---|---|---|---|
| policyA | allowedLocations | array  |Questo parametro prevede come valore un elenco di stringhe, perché il parametro è stato definito come di tipo matrice |
| policyB | allowedSingleLocation |string |Questo parametro prevede come valore una parola, perché il parametro è stato definito come di tipo stringa |

In questo scenario, per la definizione dei parametri dell'iniziativa **initiativeC** sono disponibili tre opzioni:

- Usare i parametri delle definizioni di criteri incluse nell'iniziativa: In questo esempio _allowedLocations_ e _allowedSingleLocation_ diventano i parametri dell'iniziativa **initiativeC**.
- Specificare valori per i parametri delle definizioni di criteri incluse nella definizione di iniziativa. In questo esempio è possibile specificare un elenco di località per il parametro **allowedLocations** di **policyA** e il parametro **allowedSingleLocation** di **policyB**. È anche possibile specificare i valori quando si assegna l'iniziativa.
- Specificare un elenco di opzioni di _valore_ utilizzabili durante l'assegnazione dell'iniziativa. Quando si assegna l'iniziativa, i parametri ereditati dalle definizioni di criteri incluse nell'iniziativa possono contenere solo valori presenti nell'elenco specificato.

Durante la creazione di opzioni di valori in una definizione di iniziativa, non è possibile a immettere un valore diverso durante l'assegnazione dell'iniziativa perché non è incluso nell'elenco.

### <a name="assignments"></a>Assegnazioni

Un'assegnazione è una definizione di criteri o un'iniziativa che è stata assegnata per l'implementazione in un ambito specifico. L'ambito può spaziare da un [gruppo di gestione](../management-groups/overview.md) a una singola risorsa. Il termine _ambito_ fa riferimento a tutte le risorse, i gruppi di risorse, le sottoscrizioni o i gruppi di gestione a cui è assegnata la definizione. Le assegnazioni vengono ereditate da tutte le risorse figlio. Questo significa che una definizione applicata a un gruppo di risorse viene anche applicata a tutte le risorse presenti nel gruppo. È tuttavia possibile escludere un ambito secondario dall'assegnazione.

Nell'ambito della sottoscrizione è ad esempio possibile assegnare una definizione che impedisca la creazione di risorse di rete. È possibile escludere un gruppo di risorse nella sottoscrizione che è pensato per l'infrastruttura di rete. e consentire quindi l'accesso a questo gruppo specifico a utenti fidati in grado di creare risorse di rete.

In altri casi, potrebbe essere opportuno assegnare una definizione dell'elenco elementi consentiti per tipo di risorsa a livello di gruppo di gestione. E assegnare quindi un criterio più permissivo, consentendo più tipi di risorse, a un gruppo di gestione figlio o anche direttamente alle sottoscrizioni. Questo esempio tuttavia non funzionerebbe perché Criteri di Azure è un sistema di rifiuto esplicito. È invece necessario escludere il gruppo di gestione figlio o la sottoscrizione dall'assegnazione a livello di gruppo di gestione. Assegnare quindi il criterio più permissivo a livello di gruppo di gestione figlio o di sottoscrizione. Se eventuali assegnazioni provocano il rifiuto di una risorsa, l'unico modo per consentire la risorsa consiste nel modificare l'assegnazione che la rifiuta.

Per altre informazioni sull'impostazione delle assegnazioni tramite il portale, vedere [Creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure](assign-policy-portal.md). Sono inoltre disponibili i passaggi per [PowerShell](assign-policy-powershell.md) e [Interfaccia della riga di comando di Azure](assign-policy-azurecli.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Numero massimo di oggetti di Criteri di Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i passaggi successivi consigliati dopo questa panoramica di Criteri di Azure e di alcuni dei concetti chiave:

- [Vedere la struttura delle definizioni dei criteri](./concepts/definition-structure.md).
- [Assegnare una definizione di criteri con il portale](./assign-policy-portal.md).
