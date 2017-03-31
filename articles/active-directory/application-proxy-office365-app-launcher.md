---
title: Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD | Microsoft Docs
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5818efb315a0452beea03cde1adc657a9520dcac
ms.openlocfilehash: 1fe3f3a697618bec5d314c6ebf161da37efc1346
ms.lasthandoff: 03/01/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD

Questo articolo illustra come configurare le app in modo che indirizzino gli utenti a una home page personalizzata quando gli utenti accedono alle app dal pannello di accesso di Azure Active Directory (Azure AD) e dall'icona di avvio delle app di Office 365.

>[!NOTE]
>Il proxy applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
>

Usando il modulo Azure AD PowerShell è possibile definire l'URL della home page personalizzata per i casi in cui si vuole che gli utenti accedano a una pagina specifica all'interno dell'app, ad esempio *https://expenseApp-contoso.msappproxy.net/login/login.aspx*.

>[!NOTE]
>Quando si fornisce agli utenti l'accesso alle app pubblicate, le app vengono visualizzate nel [pannello di accesso di Azure AD](active-directory-saas-access-panel-introduction.md) e nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).
>

Quando gli utenti avviano le app, per impostazione predefinita vengono indirizzati all'URL del dominio radice dell'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Ad esempio, per l'applicazione back-end http://ExpenseApp, l'URL viene pubblicato come *https://expenseApp-contoso.msappproxy.net*. Per impostazione predefinita, l'URL della home page è impostato su *https://expenseApp-contoso.msappproxy.net*.

## <a name="determine-the-home-page-url"></a>Determinare l'URL della home page

Prima di impostare l'URL della home page, tenere presente quanto segue:

* Assicurarsi che il percorso specificato sia un percorso di sottodominio dell'URL del dominio radice.

 Se ad esempio l'app pubblicata è accessibile dall'URL del dominio radice https://intranet-contoso.msappproxy.net/, l'URL della home page configurato deve iniziare con https://intranet-contoso.msappproxy.net/.

* Se l'URL del dominio radice è https://apps.contoso.com/app1/, l'URL della home page configurato deve iniziare con https://apps.contoso.com/app1/.

* Se si apporta una modifica all'app pubblicata, la modifica potrebbe reimpostare il valore dell'URL della home page. Quando si decide di aggiornare l'app è quindi necessario verificare ed eventualmente aggiornare l'URL della home page.

Nella sezione successiva verrà descritta dettagliatamente la configurazione dell'URL di una home page personalizzata per l'app pubblicata. 

## <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Prima di definire l'URL di una home page personalizzata tramite PowerShell è necessario installare un pacchetto non standard del modulo Azure AD PowerShell. È possibile scaricare il pacchetto da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), che usa l'endpoint API Graph. 

Per installare il pacchetto usando PowerShell procedere come segue:

1. Aprire una finestra di PowerShell standard ed eseguire questo comando:

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    Se il comando non viene eseguito come amministratore, è necessario usare l'opzione **-scope currentuser**.
2. Durante l'installazione selezionare **Y** per installare due pacchetti da Nuget.org. Sono necessari entrambi i pacchetti. 

## <a name="set-a-custom-home-page-url-by-using-the-azure-ad-powershell-module"></a>Impostare l'URL di una home page personalizzata tramite il modulo Azure AD PowerShell

Ora che è stato installato il modulo Azure AD PowerShell, è possibile impostare l'URL della home page. Seguire le istruzioni delle due sezioni successive.

### <a name="step-1-find-the-objectid-of-the-app"></a>Passaggio 1: trovare il valore ObjectID dell'app

Ottenere il valore ObjectID dell'app e quindi cercare l'app tramite la relativa home page.

1. Aprire PowerShell e importare il modulo Azure AD usando questo comando:

    ```
    Import-Module AzureAD
    ```

2. Accedere al modulo di Azure AD tramite il cmdlet seguente e quindi seguire le istruzioni visualizzate sullo schermo. Eseguire l'accesso come amministratore del tenant.

    ```
    Connect-AzureAD
    ```
3. Usare questo cmdlet per trovare l'app in base alle home page contenente *sharepoint iddemo*. Per modificare l'app, sostituire il valore seguente con il valore corretto per l'app.

    ```
    Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
    ```
4. Viene visualizzato un risultato simile a quello illustrato di seguito. Copiare il GUID (ObjectID) per usarlo in "Passaggio 2: aggiornare l'URL della home page".

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="step-2-update-the-home-page-url"></a>Passaggio 2: aggiornare l'URL della home page

Nello stesso modulo di PowerShell usato in "Passaggio 1: trovare il valore ObjectID dell'app" procedere come segue:

1. Verificare che l'app sia corretta e sostituire *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* con il valore GUID (ObjectID) copiato nel passaggio precedente.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Ora che è stata verificata l'app, è possibile aggiornare la home page come indicato di seguito.

2. Creare un oggetto applicazione vuoto per le modifiche da apportare.  

 >[!NOTE]
 >Questa è solo una variabile che dovrà contenere i valori da aggiornare, perciò non è stato effettivamente creato nulla.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Impostare l'URL della home page sul valore desiderato. Il valore deve essere un percorso di sottodominio dell'app pubblicata. Se ad esempio si modifica l'URL della home page da *https://sharepoint-iddemo.msappproxy.net/* a *https://sharepoint-iddemo.msappproxy.net/hybrid/*, gli utenti verranno indirizzati direttamente alla home page personalizzata.

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Eseguire l'aggiornamento usando il GUID (ObjectID) copiato in "Passaggio 1: trovare il valore ObjectID dell'app".

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. Verificare che la modifica sia stata completata riavviando l'app.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Le modifiche apportate all'app possono reimpostare l'URL della home page. In questo caso, ripetere il processo illustrato in "Passaggio 2: aggiornare l'URL della home page".

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Abilitare il proxy applicazione nel portale di Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)

