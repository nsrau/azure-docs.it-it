---
title: Progettare flussi di lavoro di criteri come codice
description: Informazioni su come progettare i flussi di lavoro per distribuire le definizioni di Criteri di Azure come codice e convalidare automaticamente le risorse.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 7fa8eb36283821527e16c1d97e326aa9dcde9dba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598223"
---
# <a name="design-policy-as-code-workflows"></a>Progettare flussi di lavoro di criteri come codice

Lungo il percorso di implementazione dei processi di governance del cloud, arriva il momento in cui si è pronti a passare dalla gestione manuale di ogni definizione di criteri, tramite il portale di Azure o i vari SDK, a qualcosa di più gestibile e ripetibile su scala aziendale. Due degli approcci principali alla gestione dei sistemi su larga scala nel cloud sono:

- Infrastruttura come codice: la pratica di trattare il contenuto che definisce gli ambienti, dal Azure Resource Manager modelli (modelli ARM) alle definizioni di criteri di Azure ai progetti di Azure, come codice sorgente.
- DevOps: l'unione di persone, processi e prodotti per consentire il recapito continuo di valore agli utenti finali.

Criteri come codice è la combinazione di queste idee. Essenzialmente, le definizioni dei criteri vengono mantenute nel controllo del codice sorgente e, ogni volta che viene apportata una modifica, tale modifica viene testata e convalidata. Tuttavia, questo non dovrebbe essere l'unico ambito del coinvolgimento dei criteri con Infrastruttura come codice o DevOps.

Il passaggio della convalida dovrebbe essere un componente anche di altri flussi di lavoro di integrazione continua o distribuzione continua. Un esempio è la distribuzione di un ambiente applicativo o di un'infrastruttura virtuale. Rendendo la convalida dei criteri di Azure un componente iniziale del processo di compilazione e distribuzione, i team delle applicazioni e delle operazioni individuano se le modifiche non sono conformi, molto prima che sia troppo tardi e si stia tentando di eseguire la distribuzione nell'ambiente di produzione.

## <a name="definitions-and-foundational-information"></a>Definizioni e informazioni fondamentali

Prima di accedere ai dettagli dei criteri come flusso di lavoro del codice, esaminare le definizioni e gli esempi seguenti:

- [Definizione criteri](./definition-structure.md)
- [Definizione di iniziativa](./initiative-definition-structure.md)

I nomi file sono allineati alle parti della definizione Policy o Initiative:
- `policy(set).json` -Intera definizione
- `policy(set).parameters.json` -La `properties.parameters` parte della definizione
- `policy.rules.json` -La `properties.policyRule` parte della definizione
- `policyset.definitions.json` -La `properties.policyDefinitions` parte della definizione

