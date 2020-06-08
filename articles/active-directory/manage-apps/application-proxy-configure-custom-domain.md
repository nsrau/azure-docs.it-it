---
title: Domini personalizzati nel proxy di applicazione di Azure AD | Documentazione Microsoft
description: Configurare e gestire domini personalizzati in Azure AD Application Proxy.
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
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda84a68921500e8ba40aa6d04bfb5939f79a1ef
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736484"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurare domini personalizzati con Azure AD Application Proxy

Quando si pubblica un'applicazione tramite Azure AD Application Proxy, si crea un URL esterno per gli utenti. Questo URL ottiene il dominio predefinito *tenant.msappproxy.net*. Ad esempio, se si pubblica un'app chiamata *Spese* in un tenant denominato *Contoso*, l'URL esterno è *https:\//spese-contoso.msappproxy.net*. Per usare il proprio nome di dominio, anziché *msappproxy.net*, è possibile configurare un dominio personalizzato per l'applicazione. 

## <a name="benefits-of-custom-domains"></a>Vantaggi dei domini personalizzati

Quando possibile, è consigliabile configurare domini personalizzati per le app. Di seguito sono indicati alcuni motivi per cui usare domini personalizzati:

- I collegamenti tra le app funzionano anche all'esterno della rete aziendale. Senza un dominio personalizzato, se l'app contiene collegamenti interni hardcoded a destinazioni all'esterno di Application Proxy e i collegamenti non sono risolvibili esternamente, questi si interrompono. Se gli URL interni ed esterni coincidono, si evita questo problema. Se non si è in grado di usare domini personalizzati, vedere [Redirect hardcoded links for apps published with Azure AD Application Proxy](../application-proxy-link-translation.md) (Reindirizzare i collegamenti hardcoded per le app pubblicate con Azure AD Application Proxy) per altri modi per risolvere questo problema. 
  
- Gli utenti possono usufruire di un'esperienza semplificata, perché possono raggiungere l'app con lo stesso URL dall'interno o dall'esterno della rete. Non devono acquisire URL interni ed esterni diversi, né tenere traccia della posizione corrente. 

- È possibile controllare la personalizzazione e creare gli URL desiderati. Un dominio personalizzato rafforza la fiducia degli utenti, che possono visualizzare e usare un nome familiare anziché *msappproxy.net*.

- Alcune configurazioni funzionano solo con domini personalizzati. Ad esempio, sono necessari domini personalizzati per le app che usano Security Assertion Markup Language (SAML), come nel caso in cui si usa Active Directory Federation Services (AD FS) ma non è possibile usare WS-Federation. Per altre informazioni, vedere [Work with claims-aware apps in Application Proxy](application-proxy-configure-for-claims-aware-applications.md) (Uso di app in grado di riconoscere le attestazioni in Application Proxy). 

Se non si è in grado di eseguire una corrispondenza tra URL interni ed esterni, non è altrettanto importante usare domini personalizzati, ma è comunque possibile usufruire degli altri vantaggi. 

## <a name="dns-configuration-options"></a>Opzioni di configurazione DNS

Sono disponibili diverse opzioni per impostare la configurazione DNS, a seconda dei requisiti:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Stessi URL interni ed esterni, comportamenti interni ed esterni diversi 

Se non si vuole che gli utenti interni vengano indirizzati tramite Application Proxy, è possibile configurare un'infrastruttura *DNS split brain*. Un'infrastruttura DNS di questo tipo indirizza gli host interni a un Domain Name Server interno e gli host esterni a un Domain Name Server esterno per la risoluzione dei nomi. 

