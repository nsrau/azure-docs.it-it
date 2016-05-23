<properties 
	pageTitle="Bloccare le risorse con Gestione risorse | Microsoft Azure" 
	description="Impedire agli utenti di aggiornare o eliminare alcune risorse applicando una restrizione a utenti e ruoli." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2016" 
	ms.author="tomfitz"/>

# Bloccare le risorse con Gestione risorse di Azure

L'amministratore può avere la necessità di bloccare una sottoscrizione, una risorsa o un gruppo di risorse per impedire che altri utenti nell'organizzazione procedano all'eliminazione accidentale di risorse strategiche. Quando sono bloccate, gli utenti autorizzati possono comunque leggere e modificare le risorse, ma non eliminarle.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione vengono usati per applicare una restrizione a tutti gli utenti e i ruoli. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo degli accessi in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

Quando si applica un blocco a un ambito padre, tutte le risorse figlio ereditano lo stesso blocco.

## Chi può creare o eliminare i blocchi nell'organizzazione

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Dei ruoli predefiniti, solo **Owner** e **User Access Administrator** garantiscono tali azioni.

## Creazione di un blocco in un modello

Nell'esempio seguente viene illustrato un modello che crea un blocco su un account di archiviazione. L'account di archiviazione a cui applicare il blocco viene specificato come parametro. Il nome del blocco corrisponde al nome della risorsa seguito da **/Microsoft.Authorization/**. In questo caso è **myLock**.

Il tipo fornito è specifico del tipo di risorsa. Per l'archiviazione, il tipo è "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## Creazione di un blocco con l'API REST

È possibile bloccare le risorse distribuite tramite l'[API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx). L'API REST consente di creare ed eliminare i blocchi e recuperare informazioni sui blocchi esistenti.

Per creare un blocco, eseguire:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Lock-name indica il nome che si desidera assegnare al blocco. Per api-version, usare **2015-01-01**.

Nella richiesta includere un oggetto JSON che specifica le proprietà per il blocco.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Per altri esempi, vedere [API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creazione di un blocco con Azure PowerShell

È possibile bloccare le risorse distribuite con Azure PowerShell tramite **New-AzuremRResourceLock**, come illustrato di seguito.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure Powershell fornisce altri comandi per la gestione dei blocchi, ad esempio **Set-AzureRmResourceLock** per aggiornare un blocco e **Remove-AzureRmResourceLock** per eliminarlo.

## Passaggi successivi

- Per altre informazioni sull'uso dei blocchi di risorse, vedere [Blocco delle risorse di Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx).
- Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md).
- Per modificare il gruppo di risorse in cui si trova una risorsa, vedere [Spostamento delle risorse in un nuovo gruppo di risorse](resource-group-move-resources.md).
- È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

<!---HONumber=AcomDC_0511_2016-->