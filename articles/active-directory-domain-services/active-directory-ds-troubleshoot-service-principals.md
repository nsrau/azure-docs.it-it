---
title: 'Azure Active Directory Domain Services: Risoluzione dei problemi relativi alla configurazione delle entità servizio | Microsoft Docs'
description: Risoluzione dei problemi relativi alla configurazione delle entità servizio di Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: e1be075ba2d3e6ae7512ccc030073fd7f1862502
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
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

**Soluzione:** per eseguire questa procedura, è necessario avere installato Azure AD PowerShell. Per informazioni sull'installazione di Azure AD PowerShell, vedere [questo articolo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

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

**Soluzione:** seguire questa procedura per ripristinare Domain Services nella directory:

1. Passare alla [pagina Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nel portale di Azure.
2. Scegliere la sottoscrizione dalla tabella associata al dominio gestito.
3. Usando il menu di spostamento a sinistra, scegliere **Provider di risorse**.
4. Cercare "Microsoft.AAD" nella tabella e fare clic su **Ripeti registrazione**.
5. Per verificare che l'avviso sia stato risolto, visualizzare la pagina relativa all'integrità del dominio gestito dopo due ore.


## <a name="service-principals-that-self-correct"></a>Entità servizio che si correggono automaticamente
Seguire questa procedura se un'entità servizio con ID ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` non è presente nella directory di Azure AD.

**Soluzione:** Azure AD Domain Services è in grado di rilevare quando questa entità servizio non è presente, è configurata in modo errato o è stata eliminata. Il servizio ricrea automaticamente questa entità servizio. Tuttavia, sarà necessario eliminare l'applicazione e l'oggetto che ha interagito con l'applicazione eliminata, perché dopo il rollover della certificazione, l'applicazione e l'oggetto non potranno più essere modificati dalla nuova entità servizio. Ciò causerà un nuovo errore nel dominio. Per evitare questo problema, seguire la procedura descritta nella [sezione per AADDS105](#alert-aadds105-password-synchronization-application-is-out-of-date). Controllare poi l'integrità del dominio gestito dopo due ore per verificare che la nuova entità servizio sia stata ricreata.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Avviso AADDS105: L'applicazione di sincronizzazione delle password non è aggiornata

**Messaggio di avviso:** The service principal with the application ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" was deleted, and Microsoft was able to recreate it (L'entità servizio con ID applicazione "d87dcbc6-a371-462e-88e3-28ad15ec4e64" è stata eliminata e Microsoft è stata in grado di ricrearla). Questa entità servizio gestisce un'altra entità servizio e un'applicazione che vengono usate per la sincronizzazione delle password. L'entità servizio e l'applicazione gestite non sono autorizzate in base alla nuova entità servizio creata e diventeranno obsolete alla scadenza del certificato di sincronizzazione. Questo significa che la nuova entità servizio creata non sarà in grado di aggiornare le applicazioni gestite precedenti con effetti sulla sincronizzazione degli oggetti da Azure Active Directory.


**Soluzione:** per eseguire questa procedura, è necessario avere installato Azure AD PowerShell. Per informazioni sull'installazione di Azure AD PowerShell, vedere [questo articolo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Per risolvere questo problema, digitare i comandi seguenti in una finestra di PowerShell:
1. Installare e importare il modulo Azure AD PowerShell.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Eliminare l'applicazione e l'oggetto precedenti usando i comandi seguenti di PowerShell

    ```powershell
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Dopo aver eliminato entrambi, il sistema esegue automaticamente la correzione e ricrea le applicazioni necessarie per la sincronizzazione delle password. Per assicurarsi che la condizione di avviso sia stata corretta, aspettare due ore e controllare l'integrità del dominio.


## <a name="contact-us"></a>Contattaci
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
