---
title: Home page personalizzata per le app pubblicate - Proxy di applicazione di Azure ADCustom home page for published apps - Azure AD Application Proxy
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275605"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD

Questo articolo illustra come configurare un'app per indirizzare un utente a una home page personalizzata. Quando pubblichi un'app con il proxy di applicazione, imposti un URL interno, ma a volte non è la pagina che un utente dovrebbe visualizzare per primo. Impostare una home page personalizzata in modo che un utente ottenga la pagina corretta quando accede all'app. Un utente vedrà la home page personalizzata impostata, indipendentemente dal fatto che acceda all'app dal pannello di accesso di Azure Active Directory o dall'icona di avvio delle app di Office 365.

Quando un utente avvia l'app, viene indirizzato per impostazione predefinita all'URL del dominio radice per l'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Usare il modulo Azure AD PowerShell per definire un URL della home page personalizzato quando si vuole che un utente dell'app aderisca a una pagina specifica all'interno dell'app.

Ecco uno scenario che spiega perché la tua azienda dovrebbe impostare una home page personalizzata:

- All'interno della rete aziendale, un utente `https://ExpenseApp/login/login.aspx` accede e accede all'app.
- Poiché si dispone di altre risorse (ad esempio le immagini) a cui il proxy `https://ExpenseApp` di applicazione deve accedere al livello superiore della struttura di cartelle, si pubblica l'app con l'URL interno.
- L'URL esterno `https://ExpenseApp-contoso.msappproxy.net`predefinito è , che non porta un utente esterno alla pagina di accesso.
- Si desidera `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` impostare come URL della home page, in modo che un utente esterno visualizzi prima la pagina di accesso.

> [!NOTE]
> Quando si fornisce agli utenti l'accesso alle app pubblicate, le app vengono visualizzate nel [pannello di accesso di Azure AD](../user-help/my-apps-portal-end-user-access.md) e nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Prima di iniziare

Prima di impostare l'URL della home page, tenere presente i requisiti presenti:

- Il percorso specificato deve essere un percorso di sottodominio dell'URL del dominio radice.

  Ad esempio, se l'URL `https://apps.contoso.com/app1/`del dominio radice è , `https://apps.contoso.com/app1/`l'URL della home page configurato deve iniziare con .

- Se si apporta una modifica all'app pubblicata, la modifica potrebbe reimpostare il valore dell'URL della home page. Quando si decide di aggiornare l'app è quindi necessario verificare ed eventualmente aggiornare l'URL della home page.

È possibile impostare l'URL della home page tramite il portale di Azure o tramite PowerShell.You can set the home page URL either through the Azure portal or by using PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Cambiare la home page nel portale di Azure

Per modificare l'URL della home page dell'app tramite il portale di Azure AD, eseguire la procedura seguente:

1. Accedere al [portale](https://portal.azure.com/) di Azure come amministratore.
1. Selezionare **Azure Active Directory**e quindi **Registrazioni app**. Viene visualizzato l'elenco delle app registrate.
1. Scegli la tua app dall'elenco. Viene visualizzata una pagina che mostra i dettagli dell'app registrata.
1. In **Gestisci**selezionare **Personalizzazione**.
1. Aggiornare l'URL della **home page** con il nuovo percorso.

   ![Pagina di personalizzazione per un'app registrata che mostra il campo URL pagina iniziale](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selezionare **Salva**.

## <a name="change-the-home-page-with-powershell"></a>Modificare la home page con PowerShell

Per configurare la home page di un'app tramite PowerShell, è necessario:To configure the home page of an app using PowerShell, you need to:

1. Installare il modulo Azure AD PowerShell.
1. Trovare il valore ObjectId dell'app.
1. Aggiornare l'URL della home page dell'app usando i comandi di PowerShell.Update the app's home page URL using PowerShell commands.

### <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Prima di definire l'URL di una home page personalizzata tramite PowerShell è necessario installare il modulo Azure AD PowerShell.È possibile scaricare il pacchetto da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), che usa l'endpoint API Graph.

Per installare il pacchetto, seguire questa procedura:

1. Aprire una finestra di PowerShell standard ed eseguire questo comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se il comando non viene eseguito come amministratore, usare l'opzione `-scope currentuser`.

1. Durante l'installazione, selezionare **Y** per installare due pacchetti da Nuget.org. Entrambi i pacchetti sono obbligatori.

### <a name="find-the-objectid-of-the-app"></a>Trovare l'ObjectId dell'app

È possibile ottenere l'ObjectId dell'app cercando l'app in base al nome visualizzato o alla home page.

1. Nella stessa finestra di PowerShell importare il modulo di Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Accedere al modulo di Azure AD come amministratore del tenant.

   ```powershell
   Connect-AzureAD
   ```

1. Trova l'app. In questo esempio viene utilizzato PowerShell per trovare l'ObjectId cercando l'app con un nome visualizzato di `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Viene visualizzato un risultato simile a quello illustrato di seguito. Copiare il GUID ObjectId da utilizzare nella sezione successiva.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   In alternativa, puoi semplicemente estrarre l'elenco di tutte le app, cercare nell'elenco l'app con un nome visualizzato o una home page specifica e copiare l'ObjectId dell'app una volta trovata l'app.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aggiornare l'URL della home page

Creare l'URL della home page e aggiornare l'app con tale valore. Continuare a usare la stessa finestra di PowerShell oppure, se si usa una `Connect-AzureAD`nuova finestra di PowerShell, accedere di nuovo al modulo di Azure AD usando . Seguire quindi questa procedura:

1. Creare una variabile per contenere il valore ObjectId copiato nella sezione precedente. Sostituisci il valore ObjectId usato in questo esempio di SharePoint con il valore ObjectId dell'app.

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Verificare di disporre dell'app corretta eseguendo il comando seguente. L'output deve essere identico all'output visualizzato nella sezione precedente ([Trovare l'ObjectId dell'app](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Creare un oggetto applicazione vuoto per le modifiche da apportare.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Impostare l'URL della home page sul valore desiderato. Il valore deve essere un percorso di sottodominio dell'app pubblicata. Se, ad esempio, si modifica l'URL della home page da `https://sharepoint-iddemo.msappproxy.net/` a `https://sharepoint-iddemo.msappproxy.net/hybrid/`, gli utenti dell'app passano direttamente alla home page personalizzata.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Eseguire l'aggiornamento della home page.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Per verificare che la modifica sia stata eseguita correttamente, eseguire nuovamente il comando seguente dal passaggio 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Per il nostro esempio, l'output dovrebbe ora apparire come segue:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Riavviare l'app per verificare che la home page venga visualizzata come prima schermata, come previsto.

> [!NOTE]
> Le modifiche apportate all'app possono reimpostare l'URL della home page. Se l'URL della home page si reimposta, ripetere la procedura in questa sezione per impostarlo di nuovo.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active DirectoryTutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)