---
title: Come condividere Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197658"
---
# <a name="share-azure-dev-spaces"></a>Condividere Azure Dev Spaces

Con Azure Dev Spaces è possibile condividere lo spazio di sviluppo con altri utenti del team. Ogni sviluppatore può lavorare nel proprio spazio senza il timore di interferire con gli altri. Inoltre, lavorare insieme in uno spazio consente di testare il codice end-to-end senza dover creare oggetti fittizi o simulare le dipendenze. Per altre informazioni, vedere la guida allo [sviluppo in team](../get-started-nodejs.md#learn-about-team-development).

Per impostare un ambiente per più sviluppatori:
1. Creare uno spazio di sviluppo in Azure. Scegliere [.NET Core e Visual Studio Code](../get-started-netcore.md), [.NET Core e Visual Studio](../get-started-netcore-visualstudio.md) o [Node.js e Visual Studio Code](../get-started-nodejs.md). È necessario poter accedere come proprietario o collaboratore alla sottoscrizione di Azure selezionata.
1. Configurare il **gruppo di risorse** di Azure Dev Space per [concedere l'accesso come collaboratore](/azure/active-directory/role-based-access-control-configure) a ogni membro del team. È possibile controllare il gruppo di risorse di un ambiente eseguendo questo comando: `azds resource list`
1. Chiedere ai membri del team di **selezionare l'ambiente** per consentire lo sviluppo all'interno dello stesso.
     * **Riga di comando o Visual Studio Code**: per visualizzare gli spazi Azure Dev Spaces a cui si ha accesso: `azds resource list`. Per selezionare un ambiente: `azds resource select`.
     * **IDE di Visual Studio**: aprire un progetto in Visual Studio, selezionare **Azure Dev Spaces** dall'elenco a discesa delle impostazioni di avvio. Selezionare un ambiente di sviluppo esistente nella finestra di dialogo visualizzata.

![Elenco a discesa delle impostazioni di avvio di Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)