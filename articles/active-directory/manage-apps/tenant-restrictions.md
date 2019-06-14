---
title: Usare restrizioni dei tenant per gestire l'accesso a soluzioni SaaS cloud applicazioni - Azure | Microsoft Docs
description: Come usare restrizioni dei tenant per gestire quali utenti possono accedere alle App basate su tenant di Azure AD.
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
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824390"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usare restrizioni dei tenant per gestire l'accesso alle applicazioni cloud SaaS

Le organizzazioni di grandi dimensioni che mettono l'accento sulla sicurezza vogliono passare a servizi cloud come Office 365, ma devono sapere che i loro utenti saranno in grado di accedere solo a risorse approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio ha esito negativo in un mondo in cui sono ospitate le app software come servizio (o SaaS) in un cloud pubblico, in esecuzione in nomi di dominio condivisi, ad esempio [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

La soluzione di Azure Active Directory (Azure AD) per risolvere questo problema è una funzionalità denominata restrizioni dei tenant. Con restrizioni dei tenant, le organizzazioni possono controllare l'accesso alle applicazioni cloud SaaS, basate su tenant di Azure AD che le applicazioni usano per single sign-on. Ad esempio, si supponga di voler consentire l'accesso alle applicazioni Office 365 dell'organizzazione, impedendolo al contempo alle istanze di quelle stesse applicazioni in altre organizzazioni.  

Con restrizioni dei tenant, le organizzazioni possono specificare l'elenco di tenant cui gli utenti sono autorizzati ad accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo si concentra su restrizioni dei tenant per Office 365, ma la funzionalità dovrebbe funzionare con qualsiasi app cloud SaaS che Usa protocolli di autenticazione moderna con Azure AD per l'accesso single sign-on. Se si usano app SaaS con un tenant Azure AD diverso da quello usato da Office 365, assicurarsi che tutti i tenant richiesti siano autorizzati. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Funzionamento

La soluzione globale è composta dai seguenti elementi:

1. **Azure AD**: Se il `Restrict-Access-To-Tenants: <permitted tenant list>` è presente, Azure AD solo rilascia token di sicurezza per i tenant autorizzati.

2. **Infrastruttura di server on-premises proxy**: Questa infrastruttura è un dispositivo proxy in grado di ispezione di Secure Sockets Layer (SSL). È necessario configurare il proxy per inserire l'intestazione contenente l'elenco di tenant autorizzati nel traffico verso Azure AD.

3. **Il software client**: Per supportare restrizioni dei tenant, il software client deve richiedere i token direttamente da Azure AD, in modo che l'infrastruttura del proxy consente di intercettare il traffico. Applicazioni di Office 365 basate su browser supportano attualmente restrizioni dei tenant, così come i client di Office che usano l'autenticazione moderna (ad esempio OAuth 2.0).

4. **L'autenticazione moderna**: Servizi cloud devono usare l'autenticazione moderna per usare restrizioni dei tenant e bloccare l'accesso a tutti i tenant non autorizzata. È necessario configurare i servizi cloud di Office 365 per l'uso di protocolli di autenticazione moderna per impostazione predefinita. Per le informazioni più aggiornate sul supporto di Office 365 per l'autenticazione moderna, leggere il [relativo documento aggiornato](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Il diagramma seguente illustra il flusso di traffico di alto livello. Restrizioni dei tenant richiede l'ispezione SSL solo sul traffico di Azure ad, non per i servizi cloud di Office 365. Questa distinzione è importante, perché il volume di traffico per l'autenticazione ad Azure AD è in genere molto inferiore rispetto a quello verso applicazioni SaaS come Exchange Online e SharePoint Online.

