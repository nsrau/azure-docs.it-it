---
title: Risolvere i problemi relativi a CI-CD, Azure DevOps e GitHub in ADF
description: Usare metodi diversi per risolvere i problemi relativi a CI-CD in ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 11/26/2020
ms.openlocfilehash: f07cc8e3d5e9d6f59671a3c8c2efd9f5fb9f27b7
ms.sourcegitcommit: 236014c3274b31f03e5fcee5de510f9cacdc27a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96299057"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Risolvere i problemi relativi a CI-CD, Azure DevOps e GitHub in ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi relativi a CI-CD, Azure DevOps e GitHub in Azure Data Factory.

In caso di domande o problemi nell'uso delle tecniche di controllo del codice sorgente o di DevOps, di seguito sono riportati alcuni articoli che possono risultare utili:

- Per informazioni sul funzionamento del controllo del codice sorgente in ADF, vedere [controllo del codice sorgente in ADF](source-control.md) . 
- Vedere  [ci-CD in ADF](continuous-integration-deployment.md) per altre informazioni su come DevOps ci-CD viene praticato in ADF.
 
## <a name="common-errors-and-messages"></a>Errori comuni e messaggi

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>La connessione al repository Git non è riuscita a causa di un tenant diverso

#### <a name="issue"></a>Problema
    
A volte si verificano problemi di autenticazione come lo stato HTTP 401. In particolare quando si hanno più tenant con account Guest, le cose potrebbero diventare più complesse.

#### <a name="cause"></a>Causa

Ciò che abbiamo osservato è che il token è stato ottenuto dal tenant originale, ma ADF si trova nel tenant Guest e tenta di usare il token per visitare DevOps nel tenant Guest. Questo non è il comportamento previsto.

#### <a name="recommendation"></a>Recommendation

Usare invece il token emesso dal tenant Guest. Ad esempio, è necessario assegnare lo stesso Azure Active Directory come tenant Guest e DevOps, in modo che sia possibile impostare correttamente il comportamento del token e usare il tenant corretto.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>I parametri del modello nel file dei parametri non sono validi

#### <a name="issue"></a>Problema

Se si elimina un trigger in dev Branch, che è già disponibile nel ramo di test o di produzione con la **stessa** configurazione, ad esempio Frequency e Interval, la distribuzione della pipeline di rilascio ha esito positivo e il trigger corrispondente verrà eliminato nei rispettivi ambienti. Tuttavia, se si dispone di una configurazione **diversa** (ad esempio frequenza e intervallo) per il trigger negli ambienti di test/produzione e se si elimina lo stesso trigger in dev, la distribuzione avrà esito negativo e si verificherà un errore.

#### <a name="cause"></a>Causa

La pipeline CI/CD ha esito negativo con l'errore seguente:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Recommendation

L'errore si verifica perché spesso si elimina un trigger con parametri, di conseguenza i parametri non saranno disponibili nel modello ARM, perché il trigger non esiste più. Poiché il parametro non è più presente nel modello ARM, è necessario aggiornare i parametri sottoposti a override nella pipeline DevOps. In caso contrario, ogni volta che i parametri nel modello ARM cambiano, devono aggiornare i parametri sottoposti a override nella pipeline DevOps (nell'attività di distribuzione).

### <a name="updating-property-type-is-not-supported"></a>Il tipo di proprietà di aggiornamento non è supportato

#### <a name="issue"></a>Problema

La pipeline di rilascio CI/CD ha esito negativo con l'errore seguente:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Causa

Ciò è dovuto a un Integration Runtime con lo stesso nome nella Factory di destinazione ma con un tipo diverso. Integration Runtime deve essere dello stesso tipo durante la distribuzione.

#### <a name="recommendation"></a>Recommendation

- Vedere le procedure consigliate per CI/CD di seguito:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- I runtime di integrazione non cambiano spesso e sono simili in tutte le fasi dell'integrazione continua/recapito continuo, quindi Data Factory si prevede di avere lo stesso nome e tipo di runtime di integrazione in tutte le fasi di CI/CD. Se il nome e i tipi & proprietà sono diversi, verificare che corrispondano alla configurazione IR di origine e di destinazione e quindi distribuire la pipeline di rilascio.
- Se si desidera condividere runtime di integrazione in tutte le fasi, è consigliabile usare una factory ternaria per contenere solo i runtime di integrazione condivisi. È possibile usare questa factory condivisa in tutti gli ambienti come tipo di runtime di integrazione collegato.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Impossibile creare o aggiornare il documento a causa di un riferimento non valido

#### <a name="issue"></a>Problema

Quando si tenta di pubblicare le modifiche in un Data Factory, viene riportato il messaggio di errore seguente:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Sintomo

La configurazione di Git è stata scollegata e configurata di nuovo con il flag "Importa risorse" selezionato, che imposta il Data Factory come "sincronizzato". Ciò significa che non viene modificata la pubblicazione.

**Risoluzione**

Scollegare la configurazione git e configurarla di nuovo e assicurarsi di non selezionare la casella di controllo "Importa risorse esistenti".

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory spostare il failover da un gruppo di risorse a un altro

#### <a name="issue"></a>Problema

Non è possibile spostare Data Factory da un gruppo di risorse a un altro, senza errori con l'errore seguente:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Risoluzione

Per consentire l'operazione di spostamento, è necessario eliminare i componenti SSIS-IR e l'IRs condiviso. Se non si vuole eliminare l'IRs, il modo migliore consiste nel seguire il documento di copia e clonazione per eseguire la copia e, al termine dell'operazione, eliminare la data factory precedente.

###  <a name="unable-to-export-and-import-arm-template"></a>Non è possibile esportare e importare il modello ARM

#### <a name="issue"></a>Problema

Non è possibile esportare e importare il modello ARM. Tuttavia, nella traccia del browser non si è verificato alcun errore nel portale. è possibile che venga visualizzato l'errore seguente:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Causa

È stato creato un ruolo del cliente come utente e non dispone dell'autorizzazione necessaria. Quando la factory viene caricata nell'interfaccia utente, viene controllata una serie di valori di controllo dell'esposizione per la factory. In questo caso, il ruolo di accesso dell'utente non è autorizzato ad accedere all'API *queryFeaturesValue* . Per accedere a questa API, la funzionalità parametri globali è disattivata. Il percorso del codice di esportazione ARM si basa in parte sulla funzionalità parametri globali.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema, è necessario aggiungere l'autorizzazione seguente al ruolo: *Microsoft. DataFactory/factorys/queryFeaturesValue/Action*. Per impostazione predefinita, questa autorizzazione deve essere inclusa nel ruolo "collaboratore Data Factory".

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)


 
