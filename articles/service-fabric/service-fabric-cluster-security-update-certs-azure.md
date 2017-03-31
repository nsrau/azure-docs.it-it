---
title: Gestire certificati in un cluster di Azure Service Fabric | Documentazione Microsoft
description: Questo articolo descrive come aggiungere nuovi certificati, eseguire il rollover di certificati e rimuovere certificati da o in un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: c433e8683755e454f9561f094269c3daccf78a62
ms.lasthandoff: 03/10/2017


---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Aggiungere o rimuovere certificati per un cluster Service Fabric in Azure
È consigliabile acquisire familiarità con l'uso dei certificati X.509 da parte di Service Fabric e con gli [scenari di sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md). È necessario comprendere cos'è un certificato del cluster e a cosa serve prima di procedere.

Service Fabric consente di specificare due certificati cluster, uno primario e uno secondario, durante la configurazione della sicurezza basata su certificati al momento della creazione del cluster, oltre ai certificati client. Per informazioni dettagliate sulla loro configurazione in fase di creazione, vedere l'argomento relativo alla [creazione di un di cluster Azure tramite il portale](service-fabric-cluster-creation-via-portal.md) o alla [creazione di un cluster di Azure tramite Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Se si specifica un solo certificato cluster in fase di creazione, questo viene usato come certificato primario. Dopo la creazione del cluster è possibile aggiungere un nuovo certificato come secondario.

> [!NOTE]
> Per un cluster protetto, è sempre necessario almeno un certificato cluster primario o secondario valido, non revocato né scaduto, e distribuito. In caso contrario, il cluster smette di funzionare. 90 giorni prima della scadenza della copertura di tutti i certificati validi il sistema genera una traccia di avviso e anche un evento di integrità dell'avviso nel nodo. Attualmente non sono disponibili messaggi di posta elettronica o altre notifiche inviati da Service Fabric su questo argomento. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Aggiungere un certificato cluster secondario tramite il portale

Non è possibile aggiungere il certificato cluster secondario tramite il portale di Azure, è necessario usare Azure PowerShell. Il processo è descritto più avanti in questo documento.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>Scambiare i certificati cluster tramite il portale

Dopo aver distribuito un certificato cluster secondario, è possibile scambiare il certificato secondario con il certificato primario passando al pannello Sicurezza e selezionando l'opzione "Scambia con primario" dal menu di scelta rapida.

![Scambiare i certificati][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Rimuovere un certificato cluster tramite il portale

Per un cluster sicuro, è sempre necessario almeno un certificato (primario o secondario) valido (non revocato né scaduto) distribuito. In caso contrario, il cluster smette di funzionare.

Per rimuovere un certificato secondario perché non venga usato per la sicurezza del cluster, passare al pannello Sicurezza e selezionare l'opzione "Elimina" dal menu di scelta rapida per il certificato secondario.

Se si intende rimuovere il certificato contrassegnato come primario, è prima necessario scambiarlo con il secondario e quindi eliminare il secondario al termine dell'aggiornamento.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Aggiungere un certificato secondario tramite PowerShell per Resource Manager

Questi passaggi presuppongono che si abbia familiarità con il funzionamento di Resource Manager, che sia stato distribuito almeno un cluster di Service Fabric usando un modello di Resource Manager e che il modello usato per configurare il cluster sia a portata di mano. Si presuppone anche che si abbia dimestichezza con l'uso di JSON.

> [!NOTE]
> In questo [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) sono disponibili un modello di esempio e i parametri che è possibile usare per procedere o come punto di partenza. 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Modificare il modello di Azure Resource Manager

Il file 5-VM-1-NodeTypes-Secure_Step2.JSON contiene tutte le modifiche illustrate in questa sezione e permette di seguire più facilmente. L'esempio è disponibile nel [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Assicurarsi di seguire tutti i passaggi**

**Passaggio 1:** aprire il modello di Resource Manager usato per la distribuzione del cluster. Se è stato scaricato l'esempio dal repository indicato in precedenza, usare 5-VM-1-NodeTypes-Secure_Step1.JSON per distribuire un cluster protetto e quindi aprire il modello.

**Passaggio 2:** aggiungere **due nuovi parametri** "secCertificateThumbprint" e "secCertificateUrlValue" di tipo stringa alla sezione parameters del modello. È possibile copiare il frammento di codice seguente e aggiungerlo al modello. A seconda dell'origine del modello, potrebbero già essere definiti. In tal caso, andare al passaggio successivo. 
 
```JSON
   "secCertificateThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Thumbprint"
      }
    },
    "secCertificateUrlValue": {
      "type": "string",
      "metadata": {
        "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
      }
    },

```

**Passaggio 3:** apportare modifiche alla risorsa **Microsoft.ServiceFabric/clusters**. Trovare la definizione della risorsa Microsoft.ServiceFabric/clusters nel modello. Il tag JSON "Certificate" nelle proprietà della definizione dovrebbe sarà simile al frammento JSON seguente:

   
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 

Aggiungere un nuovo tag "thumbprintSecondary" e assegnare un valore "[parameters('secCertificateThumbprint')]".  

A seconda dell'origine del modello, la definizione della risorsa dovrebbe avere un aspetto simile al seguente, anche se non identico. 

```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 

Per eseguire il **rollover del certificato**, è possibile specificare il nuovo certificato come primario e spostare il certificato primario corrente come secondario. Ciò determina il rollover del certificato primario corrente nel nuovo certificato in un solo passaggio di distribuzione.

```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('secCertificateThumbprint')]",
          "thumbprintSecondary": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
     }
