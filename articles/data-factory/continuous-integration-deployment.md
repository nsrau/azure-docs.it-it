---
title: Integrazione e recapito continui in Azure Data Factory
description: Informazioni su come usare l'integrazione e il recapito continui per spostare le pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 4de682bd315eef100bdbf8dd24faa128c5b8c2a1
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815811"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integrazione e recapito continui in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Panoramica

L'integrazione continua è la procedura che consente di testare ogni modifica apportata alla base di codici automaticamente e quanto prima possibile. Il recapito continuo segue i test eseguiti durante l'integrazione continua ed esegue il push delle modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory per integrazione e recapito continui (CI/CD) si intende lo spostamento delle pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro. Azure Data Factory usa [modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) per archiviare la configurazione di varie entità ADF (pipeline, set di dati, flussi di dati e così via). Per alzare di livello una data factory a un altro ambiente, esistono due metodi consigliati:

-    Distribuzione automatizzata tramite l'integrazione di Data Factory con [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
-    Caricamento manuale di un modello di Resource Manager usando l'integrazione di Data Factory UX con Azure Resource Manager.

Per un'introduzione di nove minuti a questa funzionalità e una dimostrazione, guardare questo video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo di vita CI/CD

Di seguito è riportata una panoramica di esempio del ciclo di vita CI/CD in una data factory di Azure configurata con Azure Repos Git. Per altre informazioni su come configurare un repository Git, vedere [Controllo del codice sorgente in Azure Data Factory](source-control.md).

1.  Una data factory di sviluppo viene creata e configurata con Azure Repos Git. Tutti gli sviluppatori devono disporre dell'autorizzazione per creare risorse di Data Factory come pipeline e set di dati.

