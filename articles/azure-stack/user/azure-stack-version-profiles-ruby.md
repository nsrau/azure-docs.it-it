---
title: Usando i profili delle versioni API con Ruby in Azure Stack | Microsoft Docs
description: Imparare a utilizzare i profili delle versioni API con Ruby in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 73c770f45666ce76b770796ac9714d007c2870cb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247188"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Usare i profili delle versioni API con Ruby in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Profili delle versioni API e Ruby

Ruby SDK per Azure Stack Resource Manager fornisce strumenti che consentono di compilare e gestire l'infrastruttura. I provider di risorse nel SDK includono calcolo, le reti virtuali e archiviazione con il linguaggio Ruby. I profili dell'API nel SDK Ruby abilitare lo sviluppo cloud ibride grazie alla possibilità di passare tra le risorse di Azure globali e le risorse in Azure Stack.

Un profilo di API è una combinazione di versioni del servizio e i provider di risorse. È possibile usare un profilo di API per combinare diversi tipi di risorse.

- Per usare le versioni più recenti di tutti i servizi, usare il **più recente** profilo di gem di rollup il SDK di Azure.
- Per usare i servizi compatibili con lo Stack di Azure, usare il **V2018_03_01** profilo di gem di rollup il SDK di Azure.
- Usare la versione più recente **api-version** di un servizio, utilizzare il **più recente** profilo della gemma specifico. Ad esempio, se si desidera utilizzare la versione più recente **api-version** del servizio di calcolo da solo, utilizzare il **più recente** profilo della **calcolo** gem.
- Usare uno specifico **api-version** per un servizio, usare le versioni API specifiche definite all'interno di gem.

> [!NOTE]
> È possibile combinare tutte le opzioni nella stessa applicazione.

## <a name="install-the-azure-ruby-sdk"></a>Installare Azure Ruby SDK

