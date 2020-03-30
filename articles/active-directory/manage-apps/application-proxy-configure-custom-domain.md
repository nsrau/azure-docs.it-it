---
title: Domini personalizzati nel proxy di applicazione di Azure AD | Documentazione Microsoft
description: Configurare e gestire domini personalizzati nel proxy di applicazione di Azure AD.
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
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481382"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurare domini personalizzati con il proxy di applicazione di Azure ADConfigure custom domains with Azure AD Application Proxy

Quando si pubblica un'applicazione tramite il proxy di applicazione di Azure Active Directory, si crea un URL esterno per gli utenti. Questo URL ottiene il dominio predefinito *tenant.msappproxy.net*. Ad esempio, se si pubblica un'app denominata *Spese* nel tenant denominato *Contoso*, l'URL esterno sarà *\/https: /expenses-contoso.msappproxy.net*. Se si desidera utilizzare il proprio nome di dominio anziché *msappproxy.net*, è possibile configurare un dominio personalizzato per l'applicazione. 

## <a name="benefits-of-custom-domains"></a>Vantaggi dei domini personalizzati

È consigliabile configurare domini personalizzati per le app quando possibile. Alcuni motivi per utilizzare domini personalizzati includono:

- I collegamenti tra le app funzionano anche all'esterno della rete aziendale. Senza un dominio personalizzato, se l'app ha collegamenti interni hardcoded a destinazioni esterne al proxy di applicazione e i collegamenti non sono risolvibili esternamente, si interromperanno. Quando gli URL interni ed esterni sono uguali, si evita questo problema. Se non si è in grado di usare domini personalizzati, vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con](../application-proxy-link-translation.md) il proxy di applicazione di Azure AD per altri modi per risolvere questo problema. 
  
- Gli utenti avranno un'esperienza più semplice, perché possono accedere all'app con lo stesso URL dall'interno o dall'esterno della rete. Non è necessario che appaiano URL interni ed esterni diversi o se tengono traccia della posizione corrente. 

- Puoi controllare la tua personalizzazione e creare gli URL che desideri. Un dominio personalizzato consente di creare l'attendibilità degli utenti, poiché gli utenti visualizzano e utilizzano un nome familiare anziché *msappproxy.net*.

- Alcune configurazioni funzionano solo con domini personalizzati. Ad esempio, sono necessari domini personalizzati per le app che utilizzano SAML (Security Assertion Markup Language), ad esempio quando si utilizza Active Directory Federation Services (ADFS), ma non è possibile utilizzare WS-Federation. Per altre informazioni, vedere Usare le app in grado di [riconoscere attestazioni in Proxy applicazione.](application-proxy-configure-for-claims-aware-applications.md) 

Se non riesci a far corrispondere gli URL interni ed esterni, non è così importante usare domini personalizzati, ma puoi comunque sfruttare gli altri vantaggi. 

## <a name="dns-configuration-options"></a>Opzioni di configurazione DNS

Sono disponibili diverse opzioni per configurare la configurazione DNS, a seconda dei requisiti:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Stesso URL interno ed esterno, comportamento interno ed esterno diverso 

Se non si desidera che gli utenti interni vengano indirizzati tramite il proxy di applicazione, è possibile impostare un *DNS split brain*. Un'infrastruttura DNS divisa indirizza gli host interni a un server dei nomi di dominio interno e gli host esterni a un server dei nomi di dominio esterno, per la risoluzione dei nomi. 

