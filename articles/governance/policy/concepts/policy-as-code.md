---
title: Progettare flussi di lavoro di criteri come codice
description: Informazioni su come progettare flussi di lavoro per distribuire le definizioni dei criteri di Azure come codice e convalidare automaticamente le risorse.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267271"
---
# <a name="design-policy-as-code-workflows"></a>Progettare flussi di lavoro di criteri come codice

Man mano che si procede nel percorso con Cloud Governance, è consigliabile passare dalla gestione manuale di ogni definizione di criteri nel portale di Azure o attraverso i vari SDK a qualcosa di più gestibile e ripetibile su scala aziendale. Due degli approcci predominanti alla gestione dei sistemi su larga scala nel cloud sono:

- Infrastruttura come codice: la pratica di trattare il contenuto che definisce gli ambienti, dai modelli di Resource Manager alle definizioni dei criteri di Azure ai blueprint di Azure, come codice sorgente.
- DevOps: l'unione di persone, processi e prodotti per consentire la distribuzione continua di valore per i nostri utenti finali.

Politica in quanto Codice è la combinazione di queste idee. Essenzialmente, mantenere le definizioni dei criteri nel controllo del codice sorgente e ogni volta che viene apportata una modifica, testare e convalidare tale modifica. Tuttavia, questo non dovrebbe essere l'entità del coinvolgimento dei criteri con l'infrastruttura come codice o DevOps.However, that shouldn't be the extent of policies involvement with Infrastructure as Code or DevOps.

Il passaggio di convalida deve anche essere un componente di altri flussi di lavoro di integrazione continua o di distribuzione continua. Gli esempi includono la distribuzione di un ambiente applicativo o di un'infrastruttura virtuale. Rendendo la convalida dei criteri di Azure un componente iniziale del processo di compilazione e distribuzione, i team dell'applicazione e delle operazioni individuano se le modifiche non sono denunciate, molto prima che sia troppo tardi e che tentino di eseguire la distribuzione nell'ambiente di produzione.

## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Il flusso di lavoro generale consigliato di Criterio come codice è simile al diagramma seguente:The recommended general workflow of Policy as Code looks like this diagram:

