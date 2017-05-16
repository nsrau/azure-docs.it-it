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
ms.date: 04/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 31e8e39580ed83f13fd3ffb9981221765063a0b7
ms.lasthandoff: 04/21/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD

Questo articolo illustra come configurare le app in modo che indirizzino gli utenti a una home page personalizzata quando gli utenti accedono alle app dal pannello di accesso di Azure Active Directory (Azure AD) e dall'icona di avvio delle app di Office 365.

Quando gli utenti avviano le app, per impostazione predefinita vengono indirizzati all'URL del dominio radice dell'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Ad esempio, per l'applicazione back-end http://ExpenseApp, l'URL viene pubblicato come *https://expenseApp-contoso.msappproxy.net*. Per impostazione predefinita, l'URL della home page è impostato su *https://expenseApp-contoso.msappproxy.net*.

Tramite il modulo Azure AD PowerShell è possibile definire URL della home page personalizzata per i casi in cui si vuole che gli utenti accedano a una pagina specifica all'interno dell'app, ad esempio *https://expenseApp-contoso.msappproxy.net/login/login.aspx*.

>[!NOTE]
>Quando si fornisce agli utenti l'accesso alle app pubblicate, le app vengono visualizzate nel [pannello di accesso di Azure AD](active-directory-saas-access-panel-introduction.md) e nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Prima di iniziare

### <a name="determine-the-home-page-url"></a>Determinare l'URL della home page

Prima di impostare l'URL della home page, tenere presente quanto segue:

* Assicurarsi che il percorso specificato sia un percorso di sottodominio dell'URL del dominio radice.

  Se l'URL del dominio radice è https://apps.contoso.com/app1/, l'URL della home page configurato deve iniziare con https://apps.contoso.com/app1/.

* Se si apporta una modifica all'app pubblicata, la modifica potrebbe reimpostare il valore dell'URL della home page. Quando si decide di aggiornare l'app è quindi necessario verificare ed eventualmente aggiornare l'URL della home page.

### <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Prima di definire l'URL di una home page personalizzata tramite PowerShell è necessario installare un pacchetto non standard del modulo Azure AD PowerShell. È possibile scaricare il pacchetto da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), che usa l'endpoint API Graph. 

Per installare il pacchetto, seguire questa procedura:

1. Aprire una finestra di PowerShell standard ed eseguire questo comando:

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    Se il comando non viene eseguito come amministratore, usare l'opzione `-scope currentuser`.
2. Durante l'installazione selezionare **Y** per installare due pacchetti da Nuget.org. Sono necessari entrambi i pacchetti. 

## <a name="step-1-find-the-objectid-of-the-app"></a>Passaggio 1: trovare il valore ObjectID dell'app

Ottenere il valore ObjectID dell'app e quindi cercare l'app tramite la relativa home page.

1. Aprire PowerShell e importare il modulo di Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Accedere al modulo di Azure AD come amministratore del tenant.

    ```
    Connect-AzureAD
    ```
3. Individuare l'app in base all'URL della home page. È possibile trovare l'URL nel portale passando ad **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**. Questo esempio usa *sharepoint-iddemo*.

    ```
    Get-AzureADApplications | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Viene visualizzato un risultato simile a quello illustrato di seguito. Copiare il GUID di ObjectID da usare nella sezione successiva.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

## <a name="step-2-update-the-home-page-url"></a>Passaggio 2: Aggiornare l'URL della home page

Nello stesso modulo PowerShell usato per il passaggio 1, procedere come segue:

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
>Le modifiche apportate all'app possono reimpostare l'URL della home page. In questo caso, ripetere il passaggio 2.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md)

