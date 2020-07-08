---
title: Uso delle restrizioni del tenant per gestire l'accesso alle app SaaS - Azure AD
description: Come usare le restrizioni del tenant per stabilire quali utenti possono accedere alle app in base al tenant Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: kenwith
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd302791aa783f1a95d48f666366aa845fcaadbb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763024"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Uso delle restrizioni del tenant per gestire l'accesso alle applicazioni cloud SaaS

Le organizzazioni di grandi dimensioni che mettono l'accento sulla sicurezza vogliono passare a servizi cloud come Office 365, ma devono sapere che i loro utenti saranno in grado di accedere solo a risorse approvate. In genere, le aziende limitano gli indirizzi IP o i nomi di dominio quando vogliono gestire gli accessi. Questo approccio non è efficace in situazioni dove le app SaaS (software come servizio) vengono ospitate in un cloud pubblico ed eseguite su nomi di dominio condivisi come [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloccare questi indirizzi impedirebbe totalmente agli utenti di accedere ad Outlook sul Web invece di limitare il loro accesso alle identità e alle risorse approvate.

La soluzione offerta da Azure Active Directory (Azure AD) per risolvere questo problema è una funzionalità denominata Restrizioni del tenant. Restrizioni del tenant consente alle organizzazioni di controllare l'accesso alle applicazioni cloud SaaS in base al tenant di Azure AD usato dalle applicazioni per il Single Sign-On. Ad esempio, si supponga di voler consentire l'accesso alle applicazioni Office 365 dell'organizzazione, impedendolo al contempo alle istanze di quelle stesse applicazioni in altre organizzazioni.  

Con Restrizioni del tenant le organizzazioni possono specificare l'elenco dei tenant ai quali i loro utenti possono accedere. Azure AD consente quindi l'accesso solo ai tenant autorizzati.

Questo articolo si concentra su Restrizioni del tenant per Office 365, ma la funzionalità può essere usata con qualsiasi app cloud SaaS che usa protocolli di autenticazione moderni con Azure AD per il Single Sign-On. Se si usano app SaaS con un tenant Azure AD diverso da quello usato da Office 365, assicurarsi che tutti i tenant richiesti siano autorizzati. Per ulteriori informazioni sulle app cloud SaaS, vedere il [Marketplace di Active Directory](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

## <a name="how-it-works"></a>Funzionamento

La soluzione globale è composta dai seguenti elementi:

1. **Azure AD**: se `Restrict-Access-To-Tenants: <permitted tenant list>` è presente, Azure AD genera unicamente token di sicurezza per i tenant autorizzati.

2. **Infrastruttura di server proxy locale**: questa infrastruttura è un dispositivo proxy idoneo per l'ispezione Transport Layer Security (TLS). Per inserire l'intestazione contenente l'elenco dei tenant consentiti nel traffico destinato a Azure AD è necessario configurare il proxy.

3. **Software client**: per supportare Restrizioni del tenant, il software client deve richiedere i token direttamente da Azure AD in modo che l'infrastruttura proxy possa intercettare il traffico. Restrizioni del tenant è attualmente supportata nelle applicazioni Office 365 basate su browser e nei client Office che usano tecniche di autenticazione moderne come OAuth 2.0.

4. **Tecniche di autenticazione moderne**: i servizi cloud devono usare tecniche di autenticazione moderne per Restrizioni del tenant e per bloccare l'accesso a tutti i tenant non consentiti. È necessario configurare i servizi cloud di Office 365 in modo che possano usare i moderni protocolli di autenticazione come impostazione predefinita. Per le informazioni più aggiornate sul supporto di Office 365 per l'autenticazione moderna, leggere il [relativo documento aggiornato](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Il diagramma seguente illustra il flusso di traffico di alto livello. Restrizioni del tenant richiedono l'ispezione TLS solo per il traffico verso Azure AD, non per i servizi cloud di Office 365. Questa distinzione è importante perché il volume di traffico per l'autenticazione in Azure AD è in genere molto inferiore rispetto a quello verso applicazioni SaaS come Exchange Online e SharePoint Online.

![Flusso di traffico di Restrizioni del tenant, diagramma](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurazione di Restrizioni del tenant

Ci sono due passaggi iniziali per quanto riguarda Restrizioni del tenant. Per prima cosa bisogna controllare che i client possano connettersi agli indirizzi corretti. In seguito bisogna configurare l'infrastruttura proxy.

### <a name="urls-and-ip-addresses"></a>URL e indirizzi IP

Per usare Restrizioni del tenant i client devono essere in grado di connettersi agli URL di Azure AD seguenti per l'autenticazione: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) e [login.windows.net](https://login.windows.net/). Inoltre, per accedere a Office 365, i client devono potersi connettere ai nomi di dominio completi (FQDN), agli URL e agli indirizzi IP definiti in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configurazione e requisiti del proxy

Per abilitare Restrizioni del tenant nell'infrastruttura proxy è necessaria la configurazione seguente. Questa guida è generica, pertanto è consigliabile consultare la documentazione del fornitore del proxy per i passaggi di implementazione specifici.

#### <a name="prerequisites"></a>Prerequisiti

- Il proxy deve poter eseguire l'intercettazione SSL, inserire intestazioni HTTP e filtrare le destinazioni tramite URL/FQDN.

- I client devono considerare attendibile la catena di certificati presentata dal proxy per le comunicazioni TLS. Ad esempio, se vengono usati certificati da un'[infrastruttura a chiave pubblica (PKI) interna](/windows/desktop/seccertenroll/public-key-infrastructure), deve essere considerato attendibile il certificato interno dell'autorità di certificazione interna.

- Questa funzionalità è inclusa nelle sottoscrizioni di Office 365, ma se si desidera usare Restrizioni del tenant per controllare l'accesso ad altre app SaaS, sono necessarie licenze Premium 1 di Azure AD.

#### <a name="configuration"></a>Configurazione

Per ogni richiesta in ingresso a login.microsoftonline.com, login.microsoft.com e login.windows.net, inserire due intestazioni HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Le intestazioni devono includere gli elementi seguenti:

- Per *Restrict-Access-to-Tenants*, usare un valore di \<permitted tenant list\> , ovvero un elenco delimitato da virgole di tenant a cui si vuole consentire agli utenti di accedere. È possibile usare qualsiasi dominio registrato con un tenant per individuare il tenant nell'elenco. Ad esempio, per consentire l'accesso ai tenant Contoso e Fabrikam, la coppia nome/valore è simile a questa: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Per *Restrict-Access-Context*, usare un valore ID di directory singola, dichiarando quale tenant imposta Restrizioni del tenant. Ad esempio, per dichiarare Contoso come tenant per l'impostazione dei criteri di Restrizioni del tenant, la coppia nome/valore è simile a questa: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> L'ID della directory si trova nel [Portale di Azure Active Directory](https://aad.portal.azure.com/). Accedere come amministratore, selezionare **Azure Active Directory**, quindi selezionare **Proprietà**.

Per impedire agli utenti di inserire le proprie intestazioni HTTP con tenant non approvati, il proxy deve sostituire l'intestazione *Restrict-Access-To-Tenants* se questa è già presente nella richiesta in ingresso.

È necessario forzare l'uso del proxy nei client per tutte le richieste a login.microsoftonline.com, login.microsoft.com e login.windows.net. Ad esempio, se vengono usati file PAC per reindirizzare i client all'uso del proxy, gli utenti finali non devono poter modificare o disabilitare tali file.

## <a name="the-user-experience"></a>Esperienza utente

In questa sezione viene illustrata l'esperienza per utenti finali e amministratori.

### <a name="end-user-experience"></a>Esperienza utente finale

Un utente di esempio si trova nella rete Contoso ma tenta di accedere online all'istanza Fabrikam di un'applicazione SaaS condivisa come Outlook. Se Fabrikam è un tenant non consentito per l'istanza Contoso, viene visualizzato un messaggio di rifiuto dell'accesso che indica che si sta tentando di accedere a una risorsa che appartiene a un'organizzazione non approvata dal reparto IT.

### <a name="admin-experience"></a>Esperienza amministratore

La configurazione di Restrizioni del tenant viene eseguita nell'infrastruttura del proxy aziendale, ma gli amministratori possono accedere direttamente ai relativi report nel portale di Azure. Per visualizzare i report:

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/). Viene visualizzato il dashboard **Interfaccia di amministrazione di Azure Active Directory**.

2. Nel riquadro sinistro selezionare **Azure Active Directory**. Viene visualizzata la pagina di panoramica di Azure Active Directory.

3. Nell'intestazione **Altre funzionalità**, selezionare **Restrizioni del tenant**.

L'amministratore per il tenant specificato come tenant Restricted-Access-Context può usare questo report per visualizzare gli accessi bloccati a causa dei criteri di Restrizioni del tenant, inclusi l'ID della directory di destinazione e le identità usate. Gli accessi sono inclusi se nelle impostazioni del tenant la restrizione di accesso è impostata sul tenant dell'utente o sul tenant della risorsa.

> [!NOTE]
> Il report può contenere informazioni limitate, ad esempio l'ID della directory di destinazione, quando un utente che si trova in un tenant diverso da Restricted-Access-Context accede. In questo caso le informazioni identificabili dall'utente, ad esempio nome e nome dell'entità utente, vengono mascherate per proteggere i dati dell'utente in altri tenant.

Come per gli altri report nel portale di Azure, è possibile usare i filtri per specificare l'ambito del report. È possibile usare filtri per intervalli di tempo, utenti, applicazioni, client o stati specifici. Se si seleziona il pulsante **Colonne** è possibile scegliere di visualizzare i dati con qualsiasi combinazione dei campi seguenti:

- **Utente**
- **Applicazione**
- **Status**
- **Data**
- **Data (UTC)** (UTC corrisponde a Coordinated Universal Time)
- **Metodo autenticazione MFA** (metodo di autenticazione a più fattori)
- **Dettaglio autenticazione MFA** (dettaglio di autenticazione a più fattori)
- **Risultato autenticazione MFA**
- **Indirizzo IP**
- **Client**
- **Nome utente**
- **Posizione**
- **ID tenant di destinazione**

## <a name="office-365-support"></a>Supporto di Office 365

Le applicazioni di Office 365 devono soddisfare due criteri per supportare pienamente Restrizioni del tenant:

1. Il client usato supporta tecniche di autenticazione moderne.
2. L'autenticazione moderna è abilitata come protocollo di autenticazione predefinito per il servizio cloud.

Fare riferimento al [documento aggiornato sull'autenticazione moderna di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) per le informazioni più recenti sui client Office che supportano attualmente l'autenticazione moderna. Questa pagina include anche collegamenti a istruzioni su come abilitare l'autenticazione moderna in tenant Exchange Online e Skype for Business Online specifici. SharePoint Online abilità già l'autenticazione moderna per impostazione predefinita.

Restrizioni del tenant è attualmente supportata nelle applicazioni Office 365 basate su browser come il portale di Office, Yammer, i siti SharePoint, Outlook sul Web e così via. I client che richiedono molte risorse (Outlook, Skype for Business, Word, Excel, PowerPoint e così via) possono applicare restrizioni del tenant solo quando si usa l'autenticazione moderna.  

I client Outlook e Skype for Business che supportano l'autenticazione moderna possono comunque usare protocolli legacy con tenant dove l'autenticazione moderna non è abilitata, ignorando di fatto Restrizioni del tenant. Restrizioni del tenant potrebbe bloccare applicazioni che usano protocolli legacy se queste contattano login.microsoftonline.com, login.microsoft.com o login.windows.net durante l'autenticazione.

In Outlook per Windows, i clienti possono scegliere di implementare delle restrizioni per impedire agli utenti finali di aggiungere ai propri profili account di posta elettronica non approvati. Ad esempio, vedere l'impostazione di criteri di gruppo [Impedisci l'aggiunta di account di Exchange non predefiniti](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Se si desidera provare la funzionalità Restrizioni del tenant prima di implementarla in tutta l'organizzazione, sono disponibili due opzioni: un approccio basato su host con uno strumento come Fiddler o una pianificazione per fasi delle impostazioni del proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler per un approccio basato su host

Fiddler è un proxy di debug Web gratuito, utilizzabile per acquisire e modificare il traffico HTTP/HTTPS, incluso l'inserimento di intestazioni HTTP. Per configurare il test di Restrizioni del tenant con Fiddler, eseguire la procedura seguente:

1. [Scaricare e installare Fiddler](https://www.telerik.com/fiddler).

2. Configurare Fiddler per decrittografare il traffico HTTPS, come indicato nella [relativa documentazione di aiuto](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurare Fiddler per inserire le intestazioni *Restrict-Access-To-Tenants* e *Restrict-Access-Context* con regole personalizzate:

   1. Nello strumento Fiddler Web Debugger, selezionare il menu **Rules** (Regole) e selezionare **Customize Rules…** (Personalizza regole…) per aprire il file CustomRules.

   2. Aggiungere le righe seguenti all'inizio della funzione `OnBeforeRequest`. Sostituire \<tenant domain\> con un dominio registrato con il tenant (ad esempio, `contoso.onmicrosoft.com` ). Sostituire \<directory ID\> con l'identificatore GUID Azure ad del tenant.

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

Per informazioni dettagliate, consultare la documentazione del proprio server proxy.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[autenticazione moderna aggiornata di Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
