---
title: Usare le restrizioni del tenant per gestire l'accesso alle app SaaS - Azure ADUse tenant restrictions to manage access to SaaS apps - Azure AD
description: Come usare le restrizioni del tenant per gestire gli utenti che possono accedere alle app in base al tenant di Azure AD.
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
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481178"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usare le restrizioni del tenant per gestire l'accesso alle applicazioni cloud SaaSUse tenant restrictions to manage access to SaaS cloud applications

Le organizzazioni di grandi dimensioni che mettono l'accento sulla sicurezza vogliono passare a servizi cloud come Office 365, ma devono sapere che i loro utenti saranno in grado di accedere solo a risorse approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio non riesce in un mondo in cui le app software come servizio (o SaaS) sono ospitate in un cloud pubblico, in esecuzione su nomi di dominio condivisi come [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

La soluzione Azure Active Directory (Azure AD) a questa richiesta è una funzionalità denominata restrizioni tenant. Con le restrizioni dei tenant, le organizzazioni possono controllare l'accesso alle applicazioni cloud SaaS, in base al tenant di Azure AD usato dalle applicazioni per l'accesso Single Sign-On. Ad esempio, si supponga di voler consentire l'accesso alle applicazioni Office 365 dell'organizzazione, impedendolo al contempo alle istanze di quelle stesse applicazioni in altre organizzazioni.  

Con le restrizioni dei tenant, le organizzazioni possono specificare l'elenco dei tenant a cui gli utenti sono autorizzati ad accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo è incentrato sulle restrizioni del tenant per Office 365, ma la funzionalità deve funzionare con qualsiasi app cloud SaaS che usa protocolli di autenticazione moderni con Azure AD per Single Sign-On. Se si usano app SaaS con un tenant Azure AD diverso da quello usato da Office 365, assicurarsi che tutti i tenant richiesti siano autorizzati. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Funzionamento

La soluzione globale è composta dai seguenti elementi:

1. **Azure AD:** `Restrict-Access-To-Tenants: <permitted tenant list>` se è presente, Azure AD rilascia solo token di sicurezza per i tenant consentiti.

2. **Infrastruttura server proxy locale:** questa infrastruttura è un dispositivo proxy in grado di eseguire l'ispezione TLS (Transport Layer Security). È necessario configurare il proxy per inserire l'intestazione contenente l'elenco dei tenant consentiti nel traffico destinato ad Azure AD.

3. **Software client:** per supportare le restrizioni del tenant, il software client deve richiedere i token direttamente da Azure AD, in modo che l'infrastruttura proxy possa intercettare il traffico. Le applicazioni di Office 365 basate su browser supportano attualmente le restrizioni dei tenant, così come i client di Office che utilizzano l'autenticazione moderna (ad esempio OAuth 2.0).

