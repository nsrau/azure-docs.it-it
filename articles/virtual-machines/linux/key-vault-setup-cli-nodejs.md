---
title: Configurare l&quot;insieme di credenziali delle chiavi per macchine virtuali Linux con l&quot;interfaccia della riga di comando 1.0 di Azure | Documentazione Microsoft
description: Come configurare un insieme di credenziali delle chiavi da usare con una macchina virtuale di Azure Resource Manager con l&quot;interfaccia della riga di comando 1.0 di Azure.
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: fed612a354d45f34619f2a66bd40d78740c43ac7
ms.lasthandoff: 05/03/2017


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Configurare l'insieme di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager con l'interfaccia della riga di comando 1.0 di Azure
In Azure Resource Manager gli stack, i segreti e i certificati vengono modellati come risorse fornite dal provider di risorse dell'insieme di credenziali delle chiavi. Per altre informazioni sugli insiemi di credenziali delle chiavi di Azure, vedere [Informazioni sull'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-whatis.md) Per consentire l'uso dell'insieme di credenziali delle chiavi con le macchine virtuali di Azure Resource Manager, è necessario impostare su true la proprietà *EnabledForDeployment* nell'insieme di credenziali delle chiavi. È possibile farlo in vari tipi di client. Questo articolo descrive come configurare un insieme di credenziali delle chiavi da usare con le macchine virtuali di Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia avanzata per il modello di distribuzione di gestione delle risorse

## <a name="use-cli-10-to-set-up-key-vault"></a>Usare l'interfaccia della riga di comando 1.0 per impostare l'insieme di credenziali delle chiavi
Per creare un insieme di credenziali delle chiavi usando l'interfaccia della riga di comando (CLI), vedere l'articolo su come [gestire l'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Per l'interfaccia della riga di comando 1.0, prima di assegnare il criterio di distribuzione è necessario creare l'insieme di credenziali delle chiavi. È quindi possibile assegnare il criterio usando il comando seguente:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilizzare modelli per configurare l'insieme di credenziali delle chiavi
Se si usa un modello, è necessario impostare la proprietà `enabledForDeployment` su `true` per la risorsa dell'insieme di credenziali delle chiavi.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Per altre opzioni che è possibile configurare quando si crea un insieme di credenziali delle chiavi utilizzando i modelli, vedere l'articolo su come [creare un insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

