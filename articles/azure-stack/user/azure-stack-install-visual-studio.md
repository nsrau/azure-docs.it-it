---
title: Installare Visual Studio e connettersi ad Azure Stack | Microsoft Docs
description: Informazioni sui passaggi necessari per installare Visual Studio e connettersi ad Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: da17d114c1ffb920fbaae85a6cdcbc35a66631a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257995"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installare Visual Studio e connettersi ad Azure Stack

*Si applica a Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare Visual Studio per scrivere e distribuire Azure Resource Manager [modelli](azure-stack-arm-templates.md) ad Azure Stack. I passaggi descritti in questo articolo viene descritto come installare Visual Studio sul [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o in un computer esterno se si prevede di usare Azure Stack tramite [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Installazione di Visual Studio

1. Scaricare ed eseguire la [instalace Webové Platformy](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Aprire il **installazione guidata piattaforma Web Microsoft**.

3. Cercare **Visual Studio Community 2015 con Microsoft Azure SDK - 2.9.6**. Fare clic su **aggiungere**, e **installare**.

4. Disinstallare il **Microsoft Azure PowerShell** che viene installato come parte di Azure SDK.

    ![Schermata di WebPI passaggi di installazione](./media/azure-stack-install-visual-studio/image1.png)

5. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)

6. Al termine dell'installazione, riavviare il sistema operativo.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Connettersi ad Azure Stack con Azure AD

1. Avviare Visual Studio.

2. Dal **View** dal menu **Cloud Explorer**.

3. Nel nuovo riquadro, selezionare **Add Account** e accedere con le credenziali di Azure Active Directory (Azure AD).  

    ![Screenshot di Esplora Cloud una volta effettuato l'accesso e connesse ad Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Dopo aver effettuato l'accesso, è possibile [distribuire modelli](azure-stack-deploy-template-visual-studio.md) o esplorare i tipi di risorse disponibili e i gruppi di risorse per creare modelli personalizzati.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Connettersi ad Azure Stack con AD FS

1. Avviare Visual Studio.

2. Dal **degli strumenti**, selezionare **opzioni**.

3. Espandere **ambiente** nel **riquadro di spostamento** e selezionare **account**.

4. Selezionare **Add**e immettere l'endpoint di Azure Resource Manger di utente. Per Azure Stack Development Kit, l'URL è: `https://management.local.azurestack/external`.  Per i sistemi integrati di Azure Stack, l'URL è: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Selezionare **Aggiungi**.  

    Visual Studio chiama Gestione risorse di Azure e consente di individuare gli endpoint, tra cui l'endpoint di autenticazione per Azure Directory Federated Services (ADFS).

    ![Screenshot di Esplora Cloud una volta effettuato l'accesso e connesse ad Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Selezionare **Cloud Explorer** dalle **visualizzazione** menu.

7. Selezionare **Add Account** e accedere con le credenziali di AD FS.  

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer esegue una query le sottoscrizioni disponibili. È possibile selezionare una sottoscrizione disponibile per la gestione.

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Esplorare le risorse esistenti, i gruppi di risorse o la distribuzione dei modelli.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su Visual Studio [fianco a fianco](/visualstudio/install/install-visual-studio-versions-side-by-side) con altre versioni di Visual Studio.
- [Sviluppare modelli per Azure Stack](azure-stack-develop-templates.md).