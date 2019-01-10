---
title: Esecuzione di Ansible con Bash in Azure Cloud Shell
description: Informazioni su come eseguire diverse attività di Ansible con Bash in Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 6bfac47e4afa41b4c75a8d33b4eea1ff5103296d
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050898"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Esecuzione di Ansible con Bash in Azure Cloud Shell

Questa esercitazione descrive come usare Bash in Cloud Shell per configurare una sottoscrizione di Azure come area di lavoro di Ansible. 

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: se non si possiede una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Configurare Azure Cloud Shell**: se non si ha familiarità con Azure Cloud Shell, l'articolo [Guida introduttiva a Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) descrive come avviare e configurare Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configurazione automatica delle credenziali

Quando si è connessi a Cloud Shell per gestire l'infrastruttura, Ansible viene autenticato in Azure senza la necessità di configurazioni aggiuntive. Se si hanno più sottoscrizioni, è possibile scegliere quale sottoscrizione Ansible dovrebbe funzionare esportando la `AZURE_SUBSCRIPTION_ID` variabile di ambiente. Per visualizzare un elenco di tutte le sottoscrizioni di Azure, eseguire il comando seguente:

```azurecli-interactive
az account list
```

Usando l’**id** della sottoscrizione con cui si desidera lavorare, impostare **AZURE_SUBSCRIPTION_ID** come indicato di seguito:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificare la configurazione
Per verificare la configurazione, usare Ansible per creare un gruppo di risorse.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Creare una macchina virtuale di base in Azure con Ansible](/azure/virtual-machines/linux/ansible-create-vm)