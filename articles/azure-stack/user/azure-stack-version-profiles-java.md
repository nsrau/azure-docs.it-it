---
title: Usare i profili delle versioni API con Java in Azure Stack | Microsoft Docs
description: Imparare a utilizzare i profili delle versioni API con Java in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: b5a876ea8b5cc70ee0ca0dcac8628c12dc2b009b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414945"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Usare i profili delle versioni API con Java in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

il SDK per Java per Azure Stack Resource Manager fornisce strumenti che consentono di compilare e gestire l'infrastruttura. I provider di risorse nel SDK includono calcolo, rete, archiviazione, servizi app, e [KeyVault](../../key-vault/key-vault-whatis.md). Java SDK incorpora i profili delle API per l'inserimento di dipendenze nel file POM. XML che carica i moduli corretti nel file Java. Tuttavia, è possibile aggiungere più profili come dipendenze, ad esempio la **2018-03-01-hybrid**, o **più recente** come profilo di Azure. Usando queste dipendenze carica il modulo corretto in modo che quando si crea il tipo di risorsa, è possibile selezionare quale versione dell'API da tali profili che si desidera utilizzare. In questo modo è possibile usare le versioni più recenti in Azure, durante lo sviluppo con le versioni dell'API più recente per Azure Stack. Uso di Java SDK consente un'esperienza di sviluppo cloud ibrido davvero. I profili di API nel SDK per Java consentono lo sviluppo cloud ibride grazie alla possibilità di passare tra le risorse di Azure globali e le risorse in Azure Stack.

## <a name="java-and-api-version-profiles"></a>Profili delle versioni API e Java

Un profilo di API è una combinazione di versioni di API e i provider di risorse. È possibile usare un profilo di API per ottenere la versione più recente e più stabile di ogni tipo di risorsa in un pacchetto di provider di risorse.

- Per usare le versioni più recenti di tutti i servizi, usare il **più recente** profilo come dipendenza.
    
   - Per usare il profilo più recente, la dipendenza serve **COM**.

   - Per usare i servizi compatibili con Azure Stack, usare il **com.microsoft.azure.profile\_2018\_03\_01\_ibrida** profilo.
    
      - Ciò è necessario specificare nel file POM. XML come dipendenza, che carica i moduli automaticamente se è la classe a destra scegliere nell'elenco a discesa come si farebbe con .NET.
        
          - La parte superiore di ogni modulo verrà visualizzata come indicato di seguito:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Le dipendenze vengono visualizzati come indicato di seguito:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Per usare versioni API specifiche per un tipo di risorsa in un provider di risorse specifico, usare le versioni API specifiche definite tramite intellisense.

Si noti che è possibile combinare tutte le opzioni nella stessa applicazione.

## <a name="install-the-azure-java-sdk"></a>Installare Azure Java SDK

Usare la procedura seguente per installare il SDK per Java:

