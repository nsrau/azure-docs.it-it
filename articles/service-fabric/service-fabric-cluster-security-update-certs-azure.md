---
title: Aggiungere, eseguire il rollover e rimuovere i certificati usati in un cluster di Service Fabric in Azure | Documentazione Microsoft
description: Descrive come caricare un certificato di cluster secondario ed eseguire il rollover del vecchio certificato primario.
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
ms.date: 08/15/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 829af0b685f07c2e529edf1a0ef72b741d37a14c


---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Aggiungere o rimuovere certificati per un cluster Service Fabric in Azure
Per acquisire familiarità con l'uso dei certificati X.509 da parte di Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md). È necessario comprendere cos'è un certificato del cluster e a cosa serve prima di procedere.

Service Fabric consente di specificare due certificati di cluster, uno primario e uno secondario, durante la configurazione della sicurezza basata su certificati al momento della creazione del cluster. Per i dettagli vedere l'argomento relativo alla [creazione di un cluster Azure tramite il portale](service-fabric-cluster-creation-via-portal.md) o alla [creazione di un cluster Azure tramite Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Se si esegue la distribuzione tramite Resource Manager e si specifica un solo certificato del cluster, questo viene usato come certificato primario. Dopo la creazione del cluster è possibile aggiungere un nuovo certificato come secondario.

> [!NOTE]
> Per un cluster sicuro, è sempre necessario almeno un certificato (primario o secondario) valido (non revocato né scaduto) distribuito. In caso contrario, il cluster smette di funzionare. 90 giorni prima della scadenza della copertura di tutti i certificati validi il sistema genera una traccia di avviso e anche un evento di integrità dell'avviso nel nodo. Attualmente non sono disponibili messaggi di posta elettronica o altre notifiche inviati da Service Fabric su questo argomento. 
> 
> 

