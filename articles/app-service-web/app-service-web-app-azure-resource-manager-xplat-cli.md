---
title: Strumenti da riga di comando multipiattaforma basati su Azure Resource Manager per app Web di Azure | Documentazione Microsoft
description: Informazioni su come usare i nuovi strumenti da riga di comando multipiattaforma basati su Azure Resource Manager per la gestione delle app Web di Azure.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 11a9e0a67b71e7ce4adf9713c825511ade4ff7d7
ms.openlocfilehash: 36a6863990e3c4cb87bc4f3a1207ab552c836c20


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Uso dell'interfaccia della riga di comando multipiattaforma basata su Azure Resource Manager per app Web di Azure
> [!div class="op_single_selector"]
> * [Interfaccia della riga di comando di Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Nella versione 0.10.5 degli strumenti da riga di comando multipiattaforma di Microsoft Azure sono stati aggiunti nuovi comandi che offrono all'utente la possibilità di usare comandi di PowerShell basati su Azure Resource Manager per gestire app Web.

Per altre informazioni sulla gestione dei gruppi di risorse, vedere [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse](../xplat-cli-azure-resource-manager.md). 

## <a name="managing-app-service-plans"></a>Gestione di piani di servizio app
### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app
Per creare un piano di servizio app, usare il comando **azure appserviceplan create**.

Di seguito sono riportate le descrizioni dei diversi parametri.

* **--resource-group**: gruppo di risorse che include il piano di servizio app appena creato.
* **--name**: nome del piano di servizio app.
* **--location**: località del piano di servizio app.
* **--tier**: SKU del piano tariffario scelto. Le opzioni disponibili sono: F1 (Gratuito), D1 (Condiviso), B1 (Basic Small), B2 (Basic Medium), B3 (Basic Large), S1 (Standard Small), S2 (Standard Medium), S3 (Standard Large), P1 (Premium Small), P2 (Premium Medium) e P3 (Premium Large).
* **--instances**: numero di ruoli di lavoro nel piano di servizio app. Il valore predefinito è 1.

Esempio di uso del cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Creare un nuovo piano di servizio app

Usare lo stesso comando **azure appserviceplan create**, con il parametro aggiuntivo **--islinux true**. Tenere presente le restrizioni e le aree descritte in [Introduzione al servizio app in Linux](app-service-linux-intro.md).

### <a name="list-existing-app-service-plans"></a>Visualizzare un elenco dei piani di servizio app esistenti
Per visualizzare un elenco dei piani di servizio app esistenti, usare il comando **azure appserviceplan list**.

Per visualizzare un elenco di tutti i piani di servizio app di uno specifico gruppo di risorse, usare:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Per ottenere un piano di servizio app specifico, usare il comando **azure appserviceplan show**:

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurare un piano di servizio app esistente
Per modificare le impostazioni di un piano di servizio app esistente, usare il comando **azure appserviceplan config**. È possibile modificare lo SKU e il numero di ruoli di lavoro. 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Ridimensionamento di un piano di servizio app
Per ridimensionare un piano di servizio app esistente, usare:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Modifica dello SKU di un piano di servizio app
Per modificare lo SKU di un piano di servizio app esistente, usare:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Eliminare un piano di servizio app esistente
Per eliminare un piano di servizio app esistente, è prima di tutto necessario spostare o eliminare tutte le app Web assegnate. Usare quindi il comando **azure webapp delete** per eliminare il piano di servizio app.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gestione di app Web del servizio app
### <a name="create-a-web-app"></a>Creare un'app Web
Per creare un'app web, usare il comando **azure webapp create**.

Di seguito sono riportate le descrizioni dei diversi parametri.

* **--name**: nome dell'app Web.
* **--plan**: nome del piano di servizio usato per l'hosting dell'app Web.
* **--resource-group**: gruppo di risorse che ospita il piano di servizio app.
* **--location**: località dell'app Web.

Esempio di uso del cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Eliminare un'app Web esistente
Per eliminare un'app Web esistente, è possibile usare il comando **azure webapp delete**. È necessario specificare il nome dell'app Web e il nome del gruppo di risorse.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Visualizzare un elenco delle app Web esistenti
Per visualizzare un elenco delle app Web esistenti, usare il comando **azure webapp list**.

Per visualizzare un elenco di tutte le app Web di uno specifico gruppo di risorse, usare:

    azure webapp list --resource-group ContosoAzureResourceGroup

Per ottenere un'app Web specifica, usare il comando **azure webapp show**.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurare un'app Web esistente
Per modificare le impostazioni e le configurazioni di un'app Web esistente, usare il comando **azure webapp config set**.

Esempio (1): modificare la versione php di un'app Web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Esempio (2): aggiungere o modificare le impostazioni dell'app

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Per conoscere le altre configurazioni modificabili, usare il comando **azure webapp config set -h**.

### <a name="change-the-state-of-an-existing-web-app"></a>Modificare lo stato di un'app Web esistente
#### <a name="restart-a-web-app"></a>Riavviare un'app Web
Per riavviare un'app Web, è necessario specificare il nome e il gruppo di risorse dell'app Web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Arrestare un'app Web
Per arrestare un'app Web, è necessario specificare il nome e il gruppo di risorse dell'app Web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Avviare un'app Web
Per avviare un'app Web, è necessario specificare il nome e il gruppo di risorse dell'app Web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gestire i profili di pubblicazione delle app Web
Ogni app Web ha un profilo di pubblicazione che può essere usato per pubblicare le app.

#### <a name="get-publishing-profile"></a>Recuperare il profilo di pubblicazione
Per recuperare il profilo di pubblicazione di un'app Web, usare:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Questo comando restituisce il nome utente e la password del profilo di pubblicazione nella riga di comando.

### <a name="manage-web-app-hostnames"></a>Gestire i nomi host dell'app Web
Per gestire le associazioni nome host per l'app Web, usare il comando **azure webapp config hostnames**.  

#### <a name="list-hostname-bindings"></a>Elencare le associazioni nome host
Per ottenere le associazioni nome host correnti per un'app Web, usare:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Aggiungere associazioni nome host
Per aggiungere associazioni nome host a un'app Web, usare:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Eliminare associazioni nome host
Per eliminare associazioni nome host, usare:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul supporto dell'interfaccia della riga di comando di Azure Resource Manager, vedere [Usare l'interfaccia della riga di comando di Azure per gestire risorse e gruppi di risorse](../xplat-cli-azure-resource-manager.md).
* Per altre informazioni sulla gestione del servizio app tramite PowerShell, vedere [Uso di comandi di PowerShell basati su Azure Resource Manager per la gestione di app Web di Azure](app-service-web-app-azure-resource-manager-powershell.md).
* Per informazioni sul servizio app di Azure in Linux, vedere [Introduzione al servizio app in Linux](app-service-linux-intro.md).



<!--HONumber=Nov16_HO3-->