Esempi di questi formati di file sono disponibili nel [repository GitHub di criteri di Azure](https://github.com/Azure/azure-policy/):

- Definizione dei criteri: [aggiungere un tag alle risorse](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- Definizione di iniziativa: [tag di fatturazione](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

Vedere anche [esportare le risorse di criteri di Azure](../how-to/export-resources.md) per ottenere le definizioni e le assegnazioni esistenti nell'ambiente di gestione del codice sorgente [GitHub](https://www.github.com).

## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Il flusso di lavoro generale consigliato di Criteri come codice è simile a questo diagramma:

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Diagramma che mostra i criteri come caselle del flusso di lavoro di codice da crea a test da distribuire." border="false":::
   Il diagramma che mostra i criteri come caselle del flusso di lavoro di codice. Creazione copre la creazione delle definizioni di criteri e iniziative. Test illustra l'assegnazione con la modalità di imposizione disabilitata. Un controllo del gateway per lo stato di conformità è seguito dalla concessione delle autorizzazioni M S I e delle risorse di correzione.  Deploy include l'aggiornamento dell'assegnazione con la modalità di imposizione abilitata.
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>Creare e aggiornare le definizioni dei criteri

Le definizioni dei criteri vengono create tramite JSON e archiviate nel controllo del codice sorgente. Ogni criterio ha il proprio set di file, ad esempio i parametri, le regole e i parametri di ambiente, che devono essere archiviati nella stessa cartella. Di seguito è riportata la struttura consigliata da usare per mantenere le definizioni dei criteri nel controllo del codice sorgente.

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

Quando viene aggiunto un nuovo criterio o ne viene aggiornato uno esistente, il flusso di lavoro deve aggiornare automaticamente la definizione dei criteri in Azure. Il test della definizione del criterio nuovo o aggiornato avviene in una seconda fase.

### <a name="create-and-update-initiative-definitions"></a>Creare e aggiornare le definizioni di iniziative

Anche le iniziative hanno il proprio file JSON e i file correlati che devono essere archiviati nella stessa cartella. La definizione di iniziativa richiede che la definizione dei criteri esista già, pertanto non può essere creata o aggiornata finché l'origine del criterio non è stata aggiornata nel controllo del codice sorgente e quindi aggiornata in Azure. Di seguito è riportata la struttura consigliata da usare per mantenere le definizioni di iniziative nel controllo del codice sorgente:

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

Analogamente alle definizioni di criteri, quando si aggiunge un'iniziativa o se ne aggiorna una esistente, il flusso di lavoro deve aggiornare automaticamente la definizione di iniziativa in Azure. Il test della definizione dell'iniziativa nuova o aggiornata avviene in una seconda fase.

### <a name="test-and-validate-the-updated-definition"></a>Testare e convalidare la definizione aggiornata

Dopo che il processo di automazione ha acquisito le definizioni di criteri o di iniziative create o aggiornate e ha eseguito l'aggiornamento all'oggetto in Azure, è il momento di testare le modifiche apportate. I criteri o le iniziative di cui fanno parte dovrebbero quindi essere assegnati alle risorse nell'ambiente più lontano da quello di produzione. Questo ambiente è in genere quello di _sviluppo_.

L'assegnazione deve _disabilitare_ la proprietà [enforcementMode](./assignment-structure.md#enforcement-mode) in modo da non bloccare le operazioni di creazione e aggiornamento delle risorse, ma in modo che la conformità delle risorse esistenti alla definizione di criteri aggiornata continui a essere controllata. Anche con enforcementMode, è consigliabile che l'ambito dell'assegnazione sia un gruppo di risorse o una sottoscrizione designata in modo specifico per la convalida dei criteri.

> [!NOTE]
> Sebbene la modalità di imposizione sia utile, non si sostituisce al test accurato di una definizione di criteri in varie condizioni. La definizione dei criteri deve essere testata con le chiamate API REST `PUT` e `PATCH`, con risorse conformi e non conformi e con casi limite come una proprietà mancante nella risorsa.

Una volta distribuita l'assegnazione, usare l'azione Policy SDK o [Azure Policy Compliance Scan](https://github.com/marketplace/actions/azure-policy-compliance-scan) per [ottenere i dati di conformità](../how-to/get-compliance-data.md) per la nuova assegnazione. L'ambiente usato per testare i criteri e le assegnazioni deve contenere risorse sia conformi che non conformi.
Come un buon unit test per il codice, occorre verificare che le risorse siano come previsto e che non siano presenti falsi positivi o falsi negativi. Se si esegue il test e la convalida solo dei comportamenti previsti, i criteri potrebbero dare risultare imprevisti e non identificati. Per altre informazioni, vedere [Valutare l'impatto di una nuova definizione di Criteri di Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Abilitare le attività di correzione

Se la convalida dell'assegnazione soddisfa le aspettative, il passaggio successivo consiste nel convalidare la correzione.
I criteri che usano [deployIfNotExists](./effects.md#deployifnotexists) o [modify](./effects.md#modify) possono essere convertiti in un'attività di correzione e correggere le risorse da uno stato non conforme.

Il primo passaggio dell'attività di correzione delle risorse consiste nel concedere all'assegnazione dei criteri l'assegnazione di ruolo definita nella definizione dei criteri. Questa assegnazione di ruolo concede all'identità gestita dell'assegnazione dei criteri un numero sufficiente di diritti per apportare le modifiche necessarie per rendere la risorsa conforme.

Quando l'assegnazione dei criteri dispone dei diritti appropriati, usare l'SDK dei criteri per attivare un'attività di correzione su un set di risorse note come non conformi. Prima di procedere, è necessario completare tre test sulle attività di correzione seguenti:

- Verificare che l'attività di correzione sia stata completata correttamente
- Eseguire la valutazione dei criteri per verificare che i risultati di conformità dei criteri siano aggiornati come previsto
- Eseguire un unit test dell'ambiente direttamente sulle risorse per verificare che le relative proprietà siano state modificate

Il test sia dei risultati di valutazione dei criteri aggiornati che dell'ambiente fornisce direttamente la conferma che le attività di correzione hanno modificato ciò che ci si aspettava e che la definizione dei criteri ha rilevato la modifica di conformità come previsto.

### <a name="update-to-enforced-assignments"></a>Aggiornare le assegnazioni applicate

Una volta completati tutti i controlli di convalida, aggiornare l'assegnazione in modo da _abilitare_ la proprietà **enforcementMode**. È consigliabile apportare questa modifica all'inizio nello stesso ambiente lontano da quello di produzione. Una volta convalidato il funzionamento previsto dell'ambiente, occorre definire l'ambito della modifica in modo da includere l'ambiente successivo e così via, finché i criteri non vengono distribuiti alle risorse di produzione.

## <a name="process-integrated-evaluations"></a>Valutazioni integrate nel processo

Il flusso di lavoro generale dei criteri come codice prevede lo sviluppo e la distribuzione di criteri e iniziative in un ambiente su larga scala. La valutazione dei criteri, tuttavia, deve far parte del processo di distribuzione per tutti i flussi di lavoro che distribuiscono o creano risorse in Azure, ad esempio la distribuzione di applicazioni o l'esecuzione di modelli ARM per la creazione dell'infrastruttura.

In questi casi, dopo aver eseguito la distribuzione dell'applicazione o dell'infrastruttura in una sottoscrizione o un gruppo di risorse di test, è necessario eseguire la valutazione dei criteri per tale ambito convalidando tutti i criteri e le iniziative esistenti. Anche se in un ambiente di questo tipo potrebbero essere configurati con la proprietà **enforcementMode** _disabilitata_, è utile sapere in anticipo se la distribuzione di un'applicazione o di un'infrastruttura viola le definizioni dei criteri. La valutazione dei criteri deve quindi essere un passaggio di questi flussi di lavoro e contrassegnare come non riuscite le distribuzioni che creano risorse non conformi.

## <a name="review"></a>Verifica

Questo articolo illustra il flusso di lavoro generale dei criteri come codice e descrive le situazioni in cui la valutazione dei criteri deve far parte di altri flussi di lavoro di distribuzione. Questo flusso di lavoro può essere usato in qualsiasi ambiente che supporti i passaggi controllati da script e l'automazione basata sui trigger.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura delle definizioni dei criteri](./definition-structure.md).
- Informazioni sulla [struttura di assegnazione dei criteri](./assignment-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
