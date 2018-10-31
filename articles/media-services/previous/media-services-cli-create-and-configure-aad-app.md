---
title: Usare l'interfaccia della riga di comando di Azure per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra come usare l'interfaccia della riga di comando di Azure per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 33d3454e50cb6eeb0e48b52f01923c8a344620eb
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944125"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-azure-media-services-api"></a>Usare l'interfaccia della riga di comando di Azure per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure

Questo argomento illustra come usare l'interfaccia della riga di comando di Azure per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) per accedere alle risorse di Servizi multimediali di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Usare Azure Cloud Shell

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Avviare Cloud Shell dal riquadro di spostamento superiore del portale.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Per altre informazioni, vedere [Panoramica di Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Creare un'app Azure AD e configurare l'accesso all'account multimediale con l'interfaccia della riga di comando di Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Ad esempio: 

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

In questo esempio l'**ambito** è il percorso completo delle risorse per l'account dei servizi multimediali. Tuttavia, l'**ambito** può essere definito a qualsiasi livello.

Ad esempio, può essere definito a uno dei livelli seguenti:
 
* Il livello **sottoscrizione**.
* Il livello **gruppo di risorse**.
* Il livello **risorsa** (ad esempio, un account multimediale).

Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Vedere anche [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-portal-upload-files.md).
