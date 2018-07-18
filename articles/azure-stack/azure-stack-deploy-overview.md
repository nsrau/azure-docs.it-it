---
title: Valutare il Kit di sviluppo dello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire il Kit di sviluppo dello Stack di Azure per scopi di valutazione.
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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235186"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guida introduttiva: valutare il Kit di sviluppo dello Stack di Azure

Il [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) è un ambiente di testing e sviluppo che è possibile distribuire per valutare e illustrano funzionalità dello Stack di Azure e servizi. Per iniziare a usare il ASDK, è necessario preparare l'host hardware del computer e quindi eseguire alcuni script (installazione richiede alcune ore). Successivamente, è possibile accedere ai portali amministratore o utente per iniziare a usare Azure Stack.

## <a name="prerequisites"></a>Prerequisiti

### <a name="asdk-host-computer-requirements"></a>Requisiti del computer host ASDK

Prima di installare il ASDK, è necessario preparare il computer che ospiterà il kit di sviluppo. Il computer host kit sviluppo devono soddisfare l'hardware, software e i requisiti di rete descritti in  **[esaminare le considerazioni di pianificazione della distribuzione ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> È possibile usare il [dello strumento di verifica dei requisiti di distribuzione di Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) dopo l'installazione del sistema operativo nel computer host kit di sviluppo per confermare che l'hardware soddisfi tutti i requisiti.

### <a name="account-requirements"></a>Requisiti dell'account

È inoltre necessario scegliere tra l'utilizzo di Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) come soluzione di identità per la distribuzione. Esaminare i requisiti di account in  **[considerazioni sulla pianificazione della distribuzione](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Scaricare ed estrarre il pacchetto di distribuzione

Dopo aver preparato il computer host kit di sviluppo, scaricare ed estrarre il pacchetto di distribuzione ASDK. Il pacchetto di distribuzione include il file Cloudbuilder.vhdx, ovvero un'unità disco rigida virtuale (VHD) con un sistema operativo avviabile, e i file di installazione dello Stack di Azure.

È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o in un altro computer. I file estratti distribuzione occupano 60 GB di spazio libero su disco, in modo usando un altro computer può aiutare a ridurre i requisiti di archiviazione nell'host di kit di sviluppo.

**[Scaricare ed estrarre il Kit di sviluppo dello Stack di Azure (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Preparare il computer host

Prima di installare il ASDK, è necessario preparare l'ambiente host e il sistema è configurato per l'avvio dal disco rigido virtuale kit sviluppo. Quando si riavvia l'host, che viene avviato da CloudBuilder.vhdx e iniziare a distribuire il ASDK.

**[Preparare il computer host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installare il ASDK nel computer host

Dopo l'avvio del computer host dal disco rigido virtuale, è possibile distribuire il kit di sviluppo all'ambiente virtuale Cloudbuilder. È possibile distribuire ASDK tramite l'interfaccia utente grafica (GUI), fornito da esecuzione dello script di PowerShell asdk installer.ps1 o da [la riga di comando di PowerShell](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Dopo che l'host viene avviato dall'immagine Cloudbuilder.vhdx, si dispone dell'opzione di configurazione [le impostazioni di telemetria di Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *prima di* il ASDK l'installazione.

**[Installare il Kit di sviluppo di Azure Stack (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Esegui le configurazioni post-distribuzione

Dopo aver installato il ASDK, esistono alcuni controlli consigliati di post-installazione e le modifiche di configurazione che devono essere rese.

**Strumenti**

Installare gli strumenti di Azure PowerShell dello Stack e GitHub e verificare l'esito dell'installazione tramite il cmdlet test-AzureStack.

**Soluzione di identità**

Per una distribuzione che usa Azure AD, è necessario attivare entrambi i Azure Stack amministratore portali tenant e. Questa attivazione consente che il portale di Azure Stack e Azure Resource Manager le autorizzazioni corrette (elencate nella pagina di consenso) per tutti gli utenti della directory.

**Scadenza password**

È consigliabile reimpostare i criteri di scadenza delle password per assicurarsi che la password per l'host di kit sviluppo senza scadenza prima la fine del periodo di valutazione.

> [!NOTE]
> È inoltre possibile configurare [le impostazioni di telemetria di Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *dopo* il ASDK l'installazione.

**[Attività di distribuzione post-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrare con Azure

È necessario registrare dello Stack di Azure con Azure in modo da poter [scaricare gli elementi di Azure marketplace](.\asdk\asdk-marketplace-item.md) allo Stack di Azure.

**[Registro dello Stack di Azure con Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni! Completando i passaggi descritti in questa Guida rapida è disponibile un ambiente ASDK con un [amministratore](https://adminportal.local.azurestack.external) portal e una [utente](https://portal.local.azurestack.external) portale.
