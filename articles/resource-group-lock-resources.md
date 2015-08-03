<properties 
	pageTitle="Bloccare le risorse con Gestione risorse di Azure" 
	description="Bloccare le risorse per impedire agli utenti di aggiornare o eliminare risorse specifiche." 
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
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Bloccare le risorse con Gestione risorse di Azure

Esistono scenari in cui l'amministratore può desiderare di applicare un blocco a una risorsa o a un gruppo di risorse per impedire che altri utenti nell'organizzazione procedano ad operazioni di scrittura oppure procedano all'eliminazione accidentale di una risorsa strategica.

La funzionalità Gestione risorse di Azure consente di limitare le operazioni sulle risorse tramite blocchi alla gestione delle risorse. I blocchi di risorse sono criteri che impongono un livello di blocco in un ambito specifico. Il livello di blocco identifica il tipo di imposizione per i criteri e attualmente dispone di due valori, ovvero **CanNotDelete**e**ReadOnly**. L'ambito viene espresso come URI e può essere una risorsa o un gruppo di risorse.

I blocchi sono funzionalità diverse dall'assegnazione delle autorizzazioni utente per eseguire determinate azioni. Per altre informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo degli accessi in base al ruolo nel portale di anteprima](role-based-access-control-configure.md) e [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md).

## Scenari comuni

Uno scenario comune potrebbe essere rappresentato da un gruppo di risorse contenente alcune risorse usate saltuariamente. Le risorse delle macchine virtuali vengono periodicamente attivate per elaborare i dati durante un determinato intervallo di tempo e quindi vengono disattivate. In questo scenario, è possibile abilitare l'arresto delle macchine virtuali, ma è fondamentale che non venga eliminato l'account di archiviazione. In questo scenario, all'account di archiviazione è possibile applicare un blocco di risorsa con il livello di blocco **CanNotDelete**.

In un altro scenario, l'azienda potrebbe essere caratterizzata da periodi in cui non si desidera applicare gli aggiornamenti all'ambiente di produzione. Il livello di blocco **ReadOnly** consente di interrompere la creazione o gli aggiornamenti. In una società di vendita al dettaglio non è possibile consentire gli aggiornamenti durante le festività. In una società di servizi finanziari è possibile applicare vincoli relativi alle distribuzioni in determinati orari di apertura dei mercati. Un blocco di risorsa può fornire un criterio per bloccare le risorse, se necessario. Tale blocco può essere applicato solo a determinate risorse oppure a tutto il gruppo di risorse.

## Creazione di un blocco in un modello

Nell'esempio seguente viene illustrato un modello che crea un blocco su un account di archiviazione. L'account di archiviazione a cui applicare il blocco viene fornito come parametro e viene usato in combinazione con la funzione concat(). Il risultato è il nome di risorsa seguito da 'Microsoft.Authorization' e quindi il nome del blocco, in questo caso **myLock**.

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

scope può essere una sottoscrizione, un gruppo di risorse o una risorsa. lock-name rappresenta il nome che si desidera assegnare al blocco. Per api-version, usare**2015-01-01**.

Nella richiesta include un oggetto JSON che specifica le proprietà per il blocco.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Per lock-level, specificare**CanNotDelete**o**ReadOnly**.

Per altri esempi, vedere [API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creazione di un blocco con Azure PowerShell

È possibile bloccare le risorse distribuite con Azure PowerShell tramite il**New-AzureResourceLock**, come illustrato di seguito.

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell fornisce altri comandi per la gestione dei blocchi, ad esempio **Set-AzureResourceLock** per aggiornare un blocco e **Remove-AzureResourceLock** per eliminare un blocco.

## Passaggi successivi

- [Utilizzo dei tag per organizzare le risorse](resource-group-using-tags.md)
- [Spostare le risorse al nuovo gruppo di risorse](resource-group-move-resources.md)

<!---HONumber=July15_HO4-->