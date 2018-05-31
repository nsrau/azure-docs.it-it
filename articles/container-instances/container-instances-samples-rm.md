---
title: Esempi di modelli di Azure Resource Manager - Istanze di contenitore di Azure
description: Esempi di modelli di Azure Resource Manager per le istanze di contenitore di Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259619"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelli di Azure Resource Manager per le istanze di contenitore di Azure

I seguenti modelli di esempio distribuiscono istanze di contenitore in varie configurazioni.

Per le opzioni di distribuzione, vedere la sezione [Distribuzione](#deployment). Se si vogliono creare modelli personalizzati, il [riferimento ai modelli di Gestione risorse][ref] per le istanze di contenitore di Azure illustra in dettaglio il formato dei modelli e le proprietà disponibili.

## <a name="sample-templates"></a>Modelli di esempio

| | |
|-|-|
| **Applicazioni** ||
| [Wordpress][app-wp] | Crea un sito Web WordPress e il relativo database MySQL in un'istanza di contenitore. Il contenuto del sito WordPress e il database MySQL sono persistenti in una condivisione di File di Azure. |
| [MS NAV con SQL Server e IIS][app-nav] | Distribuisce un singolo contenitore Windows con un ambiente Dynamics NAV/Dynamics 365 Business Central completo e indipendente. |
| **Volumi** ||
| [emptyDir][vol-emptydir] | Distribuisce due contenitori Linux che condividono un volume emptyDir. |
| [gitRepo][vol-gitrepo] | Distribuisce un contenitore Linux che clona un repository GitHub e lo monta come volume. |
| [secret][vol-secret] | Distribuisce un contenitore Linux con un certificato PFX montato come volume segreto. |
| **Rete** ||
| [Contenitore con esposizione UDP][net-udp] | Distribuisce un contenitore Windows o Linux che espone una porta UDP. |
| [Contenitore Linux con IP pubblico][net-publicip] | Distribuisce un singolo contenitore Linux accessibile attraverso un indirizzo IP pubblico. |
| **Risorse di Azure** ||
| [Creare account di archiviazione di Azure e condivisione di File][az-files] | Usa l'interfaccia della riga di comando di Azure in un'istanza di contenitore per creare un account di archiviazione e una condivisione di File di Azure.

## <a name="deployment"></a>Distribuzione

Sono disponibili diverse opzioni per la distribuzione delle risorse con modelli di Gestione risorse:

[Interfaccia della riga di comando di Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portale di Azure][deploy-portal]

[API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
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