---
title: Utilizzo dei profili versione API con Ruby nello Stack di Azure | Documenti Microsoft
description: Informazioni sull'utilizzo dei profili versione API con Ruby nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 98d285bf8ec92e7b6baf709d4796ce2f4420c4f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Usare i profili di versione API con Ruby nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Profili versione Ruby e API

il SDK di Ruby per Gestione risorse di Azure Stack fornisce strumenti che consentono di compilare e gestire l'infrastruttura. Provider di risorse SDK includono calcolo, le reti virtuali e archiviazione con il linguaggio Ruby. I profili di API in SDK Ruby consentono lo sviluppo di cloud ibrido grazie alla possibilità di passare tra le risorse di Azure globale e le risorse sullo Stack di Azure.

Un profilo di API è una combinazione di versioni del servizio e i provider di risorse. È possibile utilizzare un profilo di API per combinare diversi tipi di risorse.

 - Per utilizzare le versioni più recenti di tutti i servizi, utilizzare il **più recente** profilo dell'indicatore di rollup di Azure SDK.
 - Per usare i servizi compatibili con lo Stack di Azure, usare il **V2017_03_09** profilo dell'indicatore di rollup di Azure SDK.
 - Per usare la versione api più recente di un servizio, usare il **più recente** profilo dell'indicatore specifico. Ad esempio, se si desidera utilizzare la versione api più recente del servizio di calcolo da solo, usare il **più recente** profilo del **calcolo** indicatore.
 - Per utilizzare api-version specifico per un servizio, utilizzare le versioni API specifiche definite all'interno di indicatore.

> [!note] 
> È possibile combinare tutte le opzioni nella stessa applicazione.

