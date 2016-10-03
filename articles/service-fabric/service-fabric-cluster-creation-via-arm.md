
<properties
   pageTitle="Creare un cluster protetto di Service Fabric tramite Azure Resource Manager | Microsoft Azure"
   description="Questo articolo descrive come configurare un cluster di Service Fabric protetto in Azure usando Azure Resource Manager, l'insieme di credenziali delle chiavi di Azure e Azure Active Directory (AAD) per l'autenticazione client."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/> 

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="vturecek"/> 

# Creare un cluster di Service Fabric in Azure tramite Azure Resource Manager

> [AZURE.SELECTOR]
- [Gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md)
- [Portale di Azure](service-fabric-cluster-creation-via-portal.md)

Questo articolo contiene una guida dettagliata che illustra i passaggi per la configurazione di un cluster di Azure Service Fabric protetto in Azure tramite Azure Resource Manager. La guida fornisce istruzioni dettagliate sulle operazioni seguenti:

 - Configurare l'insieme di credenziali delle chiavi per la gestione delle chiavi ai fini della protezione del cluster e delle applicazioni.
 - Creare un cluster protetto in Azure con Azure Resource Manager.
 - Autenticare gli utenti con Azure Active Directory (AAD) per la gestione dei cluster.

Un cluster protetto è un cluster che impedisce l'accesso non autorizzato alle operazioni di gestione, tra cui distribuzione, aggiornamento ed eliminazione di applicazioni, servizi e dati in esso contenuti. Un cluster non protetto è un cluster a cui tutti gli utenti possono connettersi in qualsiasi momento ed eseguire operazioni di gestione. Sebbene sia possibile creare un cluster non protetto, è **consigliabile creare un cluster protetto**. Un cluster non protetto **non può essere protetto in un secondo momento**. Sarà necessario crearne uno nuovo.

## Accedere ad Azure
Questa guida usa [Azure PowerShell][azure-powershell]. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.

Accedere all'account Azure:

```powershell
Login-AzureRmAccount
```

Selezionare la propria sottoscrizione:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## Configurare l'insieme di credenziali delle chiavi

Questa parte della guida illustra la creazione di un insieme di credenziali delle chiavi per un cluster di Service Fabric in Azure e per le applicazioni di Service Fabric. Per una guida completa sull'insieme di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure][key-vault-get-started].

Service Fabric usa certificati X.509 per proteggere un cluster e fornire le funzionalità di sicurezza dell'applicazione. L'insieme di credenziali delle chiavi di Azure viene usato per gestire i certificati dei cluster di Service Fabric in Azure. Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati dall'insieme di credenziali delle chiavi e li installa nelle macchine virtuali del cluster.

Nel diagramma seguente viene illustrata la relazione tra l'insieme di credenziali delle chiavi, un cluster di Service Fabric e il provider di risorse di Azure che usa i certificati archiviati nell'insieme di credenziali delle chiavi durante la creazione di un cluster:

![Installazione del certificato][cluster-security-cert-installation] 

### Creare un gruppo di risorse

Il primo passaggio consiste nel creare un nuovo gruppo di risorse specifico per l'insieme di credenziali delle chiavi. È consigliabile inserire l'insieme di credenziali delle chiavi nel proprio gruppo di risorse in modo che sia possibile rimuovere i gruppi di risorse di calcolo e archiviazione, ad esempio il gruppo di risorse con il cluster Service Fabric, senza perdere le chiavi e i segreti. Il gruppo di risorse che contiene l'insieme di credenziali delle chiavi deve essere situato nella stessa area del cluster che lo usa.

```powershell

	New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
	WARNING: The output object type of this cmdlet will be modified in a future release.
	
	ResourceGroupName : mycluster-keyvault
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### Creare un insieme di credenziali delle chiavi 

Creare un insieme di credenziali delle chiavi nel nuovo gruppo di risorse. L'insieme di credenziali delle chiavi **deve essere abilitato per la distribuzione** per consentire al provider di risorse di Service Fabric di ottenere i certificati e installarli nei nodi del cluster:

```powershell

	New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
	
	
	Vault Name                       : myvault
	Resource Group Name              : mycluster-keyvault
	Location                         : West US
	Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
	Vault URI                        : https://myvault.vault.azure.net
	Tenant ID                        : <guid>
	SKU                              : Standard
	Enabled For Deployment?          : False
	Enabled For Template Deployment? : False
	Enabled For Disk Encryption?     : False
	Access Policies                  :
	                                   Tenant ID                :    <guid>
	                                   Object ID                :    <guid>
	                                   Application ID           :
	                                   Display Name             :    
	                                   Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
	                                   Permissions to Secrets   :    all
	
	
	Tags                             :
