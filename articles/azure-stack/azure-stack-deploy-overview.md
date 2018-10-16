---
title: Valuta Azure Stack Development Kit | Microsoft Docs
description: Informazioni su come distribuire Azure Stack Development Kit per scopi di valutazione.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/15/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: bf07fe56c65e53f5485b9927e0d704f80842cf3a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338703"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guida introduttiva: valutare Azure Stack Development Kit

Il [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) è un ambiente di test e sviluppo che è possibile distribuire per valutare e illustrano le funzionalità di Azure Stack e i servizi. Per iniziare a usare il ASDK, è necessario preparare l'host hardware del computer e quindi eseguire alcuni script (installazione richiede alcune ore). Successivamente, è possibile accedere ai portali di amministratore o utente per iniziare a usare Azure Stack.

## <a name="prerequisites"></a>Prerequisiti

### <a name="asdk-host-computer-requirements"></a>Requisiti del computer host ASDK

Prima di installare il ASDK, è necessario preparare il computer che ospiterà il kit di sviluppo. Il computer host kit di sviluppo deve soddisfare l'hardware, software e i requisiti di rete descritti in  **[esaminare le considerazioni di pianificazione della distribuzione di ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> È possibile usare la [dello strumento di controllare i requisiti di distribuzione di Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) dopo l'installazione del sistema operativo nel computer host kit di sviluppo per verificare che l'hardware soddisfi tutti i requisiti.

### <a name="account-requirements"></a>Requisiti dell'account

È anche necessario scegliere tra l'uso di Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) come soluzione di identità per la distribuzione. Esaminare i requisiti dell'account nel  **[considerazioni sulla pianificazione della distribuzione](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Scaricare ed estrarre il pacchetto di distribuzione

Dopo aver preparato il computer host kit di sviluppo, scaricare ed estrarre il pacchetto di distribuzione ASDK. Il pacchetto di distribuzione include il file Cloudbuilder.vhdx, ovvero un disco rigido virtuale (VHD) con un sistema operativo avviabile, e i file di installazione di Azure Stack.

È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o in un altro computer. I file estratti distribuzione occupano 60 GB di spazio libero su disco, in modo usando un altro computer consente di ridurre i requisiti di archiviazione nell'host di kit di sviluppo.

**[Scaricare ed estrarre Azure Stack Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Preparare il computer host

Prima di installare il ASDK, è necessario preparare l'ambiente host e il sistema è configurato per l'avvio nel Kit di sviluppo disco rigido virtuale. Quando si riavvia l'host, venga avviato dalla CloudBuilder.vhdx e avviare la distribuzione di ASDK.

**[Preparare il computer host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installare il ASDK nel computer host

Dopo che il computer host viene avviato dal disco rigido virtuale, è possibile distribuire il kit di sviluppo all'ambiente virtuale Cloudbuilder. È possibile distribuire il ASDK usando l'interfaccia utente grafica (GUI), fornita eseguendo lo script di PowerShell asdk installer.ps1 o da [la riga di comando di PowerShell](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Dopo che l'host viene avviato dall'immagine Cloudbuilder.vhdx, hai la possibilità di configurare [le impostazioni di telemetria di Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *prima di* installando il ASDK.

**[Installare Azure Stack Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Eseguire le configurazioni di post-distribuzione

Dopo aver installato il ASDK, esistono alcuni controlli di post-installazione consigliati e modifiche di configurazione che devono essere resi.

**Strumenti**

Installare gli strumenti PowerShell per Azure Stack e GitHub e verificare l'esito dell'installazione tramite il cmdlet test-AzureStack.

**Soluzione di identità**

Per una distribuzione che usa Azure AD, è necessario attivare entrambi i portali a amministratore e del tenant di Azure Stack. Questa attivazione dà il consenso a fornendo il portale di Azure Stack e Azure Resource Manager le autorizzazioni corrette (elencate nella pagina di consenso) per tutti gli utenti della directory.

**Scadenza password**

È consigliabile reimpostare i criteri di scadenza delle password per assicurarsi che la password per l'host del kit di sviluppo non scada prima della scadenza periodo della valutazione.

> [!NOTE]
> È inoltre possibile configurare [le impostazioni di telemetria di Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *dopo* installando il ASDK.

**[Attività di distribuzione post-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrazione in Azure

È necessario registrare Azure Stack con Azure in modo da poter [download di Azure marketplace elementi](.\asdk\asdk-marketplace-item.md) ad Azure Stack.

**[Registrare Azure Stack con Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! Completando la procedura descritta in questa Guida introduttiva è disponibile un ambiente ASDK con un [administrator](https://adminportal.local.azurestack.external) portale e una [utente](https://portal.local.azurestack.external) portale.
