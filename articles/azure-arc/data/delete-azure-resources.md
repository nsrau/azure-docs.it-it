---
title: Eliminare le risorse in Azure
description: Eliminare le risorse in Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3fe7b39d0e47965a9603b276960d48d6d3063073
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108253"
---
# <a name="delete-resources-from-azure"></a>Eliminare le risorse in Azure

> [!NOTE]
>  Le opzioni per eliminare le risorse in questo articolo sono irreversibili.

> [!NOTE]
>  Poiché l'unica modalità di connettività offerta per Azure Arc Enabled Data Services è attualmente la modalità connessa indiretta, l'eliminazione di un'istanza da Kubernetes non lo rimuoverà da Azure e l'eliminazione di un'istanza da Azure non lo rimuoverà da Kubernetes.  Per ora l'eliminazione di una risorsa è un processo in due fasi che verrà migliorato in futuro.  In futuro, Kubernetes sarà l'origine della verità e Azure verrà aggiornato per riflettere l'it.

In alcuni casi, potrebbe essere necessario eliminare manualmente le risorse di Azure Arc abilitate per i servizi dati in Azure Resource Manager (ARM).  È possibile eliminare queste risorse usando una delle opzioni seguenti.

- [Eliminare le risorse in Azure](#delete-resources-from-azure)
  - [Eliminare un intero gruppo di risorse](#delete-an-entire-resource-group)
  - [Eliminare risorse specifiche nel gruppo di risorse](#delete-specific-resources-in-the-resource-group)
  - [Eliminare le risorse usando l'interfaccia della riga di comando di Azure](#delete-resources-using-the-azure-cli)
    - [Eliminare risorse dell'istanza gestita di SQL usando l'interfaccia della riga di comando di Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Eliminare le risorse del gruppo di server di iperscala PostgreSQL usando l'interfaccia della riga di comando](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Eliminare le risorse di Azure Arc data controller usando l'interfaccia della riga di comando di Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Eliminare un gruppo di risorse usando l'interfaccia della riga di comando di Azure](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Eliminare un intero gruppo di risorse
Se si usa un gruppo di risorse specifico e dedicato per i servizi dati abilitati per Azure Arc e si vuole eliminare *tutti gli elementi* all'interno del gruppo di risorse, è possibile eliminare il gruppo di risorse che eliminerà tutto il contenuto.  

È possibile eliminare un gruppo di risorse nel portale di Azure eseguendo le operazioni seguenti:

- Passare al gruppo di risorse nel portale di Azure in cui sono state create le risorse di Azure Arc Enabled Data Services.
- Fare clic sul pulsante **Elimina gruppo di risorse** .
- Per confermare l'eliminazione, immettere il nome del gruppo di risorse e fare clic su **Elimina**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Eliminare risorse specifiche nel gruppo di risorse

È possibile eliminare le risorse di servizi dati abilitate per Azure Arc specifiche in un gruppo di risorse nel portale di Azure eseguendo le operazioni seguenti:

- Passare al gruppo di risorse nel portale di Azure in cui sono state create le risorse di Azure Arc Enabled Data Services.
- Selezionare tutte le risorse da eliminare.
- Fare clic sul pulsante Elimina.
- Confermare l'eliminazione digitando ' Sì' e fare clic su **Elimina**.

## <a name="delete-resources-using-the-azure-cli"></a>Eliminare le risorse usando l'interfaccia della riga di comando di Azure

È possibile eliminare risorse specifiche di Azure Arc abilitate con l'interfaccia della riga di comando di Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Eliminare risorse dell'istanza gestita di SQL usando l'interfaccia della riga di comando di Azure

Per eliminare le risorse dell'istanza gestita di SQL da Azure usando l'interfaccia della riga di comando di Azure, sostituire i valori segnaposto nel comando seguente ed eseguirlo.

```console
az resource delete --name <sql instance name> --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Eliminare le risorse del gruppo di server di iperscala PostgreSQL usando l'interfaccia della riga di comando

Per eliminare una risorsa del gruppo di server di iperscala PostgreSQL da Azure usando l'interfaccia della riga di comando di Azure, sostituire i valori segnaposto nel comando seguente ed eseguirlo.

```console
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Eliminare le risorse di Azure Arc data controller usando l'interfaccia della riga di comando di Azure

> [!NOTE]
> Prima di eliminare un controller dati di Azure Arc, è necessario eliminare tutte le risorse dell'istanza del database gestite.

Per eliminare un controller dati di Azure Arc da Azure usando l'interfaccia della riga di comando di Azure, sostituire i valori segnaposto nel comando seguente ed eseguirlo.

```console
az resource delete --name <data controller name> --resource-type Microsoft.AzureData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Eliminare un gruppo di risorse usando l'interfaccia della riga di comando di Azure

È anche possibile usare l'interfaccia della riga di comando di Azure per [eliminare un gruppo di risorse](../../azure-resource-manager/management/delete-resource-group.md).