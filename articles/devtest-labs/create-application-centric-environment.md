---
title: Creare un ambiente incentrato sulle applicazioni-Azure
description: Questo articolo illustra come creare un ambiente incentrato sulle applicazioni con Cloud Shell Colonia e Microsoft Azure.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918259"
---
# <a name="create-an-application-centric-environment"></a>Creare un ambiente incentrato sulle applicazioni

[CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) è una piattaforma SaaS per la distribuzione di automazione dell'infrastruttura su larga scala per ambienti complessi incentrati sulle applicazioni su tecnologie cloud, tra cui Azure e Kubernetes. CloudShell Colony si integra Azure DevTest Labs per aiutare i team di sviluppo a distribuire applicazioni complesse in tutto il flusso di valori, fino alla produzione.

Questo articolo illustra come creare un ambiente incentrato sulle applicazioni con CloudShell colone e Microsoft Azure.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Configurare l'ambiente con CloudShell Colony e Microsoft Azure

1. Iscriversi per ottenere una versione di valutazione gratuita di [Colonia](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Iscriversi per ottenere una versione di valutazione gratuita":::    
1. Collegare l'account Azure ([vedere i passaggi qui](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Benvenuti in Colonia":::     
1. Invitare gli utenti nello spazio.
1. Creare il primo progetto usando un file YAML ([visualizzare i passaggi qui](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Collegare il repository del progetto in GitHub o BitBucket a Colonia.
    1. Usare un progetto di esempio di Colonia come base e modificarlo in base alle esigenze.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Test di stress":::    
    1. Pubblicare il progetto per consentire ad altri utenti di usare.
1. Avviare l'ambiente dell'applicazione in una sandbox usando Colonia.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Avviare l'ambiente dell'applicazione in una sandbox usando Colonia":::    

> [!NOTE]
> È anche possibile integrare il progetto come parte di un flusso di lavoro di integrazione continua/recapito continuo in Azure DevOps ([vedere i passaggi qui](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Connettersi alla pipeline di Azure DevOps":::    

## <a name="next-steps"></a>Passaggi successivi

[Richiedi una demo di Colonia](https://info.quali.com/cloudshell-colony-demo-request)