## <a name="install-the-azure-ruby-sdk"></a>Installare Azure SDK Ruby

 - Seguire le istruzioni ufficiale per installare [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Seguire le istruzioni ufficiale per installare [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Durante l'installazione di scegliere **aggiungere Ruby alla variabile PATH**
    - Installare il kit di sviluppo durante l'installazione Ruby quando richiesto.
    - Installare quindi bundler usando il comando seguente:  
      `Gem install bundler`
 - Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da utilizzare in un secondo momento. Le istruzioni per creare una sottoscrizione si [qui](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Creare un'entità servizio e salvare il relativo ID e il segreto. Le istruzioni per creare un'entità servizio per lo Stack di Azure si [qui](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Verificare che l'entità servizio disponga del ruolo di collaboratore/proprietario per la sottoscrizione. Le istruzioni su come assegnare ruoli a un'entità servizio sono [qui](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Installare i pacchetti rubygem

È possibile installare i pacchetti di azure rubygem direttamente.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Tenere presente il Ruby Gestione risorse di Azure SDK è disponibile in anteprima e molto probabilmente avranno interfaccia modifiche di rilievo nelle prossime versioni. Un maggior numero di versione secondaria può indicare modifiche di rilievo.

## <a name="usage-of-the-azuresdk-gem"></a>Utilizzo dell'indicatore azure_sdk

L'indicatore, azure_sdk, è un rollup di tutti i gems supportati in SDK Ruby. Questo indicatore è costituito da un **più recente** profile, che supporta la versione più recente di tutti i servizi. Introduce un profilo con controllo delle versioni **V2017_03_09** profilo, che viene compilato per lo Stack di Azure.

È possibile installare l'indicatore di rollup azure_sdk con il comando seguente:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Prerequisito

Per utilizzare Ruby Azure SDK con lo Stack di Azure, è necessario fornire i valori seguenti e quindi impostare i valori con le variabili di ambiente. Vedere le istruzioni dopo la tabella per il sistema operativo su come impostare le variabili di ambiente. 

| Valore | Variabili di ambiente | DESCRIZIONE | 
| --- | --- | --- | --- |
| ID tenant | AZURE_TENANT_ID | Il valore dello Stack di Azure [ID tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID client | AZURE_CLIENT_ID | Il servizio ID principale dell'applicazione salvati quando un'entità servizio è stata creata nella sezione precedente di questo documento.  |
| ID sottoscrizione | AZURE_SUBSCRIPTION_ID | Il [ID sottoscrizione](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) è la modalità di accesso offre nello Stack di Azure. |
| Client Secret | AZURE_CLIENT_SECRET | L'applicazione dell'entità servizio segreto salvato alla creazione dell'entità servizio. |
| Endpoint di gestione risorse | ARM_ENDPOINT | Vedere [endpoint di gestione risorse di Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>L'endpoint di gestione risorse di Azure Stack

Gestione risorse di Microsoft Azure è un framework di gestione che consente agli amministratori di distribuire, gestire e monitorare le risorse di Azure. Gestione risorse di Azure è possibile gestire queste attività come un gruppo, anziché singolarmente, in un'unica operazione.

È possibile ottenere le informazioni sui metadati dall'endpoint di gestione risorse. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice.
  > [!note]  
  > Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/`  
  > Il **ResourceManagerUrl** in sistemi integrati: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  File JSON di esempio:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>Set di variabili di ambiente

**Microsoft Windows**  
Per impostare le variabili di ambiente, nel prompt dei comandi di Windows, usare il formato seguente:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**sistemi basati su Unix, Linux e macOS**  
Nei sistemi basati su Unix, è possibile utilizzare il comando, ad esempio:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Profili API esistenti

L'indicatore di rollup azure_sdk contiene i due profili seguenti:

1. **V2017_03_09**  
  Profilo compilato per lo Stack di Azure. Usare questo profilo per i servizi per essere più compatibile con lo Stack di Azure.
2. **Più recente**  
  Profilo è costituito da versioni più recenti di tutti i servizi. Utilizzare le versioni più recenti di tutti i servizi.

Per ulteriori informazioni sui profili dello Stack di Azure e l'API, vedere una [profili di riepilogo dell'API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure-utilizzo API SDK Ruby profilo

Le righe seguenti devono essere utilizzate per creare un client di profilo. Questo parametro è solo necessario per lo Stack di Azure o altri cloud privati. Azure globale dispone già di queste impostazioni per impostazione predefinita.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

Il client di profilo può essere utilizzato per l'accesso ai provider di risorse singole, ad esempio di calcolo, archiviazione e rete.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Definire le funzioni di impostazione ambiente AzureStack

Per autenticare l'entità servizio per l'ambiente dello Stack di Azure, definire l'endpoint che utilizzano **get_active_directory_settings()**. Questo metodo Usa il **ARM_Endpoint** variabile di ambiente impostate quando si stabiliscono le variabili ambientali.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Esempi di utilizzo dei profili di API

È possibile utilizzare negli esempi seguenti disponibili in GitHub repositoreis come un riferimento di creazione di soluzioni con i profili Ruby e API dello Stack di Azure:

 - [Gestire risorse e gruppi di risorse di Azure con Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Gestire macchine virtuali utilizzando Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Gestione risorse di esempio e i gruppi

Per eseguire l'esempio, assicurarsi di aver installato Ruby. Se si utilizza codice di Visual Studio, scaricare il SDK Ruby come anche un'estensione. 

> [!note]  
> È possibile ottenere il repository per l'esempio in "[Azure gestire risorse e gruppi di risorse con Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Clonare il repository.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Installare le dipendenze utilizzando bundle.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Creare un'entità servizio di Azure con PowerShell e recuperare i valori necessari. 

  Per istruzioni sulla creazione di un'entità servizio, vedere [utilizzo di Azure PowerShell per creare un'entità servizio con un certificato](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  I valori necessiti sono:
  - ID tenant
  - ID client
  - Client Secret
  - ID sottoscrizione
  - Endpoint di gestione risorse

  Impostare le seguenti variabili di ambiente usando le informazioni di cui che è stato recuperato dall'entità servizio è stato creato.

  - esportare AZURE_TENANT_ID = {id tenant}
  - esportare AZURE_CLIENT_ID = {l'id client}
  - esportare AZURE_CLIENT_SECRET = {tua chiave privata client}
  - esportare AZURE_SUBSCRIPTION_ID = {l'id sottoscrizione}
  - esportare ARM_ENDPOINT = {l'url di gestione risorse AzureStack}

  > [!note]  
  > In Windows, utilizzare set anziché esportazione.

4. Verificare che la variabile di percorso è impostata nel percorso AzureStack. Ad esempio locale = "local"

5. Aggiungere la riga di codice seguente se si utilizza lo Stack di Azure o altri cloud privati per gli endpoint a destra active directory di destinazione.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. All'interno di variabile di opzioni, aggiungere le impostazioni di active directory e l'URL di base per lavorare con lo Stack di Azure. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Creazione profilo client che ha come destinazione il profilo di Stack di Azure:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Per autenticare l'entità servizio con lo Stack di Azure, gli endpoint devono essere definiti utilizzando **get_active_directory_settings()**. Questo metodo Usa il **ARM_Endpoint** variabile di ambiente impostate quando si stabiliscono le variabili ambientali.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Eseguire l'esempio.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