![Panoramica del flusso di lavoro Dei criteri come codice](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Creare e aggiornare le definizioni dei criteri

Le definizioni dei criteri vengono create tramite JSON e archiviate nel controllo del codice sorgente. Ogni criterio dispone di un proprio set di file, ad esempio i parametri, le regole e i parametri di ambiente, che devono essere archiviati nella stessa cartella. La struttura seguente è un modo consigliato per mantenere le definizioni dei criteri nel controllo del codice sorgente.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Quando viene aggiunto un nuovo criterio o uno esistente aggiornato, il flusso di lavoro deve aggiornare automaticamente la definizione dei criteri in Azure.When a new policy is added or an existing one updated, the workflow should automatically update the policy definition in Azure. Il test della definizione dei criteri nuova o aggiornata viene fornito in un passaggio successivo.

### <a name="create-and-update-initiative-definitions"></a>Creare e aggiornare le definizioni delle iperdistruzioni

Analogamente, le iniziative hanno il proprio file JSON e i file correlati che devono essere archiviati nella stessa cartella. The initiative definition requires the policy definition to already exist, so can't be created or updated until the source for the policy has been updated in source control and then updated in Azure. La struttura seguente è un modo consigliato per mantenere le definizioni dell'iniziativa nel controllo del codice sorgente:The following structure is a recommended way of keeping your initiative definitions in source control:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Analogamente alle definizioni di criteri, durante l'aggiunta o l'aggiornamento di un'iniziativa esistente, il flusso di lavoro deve aggiornare automaticamente la definizione dell'iniziativa in Azure.Like policy definitions, when adding or updating an existing initiative, the workflow should automatically update the initiative definition in Azure. Il test della definizione dell'iniziativa nuova o aggiornata viene eseguito in un passaggio successivo.

### <a name="test-and-validate-the-updated-definition"></a>Testare e convalidare la definizione aggiornata

Dopo che l'automazione ha accettato i criteri o le definizioni di iniziativa appena create o aggiornate e ha apportato l'aggiornamento all'oggetto in Azure, è il momento di testare le modifiche apportate. Il criterio o l'iniziativa di cui fa parte deve quindi essere assegnata alle risorse nell'ambiente più lontano dalla produzione. Questo ambiente è in genere _Dev_.

L'assegnazione deve usare [enforcementMode](./assignment-structure.md#enforcement-mode) di _disabled_ in modo che la creazione di risorse e gli aggiornamenti non vengano bloccati, ma che le risorse esistenti vengano comunque controllate per verificare la conformità alla definizione dei criteri aggiornata. Anche con enforcementMode, è consigliabile che l'ambito dell'assegnazione sia un gruppo di risorse o una sottoscrizione usata in modo specifico per la convalida dei criteri.

> [!NOTE]
> Sebbene la modalità di applicazione sia utile, non sostituisce il test completo di una definizione di criteri in varie condizioni. La definizione dei criteri `PUT` `PATCH` deve essere testata con le chiamate all'API REST e, le risorse conformi e non conformi e i casi perimetrali come una proprietà mancante dalla risorsa.

Dopo aver distribuito l'assegnazione, utilizzare Policy SDK per ottenere i dati di [conformità](../how-to/get-compliance-data.md) per la nuova assegnazione. L'ambiente utilizzato per testare i criteri e le assegnazioni deve disporre di risorse conformi e non conformi. Come un buon unit test per il codice, si desidera testare che le risorse sono come previsto e che non si dispone di falsi positivi o falsi negativi. Se si testa e si convalida solo per ciò che si prevede, potrebbe esserci un impatto imprevisto e non identificato dal criterio. Per altre informazioni, vedere [Valutare l'impatto di un nuovo criterio di Azure.For](./evaluate-impact.md)more information, see Evaluate the impact of a new Azure policy.

### <a name="enable-remediation-tasks"></a>Abilitare le attività di correzioneEnable remediation tasks

Se la convalida dell'assegnazione soddisfa le aspettative, il passaggio successivo consiste nel convalidare la correzione.
I criteri che utilizzano [deployIfNotExists](./effects.md#deployifnotexists) o [modify](./effects.md#modify) possono essere trasformati in un'attività di correzione e correggere le risorse da uno stato non conforme.

Il primo passaggio per eseguire questa operazione consiste nel concedere all'assegnazione dei criteri l'assegnazione di ruolo definita nella definizione dei criteri. Questa assegnazione di ruolo fornisce all'identità gestita per l'assegnazione dei criteri i diritti sufficienti per apportare le modifiche necessarie per rendere la risorsa conforme.

Una volta che l'assegnazione dei criteri dispone dei diritti appropriati, utilizzare Policy SDK per attivare un'attività di correzione su un set di risorse note per non conformi. Prima di procedere, è necessario completare tre test a fronte di queste attività corretti:

- Verificare che l'attività di correzione sia stata completata correttamente
- Eseguire la valutazione dei criteri per verificare che i risultati della conformità dei criteri vengano aggiornati come previsto
- Eseguire uno unit test dell'ambiente direttamente sulle risorse per convalidare le relative proprietà sono state modificate

Il test dei risultati aggiornati della valutazione dei criteri e dell'ambiente fornisce la conferma che le attività di correzione hanno modificato le attività previste e che la definizione dei criteri ha modificato la conformità come previsto.

### <a name="update-to-enforced-assignments"></a>Aggiornamento alle assegnazioni applicate

Una volta completati tutti i gate di convalida, aggiornare l'assegnazione per utilizzare **enforcementMode** di _enabled_. Questa modifica dovrebbe essere inizialmente apportata nello stesso ambiente lontano dalla produzione. Una volta che l'ambiente viene convalidato come funziona come previsto, la modifica deve essere definito per includere l'ambiente successivo e così via fino a quando il criterio non viene distribuito nelle risorse di produzione.

## <a name="process-integrated-evaluations"></a>Valutazioni integrate del processo

Il flusso di lavoro generale per i criteri in quanto codice è per lo sviluppo e la distribuzione di criteri e iniziative in un ambiente su larga scala. Tuttavia, la valutazione dei criteri deve essere parte del processo di distribuzione per qualsiasi flusso di lavoro che distribuisce o crea risorse in Azure, ad esempio la distribuzione di applicazioni o l'esecuzione di modelli di Resource Manager per creare l'infrastruttura.

In questi casi, dopo la distribuzione dell'applicazione o dell'infrastruttura a una sottoscrizione di test o a un gruppo di risorse, la valutazione dei criteri deve essere eseguita per tale ambito per verificare la convalida di tutti i criteri e le iniziative esistenti. Anche se possono essere configurati come **enforcementMode** _disabilitato_ in un ambiente di questo tipo, è utile sapere in anticipo se una distribuzione di un'applicazione o di un'infrastruttura è in violazione delle definizioni dei criteri in anticipo. Questa valutazione dei criteri deve pertanto essere un passaggio in tali flussi di lavoro e non riuscire le distribuzioni che creano risorse non conformi.

## <a name="review"></a>Verifica

In questo articolo viene illustrato il flusso di lavoro generale per i criteri come codice e anche in cui la valutazione dei criteri deve far parte di altri flussi di lavoro di distribuzione. Questo flusso di lavoro può essere utilizzato in qualsiasi ambiente che supporta i passaggi tramite script e l'automazione in base ai trigger.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla struttura di [definizione dei criteri](./definition-structure.md).
- Informazioni sulla struttura delle [assegnazioni dei criteri](./assignment-structure.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).