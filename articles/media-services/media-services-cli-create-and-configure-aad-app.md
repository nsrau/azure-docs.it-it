---
title: Usare l'interfaccia della riga di comando 2.0 per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra come usare l'interfaccia della riga di comando 2.0 per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 01a2bb6d99776feec936315bc882c3097ce832d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Usare l'interfaccia della riga di comando 2.0 per creare un'app AAD e configurarla per l'accesso all'API Servizi multimediali di Azure

Questo argomento illustra come usare l'interfaccia della riga di comando 2.0 per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) per accedere alle risorse di Servizi multimediali di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Usare Azure Cloud Shell

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Avviare Cloud Shell dal riquadro di spostamento superiore del portale.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Per altre informazioni, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Creare un'app Azure AD e configurare l'accesso all'account multimediale con l'interfaccia della riga di comando 2.0
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create -- assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

ad esempio:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

In questo esempio l'**ambito** è il percorso completo delle risorse per l'account dei servizi multimediali. Tuttavia, l'**ambito** può essere definito a qualsiasi livello.

Ad esempio, può essere definito a uno dei livelli seguenti:
 
* Il livello **sottoscrizione**.
* Il livello **gruppo di risorse**.
* Il livello **risorsa** (ad esempio, un account multimediale).

Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Vedere anche [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md). 

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-portal-upload-files.md).
