---
title: Usare un modello per aggiungere una macchina virtuale Windows a Azure AD DS. Documenti Microsoft
description: Informazioni su come usare i modelli di Azure Resource Manager per aggiungere una macchina virtuale di Windows Server nuova o esistente a un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to use Azure Resource Manager templates to join a new or existing Windows Server VM to an Azure Active Directory Domain Services managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: c9b25fe7bc47e05972aebb194e9d94c1ea6dd247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298735"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure Active Directory usando un modello di Resource Manager

Per automatizzare la distribuzione e la configurazione di macchine virtuali di Azure, è possibile usare un modello di Resource Manager.To automate the deployment and configuration of Azure virtual machines (VMs), you can use a Resource Manager template. Questi modelli consentono di creare distribuzioni coerenti ogni volta. Le estensioni possono anche essere incluse nei modelli per configurare automaticamente una macchina virtuale come parte della distribuzione. Un'estensione utile aggiunge le macchine virtuali a un dominio, che può essere usato con i domini gestiti di Servizi di dominio Azure Active Directory (Azure AD DS).

Questo articolo illustra come creare e aggiungere una macchina virtuale di Windows Server a un dominio gestito di Azure AD DS usando i modelli di Resource Manager.This article shows you how to create and join a Windows Server VM to an Azure AD DS managed domain using Resource Manager templates. Si apprenderà anche come aggiungere una macchina virtuale windows Server esistente a un dominio di Azure AD DS.You also learn how to join an existing Windows Server VM to an Azure AD DS domain.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.A user account that's a part of the Azure AD DS managed domain.

## <a name="azure-resource-manager-template-overview"></a>Panoramica del modello di Azure Resource ManagerAzure Resource Manager template overview

I modelli di Resource Manager consentono di definire l'infrastruttura di Azure nel codice. Le risorse necessarie, le connessioni di rete o la configurazione delle macchine virtuali possono essere tutte definite in un modello. Questi modelli creano distribuzioni coerenti e riproducibili ogni volta e possono essere sottoposti a controllo delle versioni quando si apportano modifiche. Per altre informazioni, vedere [Panoramica][template-overview]dei modelli di Azure Resource Manager.For more information, see Azure Resource Manager templates overview .

Ogni risorsa viene definita in un modello usando JavaScript Object Notation (JSON). L'esempio JSON seguente usa il tipo di risorsa Microsoft.Compute/virtualMachines/extensions per installare l'estensione di aggiunta al dominio di Active Directory.The following JSON example uses the *Microsoft.Compute/virtualMachines/extensions* resource type to install the Active Directory domain aggiunta extension. Vengono utilizzati i parametri specificati in fase di distribuzione. Quando l'estensione viene distribuita, la macchina virtuale viene aggiunta al dominio gestito di Azure AD DS specificato.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Questa estensione di macchina virtuale può essere distribuita anche se non si crea una macchina virtuale nello stesso modello. Negli esempi di questo articolo vengono illustrati entrambi gli approcci seguenti:The examples in this article show both of the following approaches:

