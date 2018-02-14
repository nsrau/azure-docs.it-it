---
title: Installazione di Visual Studio e connettersi a Azure Stack | Documenti Microsoft
description: Informazioni sui passaggi necessari per installare Visual Studio e connettersi a Azure Stack
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: f85584b02f6b2f7436f711e794bb8fcad00ced4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installazione di Visual Studio e connettersi a Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare Visual Studio per creare e distribuire Azure Resource Manager [modelli](azure-stack-arm-templates.md) nello Stack di Azure. È possibile utilizzare i passaggi descritti in questo articolo per l'installazione di Visual Studio da [Kit di sviluppo di Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows se si è connessi tramite [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Questi passaggi eseguono una nuova installazione di Visual Studio 2015 Community Edition. Altre informazioni sui [coesistenza](https://msdn.microsoft.com/library/ms246609.aspx) tra le altre versioni di Visual Studio.

## <a name="install-visual-studio"></a>Installazione di Visual Studio
1. Scaricare ed eseguire il [installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Cercare **Visual Studio Community 2015 con Microsoft Azure SDK - 2.9.6**, fare clic su **Aggiungi**, e **installare**.

    ![Schermata di WebPI passaggi di installazione](./media/azure-stack-install-visual-studio/image1.png) 

3. Disinstallare il **Microsoft Azure PowerShell** che viene installato come parte di Azure SDK.

    ![Schermata dell'interfaccia di programmi di installazione per Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png) 

4. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)

5. Dopo il completamento dell'installazione, riavviare il sistema operativo.

## <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

1. Avviare Visual Studio.

2. Dal **vista** dal menu **Cloud Explorer**.

3. Selezionare il nuovo riquadro **Aggiungi Account** e accedere con le credenziali di Azure Active Directory.  
    ![Schermata di Cloud Explorer una volta effettuato l'accesso e connesso allo Stack di Azure](./media/azure-stack-install-visual-studio/image6.png)

Una volta effettuato l'accesso, è possibile [distribuire modelli](azure-stack-deploy-template-visual-studio.md) o cercare i tipi di risorse disponibili e i gruppi di risorse per creare modelli personalizzati.  

## <a name="next-steps"></a>Fasi successive

 - [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