``` 


**Passaggio 4:** apportare modifiche a **tutte** le definizioni di risorse **Microsoft.Compute/virtualMachineScaleSets**. Trovare la definizione della risorsa Microsoft.Compute/virtualMachineScaleSets. Scorrere fino alla sezione "publisher": "Microsoft.Azure.ServiceFabric" in "virtualMachineProfile".

Nelle impostazioni di pubblicazione di Service Fabric dovrebbe essere presente una sezione simile alla seguente.

![Json_Pub_Setting1][Json_Pub_Setting1]

Aggiungere qui le nuove voci del certificato.

```JSON
               "certificateSecondary": {
                    "thumbprint": "[parameters('secCertificateThumbprint')]",
                    "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },

```

L'aspetto delle proprietà dovrebbe essere simile al seguente:

![Json_Pub_Setting2][Json_Pub_Setting2]

Per eseguire il **rollover del certificato**, è possibile specificare il nuovo certificato come primario e spostare il certificato primario corrente come secondario. Ciò determina il rollover del certificato corrente nel nuovo certificato in un passaggio di distribuzione. 


```JSON
               "certificate": {
                   "thumbprint": "[parameters('secCertificateThumbprint')]",
                   "x509StoreName": "[parameters('certificateStoreValue')]"
                     },
               "certificateSecondary": {
                    "thumbprint": "[parameters('certificateThumbprint')]",
                    "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },

```
L'aspetto delle proprietà dovrebbe essere simile al seguente:

![Json_Pub_Setting3][Json_Pub_Setting3]


**Passaggio 5:** apportare modifiche a **tutte** le definizioni di risorse **Microsoft.Compute/virtualMachineScaleSets**. Trovare la definizione della risorsa Microsoft.Compute/virtualMachineScaleSets. Scorrere fino alla sezione "vaultCertificates": in "OSProfile". Dovrebbe avere un aspetto simile al seguente:


![Json_Pub_Setting4][Json_Pub_Setting4]

Aggiungere secCertificateUrlValue usando il frammento di codice seguente:

```Json
                  {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('secCertificateUrlValue')]"
                  }

```
A questo punto il file JSON risultante dovrebbe avere un aspetto simile al seguente:
![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Assicurarsi che nel modello siano stati ripetuti i passaggi 4 e 5 per tutte le definizioni della risorsa Nodetypes/Microsoft.Compute/virtualMachineScaleSets. Se si salta un passaggio il certificato non viene installato nel VMSS e si possono verificare effetti imprevedibili del cluster, inclusa l'indisponibilità del cluster, in assenza di certificati validi che il cluster possa usare per la sicurezza. È quindi importante verificare, prima di procedere.
> 
> 


### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Modificare il file di modello per riflettere i nuovi parametri aggiunti in precedenza
Se si segue l'esempio del [repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample), è possibile iniziare a modificare l'esempio 5-VM-1-NodeTypes-Secure.paramters_Step2.JSON. 

Modificare il file dei parametri del modello di Resource Manager e aggiungere i due nuovi parametri per secCertificateThumbprint e secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Distribuire il modello in Azure

- È ora possibile distribuire il progetto in Azure. Aprire il prompt dei comandi di Azure PowerShell versione 1+.
- Accedere al proprio account Azure e selezionare la sottoscrizione di Azure specifica. Questo passaggio è importante per gli utenti che hanno accesso a più sottoscrizioni di Azure.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Testare il modello prima di distribuirlo. Usare lo stesso gruppo di risorse in cui è attualmente distribuito il cluster.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Distribuire il modello nel gruppo di risorse. Usare lo stesso gruppo di risorse in cui è attualmente distribuito il cluster. Eseguire il comando New-AzureRmResourceGroupDeployment. Non è necessario specificare la modalità perché il valore predefinito è **incrementale**.

> [!NOTE]
> Se si imposta la modalità come completa, è possibile eliminare inavvertitamente le risorse non presenti nel modello. Non usarla in questo scenario.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Di seguito è riportato un esempio compilato dello stesso in PowerShell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Una volta completata la distribuzione, connettersi al cluster tramite il nuovo certificato ed eseguire alcune query, se si è in grado di eseguire questa operazione. È quindi possibile eliminare il vecchio certificato. 

Se si usa un certificato autofirmato, non dimenticare di importarlo nell'archivio certificati locale TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Per riferimento rapido ecco il comando per connettersi a un cluster sicuro 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Per riferimento rapido ecco il comando per ottenere l'integrità del cluster

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Distribuzione di certificati dell'applicazione nel cluster

Per fare in modo che i certificati vengano distribuiti da un insieme di credenziali delle chiavi ai nodi, è possibile usare la stessa procedura descritta nel passaggio 5 precedente. È sufficiente definire e usare parametri diversi.


## <a name="adding-or-removing-client-certificates"></a>Aggiunta o rimozione di certificati client

Oltre ai certificati cluster, è possibile aggiungere certificati client per eseguire operazioni di gestione su un cluster di Service Fabric.

È possibile aggiungere due tipi di certificati client, Amministratore o Sola lettura, che permettono di controllare l'accesso alle operazioni di amministrazione e di query nel cluster. Per impostazione predefinita, i certificati cluster vengono aggiunti all'elenco di certificati Amministratore consentiti.

È possibile specificare un numero qualsiasi di certificati client. Ogni aggiunta o eliminazione comporta un aggiornamento della configurazione del cluster di Service Fabric.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Aggiunta di certificati client Amministratore o Sola lettura tramite il portale

1. Passare al pannello Sicurezza e selezionare il pulsante "+ Autenticazione" nella parte superiore del pannello.
2. Nel pannello "Aggiungi autenticazione" scegliere il "Tipo di autenticazione", "Client di sola lettura" o "Client amministratore".
3. Scegliere il metodo di autorizzazione. Indica a Service Fabric se cercare questo certificato usando il nome del soggetto o l'identificazione personale. In generale, usare il metodo di autorizzazione del nome del soggetto non è una procedura di sicurezza consigliata. 

![Aggiungere il certificato client][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Eliminazione di certificati client Amministratore o Sola lettura tramite il portale

Per rimuovere un certificato secondario perché non venga usato per la sicurezza del cluster, passare al pannello Sicurezza e selezionare l'opzione "Elimina" dal menu di scelta rapida per il certificato specifico.



## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla gestione del cluster, leggere questi articoli:

* [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
* [Configurare l’accesso in base al ruolo per i client](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG



