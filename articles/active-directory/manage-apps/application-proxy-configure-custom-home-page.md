---
title: Home page personalizzati per le app pubblicate-Azure AD proxy di applicazione
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275605"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD

Questo articolo illustra come configurare un'app per indirizzare un utente a una home page personalizzata. Quando si pubblica un'app con il proxy di applicazione, si imposta un URL interno, ma a volte non è la pagina che un utente dovrebbe visualizzare per primo. Impostare un home page personalizzato in modo che un utente ottenga la pagina corretta quando accede all'app. Un utente visualizzerà i home page personalizzati impostati, indipendentemente dal fatto che accedano all'app dal pannello di accesso Azure Active Directory o dall'utilità di avvio delle app di Office 365.

Quando un utente avvia l'app, viene indirizzata per impostazione predefinita all'URL del dominio radice per l'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Usare il modulo Azure AD PowerShell per definire un URL home page personalizzato quando si vuole che un utente dell'app si trovi in una pagina specifica all'interno dell'app.

Ecco uno scenario che spiega perché l'azienda potrebbe impostare un home page personalizzato:

- All'interno della rete aziendale, un utente passa a `https://ExpenseApp/login/login.aspx` per accedere all'app.
- Poiché sono presenti altre risorse (ad esempio immagini) a cui il proxy di applicazione deve accedere al livello superiore della struttura di cartelle, l'app viene pubblicata con `https://ExpenseApp` come URL interno.
- L'URL esterno predefinito è `https://ExpenseApp-contoso.msappproxy.net`, che non porta un utente esterno alla pagina di accesso.
- Si desidera impostare `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` come URL home page, quindi un utente esterno Visualizza prima la pagina di accesso.

> [!NOTE]
> Quando si fornisce agli utenti l'accesso alle app pubblicate, le app vengono visualizzate nel [pannello di accesso di Azure AD](../user-help/my-apps-portal-end-user-access.md) e nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Prima di iniziare

Prima di impostare l'URL della home page, tenere presente i requisiti presenti:

- Il percorso specificato deve essere un percorso di sottodominio dell'URL del dominio radice.

  Ad esempio, se l'URL del dominio radice è `https://apps.contoso.com/app1/`, l'URL home page configurato deve iniziare con `https://apps.contoso.com/app1/`.

- Se si apporta una modifica all'app pubblicata, la modifica potrebbe reimpostare il valore dell'URL della home page. Quando si decide di aggiornare l'app è quindi necessario verificare ed eventualmente aggiornare l'URL della home page.

È possibile impostare l'URL home page tramite la portale di Azure o tramite PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Cambiare la home page nel portale di Azure

Per modificare l'URL di home page dell'app tramite il portale di Azure AD, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore.
1. Selezionare **Azure Active Directory**, quindi **registrazioni app**. Viene visualizzato l'elenco delle app registrate.
1. Scegli l'app dall'elenco. Viene visualizzata una pagina che mostra i dettagli dell'app registrata.
1. In **Gestisci**selezionare **personalizzazione**.
1. Aggiornare l' **URL della Home page** con il nuovo percorso.

   ![Pagina di personalizzazione per un'app registrata che mostra il campo URL della Home page](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selezionare **Salva**.

## <a name="change-the-home-page-with-powershell"></a>Modificare la home page con PowerShell

Per configurare la home page di un'app usando PowerShell, è necessario:

1. Installare il modulo Azure AD PowerShell.
1. Trovare il valore ObjectId dell'app.
1. Aggiornare l'URL di home page dell'app usando i comandi di PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Prima di definire l'URL di una home page personalizzata tramite PowerShell è necessario installare il modulo Azure AD PowerShell. È possibile scaricare il pacchetto dalla [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), che usa l'endpoint di API Graph.

Per installare il pacchetto, seguire questa procedura:

1. Aprire una finestra di PowerShell standard ed eseguire questo comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se il comando non viene eseguito come amministratore, usare l'opzione `-scope currentuser`.

1. Durante l'installazione, selezionare **Y** per installare due pacchetti da NuGet.org. Entrambi i pacchetti sono obbligatori.

### <a name="find-the-objectid-of-the-app"></a>Trovare il valore ObjectId dell'app

Per ottenere il valore ObjectId dell'app, cercare l'app in base al relativo nome visualizzato o home page.

1. Nella stessa finestra di PowerShell importare il modulo di Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Accedere al modulo di Azure AD come amministratore del tenant.

   ```powershell
   Connect-AzureAD
   ```

1. Trovare l'app. Questo esempio USA PowerShell per trovare l'ObjectId cercando l'app con il nome visualizzato `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Viene visualizzato un risultato simile a quello illustrato di seguito. Copiare il GUID ObjectId da usare nella sezione successiva.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   In alternativa, è possibile eseguire il pull dell'elenco di tutte le app, cercare l'app con un nome visualizzato specifico o home page e copiare il valore ObjectId dell'app dopo aver trovato l'app.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aggiornare l'URL della home page

Creare l'URL home page e aggiornare l'app con tale valore. Continuare a usare la stessa finestra di PowerShell o, se si usa una nuova finestra di PowerShell, accedere di nuovo al modulo Azure AD usando `Connect-AzureAD`. Seguire quindi questa procedura:

1. Creare una variabile che contenga il valore ObjectId copiato nella sezione precedente. Sostituire il valore ObjectId usato per in questo esempio di SharePoint con il valore ObjectId dell'app.

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Verificare che l'app sia corretta eseguendo il comando seguente. L'output deve essere identico all'output visualizzato nella sezione precedente ([trovare il valore ObjectID dell'app](#find-the-objectid-of-the-app)).

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

1. Eseguire l'aggiornamento del home page.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Per verificare che la modifica sia stata eseguita correttamente, eseguire di nuovo il comando seguente dal passaggio 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Per questo esempio, l'output dovrebbe essere visualizzato come segue:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Riavviare l'app per verificare che il home page venga visualizzato come prima schermata, come previsto.

> [!NOTE]
> Le modifiche apportate all'app possono reimpostare l'URL della home page. Se l'URL della home page si reimposta, ripetere la procedura in questa sezione per impostarlo di nuovo.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md)