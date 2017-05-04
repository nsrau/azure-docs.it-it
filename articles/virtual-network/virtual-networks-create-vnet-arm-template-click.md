---
title: Creare una rete virtuale | Modello di Azure Resource Manager | Documentazione Microsoft
description: Informazioni su come creare una rete virtuale usando un modello di Azure Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a179f184b7d6a425d0fed4b58551b08bb937ea87
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Creare una rete virtuale usando un modello di Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure offre due modelli di distribuzione, ovvero Azure Resource Manager e la distribuzione classica. Microsoft consiglia di creare le risorse tramite il modello di distribuzione Resource Manager. Per altre informazioni sulle differenze tra i due modelli, leggere l'articolo [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).
 
Questo articolo illustra come creare una rete virtuale tramite il modello di distribuzione Resource Manager usando un modello di Azure Resource Manager. È anche possibile creare una rete virtuale tramite Resource Manager usando altri strumenti oppure tramite il modello di distribuzione classica selezionando un'opzione diversa dall'elenco seguente:

> [!div class="op_single_selector"]
- [Portale](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Modello](virtual-networks-create-vnet-arm-template-click.md)
- [Portale (versione classica)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (versione classica)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Interfaccia della riga di comando (versione classica)](virtual-networks-create-vnet-classic-cli.md)

Verrà illustrato come scaricare e modificare un modello di Gestione risorse di Azure esistente da GitHub e distribuire il modello da GitHub, PowerShell e dall'interfaccia della riga di comando di Azure.

