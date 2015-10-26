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
	ms.date="10/14/2015" 
	ms.author="tomfitz"/>

# Bloccare le risorse con Gestione risorse di Azure

Esistono scenari in cui l'amministratore può desiderare di applicare un blocco a una sottoscrizione, a una risorsa o a un gruppo di risorse per impedire che altri utenti nell'organizzazione procedano ad operazioni di scrittura o all'eliminazione accidentale di una risorsa strategica.

La funzionalità Gestione risorse di Azure consente di limitare le operazioni sulle risorse tramite blocchi alla gestione delle risorse. I blocchi sono criteri che impongono un livello di blocco in un ambito specifico. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Il livello di blocco identifica il tipo di imposizione per i criteri e attualmente dispone di due valori, ovvero **CanNotDelete** e **ReadOnly**. **CanNotDelete** significa che gli utenti autorizzati possono comunque leggere e modificare le risorse, ma non eliminare le risorse limitate. **ReadOnly** significa che gli utenti autorizzati possono solo leggere la risorsa, ma non modificare o eliminare le risorse limitate.

L'uso dei blocchi piuttosto che del controllo degli accessi in base al ruolo per assegnare autorizzazioni utente per l'esecuzione di determinate azioni presenta caratteristiche diverse. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo degli accessi in base al ruolo nel portale di anteprima](role-based-access-control-configure.md) e [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md). Diversamente dal controllo degli accessi in base al ruolo, è possibile usare blocchi di gestione per applicare una restrizione a utenti e ruoli, e in genere i blocchi si applicano solo per una durata limitata.

## Scenari comuni

Uno scenario comune potrebbe essere rappresentato da un gruppo di risorse contenente alcune risorse usate saltuariamente. Le risorse delle macchine virtuali vengono periodicamente attivate per elaborare i dati durante un determinato intervallo di tempo e quindi vengono disattivate. In questo scenario, è possibile abilitare l'arresto delle macchine virtuali, ma è fondamentale che non venga eliminato l'account di archiviazione. In questo scenario, all'account di archiviazione è possibile applicare un blocco di risorsa con il livello di blocco **CanNotDelete**.

In un altro scenario, l'azienda potrebbe essere caratterizzata da periodi in cui non si desidera applicare gli aggiornamenti all'ambiente di produzione. Il livello di blocco **ReadOnly** consente di interrompere la creazione o gli aggiornamenti. In una società di vendita al dettaglio non è possibile consentire gli aggiornamenti durante le festività. In una società di servizi finanziari è possibile applicare vincoli relativi alle distribuzioni in determinati orari di apertura dei mercati. Un blocco di risorsa può fornire un criterio per bloccare le risorse, se necessario. Tale blocco può essere applicato solo a determinate risorse oppure a tutto il gruppo di risorse.

## Chi può creare o eliminare i blocchi nell'organizzazione

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Dei ruoli predefiniti, solo **Owner** e **User Access Administrator** garantiscono tali azioni. Per altre informazioni sull'assegnazione del controllo degli accessi, vedere [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md).

## Ereditarietà del blocco

Quando si applica un blocco a un ambito padre, tutte le risorse figlio ereditano lo stesso blocco.

Se si applica più di un blocco a una risorsa, il blocco più restrittivo ha la precedenza. Ad esempio, se si applica **ReadOnly** a livello padre (ad esempio, il gruppo di risorse) e **CanNotDelete** a una risorsa all'interno del gruppo, il blocco più restrittivo (ReadOnly) dell'elemento padre ha la precedenza.

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

L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Lock-name indica il nome che si desidera assegnare al blocco. Per api-version, usare**2015-01-01**.

Nella richiesta includere un oggetto JSON che specifica le proprietà per il blocco.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Per lock-level, specificare**CanNotDelete** o **ReadOnly**.

Per altri esempi, vedere [API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creazione di un blocco con Azure PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

È possibile bloccare le risorse distribuite con Azure PowerShell tramite **New-AzuremRResourceLock**, come illustrato di seguito. Con PowerShell è possibile impostare solo **LockLevel** su **CanNotDelete**.

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

PowerShell di Azure fornisce altri comandi per la gestione dei blocchi, ad esempio **Set-AzuremRResourceLock** per aggiornare un blocco e **Remove-AzuremRResourceLock** per eliminarlo.

## Passaggi successivi

- Per altre informazioni sull'uso dei blocchi di risorse, vedere il post di blog relativo al [blocco delle risorse di Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx).
- Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md).
- Per modificare il gruppo di risorse in cui si trova una risorsa, vedere l'articolo relativo allo [spostamento delle risorse in un nuovo gruppo di risorse](resource-group-move-resources.md).

<!---HONumber=Oct15_HO3-->