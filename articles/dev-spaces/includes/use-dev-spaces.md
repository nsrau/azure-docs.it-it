---
title: File di inclusione
description: File di inclusione
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: a8f5adc8e6745fdbfd31d1a9cb9407672d61da07
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129303"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurare il cluster AKS per l'uso di Azure Dev Spaces

Aprire una finestra di comando e immettere il comando seguente dell'interfaccia della riga di comando di Azure, usando il gruppo di risorse contenente il cluster AKS e il nome del cluster AKS. Il comando configura il cluster con il supporto per gli spazi Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

