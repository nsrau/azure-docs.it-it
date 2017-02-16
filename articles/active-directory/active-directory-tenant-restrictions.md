---
title: Gestire l&quot;accesso alle app cloud limitando i tenant - Azure | Microsoft Docs
description: Come usare le restrizioni dei tenant per gestire gli utenti che possono accedere alle app in base ai tenant di Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c579c0866387a5eff17b4dbfe25a6cb7d1d47700
ms.openlocfilehash: 4adf15e4767344d450b7411733a5d2f09cb9f06b


---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usare le restrizioni dei tenant per gestire l'accesso alle applicazioni cloud SaaS

Le organizzazioni di grandi dimensioni che mettono l'accento sulla sicurezza vogliono spostarsi in servizi cloud come Office 365, ma devono sapere che i propri utenti non saranno in grado di accedere alle risorse non approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio non è efficace in situazioni dove le app SaaS vengono ospitate in un cloud pubblico ed eseguite su nomi di dominio condivisi come outlook.office.com e login.microsoftonline.com. Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

Azure Active Directory è in grado di risolvere questa problematica grazie alle restrizioni dei tenant. Le restrizioni dei tenant consentono alle organizzazioni di controllare l'accesso alle applicazioni cloud SaaS, in base al tenant di Azure AD usato dalle applicazioni per il Single Sign-On. Ad esempio, si supponga di voler consentire l'accesso alle applicazioni Office 365 dell'organizzazione, impedendolo al contempo alle istanze di quelle stesse applicazioni in altre organizzazioni.  

Le restrizioni dei tenant danno alle organizzazioni la facoltà di specificare l'elenco di tenant cui gli utenti possono accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo si concentra sulle restrizioni dei tenant per Office 365, ma la funzionalità dovrebbe funzionare con qualsiasi app cloud SaaS che utilizza protocolli di autenticazione moderna con Azure AD per il Single Sign-On. Se si usano app SaaS con un tenant Azure AD diverso da quello usato da Office 365, assicurarsi che tutti i tenant richiesti siano autorizzati. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-does-it-work"></a>Come funziona?

La soluzione globale è composta dai seguenti elementi: 

1. **Azure AD**: se `Restrict-Access-To-Tenants: <permitted tenant list>` è presente, Azure AD genera unicamente token di sicurezza per i tenant autorizzati. 

2. **Infrastruttura del server proxy locale**: un dispositivo proxy in grado di eseguire ispezioni SSL, configurato per inserire l'intestazione contenente l'elenco di tenant autorizzati nel traffico verso Azure AD. 

3. **Software client**: per supportare le restrizioni dei tenant, il software client deve richiedere i token direttamente da Azure AD, in modo che l'infrastruttura proxy possa intercettare il traffico. Le restrizioni dei tenant sono attualmente supportate nelle applicazioni Office 365 basate su browser e nei client Office dove vengono usate tecniche di autenticazione moderne come OAuth 2.0. 

4. **Autenticazione moderna**: i servizi cloud devono usare un'autenticazione moderna per usare le restrizioni dei tenant e bloccare l'accesso a tutti i tenant non autorizzati. È necessario configurare i servizi cloud di Office 365 affinché possano usare i protocolli di autenticazione moderna per impostazione predefinita. Per le informazioni più aggiornate sul supporto di Office 365 per l'autenticazione moderna, leggere il [relativo documento aggiornato](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Il diagramma seguente illustra il flusso di traffico di alto livello. L'ispezione SSL è necessaria solo per il traffico verso Azure AD e non verso i servizi cloud di Office 365. Questa distinzione è importante perché il volume di traffico per l'autenticazione in Azure AD è in genere molto inferiore rispetto a quello verso applicazioni SaaS come Exchange Online e SharePoint Online.

