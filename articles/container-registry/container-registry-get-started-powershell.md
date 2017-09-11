---
title: Archivi di registri contenitori di Azure | Documentazione Microsoft
description: Come usare gli archivi di registri contenitori di Azure per le immagini Docker
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Creare un registro contenitori Docker privati con Azure PowerShell
Usare i comandi in [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) per creare un registro contenitori e gestirne le impostazioni dal computer Windows. È anche possibile creare e gestire registri di contenitori usando il [portale di Azure](container-registry-get-started-portal.md), l'[interfaccia della riga di comando di Azure](https://go.microsoft.com/fwlink/p/?linkid=834376) oppure a livello di codice con l'[API REST](container-registry-get-started-azure-cli.md) del servizio Registro contenitori.


* Per informazioni di base e concetti, vedere la [panoramica](container-registry-intro.md)
* Per un elenco completo dei cmdlet supportati, vedere [AzureRM.ContainerRegistry](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/).


## <a name="prerequisites"></a>Prerequisiti
* **Azure PowerShell**: per installare e iniziare a usare Azure PowerShell, vedere le [istruzioni di installazione](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). Accedere alla sottoscrizione di Azure usando `Login-AzureRMAccount`. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep) (Introduzione ad Azure PowerShell).
* **Gruppo di risorse**: creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups) prima di creare un registro di contenitori o usare un gruppo di risorse esistente. Verificare che il gruppo di risorse si trovi in una posizione in cui il servizio Container Registry è [disponibile](https://azure.microsoft.com/regions/services/). Per creare un gruppo di risorse con Azure PowerShell, vedere la [documentazione di riferimento su PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group).
* **Account di archiviazione** (facoltativo): creare un [account di archiviazione](../storage/common/storage-introduction.md) di Azure standard per eseguire il backup del registro di contenitori nella stessa posizione. Se non si specifica un account di archiviazione durante la creazione di un registro con `New-AzureRMContainerRegistry`, l'account viene creato automaticamente. Per creare un account di archiviazione con PowerShell, vedere la [documentazione di riferimento su PowerShell](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount). Archiviazione Premium non è attualmente supportata.
* **Entità servizio** (facoltativo): quando si crea un registro con PowerShell, per impostazione predefinita il registro non è configurato per l'accesso. A seconda delle esigenze, è possibile assegnare a un registro un'entità servizio Azure Active Directory esistente oppure crearne e assegnarne una nuova. In alternativa è possibile abilitare l'account di utente amministratore del registro. Vedere le sezioni più avanti in questo articolo. Per altre informazioni sull'accesso al registro, vedere [Authenticate with the container registry](container-registry-authentication.md) (Eseguire l'autenticazione al registro di contenitori).

## <a name="create-a-container-registry"></a>Creare un registro di contenitori
Eseguire il comando `New-AzureRMContainerRegistry` per creare un registro di contenitori.

> [!TIP]
> Quando si crea un registro, specificare un nome di dominio univoco globale di primo livello, contenente solo lettere e numeri. Il nome del registro negli esempi è `MyRegistry`, ma è necessario sostituirlo con un nome univoco personalizzato.
>
>

Il comando seguente usa i parametri minimi per creare il registro di contenitori `MyRegistry` nel gruppo di risorse `MyResourceGroup` nell'area South Central US:

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` è facoltativo. Se non è specificato, verrà creato un account di archiviazione con un nome costituito dal nome registro e un timestamp nel gruppo di risorse specificato.

## <a name="assign-a-service-principal"></a>Assegnare un'entità servizio
Usare i comandi di PowerShell per assegnare a un registro un'[entità servizio](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure Active Directory. All'entità servizio in questi esempi è assegnato il ruolo di proprietario, ma è possibile assegnare [altri ruoli](../active-directory/role-based-access-control-configure.md).

### <a name="create-a-service-principal"></a>Creare un'entità servizio
Con il comando seguente viene creata una nuova entità servizio. Specificare una password complessa con il parametro `-Password`.

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>Assegnare un'entità servizio nuova o esistente
È possibile assegnare un'entità servizio nuova o esistente a un registro. Per assegnare a tale entità il ruolo di proprietario per l'accesso al registro, eseguire un comando simile all'esempio seguente:

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>Accedere al registro con l'entità servizio
Dopo l'assegnazione dell'entità servizio al registro, è possibile accedere con il comando seguente:

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>Gestire le credenziali di amministratore
Per ogni registro di contenitori viene creato automaticamente un account amministratore che è disabilitato per impostazione predefinita. Gli esempi seguenti mostrano i comandi di PowerShell che consentono di gestire le credenziali di amministratore per il registro contenitori.

### <a name="obtain-admin-user-credentials"></a>Ottenere le credenziali di utente amministratore
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Abilitare l'utente amministratore per un registro esistente
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Disabilitare l'utente amministratore per un registro esistente
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Docker](container-registry-get-started-docker-cli.md)

