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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 2afbea68c017805e9bd7db43b03face0705608b7
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42358748"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installare Visual Studio e connettersi ad Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare Visual Studio per scrivere e distribuire Azure Resource Manager [modelli](azure-stack-arm-templates.md) ad Azure Stack. I passaggi descritti in questo articolo illustrano come installare Visual Studio sul [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o in un computer esterno se si prevede di Azure Stack tramite il [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

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

4. Selezionare **Add**e immettere l'endpoint di Azure Resource Manger di utente.  
  Per Azure Stack Development kit, l'URL è: `https://management.local.azurestack/external`.  
  Per Azure Stack integrati sistemi l'URL è: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Selezionare **Aggiungi**.  

    Visual Studio chiama il gestore delle risorse di Azure e consente di individuare gli endpoint inclusi l'endpoint di autenticazione per Azure Directory Federated Services (ADFS).

    ![Screenshot di Esplora Cloud una volta effettuato l'accesso e connesse ad Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Selezionare **Cloud Explorer** dalle **visualizzazione** menu.
7. Selezionare **Add Account** e accedere con le credenziali di AD FS.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer esegue una query le sottoscrizioni disponibili. È possibile selezionare uno una sottoscrizione disponibile per la gestione.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Esplorazione di risorse esistente, i gruppi di risorse o la distribuzione dei modelli.

## <a name="next-steps"></a>Passaggi successivi

 - Altre informazioni, vedere [coesistenza](https://msdn.microsoft.com/library/ms246609.aspx) con altre versioni di Visual Studio.
 - [Sviluppo di modelli per Azure Stack](azure-stack-develop-templates.md)