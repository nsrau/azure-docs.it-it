---
title: Gestire l'accesso alle app cloud limitando i tenant - Azure | Microsoft Docs
description: Come usare Restrizioni dei tenant per gestire gli utenti che possono accedere alle app in base ai tenant di Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: kgremban
ms.openlocfilehash: 63e0fa54433a60fe7384d21cf7d215cc8283afca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usare Restrizioni dei tenant per gestire l'accesso alle applicazioni cloud SaaS

Le organizzazioni di grandi dimensioni che mettono l'accento sulla sicurezza vogliono passare a servizi cloud come Office 365, ma devono sapere che i loro utenti saranno in grado di accedere solo a risorse approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio non è efficace in situazioni dove le app SaaS vengono ospitate in un cloud pubblico ed eseguite su nomi di dominio condivisi come outlook.office.com e login.microsoftonline.com. Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

La soluzione offerta da Azure Active Directory per risolvere questo problema è costituita da una funzionalità denominata Restrizioni dei tenant. Restrizioni dei tenant consente alle organizzazioni di controllare l'accesso alle applicazioni cloud SaaS, in base al tenant di Azure AD usato dalle applicazioni per il Single Sign-On. Ad esempio, si supponga di voler consentire l'accesso alle applicazioni Office 365 dell'organizzazione, impedendolo al contempo alle istanze di quelle stesse applicazioni in altre organizzazioni.  

Restrizioni dei tenant dà alle organizzazioni la facoltà di specificare l'elenco di tenant cui gli utenti possono accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo si concentra su Restrizioni dei tenant per Office 365, ma la funzionalità dovrebbe funzionare con qualsiasi app cloud SaaS che utilizza protocolli di autenticazione moderna con Azure AD per il Single Sign-On. Se si usano app SaaS con un tenant Azure AD diverso da quello usato da Office 365, assicurarsi che tutti i tenant richiesti siano autorizzati. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-it-works"></a>Funzionamento

La soluzione globale è composta dai seguenti elementi: 

1. **Azure AD**: se `Restrict-Access-To-Tenants: <permitted tenant list>` è presente, Azure AD genera unicamente token di sicurezza per i tenant autorizzati. 

2. **Infrastruttura del server proxy locale**: un dispositivo proxy in grado di eseguire ispezioni SSL, configurato per inserire l'intestazione contenente l'elenco di tenant autorizzati nel traffico verso Azure AD. 

3. **Software client**: per supportare Restrizioni dei tenant, il software client deve richiedere i token direttamente da Azure AD, in modo che l'infrastruttura proxy possa intercettare il traffico. Restrizioni dei tenant è attualmente supportata nelle applicazioni Office 365 basate su browser e nei client Office dove vengono usate tecniche di autenticazione moderne come OAuth 2.0. 

4. **Autenticazione moderna**: i servizi cloud devono usare un'autenticazione moderna per usare Restrizioni dei tenant e bloccare l'accesso a tutti i tenant non autorizzati. È necessario configurare i servizi cloud di Office 365 affinché possano usare i protocolli di autenticazione moderna per impostazione predefinita. Per le informazioni più aggiornate sul supporto di Office 365 per l'autenticazione moderna, leggere il [relativo documento aggiornato](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Il diagramma seguente illustra il flusso di traffico di alto livello. L'ispezione SSL è necessaria solo per il traffico verso Azure AD e non verso i servizi cloud di Office 365. Questa distinzione è importante perché il volume di traffico per l'autenticazione in Azure AD è in genere molto inferiore rispetto a quello verso applicazioni SaaS come Exchange Online e SharePoint Online.

