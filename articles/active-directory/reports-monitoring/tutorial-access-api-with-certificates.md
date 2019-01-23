---
title: "Esercitazione: Ottenere i dati usando l'API di creazione report di Azure AD con i certificati | Microsoft Docs"
description: Questa esercitazione spiega come usare l'API di creazione report di Azure AD con le credenziali del certificato per ottenere i dati provenienti da directory senza intervento dell'utente.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 5714ed552c81d28a253aa57ad6e2ba1d67e543a1
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214267"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Esercitazione: Ottenere dati con l'API di creazione report di Azure Active Directory con certificati

Le [API di creazione report di Azure Active Directory (Azure AD)](concept-reporting-api.md) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti. Se si vuole accedere all'API di creazione report di Azure AD senza l'intervento dell'utente, è necessario configurare l'accesso per l'uso di certificati.

In questa esercitazione si apprenderà come usare un certificato di test per accedere all'API Graph Microsoft e creare report. Non è consigliabile usare certificati di test in un ambiente di produzione. 

## <a name="prerequisites"></a>Prerequisiti

1. Per accedere a dati di accesso, assicurarsi di avere un tenant di Azure Active Directory con una licenza Premium (P1/P2). vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. Si noti che se i dati sulle attività non fossero disponibili prima dell'aggiornamento, saranno necessari un paio di giorni per visualizzare i dati nei report dopo aver eseguito l'aggiornamento a una licenza Premium. 

2. Creare o passare a un account utente nel ruolo **Amministratore globale**, **Amministratore della sicurezza**, **Ruolo con autorizzazioni di lettura per la sicurezza** o **Ruolo con autorizzazioni di lettura per i report** per il tenant. 

3. Completare i [prerequisiti di accesso all'API di creazione report di Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Scaricare e installare [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installare [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Questo modulo offre diversi cmdlet di utilità, tra cui:
    - Le librerie ADAL necessarie per l'autenticazione
    - Token di accesso dell'utente, chiavi dell'applicazione e certificati con ADAL
    - API Graph che gestisce i risultati di paging

6. Se è la prima volta che si usa il modulo, eseguire **Install-MSCloudIdUtilsModule**; in caso contrario, è possibile importarlo tramite il comando **Import-Module** di Powershell. La sessione avrà un aspetto simile a questa schermata: ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Usare il cmdlet **New-SelfSignedCertificate** di Powershell per creare un certificato di test.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Usare il cmdlet **Export-Certificate** per esportarlo in un file del certificato.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Ottenere dati con l'API di creazione report di Azure Active Directory con certificati

1. Passare al [portale di Azure](https://portal.azure.com), selezionare **Azure Active Directory**, quindi selezionare **Registrazioni app** e scegliere l'app dall'elenco. 

2. Selezionare **Impostazioni** > **Chiavi** e selezionare **Carica la chiave pubblica**.

3. Selezionare il file del certificato nel passaggio precedente e selezionare **Salva**. 

4. Si noti l'ID applicazione e l'identificazione personale del certificato appena registrato con l'applicazione. Per trovare l'identificazione personale, dalla pagina dell'applicazione del portale, passare a **Impostazioni** e fare clic su **Chiavi**. L'identificazione personale si troverà nell'elenco **Chiavi pubbliche**.

5. Aprire il manifesto dell'applicazione nell'editor del manifesto inline e sostituire la proprietà *keyCredentials* con le nuove informazioni del certificato usando lo schema seguente. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Salvare il manifesto. 
  
7. A questo punto è possibile ottenere un token di accesso per l'API Graph usando questo certificato. Usare il cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** dal modulo MSCloudIdUtils di PowerShell passando l'ID dell'applicazione e l'identificazione personale ottenuta nel passaggio precedente. 

 ![Portale di Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Usare il token di accesso nello script di PowerShell per eseguire una query sull'API Graph. Usare il cmdlet **Invoke-MSCloudIdMSGraphQuery** da MSCloudIDUtils per enumerare l'endpoit Signins e directoryAudits. Questo cmdlet gestisce i risultati di multi-paging e li invia alla pipeline di PowerShell.

9. Eseguire una query sull'endpoint directoryAudits per recuperare i log di controllo. 
 ![Portale di Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Eseguire una query dell'endpoint Signins per recuperare i log di accesso.
 ![Portale di Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

11. È ora possibile scegliere di esportare i dati in un file CSV e salvarlo in un sistema SIEM. È anche possibile eseguire il wrapping dello script in un'attività pianificata per ottenere periodicamente i dati di Azure AD dal tenant senza dover archiviare le chiavi dell'applicazione nel codice sorgente. 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una prima impressione delle API di creazione report](concept-reporting-api.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