![DNS "split brain"](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>URL interni ed esterni diversi 

Se gli URL interni ed esterni sono diversi, non è necessario configurare il comportamento split-brain, perché il routing utente è determinato dall'URL. In questo caso, si modifica solo il DNS esterno e si instrada l'URL esterno all'endpoint del proxy di applicazione. 

Quando si seleziona un dominio personalizzato per un URL esterno, una barra delle informazioni mostra la voce CNAME che è necessario aggiungere al provider DNS esterno. Puoi sempre vedere queste informazioni accedendo alla pagina **Proxy dell'applicazione** dell'app.

## <a name="set-up-and-use-custom-domains"></a>Configurare e utilizzare domini personalizzati

Per configurare un'app locale per l'uso di un dominio personalizzato, sono necessari un dominio personalizzato di Azure Active Directory verificato, un certificato PFX per il dominio personalizzato e un'app locale da configurare. 

### <a name="create-and-verify-a-custom-domain"></a>Creare e verificare un dominio personalizzato

Per creare e verificare un dominio personalizzato:

1. In Azure Active Directory selezionare Nomi di **dominio personalizzati** nel riquadro di spostamento sinistro e quindi selezionare Aggiungi **dominio personalizzato**. 
1. Immettere il nome di dominio personalizzato e selezionare **Aggiungi dominio**. 
1. Nella pagina del dominio copiare le informazioni del record TXT per il dominio. 
1. Accedi al registrar e crea un nuovo record TXT per il tuo dominio, in base alle informazioni DNS copiate.
1. Dopo aver registrato il dominio, nella pagina del dominio in Azure Active Directory **selezionare Verifica**. Una volta che lo stato del dominio è **Verificato**, è possibile usare il dominio in tutte le configurazioni di Azure AD, incluso il proxy di applicazione. 

Per istruzioni più dettagliate, vedere Aggiungere il nome di dominio personalizzato usando il portale di Azure Active Directory.For more detailed instructions, see [Add your custom domain name using the Azure Active Directory portal.](../fundamentals/add-custom-domain.md)

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configurare un'app per l'uso di un dominio personalizzatoConfigure an app to use a custom domain

Per pubblicare l'app tramite il proxy di applicazione con un dominio personalizzato:

1. Per una nuova app, in Azure Active Directory selezionare **Applicazioni aziendali** nel riquadro di spostamento sinistro. Selezionare **Nuova applicazione**. Nella sezione **Applicazioni locali** selezionare **Aggiungi un'applicazione locale**. 
   
   Per un'app già presente nelle **applicazioni aziendali,** selezionarla dall'elenco e quindi selezionare **Proxy di applicazione** nel riquadro di spostamento sinistro. 

2. Nella pagina Impostazioni proxy applicazione immettere un **Nome** se si aggiunge un'applicazione locale personalizzata.

3.  Nel campo **URL interno** immettere l'URL interno dell'app.
   
4. Nel campo **URL esterno,** selezionare l'elenco e selezionare il dominio personalizzato che si desidera utilizzare.
   
5. Selezionare **Aggiungi**.
   
   ![Seleziona dominio personalizzato](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se il dominio dispone già di un certificato, nel campo **Certificato** vengono visualizzate le informazioni sul certificato. In caso contrario, selezionare il campo **Certificato.** 
   
   ![Fare clic per caricare un certificato](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Nella pagina **Certificato SSL** individuare e selezionare il file del certificato PFX. Immettere la password per il certificato e selezionare **Carica certificato**. Per altre informazioni sui certificati, vedere la sezione [Certificati per domini personalizzati.](#certificates-for-custom-domains)
   
   ![Caricamento del certificato](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Un dominio personalizzato richiede solo il relativo certificato caricato una sola volta. Successivamente, il certificato caricato viene applicato automaticamente quando si utilizza il dominio personalizzato per altre app.
   
8. Se è stato aggiunto un certificato, nella pagina **Proxy di applicazione** selezionare **Salva**. 
   
9. Nella barra delle informazioni della pagina **Proxy di applicazione** prendere nota della voce CNAME da aggiungere alla zona DNS. 
   
   ![Aggiungere la voce DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Seguire le istruzioni in [Manage DNS records and record sets by using the Azure portal](../../dns/dns-operations-recordsets-portal.md) to add a DNS record that redirects the new external URL to the *msappproxy.net* domain.
   
11. Per verificare che il record DNS sia configurato correttamente, utilizzare il comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) per verificare che l'URL esterno sia raggiungibile e che il dominio *msapproxy.net* venga visualizzato come alias.

L'applicazione è ora configurata per l'utilizzo del dominio personalizzato. Assicurarsi di assegnare gli utenti all'applicazione prima di testarla o rilasciarla. 

Per modificare il dominio per un'app, seleziona un dominio diverso dall'elenco a discesa in **URL esterno** nella pagina **Proxy di** applicazione dell'app. Caricare un certificato per il dominio aggiornato, se necessario, e aggiornare il record DNS. Se il dominio personalizzato desiderato non è visualizzato nell'elenco a discesa in **URL esterno**, potrebbe non essere verificato.

Per istruzioni più dettagliate sul proxy di applicazione, vedere [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory.](application-proxy-add-on-premises-application.md)

## <a name="certificates-for-custom-domains"></a>Certificati per domini personalizzati

Un certificato crea la connessione TLS sicura per il dominio personalizzato. 

### <a name="certificate-formats"></a>Formati dei certificati

È necessario utilizzare un certificato PFX per assicurarsi che siano inclusi tutti i certificati intermedi necessari. Il certificato deve includere la chiave privata.

Non esistono restrizioni sui metodi di firma del certificato. Sono supportati Ecc (Elliptic Curve Cryptography), SAN (Subject Alternative Name) e altri tipi di certificati comuni. 

È possibile utilizzare i certificati con caratteri jolly purché il carattere jolly corrisponda all'URL esterno. È necessario utilizzare certificati con caratteri jolly per [le applicazioni con caratteri jolly.](application-proxy-wildcard.md) Se si desidera utilizzare il certificato per accedere anche ai sottodomini, è necessario aggiungere i caratteri jolly del sottodominio come nomi alternativi del soggetto nello stesso certificato. Ad esempio, un * \** certificato per adventure-works.com non funzionerà per * \*.apps.adventure-works.com* a meno che non si aggiuva * \*.apps.adventure-works.com* come nome alternativo del soggetto. 

È possibile utilizzare i certificati emessi dalla propria infrastruttura a chiave pubblica (PKI) se la catena di certificati è installata nei dispositivi client. Intune può distribuire questi certificati ai dispositivi gestiti. Per i dispositivi non gestiti, è necessario installare manualmente questi certificati. 

Non è consigliabile utilizzare una CA radice privata poiché anche la CA radice privata dovrebbe essere inviata ai computer client, il che potrebbe introdurre molte sfide.

### <a name="certificate-management"></a>Gestione dei certificati

Tutta la gestione dei certificati è attraverso le singole pagine dell'applicazione. Passare alla pagina **Proxy di applicazione** dell'applicazione per accedere al campo **Certificato.**

È possibile utilizzare lo stesso certificato per più applicazioni. Se un certificato caricato funziona con un'altra applicazione, verrà applicato automaticamente. Non ti verrà chiesto di caricarlo di nuovo quando aggiungi o configuri l'app. 

Quando un certificato scade, viene visualizzato un avviso che indica di caricare un altro certificato. Se il certificato viene revocato, gli utenti potrebbero visualizzare un avviso di sicurezza quando accedono all'app. Per aggiornare il certificato per un'app, passare alla pagina **Proxy di applicazione** per l'app, selezionare **Certificato**e caricare un nuovo certificato. Se il certificato precedente non è utilizzato da altre app, viene eliminato automaticamente. 

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md) alle app pubblicate con l'autenticazione di Azure AD.
* [Abilitare l'accesso condizionale](../conditional-access/overview.md) alle app pubblicate.

