---
title: Esempi di modelli di Azure Resource Manager
description: Trovare esempi di modelli di Azure Resource Manager per distribuire le istanze del contenitore di Azure in configurazioni diverseFind Azure Resource Manager template samples to deploy Azure Container Instances in different configurations
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981657"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelli di Azure Resource Manager per Istanze di Azure Container

I seguenti modelli di esempio distribuiscono istanze di contenitore in varie configurazioni.

Per le opzioni di distribuzione, vedere la sezione [Distribuzione](#deployment). Se si vogliono creare modelli personalizzati, il [riferimento ai modelli di Gestione risorse][ref] per Istanze di Azure Container illustra in dettaglio il formato dei modelli e le proprietà disponibili.

## <a name="sample-templates"></a>Modelli di esempio

| | |
|-|-|
| **Applicazioni** ||
| [Wordpress][app-wp] | Crea un sito Web WordPress e il relativo database MySQL in un gruppo di contenitori. Il contenuto del sito WordPress e il database MySQL sono persistenti in una condivisione di File di Azure. Crea anche un gateway applicazione per esporre l'accesso alla rete pubblica a WordPress. |
| [MS NAV con SQL Server e IIS][app-nav] | Distribuisce un singolo contenitore Windows con un ambiente Dynamics NAV/Dynamics 365 Business Central completo e indipendente. |
| **Volumi** ||
| [emptyDir][vol-emptydir] | Distribuisce due contenitori Linux che condividono un volume emptyDir. |
| [gitRepo][vol-gitrepo] | Distribuisce un contenitore Linux che clona un repository GitHub e lo monta come volume. |
| [Segreto][vol-secret] | Distribuisce un contenitore Linux con un certificato PFX montato come volume segreto. |
| **Rete** ||
| [Contenitore con esposizione UDP][net-udp] | Distribuisce un contenitore Windows o Linux che espone una porta UDP. |
| [Contenitore Linux con IP pubblico][net-publicip] | Distribuisce un singolo contenitore Linux accessibile attraverso un indirizzo IP pubblico. |
| [Distribuire un gruppo di contenitori con una rete virtuale (anteprima)Deploy a container group with a virtual network (preview)][net-vnet] | Distribuisce una nuova rete virtuale, subnet, profilo di rete e gruppo di contenitori. |
| **Risorse di Azure** ||
| [Creare account di archiviazione di Azure e condivisione di File][az-files] | Usa l'interfaccia della riga di comando di Azure in un'istanza di contenitore per creare un account di archiviazione e una condivisione di File di Azure.

## <a name="deployment"></a>Distribuzione

Sono disponibili diverse opzioni per la distribuzione delle risorse con modelli di Gestione risorse:

[Interfaccia della riga di comando di AzureAzure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portale di Azure][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
