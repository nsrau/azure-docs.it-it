---
title: "Azure Active Directory Domain Services: Risoluzione dei problemi relativi alla configurazione delle entità servizio | Microsoft Docs"
description: "Risoluzione dei problemi relativi alla configurazione delle entità servizio di Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2018
ms.author: ergreenl
ms.openlocfilehash: 7388bb291f665f195355a01d19a82cba9ed453eb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Risolvere i problemi di configurazione non valida delle entità servizio per il dominio gestito

Questo articolo consente di individuare e risolvere gli errori di configurazione relativi alle entità servizio che causano il messaggio di avviso seguente:

## <a name="alert-aadds102-service-principal-not-found"></a>Avviso AADDS102: Entità servizio non trovata
**Messaggio di avviso:** *Un'entità servizio necessaria per il funzionamento corretto di Azure AD Domain Services è stata eliminata dal tenant di Azure AD. Questa configurazione impedisce a Microsoft di monitorare, applicare patch e sincronizzare il dominio gestito.*

Le [entità servizio](../active-directory/develop/active-directory-application-objects.md) sono applicazioni usate da Microsoft per la gestione, l'aggiornamento e la manutenzione del dominio gestito. Se vengono eliminate, Microsoft non è più in grado di gestire il dominio. 


## <a name="check-for-missing-service-principals"></a>Verificare la presenza delle entità servizio mancanti
Seguire questa procedura per determinare quali entità servizio devono essere ricreate:

1. Passare alla pagina [Applicazioni aziendali - Tutte le applicazioni](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) nel portale di Azure.
2. Nell'elenco a discesa **Mostra** selezionare **Tutte le applicazioni** e fare clic su **Applica**.
3. Usando la tabella seguente, cercare ogni applicazione incollando il relativo ID nella casella di ricerca e premendo INVIO. Se la ricerca non restituisce alcun risultato, è necessario ricreare l'entità servizio seguendo la procedura indicata nella colonna "Risoluzione".

| ID applicazione | Risoluzione |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Ricreare un'entità servizio mancante con PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Ripetere la registrazione nello spazio dei nomi Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Ripetere la registrazione nello spazio dei nomi Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Entità servizio che si correggono automaticamente](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Ricreare un'entità servizio mancante con PowerShell
Seguire questa procedura se un'entità servizio con ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` non è presente nella directory di Azure AD.

**Correzione:** per eseguire questa procedura, è necessario aver installato Azure AD PowerShell. Per informazioni sull'installazione di Azure AD PowerShell, vedere [questo articolo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Per risolvere questo problema, digitare i comandi seguenti in una finestra di PowerShell:
1. Installare e importare il modulo Azure AD PowerShell.
    
    ```powershell 
    Install-Module AzureAD
    Import-Module AzureAD
    ```
    
2. Verificare se l'entità servizio necessaria per Azure AD Domain Services manca nella directory eseguendo il comando PowerShell seguente:
    
    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```
    
3. Creare l'entità servizio digitando il comando PowerShell seguente:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```
    
4. Dopo aver creato l'entità servizio mancante, attendere due ore e controllare l'integrità del dominio gestito.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Ripetere la registrazione nello spazio dei nomi Microsoft.AAD tramite il portale di Azure
Seguire questa procedura se un'entità servizio con ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` o ```abba844e-bc0e-44b0-947a-dc74e5d09022``` non è presente nella directory di Azure AD.

**Correzione:** eseguire la procedura seguente per ripristinare Domain Services nella directory:

1. Passare alla [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure.
2. Scegliere la sottoscrizione dalla tabella associata al dominio gestito.
3. Usando il menu di spostamento a sinistra, scegliere **Provider di risorse**.
4. Cercare "Microsoft.AAD" nella tabella e fare clic su **Ripeti registrazione**.
5. Per verificare che l'avviso sia stato risolto, visualizzare la pagina relativa all'integrità del dominio gestito dopo due ore.


## <a name="service-principals-that-self-correct"></a>Entità servizio che si correggono automaticamente
Seguire questa procedura se un'entità servizio con ID ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` non è presente nella directory di Azure AD.

**Correzione:** Azure AD Domain Services è capace di rilevare quando questa entità servizio non è presente, è configurata in modo errato o è stata eliminata. Il servizio ricrea automaticamente questa entità servizio. Controllare l'integrità del dominio gestito dopo due ore per verificare che l'entità servizio sia stata ricreata.


## <a name="contact-us"></a>Contattaci
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