![Flusso del traffico di restrizioni del tenant - diagramma](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Impostare le restrizioni dei tenant

Esistono due passaggi per iniziare a usare restrizioni dei tenant. In primo luogo, assicurarsi che i client possano connettersi agli indirizzi giusti. In secondo luogo, configurare l'infrastruttura del proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per usare restrizioni dei tenant, devono essere in grado di connettersi al seguente URL di Azure AD per autenticare i client: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), e [ Login.Windows.NET](https://login.windows.net/). Inoltre, per accedere a Office 365, i client devono trovarsi anche in grado di connettersi per i nomi di dominio completo (FQDN), gli URL, e gli indirizzi IP definito in [intervalli di indirizzi IP e Office 365 URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

La configurazione seguente è necessaria per abilitare restrizioni dei tenant tramite l'infrastruttura del proxy. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>Prerequisiti

- Il proxy deve poter eseguire l'intercettazione SSL, inserire intestazioni HTTP e filtrare le destinazioni tramite URL/FQDN.

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni SSL. Ad esempio, se i certificati da interna [infrastruttura a chiave pubblica (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) sono utilizzato, interna emissione certificato certificato dell'autorità radice deve essere attendibile.

- Questa funzionalità è inclusa nelle sottoscrizioni di Office 365, ma se si desidera usare restrizioni dei tenant per controllare l'accesso ad altre App SaaS, le licenze di Azure AD Premium 1 sono necessari.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in ingresso a login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Le intestazioni devono includere gli elementi seguenti:

- Per la *Restrict-Access-To-Tenants*, usare il valore \<elenco tenant consentiti\>, ovvero un elenco delimitato da virgole dei tenant per consentire agli utenti di accedere. È possibile usare qualsiasi dominio registrato con un tenant per individuare il tenant nell'elenco. Ad esempio, per consentire l'accesso ai tenant Contoso e Fabrikam, la coppia nome/valore è simile a questa: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Per la *Restrict-Access-Context*, usare un valore di un ID di directory singola, dichiarando quale tenant imposta il tenant di restrizioni. Per dichiarare Contoso come tenant di impostare i criteri di restrizioni del tenant, ad esempio, la coppia nome/valore è simile a: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> È possibile trovare l'ID di directory nel [portale di Azure Active Directory](https://aad.portal.azure.com/). Accedere come amministratore, selezionare **Azure Active Directory**, quindi selezionare **Proprietà**.

Per impedire agli utenti di inserire le proprie intestazioni HTTP con i tenant non approvati, il proxy deve sostituire il *Restrict-Access-To-Tenants* intestazione se già presente nella richiesta in ingresso.

È necessario forzare l'uso del proxy nei client per tutte le richieste a login.microsoftonline.com, login.microsoft.com e login.windows.net. Ad esempio, se vengono usati file PAC per indirizzare i client di usare il proxy, gli utenti finali non devono essere in grado di modificare o disabilitare tali file.

## <a name="the-user-experience"></a>Esperienza utente

In questa sezione descrive l'esperienza per gli utenti finali e amministratori.

### <a name="end-user-experience"></a>Esperienza utente finale

Un utente di esempio si trova nella rete Contoso ma tenta di accedere online all'istanza Fabrikam di un'applicazione SaaS condivisa come Outlook. Se Fabrikam è un tenant non consentito per l'istanza di Contoso, l'utente visualizza un messaggio di rifiuto di accesso, expandedlabel con che si sta tentando di accedere a una risorsa che appartiene a un'organizzazione non approvata dal proprio reparto IT.

### <a name="admin-experience"></a>Esperienza amministratore

Configurazione di restrizioni dei tenant viene eseguita sull'infrastruttura del proxy aziendale, gli amministratori possono accedere i report di restrizioni del tenant nel portale di Azure direttamente. Per visualizzare i report:

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com/). Il **interfaccia di amministrazione di Azure Active Directory** viene visualizzato il dashboard.

2. Nel riquadro sinistro selezionare **Azure Active Directory**. Viene visualizzata la pagina di panoramica di Azure Active Directory.

3. Nel **altre funzionalità** titolo, selezionare **restrizioni del Tenant**.

L'amministratore per il tenant specificato come tenant Restricted-Access-Context può usare questo report per visualizzare gli accessi bloccati a causa dei criteri di restrizioni di tenant, tra cui l'identità usata e la directory di destinazione ID. Gli accessi sono inclusi se nelle impostazioni del tenant la restrizione di accesso è impostata sul tenant dell'utente o sul tenant della risorsa.

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile filtrare un intervallo di tempo specifico, utente, applicazione, client o stato. Se si seleziona il **colonne** pulsante, è possibile scegliere di visualizzare i dati con qualsiasi combinazione dei seguenti campi:

- **Utente**
- **Applicazione**
- **Status**
- **Data**
- **Data (UTC)** (in UTC è Coordinated Universal Time)
- **Metodo autenticazione MFA** (metodo di autenticazione a più fattori)
- **Dettaglio autenticazione MFA** (dettagli di autenticazione a più fattori)
- **Risultato autenticazione MFA**
- **Indirizzo IP**
- **Client**
- **Nome utente**
- **Posizione**
- **ID tenant di destinazione**

## <a name="office-365-support"></a>Supporto di Office 365

Le applicazioni di Office 365 devono soddisfare due criteri per supportare pienamente restrizioni dei tenant:

1. Il client usato supporta l'autenticazione moderna.
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. SharePoint Online consente già l'autenticazione moderna per impostazione predefinita.

Applicazioni di Office 365 basate su browser (il portale di Office, Yammer, i siti SharePoint, Outlook sul Web e altro ancora) supporta attualmente restrizioni dei tenant. Thick client (Outlook, Skype per Business, Word, Excel, PowerPoint e altro ancora) possono imporre restrizioni dei tenant solo quando si usa l'autenticazione moderna.  

Outlook e Skype per i client di Business che supportano l'autenticazione moderna possono comunque usare protocolli legacy con tenant dove l'autenticazione moderna non è abilitata, ignorando di fatto restrizioni dei tenant. Restrizioni dei tenant potrebbero bloccare le applicazioni che usano protocolli legacy se contattano login.microsoftonline.com, login.microsoft.com o login.windows.net durante l'autenticazione.

In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Se si desidera provare restrizioni dei tenant prima di implementarlo per l'intera organizzazione, sono disponibili due opzioni: un approccio basato su host con uno strumento come Fiddler o un'implementazione per fasi delle impostazioni del proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare Fiddler per restrizioni dei tenant di test, seguire i passaggi seguenti:

1. [Scaricare e installare Fiddler](https://www.telerik.com/fiddler).

2. Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:

   1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.

   2. Aggiungere le righe seguenti all'inizio del `OnBeforeRequest` (funzione). Sostituire \<dominio tenant\> con un dominio registrato con il tenant (ad esempio, `contoso.onmicrosoft.com`). Sostituire \<l'ID della directory\> con l'identificatore GUID di Azure AD del proprio tenant.

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

Per informazioni dettagliate, vedere la documentazione del server proxy.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[autenticazione moderna aggiornata di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