1.  Uno sviluppatore [crea un ramo di funzionalità](source-control.md#creating-feature-branches) per apportare una modifica. Viene eseguito il debug delle esecuzioni della pipeline con le modifiche più recenti. Per altre informazioni su come eseguire il debug di un'esecuzione di pipeline, vedere [Sviluppo e debug iterativi con Azure Data Factory](iterative-development-debugging.md).

1.  Quando uno sviluppatore è soddisfatto delle modifiche, può creare una richiesta pull dal proprio ramo di funzionalità al ramo principale o al ramo di collaborazione per far esaminare le modifiche ai colleghi.

1.  Una volta che una richiesta pull è stata approvata e le modifiche sono state unite nel ramo principale, queste vengono pubblicate nella factory di sviluppo.

1.  Quando il team è pronto a distribuire le modifiche a una factory di test o UAT (test di accettazione utente), il team passa alla versione Azure Pipelines e distribuisce la versione desiderata della factory di sviluppo in UAT. Questa distribuzione viene eseguita come parte di un'attività di Azure Pipelines e usa i parametri di modello di Resource Manager per applicare la configurazione appropriata.

1.  Una volta verificate le modifiche nella factory di test, eseguire la distribuzione nella factory di produzione usando l'attività successiva della versione delle pipeline.

> [!NOTE]
> Solo la factory di sviluppo è associata a un repository Git. Le factory di test e di produzione non devono essere associate a un repository Git e devono essere aggiornate solo tramite una pipeline di Azure DevOps o tramite un modello di Resource Management.

Nell'immagine seguente vengono evidenziati i diversi passaggi di questo ciclo di vita.

![Diagramma dell'integrazione continua con Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizzare l'integrazione continua con le versioni di Azure Pipelines

Di seguito è riportata una guida per la configurazione di una versione di Azure Pipelines che automatizza la distribuzione di una data factory in più ambienti.

### <a name="requirements"></a>Requisiti

-   Una sottoscrizione di Azure collegata a Visual Studio Team Foundation Server o ad Azure Repos con l' [endpoint servizio di Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Una data factory configurata con l'integrazione Git per Azure Repos.

-   Un  [insieme di credenziali delle chiavi di Azure](https://azure.microsoft.com/services/key-vault/) che contiene i segreti per ogni ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurare una versione di Azure Pipelines

1.  In [Azure DevOps](https://dev.azure.com/) aprire il progetto configurato con la data factory.

1.  Sul lato sinistro della pagina selezionare **Pipeline**, quindi selezionare **Versioni**.

    ![Selezionare Pipeline, Versioni](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selezionare **Nuova pipeline** oppure, se sono presenti pipeline esistenti, selezionare **Nuova**, quindi **Nuova pipeline di versione**.

1.  Selezionare il modello **Fase vuota**.

    ![Selezionare Fase vuota](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Nella casella **Nome fase** immettere il nome dell'ambiente.

1.  Selezionare **Aggiungi artefatto**, quindi selezionare il repository Git configurato con la data factory di sviluppo. Selezionare la [pubblicazione del ramo](source-control.md#configure-publishing-settings) del repository per il **ramo predefinito**. Per impostazione predefinita, questa pubblicazione del ramo è `adf_publish`. Per la **versione predefinita**, selezionare **Più recente dal ramo predefinito**.

    ![Aggiungere un elemento](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Aggiungere un'attività di distribuzione di Azure Resource Manager:

    a.  Nella visualizzazione dei passaggi selezionare **Visualizza le attività della fase**.

    ![Visualizzazione dei passaggi](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Creare una nuova attività. Cercare la **distribuzione del modello ARM**e quindi selezionare **Aggiungi**.

    c.  Nell'attività Distribuzione scegliere la sottoscrizione, il gruppo di risorse e la posizione per la data factory di destinazione. Fornire le credenziali, se necessario.

    d.  Nell'elenco **Azione** selezionare **Creare o aggiornare un gruppo di risorse**.

    e.  Selezionare il pulsante con i puntini di sospensione ( **...** ) accanto alla casella **Modello**. Cercare il modello di Azure Resource Manager generato nel ramo di pubblicazione del repository Git configurato. Cercare il file `ARMTemplateForFactory.json` nella cartella <FactoryName> del ramo adf_publish.

    f.  Selezionare **...** accanto alla casella **Parametri modello** per scegliere il file dei parametri. Cercare il file `ARMTemplateParametersForFactory.json` nella cartella <FactoryName> del ramo adf_publish.

    g.  Selezionare **...** accanto alla casella **Esegui override dei parametri del modello** e immettere i valori dei parametri desiderati per la data factory di destinazione. Per le credenziali provenienti da Azure Key Vault, immettere il nome del segreto tra virgolette doppie. Se, ad esempio, il nome del segreto è cred1, immettere **"$(cred1)"** per questo valore.

    h. Selezionare **Incrementale** per la **Modalità di distribuzione**.

    > [!WARNING]
    > In modalità di distribuzione completa, le risorse esistenti nel gruppo di risorse, ma non specificate nel nuovo modello di Gestione risorse verranno **eliminate**. Per altre informazioni, vedere [Azure Resource Manager modalità di distribuzione](../azure-resource-manager/templates/deployment-modes.md)

    ![Distribuzione di produzione della Data Factory](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salvare la pipeline di versione.

1. Per attivare una versione, selezionare **Crea versione**. Per automatizzare la creazione di versioni, vedere [Trigger versione di Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![Selezionare Crea versione](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Negli scenari CI/CD, il tipo di runtime di integrazione (IR) in ambienti diversi deve essere lo stesso. Ad esempio, se si dispone di un runtime di integrazione self-hosted nell'ambiente di sviluppo, anche lo stesso runtime di integrazione deve essere di tipo self-hosted in altri ambienti, come quelli di test e produzione. Analogamente, se si condividono runtime di integrazione tra più fasi, è necessario configurarli come self-hosted collegati in tutti gli ambienti, ad esempio quelli di sviluppo, test e produzione.

### <a name="get-secrets-from-azure-key-vault"></a>Ottenere segreti da Azure Key Vault

Se sono presenti segreti da passare in un modello di Azure Resource Manager, è consigliabile usare Azure Key Vault con la versione di Azure Pipelines.

Esistono due modi per gestire i segreti:

1.  Aggiungere i segreti al file dei parametri. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../azure-resource-manager/templates/key-vault-parameter.md).

    Creare una copia del file dei parametri caricato nel ramo di pubblicazione. Impostare i valori dei parametri che si desidera ottenere da Key Vault usando il formato seguente:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Quando si usa questo metodo, viene eseguito il pull automatico del segreto dall'insieme di credenziali delle chiavi.

    Il file dei parametri deve essere anche nel ramo di pubblicazione.

1. Aggiungere un'[attività di Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) prima dell'attività di distribuzione di Azure Resource Manager descritta nella sezione precedente:

    1.  Nella scheda **Attività** creare una nuova attività. Cercare **Azure Key Vault** e aggiungerlo.

    1.  Nell'attività di Key Vault selezionare la sottoscrizione in cui è stato creato l'insieme di credenziali delle chiavi. Fornire le credenziali, se necessario, quindi selezionare l'insieme di credenziali delle chiavi.

    ![Aggiungere un'attività di Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Concedere le autorizzazioni all'agente di Azure Pipelines

L'attività di Azure Key Vault potrebbe non riuscire con un errore di accesso negato se non sono impostate le autorizzazioni corrette. Scaricare i log per la versione e individuare il file con estensione ps1 contenente il comando per concedere le autorizzazioni all'agente di Azure Pipelines. È possibile eseguire il comando direttamente. In alternativa, è possibile copiare l'ID dell'entità di sicurezza dal file e aggiungere manualmente il criterio di accesso nel portale di Azure. `Get` e `List` sono le autorizzazioni minime necessarie.

### <a name="updating-active-triggers"></a>Aggiornamento di trigger attivi

La distribuzione può non riuscire se si prova ad aggiornare i trigger attivi. Per aggiornare trigger attivi, è necessario arrestarli manualmente e riavviarli dopo la distribuzione. Questa operazione può essere eseguita usando un'attività di Azure PowerShell:

1.  Nella scheda **Attività** della versione aggiungere un'**attività di Azure PowerShell**. Scegliere la versione dell'attività 4.*. 

1.  Selezionare la sottoscrizione in cui risiede la factory.

1.  Selezionare **Percorso file script** come tipo di script. A questo scopo, è necessario salvare lo script di PowerShell nel repository. Lo script di PowerShell seguente può essere usato per interrompere trigger:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

È possibile completare passaggi simili (con la funzione `Start-AzDataFactoryV2Trigger`) per riavviare i trigger dopo la distribuzione.

Il team della data factory ha fornito uno [script pre-distribuzione e post-distribuzione di esempio](#script) disponibile alla fine di questo articolo. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Alzare di livello manualmente un modello di Resource Manager per ogni ambiente

1. Nell'elenco **Modello ARM** selezionare **Esporta modello ARM** per esportare il modello di Resource Manager per la data factory nell'ambiente di sviluppo.

   ![Esportare un modello di Resource Manager](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Nelle data factory di test e di produzione selezionare **Importa modello ARM**. Questa azione consente di passare al portale di Azure, in cui è possibile importare il modello esportato. Selezionare **Creare un modello personalizzato nell'editor** per aprire l'editor modelli di Resource Manager.

   ![Creare un modello personalizzato](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selezionare **Carica file**, quindi selezionare il modello di Resource Manager generato. Si tratta del file **arm_template.json** che si trova nel file con estensione zip esportato nel passaggio 1.

   ![Modifica del modello](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Nella sezione Impostazioni immettere i valori di configurazione, ad esempio le credenziali del servizio collegato. Al termine, selezionare **Acquisto** per distribuire il modello di Resource Manager.

   ![Sezione Impostazioni](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usare i parametri personalizzati con il modello di Resource Manager

Se la factory di sviluppo dispone di un repository Git associato, è possibile eseguire l'override dei parametri predefiniti del modello di Resource Manager generato pubblicando o esportando il modello. In questi scenari potrebbe essere necessario eseguire l'override del modello di parametrizzazione predefinito:

* Si usano CI/CD automatizzati e si desidera modificare alcune proprietà durante la distribuzione di Resource Manager, ma le proprietà non sono parametrizzate per impostazione predefinita.
* La factory è talmente grande che il modello di Resource Manager predefinito non è valido perché contiene più del numero massimo di parametri consentito (256).

Per eseguire l'override del modello di parametrizzazione predefinito, creare un file denominato **arm-template-parameters-definition.json** nella cartella radice del ramo Git. È necessario usare il nome file esatto.

   ![File dei parametri personalizzati](media/continuous-integration-deployment/custom-parameters.png)

Quando si esegue la pubblicazione dal ramo di collaborazione, Data Factory leggerà questo file e userà la relativa configurazione per generare le proprietà da parametrizzare. Se non viene trovato alcun file, viene usato il modello predefinito.

Quando si esporta un modello di Resource Manager, Data Factory legge questo file da qualunque ramo in cui si sta lavorando, non solo dal ramo di collaborazione. È possibile creare o modificare il file da un ramo privato, in cui è possibile testare le modifiche selezionando **Esporta modello ARM** nell'interfaccia utente. È quindi possibile unire il file nel ramo di collaborazione.

> [!NOTE]
> Un modello di parametrizzazione personalizzato non modifica il limite di parametri del modello ARM di 256. Consente di scegliere e diminuire il numero di proprietà con parametri.

### <a name="custom-parameter-syntax"></a>Sintassi dei parametri personalizzata

Di seguito sono riportate alcune linee guida da seguire quando si crea il file di parametri personalizzati **arm-template-parameters-definition.json**. Il file è costituito da una sezione per ogni tipo di entità: trigger, pipeline, servizio collegato, set di dati, runtime di integrazione e flusso di dati.

* Immettere il percorso della proprietà nel tipo di entità pertinente.
* L'impostazione di un nome di proprietà su `*` indica che si desidera parametrizzare tutte le proprietà al suo interno (solo fino al primo livello, non in modo ricorsivo). È anche possibile fornire eccezioni a questa configurazione.
* Quando si imposta il valore di una proprietà come stringa, si indica che si vuole parametrizzare la proprietà. Usare il formato `<action>:<name>:<stype>`.
   *  `<action>` può essere uno di questi caratteri:
      * `=` specifica di mantenere il valore corrente come valore predefinito per il parametro.
      * `-` specifica di non mantenere il valore predefinito per il parametro.
      * `|` è un caso speciale per i segreti di Azure Key Vault per stringhe di connessione o chiavi.
   * `<name>` è il nome del parametro. Se è vuoto, viene usato il nome della proprietà. Se il valore inizia con un carattere `-`, il nome viene abbreviato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` viene abbreviato in `AzureStorage1_connectionString`.
   * `<stype>` è il tipo di parametro. Se `<stype>` è vuoto, il tipo predefinito è `string`. I valori supportati sono: `string`, `bool`, `number`, `object` e `securestring`.
* Quando si specifica una matrice nel file di definizione, si indica che la proprietà corrispondente nel modello è una matrice. Data Factory esegue l'iterazione tra tutti gli oggetti della matrice usando la definizione specificata nell'oggetto runtime di integrazione della matrice. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Una definizione non può essere specifica di un'istanza di risorsa. Qualunque definizione viene applicata a tutte le risorse di quel tipo.
* Per impostazione predefinita, vengono parametrizzate tutte le stringhe sicure, ad esempio i segreti di Key Vault, e le stringhe sicure, ad esempio le stringhe di connessione, le chiavi e i token.
 
### <a name="sample-parameterization-template"></a>Modello di parametrizzazione di esempio

Di seguito è riportato un esempio dell'aspetto che potrebbe avere un modello di parametrizzazione:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Di seguito è riportata una spiegazione del modo in cui viene costruito il modello precedente, suddiviso per tipo di risorsa.

#### <a name="pipelines"></a>Pipeline
    
* Qualunque proprietà nel percorso `activities/typeProperties/waitTimeInSeconds` è parametrizzata. Qualunque attività in una pipeline che dispone di una proprietà a livello di codice denominata `waitTimeInSeconds` (ad esempio, l'attività `Wait`) viene parametrizzata come numero, con un nome predefinito. Non avrà tuttavia un valore predefinito nel modello di Resource Manager. Sarà un input obbligatorio durante la distribuzione di Resource Manager.
* Analogamente, una proprietà denominata `headers` (ad esempio, in un'attività `Web`) viene parametrizzata con il tipo `object` (JObject). Ha un valore predefinito, che è lo stesso valore di quello della factory di origine.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Tutte le proprietà nel percorso `typeProperties` vengono parametrizzate con i rispettivi valori predefiniti. Esistono ad esempio due proprietà nelle proprietà del tipo `IntegrationRuntimes`: `computeProperties` e `ssisProperties`. Entrambi i tipi di proprietà vengono creati con i rispettivi valori e tipi predefiniti (oggetto).

#### <a name="triggers"></a>Trigger

* In `typeProperties`, sono parametrizzate due proprietà. La prima è `maxConcurrency`, che è specificata per avere un valore predefinito ed è di tipo`string`. Ha il nome di parametro predefinito `<entityName>_properties_typeProperties_maxConcurrency`.
* Anche la proprietà `recurrence` è parametrizzata. Al suo interno, tutte le proprietà a tale livello vengono specificate per essere parametrizzate come stringhe, con valori predefiniti e nomi di parametro. Un'eccezione è la proprietà `interval`, che è parametrizzata come tipo `number`. Il nome del parametro ha il suffisso `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Analogamente, la proprietà `freq` è una stringa e viene parametrizzata come stringa. Tuttavia, la proprietà `freq` è parametrizzata senza un valore predefinito. Il nome viene abbreviato e seguito da un suffisso. Ad esempio: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* I servizi collegati sono univoci. Poiché i servizi collegati e i set di dati hanno un'ampia gamma di tipi, è possibile fornire una personalizzazione specifica del tipo. In questo esempio, per tutti i servizi collegati di tipo `AzureDataLakeStore`, verrà applicato un modello specifico. Per tutti gli altri (tramite `*`), verrà applicato un modello diverso.
* La proprietà `connectionString` verrà parametrizzata come valore `securestring`. Non avrà un valore predefinito. Avrà un nome di parametro abbreviato con il suffisso `connectionString`.
* La proprietà `secretAccessKey` è un `AzureKeyVaultSecret`, ad esempio in un servizio collegato Amazon S3. Viene parametrizzata automaticamente come segreto di Azure Key Vault e recuperata dall'insieme di credenziali delle chiavi configurato. È anche possibile parametrizzare l'insieme di credenziali delle chiavi stesso.

#### <a name="datasets"></a>Set di dati

* Sebbene la personalizzazione specifica del tipo sia disponibile per i set di dati, è possibile fornire la configurazione senza avere esplicitamente una configurazione a livello di \*. Nell'esempio precedente, vengono parametrizzate tutte le proprietà del set di dati in `typeProperties`.

### <a name="default-parameterization-template"></a>Modello di parametrizzazione predefinito

Di seguito è riportato il modello di parametrizzazione predefinito corrente. Se è necessario aggiungere solo alcuni parametri, la modifica diretta di questo modello potrebbe essere opportuna, perché non si perderà la struttura di parametrizzazione esistente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Esempio: parametrizzazione di un ID cluster interattivo di Azure Databricks esistente

L'esempio seguente illustra come aggiungere un singolo valore al modello di parametrizzazione predefinito. Si vuole aggiungere solo un ID cluster interattivo Azure Databricks esistente per un servizio collegato Databricks al file dei parametri. Si noti che questo file corrisponde al file precedente, ad eccezione dell'aggiunta di `existingClusterId` nel campo delle proprietà di `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelli di Resource Manager collegati

Se sono stati configurati CI/CD per le data factory, è possibile che vengano superati i limiti del modello di Azure Resource Manager man mano che le dimensioni della factory aumentano. Ad esempio, un limite è il numero massimo di risorse in un modello di Resource Manager. Per contenere factory di grandi dimensioni durante la generazione del modello di Resource Manager completo per una factory, Data Factory ora genera anche modelli di Resource Manager collegati. Con questa funzionalità, l'intero payload della factory viene suddiviso in diversi file in modo da non essere vincolati ai limiti.

Se Git è stato configurato, i modelli collegati vengono generati e salvati insieme ai modelli di Resource Manager completi nel ramo adf_publish, in una nuova cartella denominata linkedTemplates:

![Cartella dei modelli di Resource Manager collegati](media/continuous-integration-deployment/linked-resource-manager-templates.png)

I modelli di Resource Manager collegati in genere comprendono un modello master e un set di modelli figlio collegati al master. Il modello padre è denominato ArmTemplate_master.json e i modelli figlio vengono denominati con il modello ArmTemplate_0.json, ArmTemplate_1.json e così via. 

Per usare modelli collegati anziché il modello di Resource Manager completo, aggiornare l'attività CI/CD in modo che punti ad ArmTemplate_master.json anziché ad ArmTemplateForFactory.json (il modello di Resource Manager completo). Resource Manager richiede anche di caricare i modelli collegati in un account di archiviazione, in modo che Azure possa accedervi durante la distribuzione. Per altre informazioni, vedere [Distribuzione di modelli di Resource Manager collegati con VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Ricordarsi di aggiungere gli script di Data Factory nella pipeline CI/CD prima e dopo l'attività di distribuzione.

Se Git non è stato configurato, è possibile accedere ai modelli collegati tramite **Esporta modello ARM** nell'elenco **Modello ARM**.

## <a name="hotfix-production-environment"></a>Ambiente di produzione hotfix

Se si distribuisce una factory in produzione e si nota che è presente un bug che deve essere corretto immediatamente, ma non è possibile distribuire il ramo di collaborazione corrente, potrebbe essere necessario distribuire un hotfix. Questo approccio è noto come QFE (Quick Fix Engineering).

1.    In Azure DevOps passare alla versione che è stata distribuita in produzione. Trovare l'ultimo commit distribuito.

2.    Dal messaggio di commit, ottenere l'ID commit del ramo di collaborazione.

3.    Creare un nuovo ramo di hotfix dal commit.

4.    Passare ad Azure Data Factory UX e quindi al ramo di hotfix.

5.    Con Azure Data Factory UX, correggere il bug. Testare le modifiche.

6.    Dopo aver verificato l'hotfix, selezionare **Esporta modello ARM** per ottenere il modello di Resource Manager per l'hotfix.

7.    Controllare manualmente questa compilazione nel ramo adf_publish.

8.    Se la pipeline di versione è stata configurata in modo da essere attivata automaticamente in base alle archiviazioni di adf_publish, verrà avviata automaticamente una nuova versione. In caso contrario, accodare manualmente una versione.

9.    Distribuire la versione dell'hotfix nelle factory di test e di produzione. Questa versione contiene il payload di produzione precedente e la correzione apportata nel passaggio 5.

10.   Aggiungere le modifiche dall'hotfix al ramo di sviluppo in modo che le versioni successive non includano lo stesso bug.

## <a name="best-practices-for-cicd"></a>Procedure consigliate per la pipeline CI/CD

Se si usa l'integrazione di Git con la data factory e si dispone di una pipeline CI/CD che sposta le modifiche apportate dall'ambiente di sviluppo all'ambiente di test e successivamente all'ambiente di produzione, è consigliabile osservare le procedure consigliate seguenti:

-   **Integrazione di Git**. Configurare solo la data factory di sviluppo con l'integrazione di Git. Le modifiche all'ambiente di test e produzione vengono distribuite tramite CI/CD e non necessitano dell'integrazione di Git.

-   **Script pre-distribuzione e post-distribuzione**. Prima del passaggio di distribuzione di Resource Manager in CI/CD, è necessario completare determinate attività, ad esempio l'arresto e il riavvio dei trigger e l'esecuzione della pulizia. È consigliabile usare gli script di PowerShell prima e dopo l'attività di distribuzione. Per altre informazioni, vedere [Aggiornamento di trigger attivi](#updating-active-triggers). Il team della data factory ha [fornito uno script](#script) da usare alla fine di questa pagina.

-   **Runtime di integrazione e condivisione**. I runtime di integrazione non cambiano spesso e sono simili in tutte le fasi di CI/CD. Di conseguenza, Data Factory prevede che l'utente abbia lo stesso nome e lo stesso tipo di runtime di integrazione in tutte le fasi di CI/CD. Se si desidera condividere runtime di integrazione in tutte le fasi, è consigliabile usare una factory ternaria per contenere solo i runtime di integrazione condivisi. È possibile usare questa factory condivisa in tutti gli ambienti come tipo di runtime di integrazione collegato.

-   **Insieme di credenziali delle chiavi**. Quando si usano servizi collegati le cui informazioni di connessione vengono archiviate in Azure Key Vault, è consigliabile conservare insiemi di credenziali delle chiavi separati per ambienti diversi. È anche possibile configurare i livelli di autorizzazione separati per ogni insieme di credenziali delle chiavi. Ad esempio, è possibile che non si voglia che i membri del team siano autorizzati ad accedere ai segreti di produzione. Se si segue questo approccio, è consigliabile mantenere gli stessi nomi dei segreti in tutte le fasi. Se si mantengono gli stessi nomi dei segreti, non è necessario parametrizzare ogni stringa di connessione negli ambienti CI/CD, perché l'unica cosa che cambia è il nome dell'insieme di credenziali delle chiavi, che è un parametro separato.

## <a name="unsupported-features"></a>Funzionalità non supportate

- Per progettazione, Data Factory non consente il cherry-pick di commit o la pubblicazione selettiva delle risorse. Le pubblicazioni includeranno tutte le modifiche apportate nella data factory.

    - Le entità della data factory dipendono l'una dall'altra. Ad esempio, i trigger dipendono dalle pipeline, le pipeline dipendono dai set di dati e da altre pipeline. La pubblicazione selettiva di un subset di risorse può causare comportamenti imprevisti ed errori.
    - Nei rari casi in cui è necessaria la pubblicazione selettiva, provare a usare un hotfix. Per ulteriori informazioni, vedere [ambiente di produzione hotfix](#hotfix-production-environment).

-   Non è possibile pubblicare da rami privati.

-   Non è attualmente possibile ospitare progetti in Bitbucket.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a>Script pre-distribuzione e post-distribuzione

Lo script di esempio seguente può essere usato per arrestare i trigger prima della distribuzione e riavviarli in seguito. Lo script include anche il codice per eliminare le risorse che sono state rimosse. Salvare lo script in un repository Git di Azure DevOps e farvi riferimento tramite un'attività di Azure PowerShell usando la versione 4.*.

Quando si esegue uno script pre-distribuzione, è necessario specificare una variante dei parametri seguenti nel campo **Argomenti script**.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Quando si esegue uno script post-distribuzione, è necessario specificare una variante dei parametri seguenti nel campo **Argomenti script**.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Attività di Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Di seguito è riportato lo script che può essere usato per pre-distribuzione e post-distribuzione. Tiene in considerazione le risorse eliminate e i riferimenti alle risorse.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