* [Creare una macchina virtuale Windows Server e aggiungere a un dominio gestitoCreate a Windows Server VM and join to a managed domain](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [aggiungere una VM Windows Server esistente a un dominio gestito](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Creare una macchina virtuale Windows Server e aggiungere a un dominio gestitoCreate a Windows Server VM and join to a managed domain

Se è necessaria una macchina virtuale di Windows Server, è possibile crearne e configurarne una usando un modello di Resource Manager.If you need a Windows Server VM, you can create and configure one using a Resource Manager template. Quando la macchina virtuale viene distribuita, viene installata un'estensione per aggiungere la macchina virtuale a un dominio gestito di Azure AD DS.When the VM is deployed, an extension is then installed to join the VM to an Azure AD DS managed domain. Se si dispone già di una macchina virtuale a cui si vuole aggiungere un dominio gestito di Azure AD DS, passare a [Aggiungere una macchina virtuale Windows Server esistente a un dominio gestito.](#join-an-existing-windows-server-vm-to-a-managed-domain)

Per creare una macchina virtuale Windows Server quindi aggiungerla a un dominio gestito di Azure AD DS, completare i passaggi seguenti:To create a Windows Server VM then join it to an Azure AD DS managed domain, complete the following steps:

1. Passare al [modello di avvio rapido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Selezionare l'opzione **Distribuisci in Azure**.
1. Nella pagina **Distribuzione personalizzata** immettere le informazioni seguenti per creare e aggiungere una macchina virtuale di Windows Server al dominio gestito di Servizi di dominio Active Directory di Azure:On the Custom deployment page, enter the following information to create and join a Windows Server VM to the Azure AD DS managed domain:

    | Impostazione                   | valore |
    |---------------------------|-------|
    | Subscription              | Selezionare la stessa sottoscrizione di Azure in cui è abilitato Azure Active Directory Domain Services. |
    | Resource group            | Scegliere il gruppo di risorse per la macchina virtuale. |
    | Location                  | Selezionare il percorso di per la macchina virtuale. |
    | Nome VNET esistente        | Nome della rete virtuale esistente a cui connettere la macchina virtuale, ad esempio *myVnet*. |
    | Nome subnet esistente      | Nome della subnet di rete virtuale esistente, ad esempio *Carichi di lavoro*. |
    | Prefisso etichetta DNS          | Immettere un nome DNS da utilizzare per la macchina virtuale, ad esempio *myvm*. |
    | Dimensioni macchina virtuale                   | Specificare una dimensione della macchina virtuale, ad esempio *Standard_DS2_v2*. |
    | Dominio da aggiungere            | Nome DNS del dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio *aaddscontoso.com*. |
    | Nome utente di dominio           | Account utente nel dominio gestito di Servizi di dominio Active Directory di Azure `contosoadmin@aaddscontoso.com`che deve essere usato per aggiungere la macchina virtuale al dominio gestito, ad esempio . Questo account deve far parte del dominio gestito di Servizi di dominio Active Directory di Azure.This account must be a part of the Azure AD DS managed domain. |
    | Password di dominio           | Password per l'account utente specificato nell'impostazione precedente. |
    | Percorso facoltativo dell'unità organizzativa          | Unità organizzativa personalizzata in cui aggiungere la macchina virtuale. Se non si specifica un valore per questo parametro, la macchina virtuale viene aggiunta all'unità organizzativa *Computer controller di dominio AAD* predefinita. |
    | Nome utente amministratore VM         | Specificare un account amministratore locale da creare nella macchina virtuale. |
    | Password amministratore macchina virtuale         | Specificare una password di amministratore locale per la macchina virtuale. Creare una password di amministratore locale complessa per la protezione dagli attacchi con forza bruta delle password. |

1. Rivedere i termini e le condizioni, quindi selezionare la casella **per accetto i termini e le condizioni sopra indicati**. Quando si è pronti, selezionare Acquista per creare e aggiungere la macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure.When ready, select **Purchase** to create and join the VM to the Azure AD DS managed domain.

> [!WARNING]
> **Gestire le password con cautela.**
> Il file dei parametri del modello richiede la password per un account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.The template parameter file requests the password for a user account that's a part of the Azure AD DS managed domain. Non immettere manualmente valori in questo file e renderlo accessibile nelle condivisioni file o in altre posizioni condivise.

Il completamento della distribuzione richiede alcuni minuti. Al termine, la macchina virtuale Windows viene creata e aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.When finished, the Windows VM is created and joined to the Azure AD DS managed domain. La macchina virtuale può essere gestita o eseguito l'accesso usando gli account di dominio.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>aggiungere una VM Windows Server esistente a un dominio gestito

Se si dispone di una macchina virtuale esistente o di un gruppo di macchine virtuali a cui si vuole aggiungere un dominio gestito di Azure AD DS, è possibile usare un modello di Resource Manager per distribuire solo l'estensione della macchina virtuale.

Per aggiungere una macchina virtuale windows Server esistente a un dominio gestito di Azure AD DS, completare i passaggi seguenti:To join an existing Windows Server VM to an Azure AD DS managed domain, complete the following steps:

1. Passare al [modello di avvio rapido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Selezionare l'opzione **Distribuisci in Azure**.
1. Nella pagina **Distribuzione personalizzata** immettere le informazioni seguenti per aggiungere la macchina virtuale al dominio gestito di Azure AD DS:On the Custom deployment page, enter the following information to join the VM to the Azure AD DS managed domain:

    | Impostazione                   | valore |
    |---------------------------|-------|
    | Subscription              | Selezionare la stessa sottoscrizione di Azure in cui è abilitato Azure Active Directory Domain Services. |
    | Resource group            | Scegliere il gruppo di risorse con la macchina virtuale esistente. |
    | Location                  | Selezionare il percorso della macchina virtuale esistente. |
    | Elenco macchine virtuali                   | Immettere l'elenco delimitato da virgole delle macchine virtuali esistenti da aggiungere al dominio gestito di Azure AD DS, ad esempio *myVM1, myVM2*. |
    | Nome utente aggiunta a dominio     | Account utente nel dominio gestito di Servizi di dominio Active Directory di Azure `contosoadmin@aaddscontoso.com`che deve essere usato per aggiungere la macchina virtuale al dominio gestito, ad esempio . Questo account deve far parte del dominio gestito di Servizi di dominio Active Directory di Azure.This account must be a part of the Azure AD DS managed domain. |
    | Password utente di aggiunta al dominio | Password per l'account utente specificato nell'impostazione precedente. |
    | Percorso facoltativo dell'unità organizzativa          | Unità organizzativa personalizzata in cui aggiungere la macchina virtuale. Se non si specifica un valore per questo parametro, la macchina virtuale viene aggiunta all'unità organizzativa *Computer controller di dominio AAD* predefinita. |

1. Rivedere i termini e le condizioni, quindi selezionare la casella **per accetto i termini e le condizioni sopra indicati**. Quando si è pronti, selezionare Acquista per aggiungere la macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure.When ready, select **Purchase** to join the VM to the Azure AD DS managed domain.

> [!WARNING]
> **Gestire le password con cautela.**
> Il file dei parametri del modello richiede la password per un account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.The template parameter file requests the password for a user account that's a part of the Azure AD DS managed domain. Non immettere manualmente valori in questo file e renderlo accessibile nelle condivisioni file o in altre posizioni condivise.

Il completamento della distribuzione richiede alcuni minuti. Al termine, le macchine virtuali Windows specificate vengono aggiunte al dominio gestito di Servizi di dominio Active Directory di Azure e possono essere gestite o con cui hanno effettuato l'accesso tramite account di dominio.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato il portale di Azure per configurare e distribuire le risorse usando i modelli. È anche possibile distribuire risorse con i modelli di Resource Manager usando [Azure PowerShell][deploy-powershell] o l'interfaccia della riga di comando di Azure.You can also deploy resources with Resource Manager templates using Azure PowerShell or the [Azure CLI.][deploy-cli]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
