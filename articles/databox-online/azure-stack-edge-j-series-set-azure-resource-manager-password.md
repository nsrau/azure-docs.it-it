---
title: Impostare Azure Resource Manager password sul dispositivo GPU Azure Stack Edge
description: Viene descritto come connettersi alla Azure Resource Manager in esecuzione sulla GPU di Azure Stack Edge utilizzando Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d64db4ab8da1f5f0f4c2dc69b2bc6d4da66c8e91
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146284"
---
# <a name="set-azure-resource-manager-password"></a>Imposta Azure Resource Manager password

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come impostare la password del Azure Resource Manager. È necessario impostare questa password quando ci si connette alle API locali del dispositivo tramite il Azure Resource Manager.

La procedura per impostare la password può variare a seconda che si usi il portale di Azure o i cmdlet di PowerShell. Ognuna di queste procedure è descritta nelle sezioni riportate di seguito.


## <a name="reset-password-via-the-azure-portal"></a>Reimposta la password tramite il portale di Azure

1. Nella portale di Azure passare alla risorsa Azure Stack Edge creata per gestire il dispositivo. Passare a **Calcolo Edge > Inizia subito**.

2. Nel riquadro di destra, dalla barra dei comandi, selezionare **Reimposta la password del ARM Edge**. 

    ![Reimposta password utente EdgeARM 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. Nel pannello **Reimposta password utente EdgeArm** specificare una password per connettersi alle API locali del dispositivo tramite il Azure Resource Manager. Confermare la password e selezionare **Reimposta**.

    ![Reimposta password utente EdgeARM 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Reimposta la password tramite PowerShell

1. Nel portale di Azure passare alla risorsa Azure Stack Edge creata per gestire il dispositivo. Prendere nota dei seguenti parametri nella pagina **Panoramica** .

    - Nome della risorsa Azure Stack Edge
    - ID sottoscrizione

2. Passare a **impostazioni > proprietà**. Prendere nota dei seguenti parametri nella pagina delle **Proprietà** .

    - Resource group
    - Chiave di crittografia CIK: selezionare Visualizza e quindi copiare la **chiave di crittografia**.

    ![Ottenere la chiave di crittografia CIK](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Identificare una password da usare per connettersi a Azure Resource Manager.

4. Avviare cloud Shell. Selezionare l'icona nell'angolo in alto a destra:

    ![Avviare cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Una volta avviato cloud Shell, potrebbe essere necessario passare a PowerShell.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Impostare il contesto. Digitare:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Di seguito è riportato un output di esempio:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Se sono presenti moduli PS precedenti, è necessario installarli.

    `Remove-Module  Az.DataBoxEdge -force`

    Di seguito è riportato un esempio di output. In questo esempio non erano presenti moduli obsoleti da installare.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Il set successivo di comandi consente di scaricare ed eseguire uno script per installare i moduli di PowerShell.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. Nel set di comandi successivo è necessario fornire il nome della risorsa, il nome del gruppo di risorse, la chiave di crittografia e la password identificata nel passaggio precedente.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    La password e i parametri della chiave di crittografia devono essere passati come stringhe protette. Usare i cmdlet seguenti per convertire la password e la chiave di crittografia in stringhe sicure.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Usare le stringhe sicure generate sopra come parametri nel cmdlet Set-AzDataBoxEdgeUser per reimpostare la password. Usare lo stesso gruppo di risorse usato durante la creazione della risorsa Azure Stack Edge/Data Box Gateway.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Ecco l'output di esempio.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Usare la nuova password per connettersi a Azure Resource Manager.

## <a name="next-steps"></a>Passaggi successivi

[Connetti a Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)
