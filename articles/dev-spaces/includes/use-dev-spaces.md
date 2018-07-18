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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823181"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurare il cluster AKS per l'uso di Azure Dev Spaces

Aprire una finestra di comando e immettere il comando seguente dell'interfaccia della riga di comando di Azure, usando il gruppo di risorse contenente il cluster AKS e il nome del cluster AKS. Il comando configura il cluster con il supporto per gli spazi Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

