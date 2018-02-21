---
title: Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD | Microsoft Docs
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: d31100015a11bc57d488f1d6af93db8526e38968
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD

In questo articolo viene illustrato come configurare le app per indirizzare gli utenti a una home page personalizzata. Quando si pubblica un'applicazione con Proxy dell'applicazione, si imposta un URL interno ma a volte non è la pagina che gli utenti dovrebbero vedere per prima. Impostare una home page personalizzata in modo che gli utenti visualizzino la pagina corretta quando accedono alle app. Gli utenti vedranno la home page personalizzata impostata sia che accedano all'app dal riquadro di accesso di Azure Active Directory che dall'icona di avvio delle app di Office 365.

Quando gli utenti avviano le app, per impostazione predefinita vengono indirizzati all'URL del dominio radice dell'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Usare il modulo PowerShell di Azure AD per definire l'URL della home page personalizzata quando si desidera che gli utenti dell'app arrivino ad una pagina specifica all'interno dell'app. 

Ecco un esempio del motivo per cui una società potrebbe impostare una home page personalizzata:
- All'interno della rete aziendale, gli utenti passano a *https://ExpenseApp/login/login.aspx* per registrarsi e accedere all'app.
- Poiché si dispone di altre risorse come le immagini a cui il Proxy dell'applicazione deve accedere al livello superiore della struttura della cartella, si pubblica l'app con *https://ExpenseApp* come URL interno.
- L'URL esterno predefinito è *https://ExpenseApp-contoso.msappproxy.net*, che non porta gli utenti alla pagina di accesso.  
- Impostare *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* come URL della home page. 

>[!NOTE]
>Quando si fornisce agli utenti l'accesso alle app pubblicate, le app vengono visualizzate nel [pannello di accesso di Azure AD](active-directory-saas-access-panel-introduction.md) e nell'[icona di avvio delle app di Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Prima di iniziare

Prima di impostare l'URL della home page, tenere presente i requisiti presenti:

* Assicurarsi che il percorso specificato sia un percorso di sottodominio dell'URL del dominio radice.

  Se l'URL del dominio radice è https://apps.contoso.com/app1/, l'URL della home page configurato deve iniziare con https://apps.contoso.com/app1/.

* Se si apporta una modifica all'app pubblicata, la modifica potrebbe reimpostare il valore dell'URL della home page. Quando si decide di aggiornare l'app è quindi necessario verificare ed eventualmente aggiornare l'URL della home page.

## <a name="change-the-home-page-in-the-azure-portal"></a>Cambiare la home page nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Registrazioni per l'app** e scegliere l'applicazione dall'elenco. 
3. Selezionare **Proprietà** dalle impostazioni.
4. Aggiornare il campo **URL della home page** con il nuovo percorso. 

   ![Fornire nuovi URL della home page](./media/application-proxy-office365-app-launcher/homepage.png)

5. Selezionare **Salva**

## <a name="change-the-home-page-with-powershell"></a>Modificare la home page con PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Prima di definire l'URL di una home page personalizzata tramite PowerShell è necessario installare il modulo Azure AD PowerShell. È possibile scaricare il pacchetto da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), che usa l'endpoint API Graph. 

Per installare il pacchetto, seguire questa procedura:

1. Aprire una finestra di PowerShell standard ed eseguire questo comando:

    ```
     Install-Module -Name AzureAD
    ```
    Se il comando non viene eseguito come amministratore, usare l'opzione `-scope currentuser`.
2. Durante l'installazione selezionare **Y** per installare due pacchetti da Nuget.org. Sono necessari entrambi i pacchetti. 

### <a name="find-the-objectid-of-the-app"></a>Trovare il valore ObjectID dell'app

Ottenere il valore ObjectID dell'app e quindi cercare l'app tramite la relativa home page.

1. Nella stessa finestra di PowerShell importare il modulo di Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Accedere al modulo di Azure AD come amministratore del tenant.

    ```
    Connect-AzureAD
    ```
3. Individuare l'app in base all'URL della home page. È possibile trovare l'URL nel portale passando ad **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**. Questo esempio usa *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Viene visualizzato un risultato simile a quello illustrato di seguito. Copiare il GUID di ObjectID da usare nella sezione successiva.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Aggiornare l'URL della home page

Creare l'URL della home page e aggiornare l'applicazione con tale valore. Continuare a usare la stessa finestra di PowerShell per eseguire questi comandi. In alternativa, se si usa una nuova finestra di PowerShell, accedere di nuovo al modulo di Azure AD usando `Connect-AzureAD`. 

1. Verificare che l'app sia corretta e sostituire *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* con il valore di ObjectID copiato nella sezione precedente.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Ora che è stata verificata l'app, è possibile aggiornare la home page come indicato di seguito.

2. Creare un oggetto applicazione vuoto per le modifiche da apportare. Questa variabile contiene i valori che si desidera aggiornare. Non viene creato nulla in questo passaggio.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Impostare l'URL della home page sul valore desiderato. Il valore deve essere un percorso di sottodominio dell'app pubblicata. Se ad esempio si modifica l'URL della home page da *https://sharepoint-iddemo.msappproxy.net/* a *https://sharepoint-iddemo.msappproxy.net/hybrid/*, gli utenti vengono indirizzati direttamente alla home page personalizzata.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Eseguire l'aggiornamento usando il GUID (ObjectID) copiato in "Passaggio 1: trovare il valore ObjectID dell'app".

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Verificare che la modifica sia stata completata riavviando l'app.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Le modifiche apportate all'app possono reimpostare l'URL della home page. Se l'URL della home page si reimposta, ripetere la procedura in questa sezione per impostarlo di nuovo.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md)