```

Se si dispone già di un insieme di credenziali delle chiavi, è possibile abilitarlo per la distribuzione tramite l'interfaccia della riga di comando di Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## Aggiungere i certificati all'insieme di credenziali delle chiavi

I certificati vengono usati in Service Fabric per fornire l'autenticazione e la crittografia e proteggere i vari aspetti di un cluster e delle sue applicazioni. Per altre informazioni sull'uso dei certificati in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric][service-fabric-cluster-security].

### Cluster e certificato del server (obbligatorio) 

Questo certificato è richiesto per proteggere un cluster e impedirne accessi non autorizzati. Il certificato fornisce protezione del cluster in due modi:
 
 - **Autenticazione del cluster:** autentica la comunicazione da nodo a nodo per la federazione di cluster. Solo i nodi che possono dimostrare la propria identità con il certificato possono essere aggiunti al cluster.
 - **Autenticazione del server:** autentica gli endpoint di gestione del cluster in un client di gestione, in modo che il client di gestione sappia con certezza di comunicare con il cluster reale. Questo certificato fornisce anche SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS.

A tale scopo, il certificato deve soddisfare i requisiti seguenti:

 - Il certificato deve includere una chiave privata.
 - Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
 - Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster Service Fabric. È necessario fornire il SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio `.cloudapp.azure.com`. È necessario acquistare un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

### Certificati delle applicazioni (facoltativo)

Per motivi di sicurezza dell'applicazione, è possibile installare nel cluster numerosi certificati aggiuntivi. Prima di creare il cluster, considerare gli scenari di protezione delle applicazioni che richiedono l'installazione di un certificato sui nodi, ad esempio:

 - Crittografia e decrittografia dei valori di configurazione dell'applicazione
 - Crittografia dei dati tra i nodi durante la replica

### Formattazione dei certificati per l'uso di provider di risorse di Azure

I file di chiave privata (con estensione pfx) possono essere aggiunti e usati direttamente tramite l'insieme di credenziali delle chiavi. Tuttavia, il provider di risorse di Azure richiede l'archiviazione delle chiavi in un particolare formato JSON che include il file con estensione pfx come stringa con codifica Base64 e la password della chiave privata. Per soddisfare questi requisiti, le chiavi devono essere inserite in una stringa JSON e quindi archiviate come *segreti* nell'insieme di credenziali delle chiavi.

Per semplificare questo processo, è [disponibile su GitHub][service-fabric-rp-helpers] un modulo di PowerShell. Per usare il modulo, seguire questa procedura:

  1. Scaricare l'intero contenuto del repository in una directory locale.
  2. Importare il modulo nella finestra di PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
Il comando `Invoke-AddCertToKeyVault` in questo modulo di PowerShell formatta in modo automatico una chiave privata del certificato in una stringa JSON e la carica nell'insieme di credenziali delle chiavi. Usare il comando per aggiungere il certificato del cluster ed eventuali certificati aggiuntivi delle applicazioni all'insieme di credenziali delle chiavi. È sufficiente ripetere questo passaggio per tutti i certificati aggiuntivi che si desidera installare nel cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
	
	Switching context to SubscriptionId <guid>
	Ensuring ResourceGroup mycluster-keyvault in West US
	WARNING: The output object type of this cmdlet will be modified in a future release.
	Using existing valut myvault in West US
	Reading pfx file from C:\path\to\key.pfx
	Writing secret to myvault in vault myvault
	
	
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```


