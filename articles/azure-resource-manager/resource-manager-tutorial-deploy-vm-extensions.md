---
title: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7e0b3fff0ed60d5eb77194e7f9081d35f2e38571
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869638"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager

Questo articolo illustra come usare le [estensioni di macchina virtuale di Azure](../virtual-machines/extensions/features-windows.md) per eseguire attività di configurazione e automazione post-distribuzione nelle VM di Azure. Sono disponibili molte estensioni diverse delle macchine virtuali da usare con macchine virtuali di Azure. In questa esercitazione si distribuisce un'estensione script personalizzato da un modello di Resource Manager per eseguire uno script di PowerShell in una VM Windows.  Lo script installa un server Web nella VM.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare uno script di PowerShell
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello
> * Verificare la distribuzione

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Azure Resource Manager.  Visualizzare [installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault è progettato per proteggere chiavi crittografiche e altri segreti. Per altre informazioni, vedere [Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager](./resource-manager-tutorial-use-key-vault.md). È consigliabile anche aggiornare la password ogni tre mesi.

## <a name="prepare-a-powershell-script"></a>Preparare uno script di PowerShell

Uno script di PowerShell con il contenuto seguente è condiviso in un [account di archiviazione di Azure con accesso pubblico](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se si sceglie di pubblicare il file in una posizione personalizzata, è necessario aggiornare l'elemento [fileUri] del modello più avanti in questa esercitazione.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selezionare **Apri** per aprire il file.
4. Sono presenti cinque risorse definite dal modello:

    * `Microsoft.Storage/storageAccounts`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
5. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

Aggiungere una risorsa estensione di macchina virtuale al modello esistente con il contenuto seguente:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Per altre informazioni sulla definizione di questa risorsa, vedere le [informazioni di riferimento sulle estensioni](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Di seguito sono illustrati alcuni elementi importanti.

* **name**: dato che la risorsa estensione è una risorsa figlio dell'oggetto macchina virtuale, il nome deve includere il nome della macchina virtuale come prefisso. Vedere [Risorse figlio](./resource-manager-templates-resources.md#child-resources).
* **dependsOn**: la risorsa estensione deve essere creata dopo che è stata creata la macchina virtuale.
* **fileUris**: posizioni in cui risiedono i file di script. Se si sceglie di non usare i file forniti, è necessario aggiornare i valori.
* **commandToExecute**: comando per richiamare lo script.  

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) per la procedura di distribuzione. È consigliabile usare una password generata per l'account amministratore della macchina virtuale. Vedere [Prerequisiti](#prerequisites).

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Nel portale selezionare la VM e nella relativa panoramica usare **Fare clic per copiare** a destra dell'indirizzo IP per copiarlo e incollarlo in una scheda del browser. Verrà visualizzata la pagina iniziale di IIS predefinita, che avrà l'aspetto seguente:

![Distribuzione di estensioni di macchina virtuale, script personalizzati e server Web IIS con Azure Resource Manager](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state distribuite una macchina virtuale e un'estensione di macchina virtuale. L'estensione ha installato il server Web IIS nella macchina virtuale. Per informazioni su come usare l'estensione di database SQL per importare un file BACPAC, vedere:

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