## <a name="add-a-secondary-certificate-using-the-portal"></a>Aggiungere un certificato secondario tramite il portale
Per aggiungere un altro certificato come secondario, è necessario caricarlo in un insieme di credenziali delle chiavi di Azure e quindi distribuirlo per le macchine virtuali nel cluster. Per altre informazioni, vedere il blog relativo alla [distribuzione di certificati nelle macchine virtuali dall'insieme di credenziali delle chiavi gestito dal cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

1. Per informazioni, vedere [Aggiungere i certificati all'insieme di credenziali delle chiavi](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).
2. Accedere al [portale di Azure](https://portal.azure.com/) e passare alla risorsa del cluster in cui si vuole aggiungere questo certificato.
3. In **IMPOSTAZIONI** fare clic su **Sicurezza** per visualizzare il pannello Sicurezza cluster.
4. Fare clic sul pulsante **"+Certificate"** (+Certificato) nella parte superiore del pannello per visualizzare il pannello **"Aggiungi certificato"**.
5. Selezionare "Identificazione personale certificato secondario" dall'elenco a discesa e compilare l'identificazione personale del certificato secondario caricato per l'insieme di credenziali.

> [!NOTE]
> A differenza che nel corso del flusso di lavoro di creazione del cluster, qui non vengono visualizzate le informazioni sull'insieme di credenziali perché si presuppone che ci si trovi in questo pannello, che sia già stato distribuito il certificato nelle VM e che il certificato sia già disponibile nell'archivio certificati locale nell'istanza VMSS.
> 
> 

Fare clic su **Certificato**. Inizia una distribuzione e una barra di stato blu verrà visualizzata nel pannello Cluster Security (Sicurezza cluster).

![Schermata delle identificazioni personali del certificato nel portale][SecurityConfigurations_02]

Al completamento della distribuzione, sarà possibile usare il certificato primario o quello secondario per eseguire operazioni di gestione sul cluster.

![Schermata della distribuzione del certificato in corso][SecurityConfigurations_03]

Di seguito è riportata una schermata che illustra l'aspetto del pannello relativo alla sicurezza dopo il completamento della distribuzione.

![Schermata delle identificazioni personali del certificato dopo la distribuzione][SecurityConfigurations_08]

È possibile usare il nuovo certificato appena aggiunto per connettersi ed eseguire operazioni nel cluster.

> [!NOTE]
> Attualmente non è possibile scambiare i certificati primario e secondario nel portale, tale funzionalità è in lavorazione. Finché è disponibile un certificato valido per il cluster, il cluster funzionerà correttamente.
> 
> 

## <a name="add-a-secondary-certificate-and-swap-it-to-be-the-primary-using-resource-manager-powershell"></a>Aggiungere un certificato secondario e fare in modo che diventi primario tramite PowerShell di Resource Manage
Questi passaggi presuppongono che si abbia familiarità con il funzionamento di Resource Manager e che sia stato distribuito almeno un cluster Service Fabric usando un modello di Resource Manager e che il modello usato per configurare il cluster sia a portata di mano. Inoltre, si presuppone che si abbia dimestichezza con l'uso di JSON.

> [!NOTE]
> Se si sta cercando un modello di esempio e i parametri che è possibile usare per procedere o come punto di partenza, scaricarlo da qui [repository di git]. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

#### <a name="edit-your-resource-manager-template"></a>Modificare il modello di Azure Resource Manager
Se è stato usato l'esempio del [Repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) per proseguire, queste modifiche saranno presenti nell'esempio 5-VM-1-NodeTypes-Secure_Step2.JSON . Usare l'esempio 5-VM-1-NodeTypes-Secure_Step1.JSON per distribuire un cluster sicuro

1. Aprire il modello di Resource Manager usato per la distribuzione del cluster.
2. Aggiungere un nuovo parametro "secCertificateThumbprint" di tipo "string". Se si usa il modello di Resource Manager scaricato dal portale durante la fase di creazione o dai modelli di avvio rapido, ricercare il parametro che dovrebbe essere già definito.  
3. Individuare la definizione di risorsa "Microsoft.ServiceFabric/clusters". Sotto le proprietà si noterà il tag JSON "Certificate" che dovrebbe essere simile al frammento JSON seguente.
   
   ```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
   ``` 
4. Aggiungere un nuovo tag "thumbprintSecondary" e assegnare un valore "[parameters('secCertificateThumbprint')]".  

La definizione della risorsa dovrebbe essere simile alla seguente; a seconda dell'origine del modello, potrebbe non essere esattamente come il frammento seguente. Come si può vedere di seguito, si sta specificando un nuovo certificato come primario e si sta spostando il certificato primario corrente come secondario.  Ciò determina il rollover del certificato corrente nel nuovo certificato in un passaggio di distribuzione.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Modificare il file di modello per riflettere i nuovi parametri aggiunti in precedenza
Se si usa l'esempio del [Repository Git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) per proseguire, è possibile iniziare a modificare l'esempio 5-VM-1-NodeTypes-Secure.paramters_Step2.JSON 

Modificare il parametro File del modello di Resource Manager, aggiungere i nuovi parametri per secCertificate e scambiare i dettagli del certificato primario esistente con il secondario e sostituire i dettagli del certificato primario con i dettagli del nuovo certificato. 

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### <a name="deploy-the-template-to-azure"></a>Distribuire il modello in Azure
1. È ora possibile distribuire il progetto in Azure. Aprire il prompt dei comandi di Azure PowerShell versione 1+.
2. Accedere al proprio account Azure e selezionare la sottoscrizione di Azure specifica. Questo passaggio è importante per gli utenti che hanno accesso a più sottoscrizioni di Azure.

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

Una volta completata la distribuzione, connettersi al cluster tramite il nuovo certificato ed eseguire alcune query, se si è in grado di eseguire questa operazione. È quindi possibile eliminare il vecchio certificato primario. 

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

## <a name="remove-the-old-certificate-using-the-portal"></a>Rimuovere il vecchio certificato tramite il portale
Ecco la procedura per rimuovere un certificato precedente in modo che il cluster non lo usi:

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare alle impostazioni di sicurezza del cluster.
2. Fare clic con il pulsante destro sul certificato da rimuovere
3. Selezionare Elimina e seguire le istruzioni. 

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla gestione del cluster, leggere questi articoli:

* [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
* [Configurare l’accesso in base al ruolo per i client](service-fabric-cluster-security-roles.md)

<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png



<!--HONumber=Nov16_HO3-->