![Flusso di traffico delle restrizioni dei tenant, diagramma](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Impostare le restrizioni dei tenant

Ci sono due passaggi iniziali per quanto riguarda le restrizioni dei tenant. Il primo consiste nell'assicurarsi che i client possano connettersi agli indirizzi giusti. Il secondo consiste nel configurare l'infrastruttura del proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per usare le restrizioni dei tenant, i client devono potersi connettere ai seguenti URL di Azure AD per l'autenticazione: login.microsoftonline.com, login.microsoft.com e login.windows.net. Inoltre, per accedere a Office 365, i client devono potersi connettere agli URL/FQDN e indirizzi IP definiti in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

La configurazione seguente è necessaria per abilitare le restrizioni dei tenant tramite l'infrastruttura del proxy. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>Prerequisiti

- Il proxy deve poter eseguire l'intercettazione SSL, inserire intestazioni HTTP e filtrare le destinazioni tramite URL/FQDN. 

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni SSL. Ad esempio, se vengono usati i certificati da un'infrastruttura PKI interna, deve essere considerato attendibile il certificato interno dell'autorità di certificazione interna.

- Questa funzionalità è inclusa nelle sottoscrizioni di Office 365, ma se si desidera usare le restrizioni dei tenant per controllare l'accesso ad altre app SaaS, saranno necessarie licenze Premium 1 di Azure AD.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in ingresso a login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Le intestazioni devono includere gli elementi seguenti: 
- Per *Restrict-Access-To-Tenants*, un valore di \<elenco tenant consentiti\>, ovvero un elenco delimitato da virgole contenente i tenant a cui gli utenti possono accedere. È possibile usare qualsiasi dominio registrato con un tenant per individuare il tenant nell'elenco. Ad esempio, per consentire l'accesso ai tenant Contoso e Fabrikam, la coppia nome/valore è simile alla seguente: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Per *Restrict-Access-Context*, un valore ID di directory singola, dichiarando quale tenant imposta le restrizioni dei tenant. Ad esempio, per dichiarare Contoso come tenant di impostazione dei criteri delle restrizioni dei tenant, la coppia nome/valore avrà un aspetto simile al seguente: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> L'ID della directory si trova nel [portale di Azure](https://portal.azure.com). Accedere come amministratore, selezionare **Azure Active Directory**, quindi selezionare **Proprietà**.

Per impedire agli utenti di inserire le proprie intestazioni HTTP con i tenant non approvati, il proxy deve sostituire l'intestazione Restrict-Access-To-Tenants se questa è già presente nella richiesta in ingresso. 

È necessario forzare l'uso del proxy nei client per tutte le richieste a login.microsoftonline.com, login.microsoft.com e login.windows.net. Ad esempio, se vengono usati file PAC per reindirizzare i client all'uso del proxy, gli utenti finali non devono poter modificare o disabilitare tali file.

## <a name="the-user-experience"></a>Esperienza utente

In questa sezione viene illustrata l'esperienza per utenti finali e amministratori.

### <a name="end-user-experience"></a>Esperienza utente finale

Un utente di esempio si trova nella rete Contoso ma tenta di accedere online all'istanza Fabrikam di un'applicazione SaaS condivisa come Outlook. Se Contoso è un tenant non consentito per l'istanza, l'utente visualizza la pagina seguente:

![Pagina di accesso negato per gli utenti in tenant non consentiti](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Esperienza amministratore

La configurazione delle restrizioni dei tenant viene eseguita nell'infrastruttura del proxy aziendale, ma gli amministratori possono accedere direttamente ai report su tali restrizioni nel portale di Azure. Per visualizzare i report, passare alla pagina di panoramica di Azure Active Directory e quindi cercare in "Altre funzionalità".

L'amministratore per il tenant specificato come tenant Restricted-Access-Context può usare questo report per visualizzare tutti gli accessi bloccati a causa dei criteri di restrizioni dei tenant, inclusi l'ID della directory di destinazione e le identità usate.

![Usare il portale di Azure per visualizzare i tentativi di accesso con restrizioni](./media/active-directory-tenant-restrictions/portal-report.png)

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile usare filtri per utenti, applicazioni, client o intervalli di tempo specifici.

## <a name="office-365-support-for-tenant-restrictions"></a>Supporto di Office 365 per le restrizioni dei tenant

Le applicazioni di Office 365 devono soddisfare due criteri per supportare pienamente le restrizioni dei tenant:

1. Il client usato supporta l'autenticazione moderna
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. L'autenticazione moderna è già abilitata per impostazione predefinita in SharePoint Online.

Le restrizioni dei tenant sono attualmente supportate nelle applicazioni Office 365 basate su browser come il portale di Office, Yammer, i siti SharePoint, Outlook sul Web e così via. Per i thick client come Outlook, Skype for Business, Word, Excel, PowerPoint e così via, le restrizioni dei tenant possono essere applicate solo quando si usa l'autenticazione moderna.  

I client Outlook e Skype for Business che supportano l'autenticazione moderna possono comunque usare protocolli legacy con tenant dove l'autenticazione moderna non è abilitata, ignorando di fatto le restrizioni. In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](http://gpsearch.azurewebsites.net/default.aspx?ref=1). In Outlook su piattaforme non Windows e in Skype for Business su tutte le piattaforme, il supporto completo per le restrizioni dei tenant verrà inserito quando sarà abilitata l'autenticazione moderna come predefinita per l'intero servizio.

## <a name="testing"></a>Test

Se si desidera provare la funzione delle restrizioni dei tenant prima di implementarla in tutta l'organizzazione, sono disponibili due opzioni: un approccio basato su host con uno strumento come Fiddler o una pianificazione per fasi delle impostazioni del proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare Fiddler per testare le restrizioni dei tenant, eseguire la procedura seguente:

1.  [Scaricare e installare Fiddler](http://www.telerik.com/fiddler).
2.  Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:
  1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.
  2. Aggiungere le righe seguenti all'inizio della funzione *OnBeforeRequest*. Sostituire il \<dominio del tenant\> con un dominio registrato con il proprio tenant, ad esempio contoso.onmicrosoft.com. Sostituire \<l'ID della directory\> con l'identificatore GUID di Azure AD del proprio tenant.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){
      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
  }
  ```
  >[!NOTE]
  > Il frammento di codice riportato sopra deve essere in un'unica riga. Non ci sono ritorni a capo dopo la parentesi di chiusura.

  Se è necessario consentire più tenant, separare i vari nomi di tenant con le virgole. Ad esempio:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Salvare e chiudere il file CustomRules.

Dopo aver configurato Fiddler, è possibile acquisire il traffico accedendo al menu **File** e selezionando **Capture Traffic** (Acquisisci traffico).

### <a name="staged-rollout-of-proxy-settings"></a>Implementazione per fasi delle impostazioni del proxy

A seconda delle funzionalità dell'infrastruttura di proxy, è possibile eseguire un'implementazione per fasi delle impostazioni agli utenti. Di seguito sono indicate due opzioni generali da tenere in considerazione:

1.  Usare file PAC per indirizzare gli utenti di test a un'infrastruttura di proxy di test, mentre gli utenti normali continuano a utilizzare l'infrastruttura del proxy di produzione.
2.  Alcuni server proxy possono supportare configurazioni diverse usando i gruppi.

Fare riferimento alla documentazione del server proxy per i dettagli specifici.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[autenticazione moderna aggiornata di Office 365](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)



<!--HONumber=Jan17_HO5-->


