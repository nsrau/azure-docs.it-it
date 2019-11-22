---
title: Usare le restrizioni del tenant per gestire l'accesso alle app SaaS-Azure AD
description: Come usare le restrizioni dei tenant per gestire gli utenti che possono accedere alle app in base al tenant Azure AD.
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
ms.openlocfilehash: 64f73dd8dbef3f08cd4ea5841e4ec21bac2f55bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276496"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usare le restrizioni del tenant per gestire l'accesso alle applicazioni cloud SaaS

Le organizzazioni di grandi dimensioni che mettono l'accento sulla sicurezza vogliono passare a servizi cloud come Office 365, ma devono sapere che i loro utenti saranno in grado di accedere solo a risorse approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio ha esito negativo in un mondo in cui le app Software as a Service (o SaaS) sono ospitate in un cloud pubblico, in esecuzione su nomi di dominio condivisi come [Outlook.Office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

La soluzione Azure Active Directory (Azure AD) a questo problema è una funzionalità denominata restrizioni dei tenant. Con le restrizioni dei tenant, le organizzazioni possono controllare l'accesso alle applicazioni cloud SaaS, in base al tenant Azure AD usato dalle applicazioni per Single Sign-On. Ad esempio, si supponga di voler consentire l'accesso alle applicazioni Office 365 dell'organizzazione, impedendolo al contempo alle istanze di quelle stesse applicazioni in altre organizzazioni.  

Con le restrizioni dei tenant, le organizzazioni possono specificare l'elenco di tenant a cui gli utenti sono autorizzati ad accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo è incentrato sulle restrizioni dei tenant per Office 365, ma la funzionalità dovrebbe funzionare con qualsiasi app Cloud SaaS che usa protocolli di autenticazione moderni con Azure AD per Single Sign-On. Se si usano app SaaS con un tenant Azure AD diverso da quello usato da Office 365, assicurarsi che tutti i tenant richiesti siano autorizzati. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Funzionamento

La soluzione globale è composta dai seguenti elementi:

1. **Azure ad**: se il `Restrict-Access-To-Tenants: <permitted tenant list>` è presente, Azure ad rilascia solo i token di sicurezza per i tenant autorizzati.

2. **Infrastruttura server proxy locale**: questa infrastruttura è un dispositivo proxy idoneo per l'ispezione Secure Sockets Layer (SSL). È necessario configurare il proxy per inserire l'intestazione contenente l'elenco dei tenant consentiti nel traffico destinato a Azure AD.

3. **Software client**: per supportare le restrizioni dei tenant, il software client deve richiedere i token direttamente da Azure ad, in modo che l'infrastruttura proxy possa intercettare il traffico. Le applicazioni Office 365 basate su browser supportano attualmente le restrizioni dei tenant, così come i client di Office che usano l'autenticazione moderna (ad esempio, OAuth 2,0).

4. **Autenticazione moderna**: i servizi cloud devono usare l'autenticazione moderna per usare le restrizioni dei tenant e bloccare l'accesso a tutti i tenant non consentiti. Per impostazione predefinita, è necessario configurare i servizi cloud di Office 365 per usare i protocolli di autenticazione moderni. Per le informazioni più aggiornate sul supporto di Office 365 per l'autenticazione moderna, leggere il [relativo documento aggiornato](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Il diagramma seguente illustra il flusso di traffico di alto livello. Per le restrizioni dei tenant è richiesta l'ispezione SSL solo sul traffico da Azure AD, non ai servizi cloud di Office 365. Questa distinzione è importante, perché il volume di traffico per l'autenticazione Azure AD è in genere molto inferiore al volume del traffico per applicazioni SaaS come Exchange Online e SharePoint Online.

