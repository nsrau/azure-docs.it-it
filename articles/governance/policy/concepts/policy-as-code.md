---
title: Progettare flussi di lavoro di criteri come codice
description: Informazioni su come progettare i flussi di lavoro per distribuire le definizioni di criteri di Azure come codice e per convalidare automaticamente le risorse.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267271"
---
# <a name="design-policy-as-code-workflows"></a>Progettare flussi di lavoro di criteri come codice

Man mano che si procede con la governance del cloud, è opportuno passare dalla gestione manuale di ogni definizione di criteri nel portale di Azure o tramite i vari SDK a qualcosa di più gestibile e ripetibile su scala aziendale. Due degli approcci principali per la gestione dei sistemi su larga scala nel cloud sono:

- Infrastruttura come codice: la pratica di trattare il contenuto che definisce gli ambienti, dal Gestione risorse modelli alle definizioni di criteri di Azure ai progetti di Azure, come codice sorgente.
- DevOps: Unione di persone, processi e prodotti per consentire il recapito continuo di valore agli utenti finali.

I criteri come codice sono la combinazione di queste idee. In pratica, Mantieni le definizioni dei criteri nel controllo del codice sorgente e ogni volta che viene apportata una modifica, verifica e convalida la modifica. Tuttavia, questo non dovrebbe essere la portata dei criteri che interessano l'infrastruttura come codice o DevOps.

Il passaggio di convalida deve anche essere un componente di altri flussi di lavoro di integrazione continua o di distribuzione continua. Gli esempi includono la distribuzione di un ambiente dell'applicazione o di un'infrastruttura virtuale. Rendendo la convalida dei criteri di Azure un componente iniziale del processo di compilazione e distribuzione, i team delle applicazioni e delle operazioni individuano se le modifiche non sono conformi, molto prima che sia troppo tardi e si stia tentando di eseguire la distribuzione nell'ambiente di produzione.

## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Il flusso di lavoro generale consigliato per i criteri come codice è simile a questo diagramma:

