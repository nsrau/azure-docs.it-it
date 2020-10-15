---
title: Configurare i criteri dell'endpoint di servizio-Azure HDInsight
description: Informazioni su come configurare i criteri dell'endpoint di servizio per la rete virtuale con Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: f2752e5ab2bf7c2926ec9e2c0e4929eab91ed377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88530971"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Configurare i criteri dell'endpoint del servizio rete virtuale per Azure HDInsight

Questo articolo fornisce informazioni su come implementare i criteri degli endpoint di servizio nelle reti virtuali con Azure HDInsight.

## <a name="background"></a>Background

Azure HDInsight consente di creare cluster nella propria rete virtuale. Se è necessario consentire il traffico in uscita dalla rete virtuale ad altri servizi di Azure come gli account di archiviazione, è possibile creare [criteri di endpoint di servizio](../virtual-network/virtual-network-service-endpoint-policies-overview.md). I criteri dell'endpoint di servizio creati tramite il portale di Azure, tuttavia, consentono solo di creare criteri per un singolo account, tutti gli account in una sottoscrizione o tutti gli account in un gruppo di risorse.

Come servizio gestito, tuttavia, Azure HDInsight raccoglie i dati e i file di log da ogni cluster in account di archiviazione specifici in ogni area. Affinché questi dati raggiungano HDInsight dalla rete virtuale, è necessario creare criteri di endpoint di servizio che consentano il traffico in uscita verso punti di raccolta dati specifici gestiti da Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Criteri dell'endpoint di servizio per HDInsight

Questi criteri di endpoint di servizio supportano le funzionalità seguenti:

- Raccolta di log e telemetria per la creazione del cluster, l'esecuzione di processi e le operazioni della piattaforma, ad esempio il ridimensionamento.
- Connessione di dischi rigidi virtuali (VHD) ai nodi del cluster appena creati per il provisioning di software e librerie nel cluster.

Se i criteri dell'endpoint di servizio non vengono creati per abilitare questo flusso di dati, la creazione del cluster potrebbe non riuscire e Azure HDInsight non sarà in grado di fornire supporto per i cluster.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Creare criteri di endpoint di servizio per HDInsight

Prima di creare nuovi cluster, assicurarsi che i criteri dell'endpoint di servizio corretti siano collegati alla rete virtuale. In caso contrario, la creazione del cluster potrebbe non riuscire o causare un errore.

Utilizzare la procedura seguente per creare i criteri dell'endpoint di servizio necessari:

1. Decidere l'area in cui verrà creato il cluster HDInsight.
1. Cercare tale area nell' [elenco delle risorse dei criteri dell'endpoint di servizio](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), che fornisce tutti i gruppi di risorse per gli account di archiviazione di HDInsight Management.
1. Selezionare l'elenco dei gruppi di risorse per l'area geografica. Di seguito è riportato un esempio delle risorse per `Canada Central` :

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Inserire l'elenco di gruppi di risorse nello script di installazione scritto nell'interfaccia della riga di comando di Azure o Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Se si preferisce configurare i criteri dell'endpoint di servizio usando PowerShell, usare il frammento di codice seguente.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Architettura della rete virtuale di HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurare un'appliance virtuale di rete](./network-virtual-appliance.md)
