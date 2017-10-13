---
title: Creare un gateway applicazione di Azure - Modelli | Microsoft Docs
description: Questa pagina fornisce istruzioni per la creazione di un gateway applicazione di Azure usando il modello di Gestione risorse di Azure
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 305a0529b6f6ad8bd96ac10da5f7ebc48317df45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Creare un gateway applicazione usando il modello di Gestione risorse di Azure

> [!div class="op_single_selector"]
> * [portale di Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell per Azure classico](application-gateway-create-gateway.md)
> * [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-create-gateway-cli.md)

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre. Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md).

Questo articolo illustra come scaricare e modificare un modello di Azure Resource Manager esistente da GitHub e distribuire il modello da GitHub, da PowerShell e dall'interfaccia della riga di comando di Azure.

Se si sta distribuendo il modello di Gestione risorse di Azure direttamente da GitHub, senza alcuna modifica, andare al passaggio che illustra la distribuzione di un modello da GitHub.

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
1. Se si ha familiarità con i modelli di Gestione risorse di Azure, procedere al passaggio 7.
1. Aprire il file salvato e visualizzare il contenuto in **parameters** nella riga
1. La sezione parameters del modello di Gestione risorse di Azure è un segnaposto per i valori che possono essere inseriti durante la distribuzione.

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

   > [!NOTE]
   > Per altre informazioni sui modelli, visitare la pagina [Resource Manager templates reference (Riferimenti ai modelli di Resource Manager)](/templates/)

1. Tornare a [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Fare clic su **azuredeploy-parameters.json** e quindi su **RAW**.
1. Salvare il file in una cartella locale nel computer.
1. Aprire il file salvato e modificare i valori dei parametri. Usare i valori riportati di seguito per la distribuzione del gateway applicazione descritto in questo scenario.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

1. Salvare il file. È possibile testare il modello JSON e il modello di parametri usando strumenti online di convalida di JSON come [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Distribuire il modello di Gestione risorse di Azure usando PowerShell

Se è la prima volta che si usa Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) e seguire le istruzioni per accedere ad Azure e selezionare la sottoscrizione.

1. Accesso a PowerShell

    ```powershell
    Login-AzureRmAccount
    ```

1. Controllare le sottoscrizioni per l'account.

    ```powershell
    Get-AzureRmSubscription
    ```

    Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

1. Scegliere le sottoscrizioni ad Azure da usare.

    ```powershell
    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
    ```

1. Se necessario, creare un gruppo di risorse usando il cmdlet **New-AzureResourceGroup** . Nell'esempio seguente viene creato un nuovo gruppo di risorse denominato AppgatewayRG nella località Stati Uniti orientali.

    ```powershell
    New-AzureRmResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Eseguire il cmdlet **New-AzureRmResourceGroupDeployment** per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati in precedenza.
    
    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Distribuire il modello di Gestione risorse di Azure usando l'interfaccia della riga di comando di Azure

Per distribuire il modello di Azure Resource Manager scaricato usando l'interfaccia della riga di comando di Azure, seguire questa procedura:

1. Se è la prima volta che si usa l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

1. Se necessario, eseguire il comando `az group create` per creare un nuovo gruppo di risorse, come illustrato nel frammento di codice seguente. Si noti l'output del comando. Nell'elenco riportato dopo l'output sono indicati i parametri usati. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (o --nome)**. Nome del nuovo gruppo di risorse. Per questo scenario, *appgatewayRG*.
    
    **-l (o --location)**. Area di Azure in cui viene creato il nuovo gruppo di risorse. Per questo scenario, *westus*.

1. Eseguire il cmdlet `az group deployment create` per distribuire la nuova rete virtuale usando il modello e i file di parametri scaricati e modificati nel passaggio precedente. Nell'elenco riportato dopo l'output sono indicati i parametri usati.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Distribuire il modello di Gestione risorse di Azure usando il pulsante per la distribuzione

Il pulsante per la distribuzione offre un altro modo per usare i modelli di Gestione risorse di Azure. Questo è un modo semplice di usare i modelli con il portale di Azure.

1. Andare a [Creare un gateway applicazione con web application firewall](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Fare clic su **Distribuzione in Azure**.

    ![Distribuzione in Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Inserire i parametri per il modello di distribuzione nel portale e fare clic su **OK**.

    ![Parametri](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Selezionare **Accetto le condizioni riportate sopra** e quindi fare clic su **Acquista**.

1. Nel pannello Distribuzione personalizzata fare clic su **Crea**.

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

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureRmResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'offload SSL, visitare [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, visitare [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

