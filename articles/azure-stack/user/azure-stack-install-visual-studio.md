---
title: Installazione di Visual Studio e connettersi a Azure Stack | Documenti Microsoft
description: Informazioni sui passaggi necessari per installare Visual Studio e connettersi a Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installazione di Visual Studio e connettersi a Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare Visual Studio per creare e distribuire Azure Resource Manager [modelli](azure-stack-arm-templates.md) nello Stack di Azure. È possibile utilizzare i passaggi descritti in questo articolo per l'installazione di Visual Studio da [Kit di sviluppo di Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows se si è connessi tramite [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Questi passaggi in questo articolo sono per una nuova installazione di Visual Studio 2015 Community Edition. Ulteriori informazioni, vedere [coesistenza](https://msdn.microsoft.com/library/ms246609.aspx) con altre versioni di Visual Studio.

## <a name="install-visual-studio"></a>Installazione di Visual Studio

1. Scaricare ed eseguire il [installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
2. Cercare **Visual Studio Community 2015 con Microsoft Azure SDK - 2.9.6**, selezionare **Add**, quindi selezionare **installare**.

    ![Acquisizione schermo di passaggi di installazione di WebPI](./media/azure-stack-install-visual-studio/image1.png)

3. Disinstallare il **Microsoft Azure PowerShell** che viene installato come parte di Azure SDK.

    ![Cattura di schermata dell'interfaccia di programmi di installazione per Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png)

4. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)

5. Dopo il completamento dell'installazione, riavviare il sistema operativo.

## <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

1. Avviare Visual Studio.

2. Dal **vista** dal menu **Cloud Explorer**.

3. Selezionare il nuovo riquadro **Aggiungi Account** e accedere con le credenziali di Azure Active Directory.
    ![Acquisizione schermo Cloud Explorer dopo l'accesso in e connesso a Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

Una volta effettuato l'accesso, è possibile [distribuire modelli](azure-stack-deploy-template-visual-studio.md) o cercare i tipi di risorse disponibili e i gruppi di risorse per creare modelli personalizzati.

## <a name="next-steps"></a>Passaggi successivi

* [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)
