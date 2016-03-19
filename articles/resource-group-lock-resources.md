<properties 
	pageTitle="Bloccare le risorse con Gestione risorse | Microsoft Azure" 
	description="Impedire agli utenti di aggiornare o eliminare alcune risorse applicando una restrizione a utenti e ruoli." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="tomfitz"/>

# Bloccare le risorse con Gestione risorse di Azure

Esistono scenari in cui l'amministratore può voler applicare un blocco a una sottoscrizione, a una risorsa o a un gruppo di risorse per impedire che altri utenti nell'organizzazione procedano all'eliminazione accidentale di una risorsa strategica.

La funzionalità Gestione risorse di Azure consente di limitare le operazioni sulle risorse tramite blocchi alla gestione delle risorse. I blocchi sono criteri che impongono un livello di blocco in un ambito specifico. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Il livello di blocco identifica il tipo di imposizione per i criteri che può essere impostato su **CanNotDelete**. **CanNotDelete** significa che gli utenti autorizzati possono comunque leggere e modificare le risorse, ma non eliminare le risorse limitate.

L'uso dei blocchi piuttosto che del controllo degli accessi in base al ruolo per assegnare autorizzazioni utente per l'esecuzione di determinate azioni presenta caratteristiche diverse. Per informazioni sull’impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md). Diversamente dal controllo degli accessi in base al ruolo, è possibile usare blocchi di gestione per applicare una restrizione a utenti e ruoli, e in genere i blocchi si applicano solo per una durata limitata.

Quando si applica un blocco a un ambito padre, tutte le risorse figlio ereditano lo stesso blocco.

## Scenari comuni

Uno scenario comune potrebbe essere rappresentato da un gruppo di risorse contenente alcune risorse usate saltuariamente. Le risorse delle macchine virtuali vengono periodicamente attivate per elaborare i dati durante un determinato intervallo di tempo e quindi vengono disattivate. In questo scenario, è possibile abilitare l'arresto delle macchine virtuali, ma è fondamentale che non venga eliminato l'account di archiviazione. In questo scenario, all'account di archiviazione è possibile applicare un blocco di risorsa con il livello di blocco **CanNotDelete**.

## Chi può creare o eliminare i blocchi nell'organizzazione

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Dei ruoli predefiniti, solo **Owner** e **User Access Administrator** garantiscono tali azioni. Per ulteriori informazioni sull’assegnazione del controllo di accesso, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

## Creazione di un blocco in un modello

Nell'esempio seguente viene illustrato un modello che crea un blocco su un account di archiviazione. L'account di archiviazione a cui applicare il blocco viene fornito come parametro e viene usato in combinazione con la funzione concat(). Il risultato è il nome di risorsa seguito da 'Microsoft.Authorization' e il nome del blocco, in questo caso **myLock**.

Il tipo fornito è specifico del tipo di risorsa. Per l'archiviazione, il tipo è "Microsoft.Storage/storageaccounts/providers/locks".

Il livello dell'ambito viene impostato tramite la proprietà **level** della risorsa. Dal momento che l'esempio fa riferimento a uno scenario in cui si desidera evitare l'eliminazione accidentale, il livello è impostato su **CannotDelete**.

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
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Per lock-level specificare **CanNotDelete**.

Per altri esempi, vedere [API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creazione di un blocco con Azure PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

È possibile bloccare le risorse distribuite con Azure PowerShell tramite **New-AzuremRResourceLock**, come illustrato di seguito. Con PowerShell è possibile impostare solo **LockLevel** su **CanNotDelete**.

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure Powershell fornisce altri comandi per la gestione dei blocchi, ad esempio **Set-AzureRmResourceLock** per aggiornare un blocco e **Remove-AzureRmResourceLock** per eliminarlo.

## Passaggi successivi

- Per altre informazioni sull'uso dei blocchi di risorse, vedere [Blocco delle risorse di Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx).
- Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md).
- Per modificare il gruppo di risorse in cui si trova una risorsa, vedere [Spostamento delle risorse in un nuovo gruppo di risorse](resource-group-move-resources.md).
- È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

<!---HONumber=AcomDC_0128_2016-->