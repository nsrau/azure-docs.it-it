---
title: Impostare una home page personalizzata per l&quot;applicazione pubblicata tramite il proxy applicazione Azure AD | Documentazione Microsoft
description: Tratta i fondamenti dei connettori del proxy applicazione Azure AD.
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
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Impostare una home page personalizzata per l'applicazione pubblicata tramite il proxy applicazione Azure AD

Questo articolo illustra come configurare l'applicazione in modo che indirizzi gli utenti a una home page personalizzata quando accedono all'applicazione dal pannello di accesso di Azure AD e da Utilità di avvio app di Office 365.

>[!NOTE]
>Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).
> 
 
Usando il modulo Azure AD Powershell, è possibile definire l'URL della home page personalizzata per i casi in cui si vuole che gli utenti accedano a una pagina specifica all'interno dell'applicazione, ad esempio https://expenseApp-contoso.msappproxy.net/login/login.aspx.

>[!NOTE]
>Quando si fornisce agli utenti l'accesso alle applicazioni pubblicate, le app sono visualizzate nel [pannello di accesso di Azure AD](active-directory-saas-access-panel-introduction.md) e in [Utilità di avvio app di Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher). 
>

Quando gli utenti avviano le app, per impostazione predefinita vengono indirizzati all'URL del dominio radice per l'app pubblicata. La pagina di destinazione viene impostata in genere sull'URL della home page. Ad esempio, per questa applicazione back-end http://ExpenseApp, viene pubblicata come https://expenseApp-contoso.msappproxy.net. Per impostazione predefinita l'URL della gome page è impostato su https://expenseApp-contoso.msappproxy.net.

## <a name="determine-the-home-page-url"></a>Determinare l'URL della home page

Esistono diversi aspetti da tenere in considerazione prima di impostare l'URL della home page:

* È necessario assicurarsi che il percorso specificato sia un percorso di sottodominio dell'URL del dominio radice.

 Ad esempio, se l'app pubblicata è accessibile dall'URL della home page https://intranet-contoso.msappproxy.net/, l'URL della home page che si configura deve iniziare con https://intranet-contoso.msappproxy.net/. 
 
* Se l'URL della home page è https://apps.contoso.com/app1/, l'URL della home page deve iniziare con https://apps.contoso.com/app1/.

* Se si apporta una modifica all'applicazione pubblicata, potrebbe essere reimpostato il valore dell'URL della home page. Pertanto, quando si decide di aggiornare l'applicazione, si deve verificare ed eventualmente aggiornare l'URL della home page.


Nella sezione successiva si vedrà come configurare l'URL della home page personalizzata per le applicazioni pubblicate. 

## <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD Powershell

Prima di poter definire un URL di home page personalizzata tramite Powershell è necessario installare un pacchetto non standard del modulo Azure AD PowerShell.  È possibile scaricare il pacchetto da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0) che usa l'endpoint dell'API Graph. 

**Per installare il pacchetto mediante Powershell:**

1. Aprire PowerShell standard.
2. Eseguire il comando seguente:

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 Se non lo si esegue come amministratore, è necessario usare l'opzione _-scope currentuser_.
3. Durante l'installazione selezionare "Y" per installare due pacchetti da Nuget.org.  Questi sono entrambi necessari per usare il pacchetto. 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Impostare l'URL della home page personalizzata mediante il modulo Azure AD Powershell

Ora che è stato installato il modulo Azure AD Powershell, è possibile impostare l'URL della home page con due semplici passaggi.

1. Trovare l'applicazione che si vuole aggiornare.
2. Aggiornare l'URL della home page dell'applicazione.

###<a name="step-1--find-the-objectid-of-the-application"></a>Passaggio 1: Trovare il valore ObjectID dell'applicazione

È necessario innanzitutto ottenere il valore ObjectID dell'applicazione e quindi cercare l'applicazione usando la home page.

1. Aprire PowerShell.
2. Installare il modulo Azure AD.
  
 ```
 Import-Module AzureAD
 ```
3. Accedere al modulo Azure AD.  Usare il cmdlet riportato di seguito e seguire le istruzioni sullo schermo. Assicurarsi di eseguire l'accesso come amministratore del tenant.
 
 ```
 Connect-AzureAD
 ```
4. Il cmdlet seguente trova le applicazioni in base alla home page contenente _sharepoint-iddemo_. Si tratta dell'applicazione che si vuole modificare. È necessario sostituire questo valore con il valore appropriato per l'applicazione.
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. Sarà visualizzato un risultato simile alla risposta seguente. Il GUID (il valore ObjectID riportato di seguito) è l'elemento che sarà necessario copiare.
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. Copiare il valore GUID (ObjectID). Servirà per il passaggio successivo.


###<a name="step-2--update-the-homepage-url"></a>Passaggio 2: Aggiornare l'URL della home page

Si usa lo stesso modulo PowerShell per aggiornare l'URL della home page utilizzato per trovare l'ID dell'applicazione. Dopo l'accesso a PowerShell, attenersi alla procedura seguente:

1. Verificare di avere l'applicazione corretta e sostituire _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ con il valore ObjectID dall'applicazione che è stato copiato al passaggio 1. 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 Ora che l'applicazione è confermata, è possibile aggiornare la home page come indicato di seguito.
 
2. Creare un oggetto applicazione vuoto per le modifiche da apportare. Questa è solo una variabile che dovrà contenere i valori da aggiornare, perciò non è stato effettivamente creato nulla.
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. Impostare la home page sul valore desiderato. Tenere presente che deve trattarsi di un percorso di sottodominio dell'applicazione pubblicata. Ad esempio, se si cambia la home page da _https://sharepoint-iddemo.msappproxy.net/_ a _https://sharepoint-iddemo.msappproxy.net/hybrid/_, gli utenti andranno direttamente alla home page personalizzata.
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. L'ultima operazione da eseguire è l'aggiornamento. Ricordarsi di usare il GUID copiato al passaggio 1.
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. A questo punto è necessario confermare la home page personalizzata avviando nuovamente l'applicazione per verificare che la modifica sia riuscita.
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>Tenere presente che le modifiche apportate all'applicazione possono reimpostare l'URL della home page. In questo caso sarà necessario ripetere la procedura.

##<a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso remoto a SharePoint con il proxy applicazione Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Abilitare il proxy applicazione nel portale di Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