![Flusso traffico restrizioni tenant-diagramma](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurare le restrizioni del tenant

Per iniziare a usare le restrizioni dei tenant, è necessario eseguire due passaggi. Prima di tutto, assicurarsi che i client possano connettersi agli indirizzi corretti. In secondo luogo, configurare l'infrastruttura del proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per usare le restrizioni dei tenant, i client devono essere in grado di connettersi ai seguenti URL di Azure AD per l'autenticazione: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)e [login.Windows.NET](https://login.windows.net/). Per accedere anche a Office 365, inoltre, i client devono essere in grado di connettersi ai nomi di dominio completi (FQDN), agli URL e agli indirizzi IP definiti negli [URL e negli intervalli di indirizzi IP di office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

La configurazione seguente è necessaria per abilitare le restrizioni dei tenant attraverso l'infrastruttura del proxy. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>prerequisiti

- Il proxy deve poter eseguire l'intercettazione SSL, inserire intestazioni HTTP e filtrare le destinazioni tramite URL/FQDN.

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni SSL. Se, ad esempio, vengono utilizzati certificati da un' [infrastruttura a chiave pubblica (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) interna, il certificato dell'autorità di certificazione radice emittente interna deve essere attendibile.

- Questa funzionalità è inclusa nelle sottoscrizioni di Office 365, ma se si vogliono usare le restrizioni del tenant per controllare l'accesso ad altre app SaaS, è necessario Azure AD Premium 1 licenze.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in ingresso a login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Le intestazioni devono includere gli elementi seguenti:

- Per *Restrict-Access-to-Tenants*, usare un valore di \<elenco tenant consentito\>, ovvero un elenco delimitato da virgole di tenant a cui si vuole consentire agli utenti di accedere. È possibile usare qualsiasi dominio registrato con un tenant per individuare il tenant nell'elenco. Ad esempio, per consentire l'accesso ai tenant Contoso e Fabrikam, la coppia nome/valore è simile a questa: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Per *Restrict-Access-context*, usare un valore di un ID di directory singolo, dichiarando il tenant che imposta le restrizioni del tenant. Per dichiarare Contoso come tenant che ha impostato i criteri di restrizione dei tenant, ad esempio, la coppia nome/valore è simile alla seguente: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> L'ID directory è reperibile nel [portale di Azure Active Directory](https://aad.portal.azure.com/). Accedere come amministratore, selezionare **Azure Active Directory**, quindi selezionare **Proprietà**.

Per impedire agli utenti di inserire un'intestazione HTTP con tenant non approvati, il proxy deve sostituire l'intestazione *Restrict-Access-to-Tenants* se è già presente nella richiesta in ingresso.

È necessario forzare l'uso del proxy nei client per tutte le richieste a login.microsoftonline.com, login.microsoft.com e login.windows.net. Se, ad esempio, i file PAC vengono usati per indirizzare i client all'uso del proxy, gli utenti finali non devono essere in grado di modificare o disabilitare i file PAC.

## <a name="the-user-experience"></a>Esperienza utente

Questa sezione descrive l'esperienza sia per gli utenti finali che per gli amministratori.

### <a name="end-user-experience"></a>Esperienza utente finale

Un utente di esempio si trova nella rete Contoso ma tenta di accedere online all'istanza Fabrikam di un'applicazione SaaS condivisa come Outlook. Se Fabrikam è un tenant non consentito per l'istanza di Contoso, l'utente visualizza un messaggio di rifiuto di accesso, che indica che si sta tentando di accedere a una risorsa che appartiene a un'organizzazione non approvata dal reparto IT.

### <a name="admin-experience"></a>Esperienza amministratore

Sebbene la configurazione delle restrizioni dei tenant venga eseguita nell'infrastruttura del proxy aziendale, gli amministratori possono accedere ai report sulle restrizioni dei tenant direttamente nel portale di Azure. Per visualizzare i report:

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com/). Viene visualizzato il dashboard dell'interfaccia di **amministrazione Azure Active Directory** .

2. Nel riquadro sinistro selezionare **Azure Active Directory**. Viene visualizzata la pagina Panoramica Azure Active Directory.

3. Nell'intestazione **altre funzionalità** selezionare **restrizioni tenant**.

L'amministratore del tenant specificato come tenant del contesto di accesso limitato può usare questo report per visualizzare gli accessi bloccati a causa dei criteri di restrizione del tenant, inclusa l'identità usata e l'ID della directory di destinazione. Gli accessi sono inclusi se nelle impostazioni del tenant la restrizione di accesso è impostata sul tenant dell'utente o sul tenant della risorsa.

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile filtrare in base a un intervallo di tempo, un utente, un'applicazione, un client o uno stato specifico. Se si seleziona il pulsante **colonne** , è possibile scegliere di visualizzare i dati con qualsiasi combinazione dei campi seguenti:

- **Utente**
- **Applicazione**
- **Status**
- **Date**
- **Data (UTC)** (dove UTC è Coordinated Universal Time)
- **Metodo** di autenticazione a più fattori (metodo di autenticazione a più fattori)
- **Dettagli** autenticazione a più fattori (dettagli autenticazione a più fattori)
- **Risultato dell'autenticazione a più fattori**
- **Indirizzo IP**
- **Client**
- **Nome utente**
- **Località**
- **ID tenant di destinazione**

## <a name="office-365-support"></a>Supporto di Office 365

Le applicazioni Office 365 devono soddisfare due criteri per supportare completamente le restrizioni del tenant:

1. Il client utilizzato supporta l'autenticazione moderna.
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. SharePoint Online consente già l'autenticazione moderna per impostazione predefinita.

Le applicazioni Office 365 basate su browser (il portale di Office, Yammer, i siti di SharePoint, Outlook sul Web e altro ancora) supportano attualmente le restrizioni dei tenant. I client spessi (Outlook, Skype for business, Word, Excel, PowerPoint e così via) possono applicare restrizioni dei tenant solo quando si usa l'autenticazione moderna.  

I client Outlook e Skype for business che supportano l'autenticazione moderna possono comunque usare protocolli legacy per i tenant in cui l'autenticazione moderna non è abilitata, ignorando efficacemente le restrizioni dei tenant. Le restrizioni del tenant possono bloccare le applicazioni che usano protocolli legacy se contattano login.microsoftonline.com, login.microsoft.com o login.windows.net durante l'autenticazione.

In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Se si vuole provare le restrizioni del tenant prima di implementarlo per l'intera organizzazione, sono disponibili due opzioni: un approccio basato su host che usa uno strumento come Fiddler o un'implementazione di gestione temporanea delle impostazioni proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare Fiddler per testare le restrizioni del tenant, seguire questa procedura:

1. [Scaricare e installare Fiddler](https://www.telerik.com/fiddler).

2. Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:

   1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.

   2. Aggiungere le righe seguenti all'inizio della funzione `OnBeforeRequest`. Sostituire \<\> di dominio tenant con un dominio registrato con il tenant, ad esempio `contoso.onmicrosoft.com`. Sostituire \<l'ID della directory\> con l'identificatore GUID di Azure AD del proprio tenant.

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
