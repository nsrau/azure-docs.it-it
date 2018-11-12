---
title: Configurare identità del cloud ibrido con le applicazioni di Azure e Azure Stack | Microsoft Docs
description: Informazioni su come configurare identità del cloud ibrido con le applicazioni di Azure e Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: e1ace09ba293cf80b2dbbe8f746eb7fd5bc8d7cb
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036394"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Esercitazione: Configurare identità del cloud ibrido per le applicazioni di Azure e Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come configurare un'identità del cloud ibrido per le applicazioni di Azure e Azure Stack.

Sono disponibili due opzioni per concedere l'accesso alle applicazioni in Azure globale e Azure Stack.

 * Quando Azure Stack è una connessione continua a Internet, è possibile usare Azure Active Directory (Azure AD).
 * Quando Azure Stack è disconnesso da Internet, è possibile usare Azure Directory Federated Services (ADFS).

Si usano le entità servizio per concedere l'accesso alle applicazioni Azure Stack allo scopo di distribuzione o la configurazione usando Azure Resource Manager in Azure Stack.

In questa esercitazione si creerà un ambiente di esempio:

> [!div class="checklist"]
> - Stabilire un'identità ibrida in globale di Azure e Azure Stack
> - Recuperare un token per accedere all'API di Azure Stack.

È necessario disporre delle autorizzazioni di operatore di Azure Stack per i passaggi in questa esercitazione.

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione ibrida applicazioni. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Creare un'entità servizio per Azure AD nel portale

Se è stato distribuito Azure Stack tramite Azure AD come archivio identità, è possibile creare le entità servizio, proprio come per Azure. Il [creano entità servizio](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) articolo illustra come eseguire i passaggi tramite il portale. Verificare di avere il [autorizzazioni di Azure AD necessarie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) prima di iniziare.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Creare un'entità servizio per AD FS usando PowerShell

Se è stato distribuito Azure Stack con AD FS, è possibile usare PowerShell per creare un'entità servizio, assegnare un ruolo per l'accesso e accesso da PowerShell usando tale identità. [Creare un'entità servizio per AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) illustra come eseguire i passaggi necessari usando PowerShell.

## <a name="using-the-azure-stack-api"></a>Usando l'API di Azure Stack

Il [API di Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) esercitazione illustra il processo di recupero di un token per accedere all'API di Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Connettersi ad Azure Stack usando Powershell

La Guida introduttiva [diventare operativi in esecuzione con PowerShell in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) illustra i passaggi necessari per installare Azure PowerShell e connettersi all'installazione di Azure Stack.

### <a name="prerequisites"></a>Prerequisiti

Un'installazione di Azure Stack è connesso ad Azure Active Directory con una sottoscrizione che è possibile accedere. Se non si dispone di un'installazione di Azure Stack, è possibile usare queste istruzioni per configurare un [Azure Stack Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Connettersi ad Azure Stack usando il codice

Per connettersi ad Azure Stack usando il codice, usare gli endpoint API di Azure Resource Manager per ottenere l'autenticazione e l'endpoint graph per l'installazione di Azure Stack e quindi eseguire l'autenticazione usando richieste REST. È possibile trovare un'applicazione client di esempio nella [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>A meno che il SDK di Azure per il linguaggio scelto supporta i profili delle API di Azure, il SDK potrebbe non funzionare con Azure Stack. Per altre informazioni sui profili di API di Azure, vedere la [gestire i profili delle versioni API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles) articolo.

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sulla modalità di gestione delle identità in Azure Stack, vedere [architettura Identità per Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).
