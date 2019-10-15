---
title: Avvio rapido - Eseguire i playbook di Ansible tramite Bash in Azure Cloud Shell
description: In questo argomento di avvio rapido viene illustrato come eseguire diverse attività di Ansible con Bash in Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 10f71d5bcb7134ca0560e4fac617e6835cb3d48c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241528"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Guida introduttiva: Esegui i playbook di Ansible tramite Bash in Azure Cloud Shell

Azure Cloud Shell è una shell interattiva accessibile dal browser per la gestione delle risorse di Azure. Cloud Shell consente di usare una riga di comando di Bash o di PowerShell. In questo articolo si usa Bash in Azure Cloud Shell per eseguire un playbook di Ansible.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurare Azure Cloud Shell**: se non si ha familiarità con Azure Cloud Shell, vedere [Avvio rapido per Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configurazione automatica delle credenziali

Quando si è connessi a Cloud Shell per gestire l'infrastruttura, Ansible viene autenticato in Azure senza la necessità di configurazioni aggiuntive. 

Quando si usano più sottoscrizioni, specificare la sottoscrizione usata da Ansible esportando la variabile di ambiente `AZURE_SUBSCRIPTION_ID`. 

Per visualizzare un elenco di tutte le sottoscrizioni di Azure, eseguire il comando seguente:

```azurecli-interactive
az account list
```

Usando l'ID della sottoscrizione di Azure, impostare `AZURE_SUBSCRIPTION_ID` come segue:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificare la configurazione
Per verificare la configurazione, usare Ansible per creare un gruppo di risorse di Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Guida introduttiva: Configurare una macchina virtuale in Azure usando Ansible](/azure/virtual-machines/linux/ansible-create-vm)