---
title: Configurare identità cloud ibrido con le applicazioni di Azure e Azure Stack | Documenti Microsoft
description: Informazioni su come configurare identità cloud ibrido con le applicazioni di Azure e Azure Stack.
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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Esercitazione: Configurare identità cloud ibrido con le applicazioni di Azure e Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Sono disponibili due opzioni per concedere l'accesso alle applicazioni in Azure globali e Stack di Azure. Quando Azure Stack dispone di una connessione continua a Internet, è possibile utilizzare Azure Active Directory (Azure AD). Quando Azure Stack viene disconnesso da Internet, è possibile utilizzare Azure Directory Federated Services (ADFS). Le entità servizio consente di concedere l'accesso alle applicazioni nello Stack di Azure ai fini di distribuzione o configurazione tramite il gestore delle risorse di Azure nello Stack di Azure. 

In questa esercitazione si creerà un ambiente di esempio a:

> [!div class="checklist"]
> * Stabilire un'identità ibrida in globale Azure e Azure Stack
> * Impossibile recuperare un token per accedere all'API di Stack di Azure.

Questi passaggi è necessario disporre delle autorizzazioni di operatore dello Stack di Azure.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Creare un'entità servizio per Azure AD nel portale

Se è già distribuito Azure Stack mediante Azure AD come archivio di identità, è possibile creare le entità servizio, come se si trattasse di Azure. [Questo documento](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) viene illustrato come eseguire la procedura tramite il portale. Verificare di disporre di [necessarie autorizzazioni di Azure AD](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) prima di iniziare.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Creare un'entità servizio per ADFS tramite PowerShell

Se è stato distribuito Azure Stack con AD FS, è possibile utilizzare PowerShell per creare un'entità servizio, assegnare un ruolo per l'accesso e accesso da PowerShell mediante tale identità. [Questo documento](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) viene illustrato come eseguire i passaggi tramite PowerShell.

## <a name="using-the-azure-stack-api"></a>Tramite l'API dello Stack di Azure

[Questa esercitazione](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) assiste il processo di recupero di un token per accedere all'API di Stack di Azure.

## <a name="connect-to-azure-stack-using-powershell"></a>Connettersi allo Stack di Azure usando Powershell

Di seguito è riportato uno script di esempio con i concetti illustrati nel presente documento per la connessione allo Stack di Azure.

### <a name="prerequisites"></a>Prerequisiti

Un'installazione di Azure Stack connesso ad Azure Active Directory con una sottoscrizione che è possibile accedere. Se non si dispone di un'installazione dello Stack di Azure, è possibile utilizzare queste istruzioni per configurare un [Kit di sviluppo di Azure Stack](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[Questa esercitazione](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) verranno illustrati i passaggi necessari per installare Azure PowerShell e connettersi all'installazione di Stack di Azure.

#### <a name="connect-to-azure-stack-using-code"></a>Connettersi allo Stack di Azure usando codice

Per connettersi allo Stack di Azure mediante il codice, usare gli endpoint di Azure Resource Manager API per ottenere l'autenticazione e l'endpoint graph per l'installazione dello Stack di Azure e quindi eseguire l'autenticazione usando richieste REST. È possibile trovare un'applicazione di esempio [qui](https://github.com/shriramnat/HybridARMApplication).

> [!Note]  
A meno che non Azure SDK per il linguaggio scelto supporta i profili di API di Azure, SDK potrebbe non funzionare con lo Stack di Azure. Per ulteriori informazioni sui profili di API di Azure, andare [qui](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Passaggi successivi

 - Per ulteriori informazioni sulla modalità di gestione di identità nello Stack di Azure, vedere [architettura di identità per lo Stack di Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Per ulteriori informazioni sui pattern di Cloud di Azure, vedere [schemi progettuali Cloud](https://docs.microsoft.com/azure/architecture/patterns).
