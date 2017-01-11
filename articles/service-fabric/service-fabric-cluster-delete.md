---
title: Eliminare un cluster di Azure e le risorse correlate | Microsoft Docs
description: Informazioni su come eliminare completamente un cluster Service Fabric rimuovendo il gruppo di risorse contenente il cluster o rimuovendo le risorse in modo selettivo.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: e73334373e9909069641cd4ad2c3280e51b5e2db


---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Eliminare un cluster Service Fabric in Azure e le risorse che utilizzate
Un cluster Service Fabric è costituito da molte risorse di Azure oltre alla risorsa cluster stessa. Per eliminare completamente un cluster Service Fabric è necessario eliminare anche tutte le risorse di cui è composto.
Sono disponibili due opzioni: eliminare il gruppo di risorse contenente il cluster (in modo da rimuovere la risorsa cluster e le altre risorse del gruppo) oppure eliminare la specifica risorsa cluster e le risorse associate (ma non le altre risorse del gruppo).

> [!NOTE]
> Quando si elimina la risorsa cluster, tutte le altre risorse che compongono il cluster Service Fabric **non** vengono eliminate.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Eliminare l'intero gruppo di risorse contenente il cluster Service Fabric
Questo è il modo più semplice per verificare che vengano eliminate tutte le risorse associate al cluster, incluso il gruppo di risorse. È possibile eliminare il gruppo di risorse mediante PowerShell o tramite il portale di Azure. Se nel gruppo sono incluse risorse non correlate al cluster Service Fabric, è possibile eliminare le risorse specifiche.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Eliminare il gruppo di risorse mediante Azure PowerShell
Per eliminare il gruppo di risorse è anche possibile eseguire i cmdlet di Azure PowerShell seguenti. Verificare che nel computer sia installato Azure PowerShell 1.0 o versione successiva. Se non è ancora installato, seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs)

Aprire una finestra di PowerShell ed eseguire i cmdlet di PowerShell seguenti:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Verrà visualizzato un prompt per confermare l'eliminazione, se non è stata usata l'opzione *-Force* . Al momento della conferma verranno eliminati il gruppo e tutte le risorse incluse.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Eliminare un gruppo di risorse nel portale di Azure
1. Eseguire l'accesso al [portale di Azure](https://portal.azure.com).
2. Passare al cluster Service Fabric da eliminare.
3. Fare clic sul nome del gruppo di risorse nella pagina delle informazioni di base del cluster.
4. Verrà visualizzata la pagina **Informazioni di base** del gruppo di risorse.
5. Fare clic su **Elimina**.
6. Seguire le istruzioni visualizzate nella pagina per completare l'eliminazione del gruppo di risorse.

![Eliminazione di un gruppo di risorse][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Eliminare la risorsa cluster e le risorse correlate, ma non le altre risorse del gruppo
Se nel gruppo sono incluse solo le risorse correlate al cluster Service Fabric da eliminare, è più semplice eliminare l'intero gruppo di risorse. Se invece si vuole eliminare in modo selettivo una risorsa alla volta nel gruppo, seguire questa procedura.

Se il cluster è stato distribuito mediante il portale o tramite uno dei modelli Resource Manager di Service Fabric inclusi nella raccolta dei modelli, tutte le risorse usate dal cluster vengono contrassegnate con i due tag seguenti. Questi tag sono utili per identificare le risorse da eliminare.

***Tag#1:*** chiave = clusterName, valore = 'nome del cluster'

***Tag#2:*** chiave = resourceName, valore = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Eliminare risorse specifiche nel portale di Azure
1. Eseguire l'accesso al [portale di Azure](https://portal.azure.com).
2. Passare al cluster Service Fabric da eliminare.
3. Scegliere **Tutte le impostazioni** nel pannello Informazioni di base.
4. Fare clic su **Tag** in **Gestione risorse** nel pannello delle impostazioni.
5. Fare clic su uno dei **tag** nel pannello Tag per ottenere l'elenco di tutte le risorse con il tag selezionato.
   
    ![Tag delle risorse][ResourceTags]
6. Nell'elenco delle risorse contrassegnate con il tag fare clic su ciascuna risorsa ed eliminarla.
   
    ![Risorse con tag][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Eliminare le risorse mediante Azure PowerShell
Per eliminare una risorsa alla volta è possibile eseguire i cmdlet di Azure PowerShell seguenti. Verificare che nel computer sia installato Azure PowerShell 1.0 o versione successiva. Se non è ancora installato, seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs)

Aprire una finestra di PowerShell ed eseguire i cmdlet di PowerShell seguenti:

```powershell
Login-AzureRmAccount
```
Per ogni risorsa da eliminare, eseguire il cmdlet seguente:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Per eliminare la risorsa cluster, eseguire il cmdlet seguente:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'aggiornamento di un cluster e il partizionamento dei servizi, vedere gli articoli seguenti:

* [Informazioni sugli aggiornamenti dei cluster](service-fabric-cluster-upgrade.md)
* [Informazioni sul partizionamento dei servizi con stato per la massima scalabilità](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG



<!--HONumber=Dec16_HO1-->


