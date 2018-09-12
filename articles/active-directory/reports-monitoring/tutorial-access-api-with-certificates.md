---
title: Ottenere dati con l'API di creazione report di Azure AD con certificati | Microsoft Docs
description: Spiega come usare l'API di creazione report di Azure AD con le credenziali del certificato per ottenere i dati provenienti da directory senza intervento dell'utente.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7e2dd4c50a1d6995302c5a2a6f9b4877253d0a41
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382281"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Ottenere dati con l'API di creazione report di Azure Active Directory con certificati

Le [API di creazione report di Azure Active Directory (Azure AD)](concept-reporting-api.md) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti. Se si vuole accedere all'API di creazione report di Azure AD senza l'intervento dell'utente, è possibile configurare l'accesso per l'uso di certificati.

Questo include i passaggi seguenti:

1. [Installare i prerequisiti](#install-prerequisites)
2. [Registrare il certificato nell'app](#register-the-certificate-in-your-app)
3. [Ottenere un token di accesso per l'API Graph di Microsoft](#get-an-access-token-for-ms-graph-api)
4. [Eseguire una query degli endpoint dell'API Graph di Microsoft](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Installare i componenti prerequisiti

1. Innanzitutto, assicurarsi che siano stati completati i [prerequisiti per l'accesso all'API di creazione report di Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

2. Scaricare e installare Azure AD Powershell V2 seguendo le istruzioni illustrate in Azure Active Directory PowerShell https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)

3. Installare MSCloudIDUtils da [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Questo modulo offre diversi cmdlet di utilità, tra cui:
    - Le librerie ADAL necessarie per l'autenticazione
    - Token di accesso dell'utente, chiavi dell'applicazione e certificati con ADAL
    - API Graph che gestisce i risultati di paging

4. Se è la prima volta che si usa il modulo, eseguire **Install-MSCloudIdUtilsModule** per completare l'installazione, in caso contrario, è possibile semplicemente importarlo tramite il comando di Powershell **Import-Module**.

La sessione avrà un aspetto simile a questa schermata:

  ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Registrare il certificato nell'app

1. In primo luogo, andare alla pagina di registrazione dell'applicazione. A tal fine, accedere al [portale di Azure](https://portal.azure.com), fare clic su **Azure Active Directory**, quindi su **Registrazioni per l'app** e selezionare l'applicazione dall'elenco. 

2. Quindi, seguire i passaggi necessari per [registrare il certificato con Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) per l'applicazione. 

3. Si noti l'ID applicazione e l'identificazione personale del certificato appena registrato con l'applicazione. Per trovare l'identificazione personale, dalla pagina dell'applicazione del portale, passare a **Impostazioni** e fare clic su **Chiavi**. L'identificazione personale si troverà nell'elenco **Chiavi pubbliche**.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Ottenere un token di accesso per l'API Graph di Microsoft

Per ottenere un token di accesso per l'API Graph di Microsoft, utilizzare il cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** dal modulo PowerShell MSCloudIdUtils. 

>[!NOTE]
>È necessario usare l'ID dell'applicazione (anche noto come ClientID) e l'identificazione personale del certificato con la chiave privata installata nell'archivio certificati del computer (archivio certificati CurrentUser o LocalMachine).
>

 ![Portale di Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Usare il token di accesso per chiamare l'API Graph

A questo punto, è possibile utilizzare il token di accesso nello script di Powershell per eseguire la query nell'API Graph. Di seguito è riportato un esempio utilizzando il cmdlet **Invoke-MSCloudIdMSGraphQuery** da MSCloudIDUtils per enumerare l'endpoint di signins o directoryAudits. Questo cmdlet gestisce i risultati di multi-paging e li invia alla pipeline di PowerShell.

### <a name="query-the-directoryaudits-endpoint"></a>Eseguire una query sull'endpoint DirectoryAudits
 ![Portale di Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Eseguire una query sull'endpoint SignIns
 ![Portale di Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

È ora possibile scegliere di esportare i dati in un file CSV e salvarlo in un sistema SIEM. È anche possibile eseguire il wrapping dello script in un'attività pianificata per ottenere periodicamente i dati di Azure AD dal tenant senza dover archiviare le chiavi dell'applicazione nel codice sorgente. 


## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una prima impressione delle API di creazione report](concept-reporting-api.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



