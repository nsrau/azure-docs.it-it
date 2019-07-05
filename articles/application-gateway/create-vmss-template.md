---
title: Creare un Gateway applicazione di Azure - modelli
description: Questo articolo fornisce istruzioni per creare un gateway applicazione di Azure usando il modello di Azure Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 6/26/2019
ms.author: victorh
ms.openlocfilehash: a762e8c9ed1981173f3729837456ac2cfea081b8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449544"
---
# <a name="create-an-application-gateway-using-the-azure-resource-manager-template"></a>Creare un gateway applicazione usando il modello di Azure Resource Manager

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre. Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md).

Questo articolo illustra come scaricare e modificare un oggetto esistente [modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) da GitHub e distribuire il modello da GitHub, Azure PowerShell e la CLI di Azure.

Se si sta distribuendo il modello direttamente da GitHub senza apportare modifiche, ignorare per distribuire un modello da GitHub.

## <a name="scenario"></a>Scenario

In questo scenario si apprenderà come:

* Creare un gateway applicazione con web application firewall.
* Creare una rete virtuale denominata VirtualNetwork1 con un blocco CIDR riservato 10.0.0.0/16.
* Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
* Impostare due indirizzi IP back-end configurati in precedenza per i server Web da usare per bilanciare il carico del traffico. In questo esempio di modello vengono usati gli indirizzi IP back-end 10.0.1.10 e 10.0.1.11.

> [!NOTE]
> Tali impostazioni sono i parametri per il modello. Per personalizzare il modello, è possibile modificare le regole, il listener, il protocollo SSL e altre opzioni nel file azuredeploy.json.

