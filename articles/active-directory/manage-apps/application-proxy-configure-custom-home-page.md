---
title: Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD | Microsoft Docs
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
ms.date: 04/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fa5c5638da390f4416afc9f4bd9c5d58c34cea8
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825582"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD

Questo articolo illustra come configurare un'app per reindirizzare l'utente a una home page personalizzata, che possono variare a seconda se sono interni o esterni. Quando si pubblica un'app con il Proxy di applicazione, si imposta l'URL interno, ma in alcuni casi che non è la pagina che verrà visualizzato prima di tutto un utente. Impostare una home page personalizzata in modo che un utente ottiene la pagina corretta quando accedono all'app. Un utente visualizzerà la home page personalizzata che è impostato, indipendentemente dal fatto che accedano all'app dal Pannello di accesso Azure Active Directory o l'utilità di avvio di app di Office 365.

Quando un utente avvia l'app, vengono indirizzati per impostazione predefinita per l'URL del dominio radice per l'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Usare il modulo di PowerShell per Azure AD per definire un URL della home page personalizzata quando si desidera che un utente dell'app a una pagina specifica all'interno dell'app.

Uno scenario di seguito che spiega il motivo per cui la società potrebbe impostare una home page personalizzata e il motivo per cui sarebbe diverso a seconda del tipo di utente:

- Poiché si dispone di altre risorse (ad esempio immagini) che il Proxy di applicazione richiede l'accesso al livello superiore della struttura di cartelle, pubblicare l'app con `https://ExpenseApp` come URL interno.
- Tuttavia, all'interno della rete aziendale, un utente accede al `https://ExpenseApp/login/login.aspx` Accedi e accedere all'app.
- L'URL esterno predefinito è `https://ExpenseApp-contoso.msappproxy.net`, che non richiede un utente esterno alla pagina di accesso.
- Si desidera impostare `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` come URL della home page esterno al contrario, pertanto, un utente esterno vede la pagina di accesso prima di tutto.

>[!NOTE]
>Quando si fornisce agli utenti l'accesso alle app pubblicate, le app vengono visualizzate nel [pannello di accesso di Azure AD](../user-help/my-apps-portal-end-user-access.md) e nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Prima di iniziare

Prima di impostare l'URL della home page, tenere presente i requisiti presenti:

- Il percorso specificato deve essere un percorso di sottodominio dell'URL del dominio radice.

  Ad esempio, se è l'URL del dominio radice `https://apps.contoso.com/app1/`, l'URL della home page configurato deve iniziare con `https://apps.contoso.com/app1/`.

- Se si apporta una modifica all'app pubblicata, la modifica potrebbe reimpostare il valore dell'URL della home page. Quando si decide di aggiornare l'app è quindi necessario verificare ed eventualmente aggiornare l'URL della home page.

È possibile modificare la pagina home interna o esterna tramite il portale di Azure o tramite PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Cambiare la home page nel portale di Azure

Per modificare le pagine home esterne e interne della tua app tramite il portale di Azure AD, seguire questa procedura:

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/). Viene visualizzato il dashboard del centro di amministrazione di Azure Active Directory.
2. Nella barra laterale, selezionare **Azure Active Directory**. Viene visualizzata la pagina di panoramica di Azure AD.
3. Nella barra laterale di panoramica, selezionare **registrazioni per l'App**. Verrà visualizzato l'elenco delle App registrate.
4. Scegliere l'app dall'elenco. Verrà visualizzata una pagina che visualizza i dettagli dell'app registrata.
5. Selezionare il collegamento sotto **URI di reindirizzamento**, che consente di visualizzare il numero degli URI di reindirizzamento per il web e i tipi di client pubblici. Viene visualizzata la pagina di autenticazione per l'app registrata.
6. Nell'ultima riga del **Redirect URIs** di tabella, impostare il **tipo** colonna **client pubblico (per dispositivi mobili e desktop)** e nel **URI di reindirizzamento**colonna, digitare l'URL interno da usare. Viene visualizzata una nuova riga vuota sotto la riga che è stato appena modificato.
7. Nella nuova riga, impostare il **tipo** colonna **Web**e nel **URI di reindirizzamento** colonna, digitare l'URL esterno da usare.
8. Se si desidera eliminare tutte le righe URI di reindirizzamento esistente, selezionare la **eliminare** icona (un Bidone della spazzatura) accanto a ogni riga indesiderata.
9. Selezionare **Salva**.

