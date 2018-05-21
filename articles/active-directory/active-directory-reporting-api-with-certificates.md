---
title: Ottenere dati con l'API di creazione report di Azure AD con certificati | Microsoft Docs
description: Spiega come usare l'API di creazione report di Azure AD con le credenziali del certificato per ottenere i dati provenienti da directory senza intervento dell'utente.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Ottenere dati con l'API di creazione report di Azure Active Directory con certificati

Le [API di creazione report di Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

Se si vuole accedere all'API di creazione report di Azure AD senza l'intervento dell'utente, è possibile configurare l'accesso per l'uso di certificati

Questo articolo:

- Illustra i passaggi necessari per l'accesso all'API di creazione report di Azure AD usando certificati.
- Presuppone che siano stati completati i [prerequisiti per l'accesso all'API di creazione report di Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 


Per accedere all'API di creazione report con certificati, è necessario:

1. Installare i prerequisiti
2. Impostare il certificato nell'app 
3. Concedere le autorizzazioni
4. Ottenere un token di accesso




Per informazioni sul codice sorgente, vedere [Sfruttare il modulo dell'API di creazione report](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

## <a name="install-prerequisites"></a>Installare i componenti prerequisiti

È necessario avere installato Azure AD PowerShell V2 e il modulo AzureADUtils.

1. Scaricare e installare Azure AD Powershell V2 seguendo le istruzioni illustrate in [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Scaricare il modulo AzureADUtils da [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Questo modulo offre diversi cmdlet di utilità, tra cui:
    - La versione più recente di ADAL con NuGet
    - Token di accesso dell'utente, chiavi dell'applicazione e certificati con ADAL
    - API Graph che gestisce i risultati di paging

**Per installare il modulo AzureADUtils:**

1. Creare una directory in cui salvare il modulo delle utilità, ad esempio c:\azureAD, e scaricare il modulo da GitHub.
2. Aprire una sessione di PowerShell e passare alla directory appena creata. 
3. Importare il modulo e installarlo nel percorso del modulo di PowerShell usando il cmdlet Install-AzureADUtilsModule. 

La sessione avrà un aspetto simile a questa schermata:

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Impostare il certificato nell'app

**Per impostare il certificato nell'app:**

1. [Ottenere l'ID oggetto](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) dell'app dal portale di Azure. 

  ![Portale di Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Aprire una sessione di PowerShell e connettersi ad Azure AD usando il cmdlet Connect-AzureAD.

  ![Portale di Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Usare il cmdlet New-AzureADApplicationCertificateCredential da AzureADUtils per aggiungere una credenziale di certificato. 

>[!Note]
>È necessario specificare l'ID oggetto dell'applicazione acquisito in precedenza, nonché l'oggetto certificato che può essere ottenuto usando l'unità Cert:
>


  ![Portale di Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Ottenere un token di accesso

Per ottenere un token di accesso, usare il cmdlet **Get-AzureADGraphAPIAccessTokenFromCert** di AzureADUtils. 

>[!NOTE]
>È necessario usare l'ID applicazione invece dell'ID oggetto usato nell'ultima sezione.
>

 ![Portale di Azure](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Usare il token di accesso per chiamare l'API Graph

È ora possibile creare lo script. Di seguito è riportato un esempio con il cmdlet **Invoke-AzureADGraphAPIQuery** di AzureADUtils. Questo cmdlet gestisce i risultati di multi-paging e li invia alla pipeline di PowerShell. 

 ![Portale di Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

A questo punto è possibile esportare in un file CSV e salvare in un sistema SIEM. È anche possibile eseguire il wrapping dello script in un'attività pianificata per ottenere periodicamente i dati di Azure AD dal tenant senza dover archiviare le chiavi dell'applicazione nel codice sorgente. 

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una prima impressione delle API di creazione report](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Creare una soluzione personalizzata](active-directory-reporting-api-getting-started-azure-portal.md#customize)