![DNS split brain](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>URL interni ed esterni diversi 

Se gli URL interni ed esterni sono diversi, non è necessario configurare il comportamento split brain, perché il routing utente è determinato dall'URL. In questo caso, modificare solo il DNS esterno ed eseguire il routing dell'URL esterno all'endpoint Application Proxy. 

Quando si seleziona un dominio personalizzato per un URL esterno, una barra delle informazioni mostra la voce CNAME che è necessario aggiungere al provider DNS esterno. Queste informazioni sono sempre visibili alla pagina **Application Proxy** dell'app.

## <a name="set-up-and-use-custom-domains"></a>Configurare e usare domini personalizzati

Per configurare un'app locale per l'uso di un dominio personalizzato, sono necessari un dominio personalizzato Azure Active Directory verificato, un certificato PFX per il dominio personalizzato e un'app locale da configurare. 

### <a name="create-and-verify-a-custom-domain"></a>Creare e verificare un dominio personalizzato

Per creare e verificare un dominio personalizzato:

1. In Azure Active Directory selezionare **Nomi di dominio personalizzati** nel riquadro di spostamento a sinistra e quindi **Aggiungi dominio personalizzato**. 
1. Immettere il nome di dominio personalizzato e selezionare **Aggiungi dominio**. 
1. Nella pagina del dominio copiare le informazioni del record TXT per il dominio. 
1. Passare al registrar del dominio e creare un nuovo record TXT per il dominio in base alle informazioni DNS copiate.
1. Dopo aver registrato il dominio, nella pagina del dominio in Azure Active Directory selezionare **Verifica**. Se lo stato del dominio è **Verificato**, è possibile usare il dominio in tutte le configurazioni Azure AD, tra cui Application Proxy. 

Per istruzioni dettagliate, vedere [Aggiungere un nome di dominio personalizzato usando il portale di Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configurare un'app per l'uso di un dominio personalizzato

Per pubblicare l'app tramite Application Proxy con un dominio personalizzato:

1. Per una nuova app, in Azure Active Directory selezionare **Applicazioni aziendali** nel riquadro di spostamento a sinistra. Selezionare **Nuova applicazione**. Nella sezione **Applicazioni locali** selezionare **Aggiungi un'applicazione locale**. 
   
   Per un'app già presente in **Applicazioni aziendali**, selezionarla dall'elenco e quindi fare clic su **Proxy dell'applicazione** nel riquadro di spostamento a sinistra. 

2. Nella pagina Impostazioni proxy di applicazione immettere un **nome** se si aggiunge un'applicazione locale.

3.  Nel campo **URL interno** immettere l'URL interno per l'app.
   
4. Nel campo **URL esterno** fare clic sull'elenco a discesa e selezionare il dominio personalizzato che si vuole usare.
   
5. Selezionare **Aggiungi**.
   
   ![Selezionare il dominio personalizzato](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se è già stato caricato un certificato per il dominio, il campo **Certificato** visualizza le informazioni sul certificato. In caso contrario, selezionare il campo **Certificato**. 
   
   ![Fare clic per caricare un certificato](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Nella pagina **Certificato SSL** individuare e selezionare il file di certificato PFX. Immettere la password per il certificato e selezionare **Carica certificato**. Per altre informazioni sui certificati, vedere la sezione [Certificati per domini personalizzati](#certificates-for-custom-domains).
   
   ![Caricamento del certificato](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Un dominio personalizzato richiede che il certificato venga caricato una sola volta. Il certificato caricato viene quindi applicato automaticamente quando si usa il dominio personalizzato per altre app.
   
8. Se è stato aggiunto un certificato, nella pagina **Proxy dell'applicazione** selezionare **Salva**. 
   
9. Nella barra delle informazioni della pagina **Proxy dell'applicazione** prendere nota della voce CNAME che è necessario aggiungere alla zona DNS. 
   
   ![Aggiungere la voce DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Seguire le istruzioni in [Gestire record e set di record DNS con il portale di Azure](../../dns/dns-operations-recordsets-portal.md) per aggiungere un record DNS che reindirizzi il nuovo URL esterno al dominio *msappproxy.net*.
   
11. Per verificare che il record DNS sia configurato correttamente, usare il comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) per verificare che l'URL esterno sia raggiungibile e che il dominio *msapproxy.net* sia visualizzato come alias.

L'applicazione è ora configurata per l'uso del dominio personalizzato. Assicurarsi di assegnare utenti all'applicazione prima di testarla o rilasciarla. 

Per modificare il dominio per un'app, selezionare un dominio diverso nell'elenco a discesa in **URL esterno** nella pagina **Proxy dell'applicazione** dell'app. Caricare un certificato per il dominio aggiornato, se necessario, e aggiornare il record DNS. Se non viene visualizzato il dominio personalizzato desiderato nell'elenco a discesa in **URL esterno**, potrebbe non essere verificato.

Per istruzioni dettagliate su Application Proxy, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificati per domini personalizzati

Un certificato crea la connessione TLS sicura per il dominio personalizzato. 

### <a name="certificate-formats"></a>Formati dei certificati

Per verificare che siano inclusi tutti i certificati intermedi necessari, è necessario usare un certificato PFX. Il certificato deve includere la chiave privata.

Non ci sono restrizioni per i metodi di firma del certificato. Sono supportati certificati ECC (Elliptic Curve Cryptography), SAN (Subject Alternative Name) e altri tipi comuni di certificato. 

È possibile usare certificati con caratteri jolly purché il carattere jolly corrisponda all'URL esterno. È necessario usare certificati con caratteri jolly per le [applicazioni con caratteri jolly](application-proxy-wildcard.md). Se si intende usare il certificato per accedere anche ai sottodomini, è necessario aggiungere i caratteri jolly dei sottodomini come nomi alternativi del soggetto nello stesso certificato. Ad esempio, un certificato per *\*.adventure-works.com* non funziona per *\*.apps.adventure-works.com* a meno che non si aggiunga *\*.apps.adventure-works.com* come nome alternativo del soggetto. 

È possibile usare i certificati emessi dalla propria infrastruttura a chiave pubblica (PKI) se la catena di certificati è installata nei dispositivi client. Intune può distribuire questi certificati nei dispositivi gestiti. Per i dispositivi non gestiti, è necessario installare manualmente questi certificati. 

Non è consigliabile usare un'autorità di certificazione radice privata, poiché anche questa deve essere sottoposta a push nei computer client, il che potrebbe comportare una serie di problemi.

### <a name="certificate-management"></a>Gestione dei certificati

La gestione di tutti i certificati avviene nelle singole pagine dell'applicazione. Passare alla pagina **Proxy dell'applicazione** per la specifica applicazione e accedere al campo **Certificato**.

È possibile usare lo stesso certificato per più applicazioni. Se un certificato caricato funziona con un'altra applicazione, verrà applicato automaticamente. Non verrà richiesto di caricarlo di nuovo quando si aggiunge o si configura l'app. 

Quando un certificato scade, un avviso richiede di caricarne un altro. Se il certificato viene revocato, gli utenti potrebbero vedere un avviso di sicurezza quando accedono all'app. Per aggiornare il certificato per un'app, passare alla pagina **Proxy dell'applicazione** per l'app, selezionare **Certificato** e caricare un nuovo certificato. Se il certificato precedente non è usato da altre app, viene eliminato automaticamente. 

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md) alle app pubblicate con l'autenticazione di Azure AD.
* [Abilitare l'accesso condizionale](../conditional-access/concept-conditional-access-cloud-apps.md) alle app cloud pubblicate.