Se si sta distribuendo semplicemente il modello di Gestione risorse di Azure direttamente da GitHub, senza alcuna modifica, ignorare il passaggio per [distribuire un modello da github](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Scaricare e comprendere il modello di Gestione risorse di Azure
È possibile scaricare il modello esistente per la creazione di una rete virtuale e di due subnet da GitHub, apportare le modifiche desiderate e riutilizzarlo. A questo scopo, seguire questa procedura:

1. Passare alla [pagina del modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Fare clic su **azuredeploy.json**, quindi fare clic su **RAW**.
3. Salvare il file in una cartella locale nel computer in uso.
4. Se si ha familiarità con i modelli, procedere al passaggio 7.
5. Aprire il file appena salvato e visualizzare il contenuto in **parameters** nella riga 5. I parametri del modello ARM costituiscono un segnaposto per i valori che possono essere compilati durante la distribuzione.
   
   | Parametro | Descrizione |
   | --- | --- |
   | **Località** |Area di Azure in cui verrà creata la rete virtuale |
   | **vnetName** |Nome per la nuova rete virtuale |
   | **addressPrefix** |Spazio di indirizzi per la rete virtuale, nel formato CIDR |
   | **subnet1Name** |Nome per la prima rete virtuale |
   | **subnet1Prefix** |Blocco CIDR per la prima subnet |
   | **subnet2Name** |Nome per la seconda rete virtuale |
   | **subnet2Prefix** |Blocco CIDR per la seconda subnet |
   
   > [!IMPORTANT]
   > I modelli di Gestione risorse di Azure conservati in GitHub possono cambiare nel tempo. Assicurarsi di aver controllato il modello prima di utilizzarlo.
   > 
   > 
6. Controllare il contenuto in **resources** e prendere nota di quanto segue:
   
   * **type**. Tipo di risorsa che sarà creato dal modello. In questo caso, **Microsoft.Network/virtualNetworks**, che rappresenta una rete virtuale.
   * **name**. Nome della risorsa. Notare l'utilizzo di **[parameters('vnetName')]**con cui si indica che il nome verrà specificato come input dall'utente o tramite un file di parametri durante la distribuzione.
   * **properties**. Elenco di proprietà per la risorsa. Questo modello utilizza le proprietà relative allo spazio di indirizzi e alla subnet durante la creazione della rete virtuale.
7. Tornare alla [pagina del modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Fare clic su **azuredeploy-parameters.json** e quindi su **RAW**.
9. Salvare il file in una cartella locale nel computer in uso.
10. Aprire il file appena salvato e modificare i valori per i parametri. Usare i valori riportati di seguito per la distribuzione della rete virtuale descritta in questo scenario:

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Salvare il file.


## <a name="deploy-the-template-using-powershell"></a>Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire questa procedura:

1. Installare e configurare Azure PowerShell eseguendo i passaggi descritti nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
2. Usare il comando seguente per creare un nuovo gruppo di risorse:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Il comando crea un gruppo di risorse denominato *TestRG* nell'area di Azure *Stati Uniti centrali*. Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Output previsto:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Eseguire il comando seguente per distribuire la nuova rete virtuale usando il modello e i file dei parametri scaricati e modificati in precedenza:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Output previsto:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Eseguire il comando seguente per visualizzare le proprietà della nuova rete virtuale:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Output previsto:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Distribuire il modello tramite clic per la distribuzione

È possibile riutilizzare modelli di Azure Resource Manager predefiniti, caricarli in un repository GitHub gestito da Microsoft e renderli disponibili alla community. Questi modelli possono essere distribuiti immediatamente da GitHub o scaricati e modificati in base alle specifiche esigenze. Per distribuire un modello che crea una rete virtuale con due subnet, seguire questa procedura:

1. Da un browser, passare a [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Scorrere verso il basso nell'elenco dei modelli e fare clic su **101-vnet-two-subnets**. Controllare il file **README.md** , come illustrato di seguito.

    ![File READEME.md in github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Fare clic su **Distribuzione in Azure**. Se necessario, immettere le credenziali di accesso di Azure. 
4. Nel pannello **Parametri** immettere i valori da usare per creare la nuova rete virtuale e quindi fare clic su **OK**. La figura seguente mostra i valori relativi allo scenario:
   
    ![Parametri del modello ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse a cui aggiungere la rete virtuale oppure fare clic su **Crea nuovo** per aggiungere la rete virtuale a un nuovo gruppo di risorse. La figura seguente mostra le impostazioni del gruppo di risorse per un nuovo gruppo di risorse denominato **TestRG**:

    ![Resource group](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Se necessario, modificare le impostazioni relative a **Sottoscrizione** e **Località** per la rete virtuale.
7. Se non si vuole visualizzare la rete virtuale come riquadro nella **Schermata iniziale**, disabilitare **Aggiungi alla Schermata iniziale**.
8. Fare clic su **Note legali**, leggere le condizioni e fare clic su **Acquista** per accettare. 
9. Fare clic su **Crea** per creare la rete virtuale.
   
    ![Invio del riquadro di distribuzione nel portale di anteprima](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Dopo il completamento dell'operazione, nel portale di Azure fare clic su **More services** (Altri servizi), digitare *reti virtuali* nella casella dei filtri visualizzata e quindi fare clic su Reti virtuali per visualizzare il pannello Reti virtuali. Nel pannello fare clic su *TestVNet*. Nel pannello *TestVNet* fare clic su **Subnet** per visualizzare le subnet create, come mostrato nella figura seguente:
    
     ![Creare una rete virtuale nel portale di anteprima](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come connettere:

- Una macchina virtuale (VM) a una rete virtuale negli articoli [Creare una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [Creare una VM Linux](../virtual-machines/linux/quick-create-portal.md). Anziché creare una rete virtuale e una subnet, come illustrato nelle procedure degli articoli, è possibile selezionare una rete virtuale e una subnet esistenti a cui connettere una VM.
- La rete virtuale ad altre reti virtuali nell'articolo [Connettere reti virtuali](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- La rete virtuale a una rete locale tramite una rete privata virtuale (VPN) da sito a sito o il circuito ExpressRoute negli articoli [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Connettere una rete virtuale a una rete locale tramite una VPN da sito a sito) e [Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).