Questi sono tutti i prerequisiti dell'insieme di credenziali delle chiavi per la configurazione di un modello di Cluster Resource Manager di Service Fabric che consente di installare certificati per l'autenticazione dei nodi, per la protezione degli endpoint di gestione e per l'autenticazione, nonché qualsiasi funzionalità aggiuntiva per la sicurezza delle applicazioni che usano certificati X.509. A questo punto, dovrebbe essere visualizzata l'impostazione seguente in Azure:

 - Gruppo di risorse dell'insieme di credenziali delle chiavi
   - Insieme di credenziali di chiave
     - Certificato di autenticazione del server del cluster
     - Certificati delle applicazioni

## Configurare Azure Active Directory per l'autenticazione client

AAD consente alle organizzazioni, note come tenant, di gestire l'accesso degli utenti alle applicazioni, che si dividono in applicazioni con un'interfaccia utente di accesso basata sul Web e applicazioni con un'esperienza client nativa. In questo documento si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

I cluster di Service Fabric offrono numerosi punti di ingresso alle relative funzionalità di gestione, tra cui [Service Fabric Explorer][service-fabric-visualizing-your-cluster], basato sul Web, e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Verranno quindi create due applicazioni AAD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.

Per semplificare alcuni dei passaggi richiesti per la configurazione di AAD con un cluster di Service Fabric è stato creato un set di script di Windows PowerShell.

>[AZURE.NOTE] Questi passaggi devono essere eseguiti *prima* di creare il cluster. Nei casi in cui gli script prevedono endpoint e nomi dei cluster è quindi necessario usare i valori pianificati anziché valori già creati.

1. [Scaricare gli script][sf-aad-ps-script-download] sul computer.

2. Fare clic con il pulsante destro del mouse sul file ZIP, scegliere **Proprietà**, quindi selezionare la casella di controllo **Sblocca**.

3. Estrarre il file con estensione zip.

4. Eseguire `SetupApplications.ps1`, indicando i parametri TenantId, ClusterName e WebApplicationReplyUrl. ad esempio:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Il **TenantId** è reperibile nell'URL del tenant nel portale di Azure classico. Il GUID incorporato nell'URL è il TenantId. ad esempio:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    **ClusterName** viene usato come prefisso per le applicazioni AAD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster, dato che il suo unico scopo è semplificare il mapping degli elementi AAD al cluster di Service Fabric con cui vengono usati.

    **WebApplicationReplyUrl** è l'endpoint predefinito che AAD restituisce agli utenti dopo aver completato la procedura di accesso. Deve essere impostato sull'endpoint di Service Fabric Explorer per il cluster, che per impostazione predefinita è:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant AAD. Al termine dell'operazione, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Tra le applicazioni del tenant nel [portale di Azure classico][azure-classic-portal] dovrebbero essere visualizzate due nuove voci:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    Tenere aperta la finestra di PowerShell perché, quando verrà creato il cluster nella sezione successiva, lo script stamperà il codice JSON richiesto dal modello di Azure Resource Manager.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## Creare un modello di Cluster Resource Manager di Service Fabric

In questa sezione, l'output dei comandi di PowerShell precedenti sarà usato in un modello di Cluster Resource Manager di Service Fabric.

In [Raccolta di modelli di avvio rapido di Azure su GitHub][azure-quickstart-templates] sono disponibili alcuni modelli di Gestione risorse di esempio. Questi modelli possono essere usati come punto di partenza per il modello del cluster.

### Creare il modello di Azure Resource Manager

Questa guida usa il modello di esempio di un [cluster protetto a 5 nodi][service-fabric-secure-cluster-5-node-1-nodetype-wad] e i parametri del modello. Scaricare `azuredeploy.json` e `azuredeploy.parameters.json` sul computer e aprire entrambi i file in un editor di testo.

### Aggiungere certificati

I certificati vengono aggiunti a un modello di Cluster Resource Manager facendo riferimento all'insieme di credenziali delle chiavi che contiene le chiavi del certificato. È consigliabile che questi valori dell'insieme di credenziali delle chiavi vengono inseriti in un file di parametri del modello di Resource Manager per mantenere il file del modello di Resource Manager riutilizzabile e privo di valori specifici di una distribuzione.

#### Aggiungere tutti i certificati per osProfile del set di scalabilità di macchine virtuali