![Scenario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Scaricare e comprendere il modello di Gestione risorse di Azure

È possibile scaricare da GitHub il modello di Gestione risorse di Azure esistente per creare una rete virtuale e due subnet, apportare eventuali modifiche e riutilizzarlo. A tale scopo, seguire questa procedura:

1. Passare a [Create Application Gateway with web application firewall enabled (Creare un gateway applicazione con web application firewall abilitato)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Fare clic su **azuredeploy.json**, quindi fare clic su **RAW**.
1. Salvare il file in una cartella locale nel computer.
1. Se si ha familiarità con i modelli di Azure Resource Manager, andare al passaggio 7.
2. Aprire il file salvato e visualizzare il contenuto in **parameters** nella riga
3. La sezione parameters del modello di Gestione risorse di Azure è un segnaposto per i valori che possono essere inseriti durante la distribuzione.

   | Parametro | Descrizione |
   | --- | --- |
   | **subnetPrefix** |Blocco CIDR della subnet del gateway applicazione. |
   | **applicationGatewaySize** | Dimensione del gateway applicazione.  WAF consente solo gateway di medie e grandi dimensioni. |
   | **backendIpaddress1** |Indirizzo IP del primo server Web. |
   | **backendIpaddress2** |Indirizzo IP del secondo server Web. |
   | **wafEnabled** | Impostazione per determinare se WAF è abilitato.|
   | **wafMode** | Modalità di web application firewall.  Le opzioni disponibili sono **prevention** (prevenzione) e **detection** (rilevamento).|
   | **wafRuleSetType** | Tipo di set di regole per WAF.  Attualmente OWASP è l'unica opzione supportata. |
   | **wafRuleSetVersion** |Versione del set di regole. Attualmente, sono supportate le opzioni OWASP CRS 2.2.9 e 3.0. |

1. Controllare il contenuto in **resources** e prendere nota delle proprietà seguenti:

   * **type**. Tipo di risorsa che sarà creato dal modello. In questo caso il tipo è `Microsoft.Network/applicationGateways`, che rappresenta un gateway applicazione.
   * **name**. Nome della risorsa. Si noti l'uso di `[parameters('applicationGatewayName')]`, che indica che il nome viene specificato come input dell'utente o di un file di parametri durante la distribuzione.
   * **properties**. Elenco di proprietà per la risorsa. Questo modello usa la rete virtuale e l'indirizzo IP pubblico durante la creazione del gateway applicazione.

1. Tornare a [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Fare clic su **azuredeploy-parameters.json** e quindi su **RAW**.
1. Salvare il file in una cartella locale nel computer.
1. Aprire il file salvato e modificare i valori dei parametri. Usare i valori riportati di seguito per la distribuzione del gateway applicazione descritto in questo scenario.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "addressPrefix": {
             "value": "10.0.0.0/16"
             },
             "subnetPrefix": {
             "value": "10.0.0.0/28"
             },
             "applicationGatewaySize": {
             "value": "WAF_Medium"
             },
             "capacity": {
             "value": 2
             },
             "backendIpAddress1": {
             "value": "10.0.1.10"
             },
             "backendIpAddress2": {
             "value": "10.0.1.11"
             },
             "wafEnabled": {
             "value": true
             },
             "wafMode": {
             "value": "Detection"
             },
             "wafRuleSetType": {
             "value": "OWASP"
             },
             "wafRuleSetVersion": {
             "value": "3.0"
             }
         }
     }
     ```

1. Salvare il file. È possibile testare il modello JSON e un modello di parametro usando strumenti online di convalida JSON come [JSlint.com](https://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-using-azure-powershell"></a>Distribuire il modello di Azure Resource Manager tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non si è mai usato Azure PowerShell, vedere: [Come installare e configurare Azure PowerShell](/powershell/azure/overview) e seguire le istruzioni per accedere ad Azure e selezionare la sottoscrizione.

1. Connect to Azure

    ```azurepowershell
    Connect-AzAccount
    ```

1. Controllare le sottoscrizioni per l'account.

    ```azurepowershell
    Get-AzSubscription
    ```

    Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

1. Scegliere quali sottoscrizioni Azure usare.

    ```azurepowershell
    Select-AzSubscription -Subscriptionid "GUID of subscription"
    ```

1. Se necessario, creare un gruppo di risorse usando il **New-AzureResourceGroup** cmdlet. Nell'esempio seguente viene creato un nuovo gruppo di risorse denominato AppgatewayRG nella località Stati Uniti orientali.

    ```azurepowershell
    New-AzResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Eseguire la **New-AzResourceGroupDeployment** cmdlet per distribuire la nuova rete virtuale usando il modello precedente e il parametro file scaricati e modificati.
    
    ```azurepowershell
    New-AzResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-using-the-azure-cli"></a>Distribuire il modello di Azure Resource Manager tramite la CLI di Azure

Per distribuire il modello di Azure Resource Manager scaricato usando CLI di Azure, seguire i passaggi seguenti:

1. Se è la prima volta che si usa l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

1. Se necessario, eseguire il comando `az group create` per creare un nuovo gruppo di risorse, come illustrato nel frammento di codice seguente. Si noti l'output del comando. Nell'elenco riportato dopo l'output sono indicati i parametri usati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (o --name)** . Nome del nuovo gruppo di risorse. Per questo scenario, *appgatewayRG*.
    
    **-l (o --location)** . Area di Azure in cui viene creato il nuovo gruppo di risorse. Per questo scenario, *westus*.

1. Eseguire il `az group deployment create` cmdlet per distribuire la nuova rete virtuale usando il modello e il parametro file scaricati e modificati nel passaggio precedente. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-using-click-to-deploy"></a>Distribuire il modello di Azure Resource Manager tramite clic per la distribuzione

Il pulsante per la distribuzione offre un altro modo per usare i modelli di Gestione risorse di Azure. Questo è un modo semplice di usare i modelli con il portale di Azure.

1. Andare a [Creare un gateway applicazione con web application firewall](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Fare clic su **Distribuzione in Azure**.

    ![Distribuisci in Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Inserire i parametri per il modello di distribuzione nel portale e fare clic su **OK**.

    ![Parametri](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Selezionare **Accetto le condizioni riportate sopra** e quindi fare clic su **Acquista**.

1. Nella pagina distribuzione personalizzata, fare clic su **Create**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Fornire i dati certificato ai modelli di Resource Manager

Quando si usa SSL con un modello, il certificato deve essere fornito in una stringa base64 anziché essere caricato. Per convertire un formato PFX o CER in una stringa base64, usare uno dei comandi seguenti. I comandi seguenti convertono il certificato in una stringa base64 che può essere assegnata al modello. L'output previsto è una stringa che può essere archiviata in una variabile e incollata nel modello.

### <a name="macos"></a>macOS

```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create durante l'esecuzione dell'esercizio, seguire questa procedura:

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell
Remove-AzResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Passaggi successivi

Se si vuole configurare l'offload SSL, vedere: [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Se si desidera configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere: [Creare un gateway applicazione con un servizio di bilanciamento del carico interno](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)