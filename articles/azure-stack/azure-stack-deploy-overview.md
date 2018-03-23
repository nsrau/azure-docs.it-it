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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guida introduttiva: Valutare il Kit di sviluppo di Azure Stack
Il [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) è un ambiente di testing e sviluppo che è possibile distribuire per valutare e illustrano funzionalità dello Stack di Azure e servizi. Per iniziare a usare il ASDK, è necessario preparare l'host hardware del computer e quindi eseguire alcuni script (installazione richiede alcune ore). Successivamente, è possibile accedere ai portali amministratore e utente per iniziare a usare Azure Stack.

## <a name="prerequisites"></a>Prerequisiti 
Prima di installare il ASDK, è necessario preparare il computer che ospiterà il kit di sviluppo (host di kit sviluppo). Il computer host kit di sviluppo deve soddisfare requisiti minimi hardware, software e rete. 

È inoltre necessario scegliere tra l'utilizzo di Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) come soluzione di identità per la distribuzione. 

Assicurarsi che l'host di kit sviluppo soddisfi i requisiti hardware minimi e che è stata apportata la decisione di soluzione di identità apportata prima di avviare la distribuzione in modo che il processo di installazione viene eseguito in modo uniforme. 

**[Esaminare le considerazioni di pianificazione della distribuzione ASDK](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> È possibile usare il [dello strumento di verifica dei requisiti di distribuzione di Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) dopo l'installazione del sistema operativo nel computer host kit di sviluppo per confermare che l'hardware soddisfi tutti i requisiti.

## <a name="download-and-extract-the-deployment-package"></a>Scaricare ed estrarre il pacchetto di distribuzione
Dopo aver verificato che il computer host di kit sviluppo soddisfi i requisiti di base per l'installazione di ASDK, il passaggio successivo consiste a scaricare ed estrarre il pacchetto di distribuzione ASDK. Il pacchetto di distribuzione include il file Cloudbuilder.vhdx, cioè un disco rigido virtuale che include i file di installazione dello Stack di Azure e un sistema operativo avviabile.

È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o in un altro computer. I file estratti distribuzione richiedere 60 GB di spazio libero su disco, in modo usando un altro computer consente di ridurre i requisiti hardware per l'host del kit di sviluppo.

**[Scaricare ed estrarre il Kit di sviluppo dello Stack di Azure (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparare il computer host kit di sviluppo
Prima di installare il ASDK nel computer host, è necessario preparare l'ambiente e il sistema è configurato per l'avvio da disco rigido virtuale. Dopo che è stato preparato il computer host kit di sviluppo, l'avvio dall'unità disco rigido CloudBuilder.vhdx macchina virtuale in modo che sia possibile cominciare la distribuzione ASDK.

**[Preparare il computer host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installare il ASDK nel computer host
Dopo aver preparato il computer host kit di sviluppo, la ASDK può essere distribuito nell'immagine CloudBuilder.vhdx. Il ASDK possono essere distribuiti tramite un'interfaccia utente grafica (GUI) fornita, scaricare ed eseguire lo script di PowerShell asdk installer.ps1 o completamente da [la riga di comando](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Facoltativamente, dopo che il computer host è stato avviato nel CloudBuilder.vhdx, è possibile configurare [le impostazioni di telemetria di Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *prima di* il ASDK l'installazione.


**[Installare il Kit di sviluppo di Azure Stack (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Esegui le configurazioni post-distribuzione
Dopo aver installato il ASDK, esistono alcuni controlli consigliati di post-installazione e le modifiche di configurazione che devono essere rese. È possibile convalidare l'installazione è stato installato correttamente usando il cmdlet test-AzureStack e installare gli strumenti di Azure PowerShell dello Stack e GitHub. 

Dopo le distribuzioni che usano Azure AD, è necessario attivare entrambi i Azure Stack amministratore portali tenant e. Questa attivazione consente che il portale di Azure Stack e Azure Resource Manager le autorizzazioni corrette (elencate nella pagina di consenso) per tutti gli utenti della directory.

È inoltre necessario reimpostare i criteri di scadenza delle password per assicurarsi che la password per l'host di kit sviluppo senza scadenza prima la fine del periodo di valutazione.

> [!NOTE]
> Facoltativamente, è inoltre possibile configurare [le impostazioni di telemetria di Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *dopo* il ASDK l'installazione.

**[Attività di distribuzione post-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrare con Azure
È necessario registrare dello Stack di Azure con Azure in modo da poter [scaricare gli elementi di Azure marketplace](.\asdk\asdk-marketplace-item.md) allo Stack di Azure.

**[Registro dello Stack di Azure con Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! Dopo aver completato questi passaggi, sarà necessario un ambiente di kit sviluppo con entrambi [amministratore](https://adminportal.local.azurestack.external) e [utente](https://portal.local.azurestack.external) portali. 
