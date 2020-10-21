---
title: Creare una zona e un record di DNS di Azure - Modello di Azure Resource Manager
titleSuffix: Azure DNS
description: Informazioni su come creare una zona e un record DNS in DNS Azure. Si tratta di un argomento di avvio rapido dettagliato per la creazione e la gestione della prima zona e del primo record DNS con un modello di Azure Resource Manager.
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 986258631d47989e5be5e738da86f844283ce706
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093821"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Creare una zona e un record DNS di Azure con un modello di Azure Resource Manager

Questo argomento di avvio rapido descrive come usare un modello di Azure Resource Manager per creare una zona DNS con un record A.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

In questo argomento di avvio rapido si creerà una zona DNS univoca con il suffisso *<span>azurequickstart.</span>org*. Nella zona verrà inoltre inserito un record *A* che punta a due indirizzi IP.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Nel modello sono state definite due risorse di Azure:

* [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
* [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A) (per creare un record A nella zona)

Per trovare altri modelli correlati a Gestione traffico di Azure, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare **Prova** per il blocco di codice seguente per aprire Azure Cloud Shell e seguire le istruzioni per la connessione ad Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Attendere finché non viene visualizzato il prompt nella console.

1. Selezionare **Copia** nel blocco di codice precedente per copiare lo script di PowerShell.

1. Fare clic con il pulsante destro del mouse sul riquadro della console della shell e quindi scegliere **Incolla**.

1. Immettere i valori desiderati.

    La distribuzione del modello crea una zona con un record A che punta a due indirizzi IP. Il nome del gruppo di risorse è il nome del progetto seguito da **rg**.

    La distribuzione del modello richiede un paio di secondi. Al termine, l'output sarà simile al seguente:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Modello di Resource Manager per la zona DNS di Azure: output della distribuzione con PowerShell":::

Per distribuire il modello viene usato Azure PowerShell. Oltre ad Azure PowerShell, è anche possibile usare il portale di Azure, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Gruppi di risorse** nel riquadro sinistro.

1. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è il nome del progetto seguito da **rg**.

1. Il gruppo di risorse dovrà contenere le risorse illustrate qui:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Modello di Resource Manager per la zona DNS di Azure: output della distribuzione con PowerShell":::

1. Selezionare la zona DNS con il suffisso **<span>azurequickstart.</span>org** per verificare che la zona sia stata creata correttamente con un record **A** che fa riferimento ai valori **1.2.3.4** e **1.2.3.5**.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Modello di Resource Manager per la zona DNS di Azure: output della distribuzione con PowerShell":::

1. Copiare uno dei nomi di server dei nomi dal passaggio precedente.

1. Aprire un prompt dei comandi ed eseguire il comando seguente:

   ```
   nslookup www.<dns zone name> <name server name>
   ```

   Ad esempio:\

   ```
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Verrà visualizzata una schermata simile allo screenshot seguente:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Modello di Resource Manager per la zona DNS di Azure: output della distribuzione con PowerShell":::

Il nome host **www<span>.2lwynbseszpam.azurequickstart.</span>org** si risolve in **1.2.3.4** e **1.2.3.5**, così come è stato configurato. Questo risultato conferma il corretto funzionamento della risoluzione dei nomi.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con la zona DNS non sono più necessarie, eliminare il gruppo di risorse. Oltre alla zona DNS verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:
* Zona DNS
* Record A

Dopo aver creato la prima zona e il primo record DNS con un modello di Azure Resource Manager, è possibile creare i record per un'app Web in un dominio personalizzato.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