4. **Autenticazione moderna:** i servizi cloud devono usare l'autenticazione moderna per usare restrizioni del tenant e bloccare l'accesso a tutti i tenant non autorizzati. È necessario configurare i servizi cloud di Office 365 per l'utilizzo di protocolli di autenticazione moderni per impostazione predefinita. Per le informazioni più aggiornate sul supporto di Office 365 per l'autenticazione moderna, leggere il [relativo documento aggiornato](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Il diagramma seguente illustra il flusso di traffico di alto livello. Le restrizioni del tenant richiedono l'ispezione TLS solo sul traffico verso Azure AD, non per i servizi cloud di Office 365.Tenant restrictions requires TLS inspection only on traffic to Azure AD, not to the Office 365 cloud services. Questa distinzione è importante, perché il volume di traffico per l'autenticazione in Azure AD è in genere molto inferiore al volume di traffico per le applicazioni SaaS come Exchange Online e SharePoint Online.

![Tenant restrizioni flusso di traffico - diagramma](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurare le restrizioni del tenant

Esistono due passaggi per iniziare a usare le restrizioni del tenant. Innanzitutto, assicurati che i tuoi clienti possano connettersi agli indirizzi corretti. In secondo luogo, configurare l'infrastruttura proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per utilizzare le restrizioni del tenant, i client devono essere in grado di connettersi ai seguenti URL di Azure AD per l'autenticazione: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)e [login.windows.net](https://login.windows.net/). Inoltre, per accedere a Office 365, i client devono anche essere in grado di connettersi ai nomi di dominio completi (FQDN), agli URL e agli indirizzi IP definiti negli URL e negli intervalli di indirizzi IP di [Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

La configurazione seguente è necessaria per abilitare le restrizioni del tenant tramite l'infrastruttura proxy. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>Prerequisiti

- Il proxy deve essere in grado di eseguire l'intercettazione TLS, l'inserimento di intestazioni HTTP e le destinazioni di filtro utilizzando FQDN/URL.

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni TLS. Ad esempio, se vengono utilizzati certificati da [un'infrastruttura a chiave pubblica (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) interna, il certificato dell'autorità di certificazione radice di emissione interna deve essere considerato attendibile.

- Questa funzionalità è inclusa negli abbonamenti a Office 365, ma se si desidera usare le restrizioni del tenant per controllare l'accesso ad altre app SaaS, sono necessarie licenze di Azure AD Premium 1.This feature is included in Office 365 subscriptions, but if you want to use tenant restrictions to control access to other SaaS apps, then Azure AD Premium 1 licenses are required.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in ingresso a login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Le intestazioni devono includere gli elementi seguenti:

- Per *Restrict-Access-To-Tenants*, utilizzare \<un\>valore di elenco tenant consentito , che è un elenco separato da virgole di tenant a cui si desidera consentire l'accesso. È possibile usare qualsiasi dominio registrato con un tenant per individuare il tenant nell'elenco. Ad esempio, per consentire l'accesso ai tenant Contoso e Fabrikam, la coppia nome/valore è simile a questa: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Per *Restrict-Access-Context*, utilizzare il valore di un singolo ID di directory, indicando quale tenant sta impostando le restrizioni del tenant. Ad esempio, per dichiarare Contoso come tenant che imposta i criteri di restrizione tenant, la coppia nome/valore è simile alla seguente:For example, to declare Contoso as the tenant that set the tenant restrictions policy, the name/value pair looks like: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> È possibile trovare l'ID directory nel portale di [Azure Active Directory.](https://aad.portal.azure.com/) Accedere come amministratore, selezionare **Azure Active Directory**, quindi selezionare **Proprietà**.

Per impedire agli utenti di inserire la propria intestazione HTTP con tenant non approvati, il proxy deve sostituire l'intestazione *Restrict-Access-To-Tenants* se è già presente nella richiesta in ingresso.

È necessario forzare l'uso del proxy nei client per tutte le richieste a login.microsoftonline.com, login.microsoft.com e login.windows.net. Ad esempio, se i file PAC vengono utilizzati per indirizzare i client a utilizzare il proxy, gli utenti finali non dovrebbero essere in grado di modificare o disabilitare i file PAC.

## <a name="the-user-experience"></a>Esperienza utente

In questa sezione viene descritta l'esperienza sia per gli utenti finali che per gli amministratori.

### <a name="end-user-experience"></a>Esperienza utente finale

Un utente di esempio si trova nella rete Contoso ma tenta di accedere online all'istanza Fabrikam di un'applicazione SaaS condivisa come Outlook. Se Fabrikam è un tenant non consentito per l'istanza Contoso, l'utente visualizza un messaggio di negazione dell'accesso, che indica che si sta tentando di accedere a una risorsa appartenente a un'organizzazione non approvata dal reparto IT.

### <a name="admin-experience"></a>Esperienza amministratore

Mentre la configurazione delle restrizioni tenant viene eseguita nell'infrastruttura proxy aziendale, gli amministratori possono accedere direttamente ai report sulle restrizioni dei tenant nel portale di Azure.While configuration of tenant restrictions is done on the corporate proxy infrastructure, admins can access the tenant restrictions reports in the Azure portal directly. Per visualizzare i rapporti:

1. Accedere al [portale](https://aad.portal.azure.com/)di Azure Active Directory . Viene visualizzato il dashboard dell'interfaccia di amministrazione di **Azure Active Directory.The Azure Active Directory admin center** dashboard appears.

2. Nel riquadro sinistro selezionare **Azure Active Directory**. Viene visualizzata la pagina Panoramica di Azure Active Directory.The Azure Active Directory overview page appears.

3. Nell'intestazione **Altre funzionalità** selezionare **Restrizioni tenant**.

L'amministratore del tenant specificato come tenant Restricted-Access-Context può usare questo report per visualizzare gli accessi bloccati a causa dei criteri di restrizione del tenant, tra cui l'identità utilizzata e l'ID della directory di destinazione. Gli accessi sono inclusi se nelle impostazioni del tenant la restrizione di accesso è impostata sul tenant dell'utente o sul tenant della risorsa.

> [!NOTE]
> Il report può contenere informazioni limitate, ad esempio l'ID della directory di destinazione, quando un utente che si trova in un tenant diverso dal tenant Restricted-Access-Context esegue l'accesso. In questo caso, le informazioni identificabili dall'utente, ad esempio il nome e il nome dell'entità utente, vengono mascherate per proteggere i dati utente in altri tenant.

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile filtrare in base a un intervallo di tempo, utente, applicazione, client o stato specifico. Se si seleziona il pulsante **Colonne,** è possibile scegliere di visualizzare i dati con qualsiasi combinazione dei seguenti campi:

- **Utente**
- **Applicazione**
- **Stato**
- **Data**
- **Data (UTC)** (dove UTC è l'ora UTC UTC coordinata)
- **Metodo di autenticazione MFA** (metodo di autenticazione a più fattori)MFA Auth Method (multifactor authentication method)
- **Dettaglio autenticazione a** più fattori (dettagli autenticazione a più fattori)MFA Auth Detail (multifactor authentication detail)
- **Risultato MFA**
- **Indirizzo IP**
- **Client**
- **Nome utente**
- **Percorso**
- **ID tenant di destinazione**

## <a name="office-365-support"></a>Supporto di Office 365

Le applicazioni di Office 365 devono soddisfare due criteri per supportare completamente le restrizioni dei tenant:

1. Il client utilizzato supporta l'autenticazione moderna.
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. SharePoint Online abilita già l'autenticazione moderna per impostazione predefinita.

Le applicazioni basate su browser di Office 365 (il portale di Office, Yammer, siti di SharePoint, Outlook sul Web e altro ancora) supportano attualmente le restrizioni dei tenant. I client spessi (Outlook, Skype for Business, Word, Excel, PowerPoint e altro ancora) possono applicare restrizioni per i tenant solo quando si utilizza l'autenticazione moderna.  

I client Outlook e Skype for Business che supportano l'autenticazione moderna possono comunque utilizzare protocolli legacy nei tenant in cui non è abilitata l'autenticazione moderna, ignorando in modo efficace le restrizioni dei tenant. Le restrizioni dei tenant possono bloccare le applicazioni che utilizzano protocolli legacy se contattano login.microsoftonline.com, login.microsoft.com o login.windows.net durante l'autenticazione.

In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Se si desidera provare le restrizioni del tenant prima di implementarle per l'intera organizzazione, sono disponibili due opzioni: un approccio basato su host che usa uno strumento come Fiddler o un'implementazione in fasi delle impostazioni proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare Fiddler per testare le restrizioni del tenant, eseguire la procedura seguente:To configure Fiddler to test tenant restrictions, perform the following steps:

1. [Scaricare e installare Fiddler](https://www.telerik.com/fiddler).

2. Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:

   1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.

   2. Aggiungere le seguenti righe all'inizio della `OnBeforeRequest` funzione. Sostituire \<il\> dominio tenant con un dominio `contoso.onmicrosoft.com`registrato con il tenant, ad esempio ). Sostituire \<l'ID della directory\> con l'identificatore GUID di Azure AD del proprio tenant.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Se è necessario consentire più tenant, separare i vari nomi di tenant con le virgole. Ad esempio:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Salvare e chiudere il file CustomRules.

Dopo aver configurato Fiddler, è possibile acquisire il traffico accedendo al menu **File** e selezionando **Capture Traffic** (Acquisisci traffico).

### <a name="staged-rollout-of-proxy-settings"></a>Implementazione per fasi delle impostazioni del proxy

A seconda delle funzionalità dell'infrastruttura di proxy, è possibile eseguire un'implementazione per fasi delle impostazioni agli utenti. Di seguito sono indicate due opzioni generali da tenere in considerazione:

1. Usare file PAC per indirizzare gli utenti di test a un'infrastruttura di proxy di test, mentre gli utenti normali continuano a utilizzare l'infrastruttura del proxy di produzione.
2. Alcuni server proxy possono supportare configurazioni diverse usando i gruppi.

Per dettagli specifici, fare riferimento alla documentazione del server proxy.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[autenticazione moderna aggiornata di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