1.  Seguire le istruzioni ufficiali per installare Git. Per istruzioni, vedere [Guida introduttiva - installazione di Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2.  Seguire le istruzioni ufficiali per installare il [SDK per Java](http://zulu.org/download/)) e [Maven](https://maven.apache.org/). La versione corretta è la versione 8 di Java Developer Kit. Apache Maven corretto è la versione 3.0 o versione successiva. Per completare la Guida introduttiva, è necessario impostare la variabile di ambiente JAVA_HOME al percorso di installazione di Java Development Kit. Per altre informazioni, vedere [creare la prima funzione con Java e Maven](../../azure-functions/functions-create-first-java-maven.md).

3.  Per installare i pacchetti di dipendenza corretto, aprire il file POM. XML nell'applicazione Java. Aggiungere una dipendenza, come illustrato nel codice seguente:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  I pacchetti che devono essere installati dipende dalla versione del profilo da utilizzare. I nomi dei pacchetti per le versioni di profili sono:
    
   - **com.microsoft.Azure.Profile\_2018\_03\_01\_ibrida**
   - **COM**
      - **più recente**

5.  Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione per un uso successivo. Per istruzioni su come creare una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6.  Creare un'entità servizio e salvare l'ID Client e il segreto Client. Per istruzioni su come creare un'entità servizio per Azure Stack, vedere [offrono alle applicazioni di accedere ad Azure Stack](../azure-stack-create-service-principals.md). Si noti che l'ID client è noto anche come ID dell'applicazione durante la creazione di un'entità servizio.

7.  Assicurarsi che all'entità servizio è installato il ruolo di collaboratore/proprietario della sottoscrizione. Per istruzioni su come assegnare un ruolo all'entità servizio, vedere [offrono alle applicazioni di accedere ad Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare Azure .NET SDK con Azure Stack, è necessario fornire i valori seguenti e quindi impostare i valori con le variabili di ambiente. Per impostare le variabili di ambiente, vedere le istruzioni che seguono la tabella per il sistema operativo.

| Valore                     | Variabili di ambiente | DESCRIZIONE                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID tenant                 | TENANT_ID            | Il valore di Azure Stack [ <span class="underline">ID tenant</span>](../azure-stack-identity-overview.md).                                                          |
| ID client                 | CLIENT_ID             | Il servizio di ID entità applicazione salvati dell'entità servizio è stato creato nella sezione precedente di questo documento.                                                                                              |
| ID sottoscrizione           | SUBSCRIPTION_ID      | Il [ <span class="underline">ID sottoscrizione</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) è la modalità di accesso offerte in Azure Stack.                |
| Client Secret             | CLIENT_SECRET        | L'applicazione di un'entità servizio segreto salvato se è stata creata l'entità servizio.                                                                                                                                   |
| Endpoint di Resource Manager | ENDPOINT              | Visualizzare [ <span class="underline">l'endpoint di gestione risorse di Azure Stack</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Località                  | POSIZIONE_RISORSA    | Locale per Azure Stack                                                                                                                                                                                                |

Per trovare l'ID tenant per Azure Stack, seguire le istruzioni riportate [qui](../azure-stack-csp-ref-operations.md). Per impostare le variabili di ambiente, eseguire le operazioni seguenti:

### <a name="microsoft-windows"></a>Microsoft Windows

Per impostare le variabili di ambiente in un prompt dei comandi di Windows, usare il formato seguente:

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>sistemi basati su Unix, Linux e macOS

In sistemi basati su Unix, è possibile usare il comando seguente:

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>L'endpoint di gestione risorse di Azure Stack

Microsoft Azure Resource Manager è un framework di gestione che consente agli amministratori di distribuire, gestire e monitorare le risorse di Azure. Azure Resource Manager può gestire queste attività come un gruppo, anziché singolarmente e in un'unica operazione.

È possibile ottenere le informazioni sui metadati dall'endpoint di Resource Manager. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice.

Tenere presente le considerazioni seguenti:

- Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: https://management.local.azurestack.external/

- Il **ResourceManagerUrl** in sistemi integrati: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

File JSON di esempio:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Profili dell'API esistenti

1.  **com.microsoft.Azure.Profile\_2018\_03\_01\_ibrida**: profilo più recente creato per Azure Stack. Usare questo profilo per i servizi di essere più compatibile con Azure Stack, purché si è in corrispondenza del timestamp 1808 o altri.

2.  **com.microsoft.Azure.Profile\_2017\_03\_09\_profilo**: se si usa un timestamp minore rispetto alla build 1808, usare questo profilo.

3.  **COM**: profilo costituito le versioni più recenti di tutti i servizi. Usare le versioni più recenti di tutti i servizi.

Per altre informazioni sui profili di Azure Stack e l'API, vedere la [profili di riepilogo dell'API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Utilizzo di profilo Azure API Java SDK

Il codice seguente esegue l'autenticazione dell'entità servizio in Azure Stack. Crea un token dal tenant ID e la base di autenticazione, che è specifica per Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

In questo modo è possibile utilizzare dipende dal profilo di API per distribuire l'applicazione correttamente in Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definire le funzioni di impostazione ambiente Azure Stack

Per registrare il cloud di Azure Stack con endpoint corretti, usare il codice seguente:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

Il `getActiveDirectorySettings` chiamata nel codice seguente recupera gli endpoint dagli endpoint di metadati. Le variabili di ambiente dalla chiamata effettuata indicato:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Esempi che usano i profili delle API

È possibile utilizzare gli esempi di GitHub seguenti come riferimento per la creazione di soluzioni con i profili .NET e API di Azure Stack:

  - [Gestire gruppi di risorse](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [Gestire gli account di archiviazione](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [Gestire una macchina virtuale](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>Progetto di Unit Test di esempio 

1.  Clonare il repository usando il comando seguente:
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  Creare un'entità servizio Azure e assegnare un ruolo per accedere alla sottoscrizione. Per istruzioni sulla creazione di un'entità servizio, vedere [usare Azure PowerShell per creare un'entità servizio con un certificato](../azure-stack-create-service-principals.md).

3.  Recuperare i valori richiesti seguenti:
    
   1.  ID tenant
   2.  ID client
   3.  Client Secret
   4.  ID sottoscrizione
   5.  Endpoint di Resource Manager
   6.  Percorso risorsa

4.  Impostare le variabili di ambiente seguenti usando le informazioni di cui che è stato recuperato dall'entità servizio è stato creato mediante il prompt dei comandi:
    
   1. esportare TENANT_ID = {proprio id tenant}
   2. esportare CLIENT_ID = {l'id client}
   3. esportare CLIENT_SECRET = {il segreto client}
   4. esportare SUBSCRIPTION_ID = {id sottoscrizione}
   5. esportare ARM_ENDPOINT = {l'URL di gestione risorse di Azure Stack}
   6. esportare posizione_risorsa = {location di Azure Stack}

   In Windows, usare **impostata** invece di **esportare**.

5.  Usare il `getactivedirectorysettings` codice per recuperare l'endpoint dei metadati di Azure Resource Manager e usare il client HTTP per impostare le informazioni sull'endpoint.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

7.  Nel file POM. XML, aggiungere la dipendenza seguente per usare il profilo 2018-03-01-ibrida per Azure Stack. Questa dipendenza installerà i moduli associati a questo profilo per i provider di risorse di calcolo, rete, archiviazione, KeyVault e servizi App.
    
   Si noti che è possibile usare il profilo più recente di Azure di destinazione:
        
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  Al prompt dei comandi che è stato aperto per impostare le variabili di ambiente, immettere la riga seguente:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui profili di API, vedere:

- [Gestire i profili di versione API in Azure Stack](azure-stack-version-profiles.md)
- [Versioni di API del provider di risorse supportate da profili](azure-stack-profiles-azure-resource-manager-versions.md)