![Panoramica dei criteri come flusso di lavoro del codice](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Creare e aggiornare le definizioni dei criteri

Le definizioni dei criteri vengono create usando JSON e archiviate nel controllo del codice sorgente. Ogni criterio dispone di un proprio set di file, ad esempio i parametri, le regole e i parametri di ambiente, che devono essere archiviati nella stessa cartella. La struttura seguente è un metodo consigliato per mantenere le definizioni dei criteri nel controllo del codice sorgente.

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

Quando viene aggiunto un nuovo criterio o ne è stato aggiornato uno esistente, il flusso di lavoro deve aggiornare automaticamente la definizione dei criteri in Azure. Il test della definizione dei criteri nuova o aggiornata viene eseguito in un passaggio successivo.

### <a name="create-and-update-initiative-definitions"></a>Creare e aggiornare le definizioni di iniziativa

Analogamente, le iniziative hanno il proprio file JSON e i file correlati che devono essere archiviati nella stessa cartella. La definizione di iniziativa richiede che la definizione dei criteri esista già, pertanto non può essere creata o aggiornata finché l'origine del criterio non è stata aggiornata nel controllo del codice sorgente e quindi aggiornata in Azure. La struttura seguente è un metodo consigliato per mantenere le definizioni di iniziativa nel controllo del codice sorgente:

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

Analogamente alle definizioni di criteri, quando si aggiunge o si aggiorna un'iniziativa esistente, il flusso di lavoro deve aggiornare automaticamente la definizione di iniziativa in Azure. Il test della definizione di iniziativa nuova o aggiornata viene eseguito in un passaggio successivo.

### <a name="test-and-validate-the-updated-definition"></a>Testare e convalidare la definizione aggiornata

Una volta che l'automazione ha eseguito le definizioni dei criteri o delle iniziative appena create o aggiornate ed eseguito l'aggiornamento all'oggetto in Azure, è possibile testare le modifiche apportate. I criteri o le iniziative di cui fa parte devono quindi essere assegnati alle risorse nell'ambiente più lontano dalla produzione. Questo ambiente è in genere lo _sviluppatore_.

L'assegnazione deve usare [enforcementMode](./assignment-structure.md#enforcement-mode) di _disabilitato_ in modo che la creazione e gli aggiornamenti delle risorse non siano bloccati, ma che le risorse esistenti siano ancora controllate per la conformità alla definizione dei criteri aggiornata. Anche con enforcementMode, è consigliabile che l'ambito di assegnazione sia un gruppo di risorse o una sottoscrizione usata in modo specifico per la convalida dei criteri.

> [!NOTE]
> Sebbene la modalità di imposizione sia utile, non è una sostituzione per il test accurato di una definizione dei criteri in varie condizioni. La definizione dei criteri deve essere testata con `PUT` e `PATCH` chiamate API REST, risorse conformi e non conformi e case perimetrali come una proprietà mancante dalla risorsa.

Dopo aver distribuito l'assegnazione, usare l'SDK dei criteri per [ottenere i dati di conformità](../how-to/get-compliance-data.md) per la nuova assegnazione. L'ambiente usato per testare i criteri e le assegnazioni deve avere risorse sia conformi che non conformi. Come un buon unit test per il codice, è opportuno verificare che le risorse siano come previsto e che non siano presenti falsi positivi o falsi negativi. Se si esegue il test e la convalida solo per ciò che si prevede, potrebbe verificarsi un effetto imprevisto e non identificato dal criterio. Per altre informazioni, vedere [valutare l'effetto di un nuovo criterio di Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Abilitare le attività di correzione

Se la convalida dell'assegnazione soddisfa le aspettative, il passaggio successivo consiste nel convalidare la correzione.
I criteri che usano [deployIfNotExists](./effects.md#deployifnotexists) o [Modify](./effects.md#modify) possono essere convertiti in un'attività di monitoraggio e aggiornamento e correggono le risorse da uno stato non conforme.

Il primo passaggio per eseguire questa operazione consiste nel concedere all'assegnazione dei criteri l'assegnazione di ruolo definita nella definizione dei criteri. Questa assegnazione di ruolo assegna all'identità gestita di assegnazione dei criteri un numero sufficiente di diritti per apportare le modifiche necessarie per rendere la conformità della risorsa.

Quando l'assegnazione di criteri dispone dei diritti appropriati, usare l'SDK dei criteri per attivare un'attività di monitoraggio e aggiornamento su un set di risorse note come non conformi. Prima di procedere, è necessario completare tre test per le attività correttive seguenti:

- Verificare che l'attività di monitoraggio e aggiornamento sia stata completata correttamente
- Eseguire la valutazione dei criteri per verificare che i risultati di conformità dei criteri siano aggiornati come previsto
- Eseguire un ambiente unit test direttamente sulle risorse per convalidare le proprietà modificate

Il test di entrambi i risultati di valutazione dei criteri aggiornati e l'ambiente forniscono direttamente conferma che le attività di correzione hanno modificato il previsto e che la definizione dei criteri ha rilevato la modifica della conformità come previsto.

### <a name="update-to-enforced-assignments"></a>Aggiornare le assegnazioni applicate

Al termine di tutte le attività di controllo di convalida, aggiornare l'assegnazione per usare **enforcementMode** di _Enabled_. Questa modifica deve essere inizialmente effettuata nello stesso ambiente lontano dalla produzione. Una volta che l'ambiente è stato convalidato come previsto, la modifica deve essere inclusa nell'ambito per includere l'ambiente successivo e così via fino a quando il criterio non viene distribuito nelle risorse di produzione.

## <a name="process-integrated-evaluations"></a>Valutazioni integrate del processo

Il flusso di lavoro generale per i criteri come codice è per lo sviluppo e la distribuzione di criteri e iniziative in un ambiente su larga scala. La valutazione dei criteri, tuttavia, deve far parte del processo di distribuzione per tutti i flussi di lavoro che distribuiscono o creano risorse in Azure, ad esempio la distribuzione di applicazioni o l'esecuzione di Gestione risorse modelli per creare un'infrastruttura.

In questi casi, dopo aver eseguito la distribuzione dell'applicazione o dell'infrastruttura a una sottoscrizione o a un gruppo di risorse di test, è necessario eseguire la valutazione dei criteri per tale ambito verificando la convalida di tutti i criteri e le iniziative esistenti. Sebbene possano essere configurate come **enforcementMode** _disabilitate_ in un ambiente di questo tipo, è utile essere a conoscenza prima che la distribuzione di un'applicazione o di un'infrastruttura violi le definizioni dei criteri in anticipo. Questa valutazione dei criteri deve quindi essere un passaggio di tali flussi di lavoro e le distribuzioni non riuscite che creano risorse non conformi.

## <a name="review"></a>Revisione

Questo articolo illustra il flusso di lavoro generale per i criteri come codice e anche per la valutazione dei criteri che deve far parte di altri flussi di lavoro di distribuzione. Questo flusso di lavoro può essere usato in qualsiasi ambiente che supporta i passaggi con script e l'automazione basata sui trigger.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura della definizione dei criteri](./definition-structure.md).
- Informazioni sulla [struttura di assegnazione dei criteri](./assignment-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/get-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).