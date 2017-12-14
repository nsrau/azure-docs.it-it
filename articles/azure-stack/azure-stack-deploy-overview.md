---
title: Guida introduttiva di Azure Stack Development Kit distribuzione | Documenti Microsoft
description: Informazioni su come distribuire il Kit di sviluppo di Azure Stack
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 04742a587284cf72632360a9575a63a576da36e8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="azure-stack-development-kit-deployment-quickstart"></a>Guida introduttiva alla distribuzione di Azure Stack Development Kit

*Si applica a: Azure Stack Development Kit*

Il [Kit di sviluppo di Azure Stack](azure-stack-poc.md) è un ambiente di testing e sviluppo che è possibile distribuire per valutare e dimostrare la funzionalità dello Stack di Azure e servizi. Per far sì che in esecuzione, è necessario preparare l'hardware di ambiente ed eseguire alcuni script (l'operazione richiederà diverse ore). Successivamente, è possibile accedere ai portali amministratori e degli utenti di gestire Azure Stack e offerte di test. 

1. [**Pianificare l'hardware e software e rete**](azure-stack-deploy.md). Il computer che ospita il kit di sviluppo (l'host di kit di sviluppo) deve soddisfare requisiti di rete, hardware e software. È inoltre necessario scegliere tra l'utilizzo di Azure Active Directory o Active Directory Federation Services. Assicurarsi di rispettare i prerequisiti prima di avviare la distribuzione in modo che il processo di installazione viene eseguito in modo uniforme. 

2. [**Scaricare ed estrarre il pacchetto di distribuzione**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). È possibile scaricare il pacchetto di distribuzione per l'host del kit di sviluppo o per un altro computer. I file estratti distribuzione richiedere 60 GB di spazio libero su disco, in modo usando un altro computer consente di ridurre i requisiti hardware per l'host del kit di sviluppo.

3. [**Preparare l'host del kit di sviluppo** ](azure-stack-run-powershell-script.md) tramite il programma di installazione. Dopo questo passaggio, verrà avviato l'host del kit di sviluppo di Cloudbuilder.vhdx (file di installazione di un disco rigido virtuale che include un sistema operativo avviabile e lo Stack di Azure).

4. [**Distribuire il kit di sviluppo** ](azure-stack-run-powershell-script.md) nell'host di kit di sviluppo.

5. Se la distribuzione di Azure Stack Usa Azure Active Directory, è necessario [registro dello Stack di Azure con Azure](azure-stack-register.md) in modo che sia possibile [il download di Azure marketplace elementi](azure-stack-download-azure-marketplace-item.md) allo Stack di Azure.

Dopo aver completato questi passaggi, sarà necessario un ambiente di kit di sviluppo con portali sia amministratore e utente. È quindi possibile [connettersi e accedere](azure-stack-connect-azure-stack.md) al portale. È possibile avviare la distribuzione di provider di risorse, creazione [offre](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)e il popolamento lo Stack di Azure [marketplace](azure-stack-marketplace.md).