## <a name="change-the-home-page-with-powershell"></a>Modificare la home page con PowerShell

Per configurare la home page di un'app tramite PowerShell, è necessario:

1. Installare il modulo Azure AD PowerShell.
2. Trovare il valore ObjectId dell'app.
3. Aggiornare l'URL della home page dell'app usando i comandi di PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Prima di definire l'URL di una home page personalizzata tramite PowerShell è necessario installare il modulo Azure AD PowerShell. È possibile scaricare il pacchetto da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), che usa l'endpoint API Graph.

Per installare il pacchetto, seguire questa procedura:

1. Aprire una finestra di PowerShell standard ed eseguire questo comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se il comando non viene eseguito come amministratore, usare l'opzione `-scope currentuser`.

2. Durante l'installazione selezionare **Y** per installare due pacchetti da Nuget.org. Sono necessari entrambi i pacchetti.

### <a name="find-the-objectid-of-the-app"></a>Trovare il valore ObjectId dell'app

Si ottiene il valore ObjectId dell'app eseguendo una ricerca per l'app in base al relativo nome visualizzato o home page di.

1. Nella stessa finestra di PowerShell importare il modulo di Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

2. Accedere al modulo di Azure AD come amministratore del tenant.

   ```powershell
   Connect-AzureAD
   ```

3. Individuare l'app. Questo esempio Usa PowerShell per trovare il valore ObjectId eseguendo una ricerca per l'app con un nome visualizzato del `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Viene visualizzato un risultato simile a quello illustrato di seguito. Copiare il GUID di ObjectId da usare nella sezione successiva.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   In alternativa, è possibile semplicemente recuperare l'elenco di tutte le app, eseguire ricerche nell'elenco per l'app con un nome specifico di visualizzazione o la home page e copiare ObjectId dell'app dopo aver trovato l'app.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aggiornare l'URL della home page

Creare l'URL della home page e aggiornare l'app con tale valore. Continuare a usare la stessa finestra di PowerShell, o se si usa una nuova finestra di PowerShell, accedere al modulo di Azure AD usando `Connect-AzureAD`. Seguire quindi questa procedura:

1. Creare una variabile per contenere il valore di ObjectId copiato nella sezione precedente. (Sostituire il valore di ObjectId usato per questo esempio di SharePoint con il valore ObjectId dell'app).

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. Verificare di avere l'app corretta eseguendo il comando seguente. L'output deve essere identico a quello illustrato nella sezione precedente ([trovare il valore ObjectId dell'app](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. Creare un oggetto applicazione vuoto per le modifiche da apportare.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. Impostare l'URL della home page sul valore desiderato. Il valore deve essere un percorso di sottodominio dell'app pubblicata. Se, ad esempio, si modifica l'URL della home page da `https://sharepoint-iddemo.msappproxy.net/` a `https://sharepoint-iddemo.msappproxy.net/hybrid/`, gli utenti dell'app passano direttamente alla home page personalizzata.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. Eseguire l'aggiornamento della home page.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. Per verificare che la modifica sia riuscita, eseguire nuovamente il comando seguente dal passaggio 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Per questo esempio, l'output dovrebbe ora apparire come segue:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. Riavviare l'app per verificare che la home page viene visualizzato come prima schermata, come previsto.

>[!NOTE]
>Le modifiche apportate all'app possono reimpostare l'URL della home page. Se l'URL della home page si reimposta, ripetere la procedura in questa sezione per impostarlo di nuovo.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'accesso remoto a SharePoint con il proxy applicazione di Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Esercitazione: Aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md)