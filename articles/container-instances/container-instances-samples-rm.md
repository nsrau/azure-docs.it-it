---
title: Esempi di modelli di Azure Resource Manager
description: Trovare Azure Resource Manager esempi di modelli per distribuire istanze di contenitore di Azure in configurazioni diverse
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: ddea823525002662363d61a795f34d6094c897d0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533436"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelli di Azure Resource Manager per Istanze di Azure Container

I seguenti modelli di esempio distribuiscono istanze di contenitore in varie configurazioni.

Per le opzioni di distribuzione, vedere la sezione [Distribuzione](#deployment). Se si vuole creare modelli personalizzati, le istanze di contenitore di Azure Gestione risorse il formato del modello dettagli di [riferimento del modello][ref] e le proprietà disponibili.

## <a name="sample-templates"></a>Modelli di esempio

| | |
|-|-|
| **Applicazioni** ||
| [WordPress][app-wp] | Consente di creare un sito web WordPress e il relativo database MySQL in un gruppo di contenitori. Il contenuto del sito WordPress e il database MySQL sono persistenti in una condivisione di File di Azure. Crea anche un gateway applicazione per esporre l'accesso alla rete pubblica a WordPress. |
| [MS NAV con SQL Server e IIS][app-nav] | Distribuisce un singolo contenitore Windows con un ambiente Dynamics NAV/Dynamics 365 Business Central completo e indipendente. |
| **Volumi** ||
| [emptyDir][vol-emptydir] | Distribuisce due contenitori Linux che condividono un volume emptyDir. |
| [gitRepo][vol-gitrepo] | Distribuisce un contenitore Linux che clona un repository GitHub e lo monta come volume. |
| [secret][vol-secret] | Distribuisce un contenitore Linux con un certificato PFX montato come volume segreto. |
| **Rete** ||
| [Contenitore con esposizione UDP][net-udp] | Distribuisce un contenitore Windows o Linux che espone una porta UDP. |
| [Contenitore Linux con IP pubblico][net-publicip] | Distribuisce un singolo contenitore Linux accessibile attraverso un indirizzo IP pubblico. |
| [Distribuire un gruppo di contenitori con una rete virtuale (anteprima)][net-vnet] | Distribuisce una nuova rete virtuale, una subnet, un profilo di rete e un gruppo di contenitori. |
| **Risorse di Azure** ||
| [Creare un account e una condivisione file di archiviazione di Azure][az-files] | Usa l'interfaccia della riga di comando di Azure in un'istanza di contenitore per creare un account di archiviazione e una condivisione di File di Azure.

## <a name="deployment"></a>Distribuzione

Sono disponibili diverse opzioni per la distribuzione delle risorse con modelli di Gestione risorse:

[interfaccia della riga di comando di Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure portal][deploy-portal]

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
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