Ogni certificato da installare nel cluster deve essere configurato nella sezione osProfile della risorsa del set di scalabilità di macchine virtuali (Microsoft.Compute/virtualMachineScaleSets). Ciò indica al provider di risorse di installare il certificato nelle macchine virtuali, includendo il certificato del cluster, nonché eventuali certificati di sicurezza dell'applicazione che si intende usare per le applicazioni:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### Configurare il certificato del cluster di Service Fabric

Il certificato di autenticazione del cluster deve anche essere configurato nella risorsa del cluster di Service Fabric (Microsoft.ServiceFabric/clusters) e nell'estensione di Service Fabric per il set di scalabilità di macchine virtuali nella risorsa del set di scalabilità di macchine virtuali. Questo consente al provider di risorse di Service Fabric di configurarlo per l'uso dell'autenticazione del cluster e del server per gli endpoint di gestione.

##### Risorsa del set di scalabilità di macchine virtuali:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### Risorsa di Service Fabric:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### Inserire la configurazione AAD

La configurazione di AAD creata in precedenza può essere inserita direttamente nel modello di Resource Manager. Tuttavia è consigliabile prima estrarre i valori nei parametri in un file dei parametri per mantenere il modello di Resource Manager riutilizzabile e privo dei valori specifici di una distribuzione.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### Configurare i parametri del modello di Resource Manager

Infine, usare i valori di output dei comandi PowerShell ADD e dell'insieme di credenziali delle chiavi per compilare il file dei parametri:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
A questo punto, dovrebbe essere visualizzata l'impostazione seguente:

 - Gruppo di risorse dell'insieme di credenziali delle chiavi
    - Insieme di credenziali di chiave
    - Certificato di autenticazione del server del cluster
    - Certificato di crittografia dei dati
 - Tenant di Azure Active Directory
    - Applicazione AAD per la gestione basata su Web e Service Fabric Explorer
    - Applicazione AAD per la gestione dei client nativi
    - Utenti con ruoli assegnati
 - Modello di Cluster Resource Manager di Service Fabric
    - Certificati configurati tramite l'insieme di credenziali delle chiavi
    - Azure Active Directory configurato

Il diagramma seguente illustra i punti in cui la configurazione dell'insieme di credenziali delle chiavi e dell'AAD rientra nel modello di Resource Manager.

![Mappa di dipendenza di Resource Manager][cluster-security-arm-dependency-map]

## Creare il cluster

A questo punto si è pronti per creare il cluster tramite la [distribuzione ARM][resource-group-template-deploy].

#### Eseguirne il test

Usare il comando PowerShell seguente per testare il modello di Resource Manager con un file di parametri:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### Distribuirlo

Se viene superato il test del modello di Resource Manager, usare il comando PowerShell seguente per distribuire il modello di Resource Manager con un file di parametri:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

## Assegnare utenti ai ruoli

Dopo aver creato le applicazioni per rappresentare il cluster, è necessario assegnare gli utenti ai ruoli supportati da Service Fabric, ovvero sola lettura e amministratore. Questa operazione può essere eseguita nel [portale di Azure classico][azure-classic-portal].

1. Passare al tenant e scegliere Applicazioni.
2. Scegliere l'applicazione Web, che avrà un nome simile a `myTestCluster_Cluster`.
3. Fare clic sulla scheda Utenti.
4. Scegliere un utente per l'assegnazione e quindi fare clic sul pulsante **Assegna** nella parte inferiore della schermata.

    ![Pulsante di assegnazione di utenti ai ruoli][assign-users-to-roles-button] 

5. Selezionare il ruolo da assegnare all'utente.

    ![Assegnare utenti ai ruoli][assign-users-to-roles-dialog] 

>[AZURE.NOTE] Per altre informazioni sui ruoli in Service Fabric, vedere [Controllo degli accessi in base al ruolo per i client di Service Fabric](service-fabric-cluster-security-roles.md).


## Passaggi successivi

A questo punto, è stato creato un cluster con Azure Active Directory che fornisce l'autenticazione per la gestione. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e scoprire come [gestire i segreti delle applicazioni](service-fabric-application-secret-management.md).

<!-- Links --> 
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[aad-graph-api-docs]: https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images --> 
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png

<!---HONumber=AcomDC_0921_2016-->