![Flusso di traffico di Restrizioni dei tenant, diagramma](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Impostare Restrizioni dei tenant

Ci sono due passaggi iniziali per quanto riguarda Restrizioni dei tenant. Il primo consiste nell'assicurarsi che i client possano connettersi agli indirizzi giusti. Il secondo consiste nel configurare l'infrastruttura del proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per usare Restrizioni dei tenant, i client devono potersi connettere ai seguenti URL di Azure AD per l'autenticazione: login.microsoftonline.com, login.microsoft.com e login.windows.net. Inoltre, per accedere a Office 365, i client devono potersi connettere agli URL/FQDN e indirizzi IP definiti in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

La configurazione seguente è necessaria per abilitare Restrizioni dei tenant tramite l'infrastruttura del proxy. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>prerequisiti

- Il proxy deve poter eseguire l'intercettazione SSL, inserire intestazioni HTTP e filtrare le destinazioni tramite URL/FQDN. 

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni SSL. Ad esempio, se vengono usati i certificati da un'infrastruttura PKI interna, deve essere considerato attendibile il certificato interno dell'autorità di certificazione interna.

- Questa funzionalità è inclusa nelle sottoscrizioni di Office 365, ma se si desidera usare Restrizioni dei tenant per controllare l'accesso ad altre app SaaS, saranno necessarie licenze Premium 1 di Azure AD.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in ingresso a login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Le intestazioni devono includere gli elementi seguenti: 
- Per *Restrict-Access-To-Tenants*, un valore di \<elenco tenant consentiti\>, ovvero un elenco delimitato da virgole contenente i tenant a cui gli utenti possono accedere. È possibile usare qualsiasi dominio registrato con un tenant per individuare il tenant nell'elenco. Ad esempio, per consentire l'accesso ai tenant Contoso e Fabrikam, la coppia nome/valore è simile alla seguente: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Per *Restrict-Access-Context*, un valore ID di directory singola, dichiarando quale tenant imposta Restrizioni dei tenant. Ad esempio, per dichiarare Contoso come tenant di impostazione dei criteri di Restrizioni dei tenant, la coppia nome/valore avrà un aspetto simile al seguente: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

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

La configurazione di Restrizioni dei tenant viene eseguita nell'infrastruttura del proxy aziendale, ma gli amministratori possono accedere direttamente ai relativi report nel portale di Azure. Per visualizzare i report, passare alla pagina di panoramica di Azure Active Directory e quindi cercare in "Altre funzionalità".

L'amministratore per il tenant specificato come tenant Restricted-Access-Context può usare questo report per visualizzare tutti gli accessi bloccati a causa dei criteri di Restrizioni dei tenant, inclusi l'ID della directory di destinazione e le identità usate.

![Usare il portale di Azure per visualizzare i tentativi di accesso con restrizioni](./media/active-directory-tenant-restrictions/portal-report.png)

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile usare filtri per utenti, applicazioni, client o intervalli di tempo specifici.

## <a name="office-365-support"></a>Supporto di Office 365

Le applicazioni di Office 365 devono soddisfare due criteri per supportare pienamente Restrizioni dei tenant:

1. Il client usato supporta l'autenticazione moderna
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. L'autenticazione moderna è già abilitata per impostazione predefinita in SharePoint Online.

Restrizioni dei tenant è attualmente supportata nelle applicazioni Office 365 basate su browser come il portale di Office, Yammer, i siti SharePoint, Outlook sul Web e così via. Per i thick client come Outlook, Skype for Business, Word, Excel, PowerPoint e così via, Restrizioni dei tenant può essere applicata solo quando si usa l'autenticazione moderna.  

I client Outlook e Skype for Business che supportano l'autenticazione moderna possono comunque usare protocolli legacy con tenant dove l'autenticazione moderna non è abilitata, ignorando di fatto Restrizioni dei tenant. In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](http://gpsearch.azurewebsites.net/default.aspx?ref=1). Per Outlook su piattaforme non Windows e per Skype for Business su tutte le piattaforme, il supporto completo per le restrizioni del tenant non è attualmente disponibile.

## <a name="testing"></a>Test

Se si desidera provare la funzionalità Restrizioni dei tenant prima di implementarla in tutta l'organizzazione, sono disponibili due opzioni: un approccio basato su host con uno strumento come Fiddler o una pianificazione per fasi delle impostazioni del proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare Fiddler per testare Restrizioni dei tenant, eseguire la procedura seguente:

1.  [Scaricare e installare Fiddler](http://www.telerik.com/fiddler).
2.  Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:
  1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.
  2. Aggiungere le righe seguenti all'inizio della funzione *OnBeforeRequest*. Sostituire il \<dominio del tenant\> con un dominio registrato con il proprio tenant, ad esempio contoso.onmicrosoft.com. Sostituire \<l'ID della directory\> con l'identificatore GUID di Azure AD del proprio tenant.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

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
