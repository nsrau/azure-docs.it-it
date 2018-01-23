---
title: Valutare il Kit di sviluppo dello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire il Kit di sviluppo dello Stack di Azure per scopi di valutazione.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guida introduttiva: Valutare il Kit di sviluppo di Azure Stack

*Si applica a: Azure Stack Development Kit*

Il [Kit di sviluppo di Azure Stack](azure-stack-poc.md) è un ambiente di testing e sviluppo che è possibile distribuire per valutare e dimostrare la funzionalità dello Stack di Azure e servizi. Per far sì che in esecuzione, è necessario preparare l'hardware di ambiente ed eseguire alcuni script (l'operazione richiederà diverse ore). Successivamente, è possibile accedere ai portali amministratori e degli utenti di gestire Azure Stack e offerte di test. 

1. [**Pianificare l'hardware e software e rete**](azure-stack-deploy.md). Il computer che ospita il kit di sviluppo (l'host di kit di sviluppo) deve soddisfare requisiti di rete, hardware e software. È inoltre necessario scegliere tra l'utilizzo di Azure Active Directory o Active Directory Federation Services. Assicurarsi di rispettare i prerequisiti prima di avviare la distribuzione in modo che il processo di installazione viene eseguito in modo uniforme. 

2. [**Scaricare ed estrarre il pacchetto di distribuzione**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o per un altro computer. I file estratti distribuzione richiedere 60 GB di spazio libero su disco, in modo usando un altro computer consente di ridurre i requisiti hardware per l'host del kit di sviluppo.

3. [**Preparare l'host del kit di sviluppo** ](azure-stack-run-powershell-script.md) tramite il programma di installazione. Dopo questo passaggio, verrà avviato l'host del kit di sviluppo di Cloudbuilder.vhdx (file di installazione di un disco rigido virtuale che include un sistema operativo avviabile e lo Stack di Azure).

4. [**Distribuire il kit di sviluppo** ](azure-stack-run-powershell-script.md) nell'host di kit di sviluppo.

5. Se la distribuzione di Azure Stack Usa Azure Active Directory, è necessario [registro dello Stack di Azure con Azure](azure-stack-register.md) in modo che sia possibile [il download di Azure marketplace elementi](azure-stack-download-azure-marketplace-item.md) allo Stack di Azure.

Dopo aver completato questi passaggi, sarà necessario un ambiente di kit di sviluppo con portali sia amministratore e utente. È quindi possibile [connettersi e accedere](azure-stack-connect-azure-stack.md) al portale. È possibile avviare la distribuzione di provider di risorse, creazione [offre](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)e il popolamento lo Stack di Azure [marketplace](azure-stack-marketplace.md).