- Attenersi alle istruzioni ufficiali [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Attenersi alle istruzioni ufficiali [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Durante l'installazione, scegliere **Ruby aggiungere alla variabile PATH**.
  - Installare il kit di sviluppo durante l'installazione di Ruby quando richiesto.
  - Installare quindi il bundler usando il comando seguente:  
    `Gem install bundler`
- Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Le istruzioni per creare una sottoscrizione sono [qui](../azure-stack-subscribe-plan-provision-vm.md).
- Creare un'entità servizio e salvare i relativi ID e segreto. Le istruzioni per creare un'entità servizio per Azure Stack sono [qui](../azure-stack-create-service-principals.md).
- Assicurarsi che l'entità servizio possiede ruolo Collaboratore/proprietario per la sottoscrizione. Le istruzioni su come assegnare un ruolo all'entità servizio sono [qui](../azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Installare i pacchetti Rubygem

È possibile installare i pacchetti Azure Rubygem direttamente.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

il SDK di Ruby di Azure Resource Manager è disponibile in anteprima e avrà probabilmente interfaccia modifiche di rilievo nelle versioni future. Un maggior numero di versione secondaria può indicare modifiche di rilievo.

## <a name="use-the-azuresdk-gem"></a>Usare la Gemma azure_sdk

La Gemma **azure_sdk**, è un aggiornamento cumulativo di tutte le risorse supportate in Ruby SDK. Questo indicatore è costituito da un **più recente** profilo, che supporta la versione più recente di tutti i servizi. Introduce due profilo con controllo delle versioni **V2017_03_09** e **V2018_03_01** profili che vengono compilati per Azure Stack.

È possibile installare la Gemma di rollup azure_sdk con il comando seguente:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Prerequisiti

Per usare Azure Ruby SDK con Azure Stack, è necessario fornire i valori seguenti e quindi impostare i valori con le variabili di ambiente. Vedere le istruzioni dopo la tabella per il sistema operativo su come impostare le variabili di ambiente.

| Valore | Variabili di ambiente | DESCRIZIONE |
| --- | --- | --- | --- |
| ID tenant | AZURE_TENANT_ID | Il valore di Azure Stack [tenant ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID client | AZURE_CLIENT_ID | Il servizio di ID entità applicazione salvati dell'entità servizio è stato creato nella sezione precedente di questo documento.  |
| ID sottoscrizione | AZURE_SUBSCRIPTION_ID | Il [ID sottoscrizione](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) è la modalità di accesso offerte in Azure Stack. |
| Client Secret | AZURE_CLIENT_SECRET | L'applicazione di un'entità servizio segreto salvato se è stata creata l'entità servizio. |
| Endpoint di Resource Manager | ARM_ENDPOINT | Visualizzare [endpoint di gestione risorse di Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>L'endpoint di gestione risorse di Azure Stack

Microsoft Azure Resource Manager è un framework di gestione che consente agli amministratori di distribuire, gestire e monitorare le risorse di Azure. Azure Resource Manager può gestire queste attività come un gruppo, anziché singolarmente e in un'unica operazione.

È possibile ottenere le informazioni sui metadati dall'endpoint di Resource Manager. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice.

 > [!NOTE]  
 > Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/` Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 File JSON di esempio:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environmental-variables"></a>Impostare le variabili ambientali

**Microsoft Windows**  
Per impostare le variabili di ambiente in un prompt dei comandi di Windows, usare il formato seguente:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux e Unix-based systems** sistemi basati su Unix In, è possibile usare il comando seguente:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Profili dell'API esistenti

La Gemma di rollup Azure_sdk presenta i seguenti tre profili:

1. **V2018_03_01** profilo creato per Azure Stack. Usare questo profilo per l'utilizzo di tutte le versioni più recenti dei servizi disponibili in Azure Stack.
2. **V2017_03_09**  
  Profilo creato per Azure Stack. Usare questo profilo per i servizi di essere più compatibile con Azure Stack.
3. **Più recente**  
  Profilo è costituito da versioni più recenti di tutti i servizi. Usare le versioni più recenti di tutti i servizi.

Per altre informazioni sui profili di Azure Stack e l'API, vedere la [profili di riepilogo dell'API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Utilizzo di profilo Azure Ruby SDK API

È possibile usare il codice seguente per creare un'istanza di un client di profilo. Questo parametro è solo necessario per Azure Stack o in altri cloud privato. Per impostazione predefinita, Azure globale ha già queste impostazioni.

```Ruby  
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
client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
```

Il client di profilo può essere usato per accedere ai provider di risorse singole, ad esempio calcolo, archiviazione e rete:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2018_03_01::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Definire le funzioni di impostazione ambiente Azure Stack

Per autenticare l'entità servizio nell'ambiente Azure Stack, definire gli endpoint usando `get_active_directory_settings()`. Questo metodo Usa il **ARM_Endpoint** variabile di ambiente impostate quando si stabiliscono le variabili di ambiente:

```Ruby  
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
```

## <a name="samples-using-api-profiles"></a>Esempi che usano i profili delle API

È possibile usare gli esempi seguenti disponibili su GitHub come riferimenti per la creazione di soluzioni con i profili di Ruby e API di Azure Stack:

- [Gestire risorse e gruppi di risorse di Azure con Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Gestire macchine virtuali con Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)
- [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Esempio di Resource Manager e gruppi

Per eseguire l'esempio, assicurarsi di avere installato Ruby. Se si usa Visual Studio Code, scaricare anche l'estensione di Ruby SDK.

> [!NOTE]  
> È possibile ottenere il repository per l'esempio all'indirizzo "[gestire Azure e gruppi di risorse con Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)".

1. Clonare il repository:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Installare le dipendenze usando bundle:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Creare un'entità servizio usando PowerShell Azure e recuperare i valori necessari.

   Per istruzioni sulla creazione di un'entità servizio, vedere [usare Azure PowerShell per creare un'entità servizio con un certificato](../azure-stack-create-service-principals.md).

   I valori necessiti sono:
   - ID tenant
   - ID client
   - Client Secret
   - ID sottoscrizione
   - Endpoint di Resource Manager

   Impostare le variabili di ambiente seguenti usando le informazioni di cui che è stato recuperato dall'entità servizio creata.

   - esportare AZURE_TENANT_ID = {proprio id tenant}
   - esportare AZURE_CLIENT_ID = {l'id client}
   - esportare AZURE_CLIENT_SECRET = {il segreto client}
   - esportare AZURE_SUBSCRIPTION_ID = {id sottoscrizione}
   - esportare ARM_ENDPOINT = {l'url di gestione risorse AzureStack}

   > [!NOTE]  
   > In Windows, utilizzare set anziché export.

4. Verificare che la variabile di percorso è impostata la posizione di Azure Stack; ad esempio, `LOCAL="local"`.

5. Aggiungere la riga di codice seguente, se si usa Azure Stack o in altri cloud privato per gli endpoint direttamente active directory di destinazione:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. Variabile di opzioni, aggiungere le impostazioni di Active Directory e l'URL di base per lavorare con Azure Stack:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Creare un profilo client che ha come destinazione del profilo Azure Stack:

   ```ruby  
   client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
   ```

8. Per autenticare l'entità servizio con Azure Stack, l'endpoint deve essere definito utilizzando **get_active_directory_settings()**. Questo metodo Usa il **ARM_Endpoint** variabile di ambiente impostate quando si stabiliscono le variabili di ambiente:

   ```ruby  
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
   ```

9. Eseguire l'esempio.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
- [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
