---
title: Domini personalizzati nel proxy di applicazione di Azure AD | Documentazione Microsoft
description: Configurare e gestire domini personalizzati nel proxy di applicazione Azure AD.
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
ms.date: 10/16/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6aa42c63809472e1681a820031e48fe4f86fb584
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756464"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurare domini personalizzati con Azure AD proxy di applicazione

Quando si pubblica un'applicazione tramite Azure Active Directory Application Proxy, si crea un URL esterno per gli utenti. Questo URL ottiene il dominio predefinito *tenant.msappproxy.net*. Se ad esempio si pubblica un'app denominata *Expenses* nel tenant denominato *Contoso*, l'URL esterno è *https: \//expenses-contoso.msappproxy.NET*. Se si vuole usare il proprio nome di dominio anziché *msappproxy.NET*, è possibile configurare un dominio personalizzato per l'applicazione. 

## <a name="benefits-of-custom-domains"></a>Vantaggi dei domini personalizzati

Quando possibile, è consigliabile configurare domini personalizzati per le app. Alcuni motivi per usare i domini personalizzati includono:

- I collegamenti tra le app funzionano anche all'esterno della rete aziendale. Senza un dominio personalizzato, se l'app contiene collegamenti interni hardcoded a destinazioni all'esterno del proxy di applicazione e i collegamenti non sono risolvibili esternamente, si interrompono. Quando gli URL interni ed esterni sono gli stessi, è possibile evitare questo problema. Se non si è in grado di usare i domini personalizzati, vedere [reindirizzare i collegamenti hardcoded per le app pubblicate con Azure ad proxy di applicazione](../application-proxy-link-translation.md) per altri modi per risolvere questo problema. 
  
- Gli utenti avranno un'esperienza più semplice, perché possono raggiungere l'app con lo stesso URL dall'interno o dall'esterno della rete. Non devono apprendere URL interni ed esterni diversi oppure tenere traccia della posizione corrente. 

- È possibile controllare la personalizzazione e creare gli URL desiderati. Un dominio personalizzato consente di creare la confidenza degli utenti, perché gli utenti visualizzano e usano un nome familiare invece di *msappproxy.NET*.

- Alcune configurazioni funzioneranno solo con domini personalizzati. Ad esempio, sono necessari domini personalizzati per le app che usano Security Assertion Markup Language (SAML), ad esempio quando si usa Active Directory Federation Services (AD FS) ma non è possibile usare WS-Federation. Per altre informazioni, vedere [usare app in grado di riconoscere attestazioni nel proxy di applicazione](application-proxy-configure-for-claims-aware-applications.md). 

Se non si è in grado di far corrispondere gli URL interni ed esterni, non è altrettanto importante usare i domini personalizzati, ma è comunque possibile usufruire degli altri vantaggi. 

## <a name="dns-configuration-options"></a>Opzioni di configurazione DNS

Sono disponibili diverse opzioni per configurare la configurazione DNS, a seconda dei requisiti:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Stesso URL interno ed esterno, comportamento interno ed esterno diverso 

Se non si vuole che gli utenti interni vengano indirizzati tramite il proxy di applicazione, è possibile configurare un *DNS "Split Brain*". Un'infrastruttura DNS divisa indirizza gli host interni a un Domain Name Server interno e gli host esterni a una Domain Name Server esterna per la risoluzione dei nomi. 

![DNS "Split Brain"](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>URL interni ed esterni diversi 

Se gli URL interni ed esterni sono diversi, non è necessario configurare il comportamento Split-Brain, perché il routing utente è determinato dall'URL. In questo caso, viene modificato solo il DNS esterno e l'URL esterno viene indirizzato all'endpoint proxy di applicazione. 

Quando si seleziona un dominio personalizzato per un URL esterno, una barra informazioni Mostra la voce CNAME che è necessario aggiungere al provider DNS esterno. Per visualizzare queste informazioni, è sempre possibile passare alla pagina del **proxy dell'applicazione** dell'app.

## <a name="set-up-and-use-custom-domains"></a>Configurare e usare domini personalizzati

Per configurare un'app locale per l'uso di un dominio personalizzato, è necessario un dominio personalizzato verificato Azure Active Directory, un certificato PFX per il dominio personalizzato e un'app locale da configurare. 

### <a name="create-and-verify-a-custom-domain"></a>Creare e verificare un dominio personalizzato

Per creare e verificare un dominio personalizzato:

1. In Azure Active Directory selezionare **nomi di dominio personalizzati** nel campo di spostamento a sinistra e quindi selezionare **Aggiungi dominio personalizzato**. 
1. Immettere il nome di dominio personalizzato e selezionare **Aggiungi dominio**. 
1. Nella pagina dominio copiare le informazioni del record TXT per il dominio. 
1. Passare al registrar e creare un nuovo record TXT per il dominio, in base alle informazioni DNS copiate.
1. Dopo aver registrato il dominio, nella pagina del dominio Azure Active Directory selezionare **Verifica**. Una volta **verificato**lo stato del dominio, è possibile usare il dominio in tutte le configurazioni di Azure ad, incluso il proxy di applicazione. 

Per istruzioni più dettagliate, vedere [aggiungere il nome di dominio personalizzato usando il portale di Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configurare un'app per l'uso di un dominio personalizzato

Per pubblicare l'app tramite il proxy di applicazione con un dominio personalizzato:

1. Per una nuova app, in Azure Active Directory selezionare **applicazioni aziendali** nel percorso di spostamento a sinistra, selezionare **nuova applicazione**, quindi selezionare **applicazione locale**. 
   
   Per un'app già in **applicazioni aziendali**, selezionarla dall'elenco e quindi selezionare **proxy applicazione** nel percorso di spostamento a sinistra. 

1. Nel campo **URL interno** della pagina **proxy applicazione** immettere l'URL interno per l'app. 
   
1. Nel campo **URL esterno** , a discesa nell'elenco, selezionare il dominio personalizzato che si vuole usare.
   
1. Selezionare **Salva**.
   
   ![Selezionare un dominio personalizzato](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
1. Se il dominio dispone già di un certificato, il campo **certificato** Visualizza le informazioni sul certificato. In caso contrario, selezionare il campo **certificato** . 
   
   ![Fare clic per caricare un certificato](./media/application-proxy-configure-custom-domain/certificate.png)
   
1. Nella pagina **certificato SSL** individuare e selezionare il file di certificato PFX. Immettere la password per il certificato e selezionare **Carica certificato**. Per ulteriori informazioni sui certificati, vedere la sezione [certificati per domini personalizzati](#certificates-for-custom-domains) .
   
   ![Caricamento del certificato](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Un dominio personalizzato richiede che il certificato venga caricato una sola volta. Successivamente, il certificato caricato viene applicato automaticamente quando si usa il dominio personalizzato per altre app.
   
1. Se è stato aggiunto un certificato, nella pagina **proxy di applicazione** selezionare **Salva**. 
   
1. Nella barra informazioni della pagina **proxy di applicazione** prendere nota della voce CNAME che è necessario aggiungere alla zona DNS. 
   
   ![Aggiungi voce DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
1. Seguire le istruzioni in [gestire record e set di record DNS usando il portale di Azure](../../dns/dns-operations-recordsets-portal.md) per aggiungere un record DNS che reindirizza il nuovo URL esterno al dominio *msappproxy.NET* .
   
1. Per verificare che il record DNS sia configurato correttamente, usare il comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) per verificare che l'URL esterno sia raggiungibile e che il dominio *msapproxy.NET* appaia come alias.

L'applicazione è ora configurata per l'uso del dominio personalizzato. Assicurarsi di assegnare gli utenti all'applicazione prima di testarla o rilasciarla. 

Per modificare il dominio per un'app, selezionare un dominio diverso nell'elenco a discesa in **URL esterno** nella pagina **proxy applicazione** dell'app. Caricare un certificato per il dominio aggiornato, se necessario, e aggiornare il record DNS. Se non viene visualizzato il dominio personalizzato desiderato nell'elenco a discesa in **URL esterno**, è possibile che non venga verificato.

Per istruzioni più dettagliate sul proxy di applicazione, vedere [esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificati per domini personalizzati

Un certificato crea la connessione SSL sicura per il dominio personalizzato. 

### <a name="certificate-formats"></a>Formati di certificato

Per assicurarsi che siano inclusi tutti i certificati intermedi necessari, è necessario usare un certificato PFX. Il certificato deve includere la chiave privata.

Non esiste alcuna restrizione sui metodi di firma del certificato. Sono supportate la crittografia a curva ellittica (ECC), il nome alternativo del soggetto (SAN) e altri tipi di certificato comuni. 

È possibile usare i certificati con caratteri jolly purché il carattere jolly corrisponda all'URL esterno. È necessario usare i certificati con caratteri jolly per [le applicazioni con caratteri jolly](application-proxy-wildcard.md). Se si desidera utilizzare il certificato per accedere anche ai sottodomini, è necessario aggiungere i caratteri jolly del sottodominio come nomi alternativi del soggetto nello stesso certificato. Ad esempio, un certificato per *\*. Adventure-Works.com* non funzionerà per *\*. Apps.Adventure-Works.com* a meno che non si aggiunga *\*. Apps.Adventure-Works.com* come nome alternativo del soggetto. 

È possibile usare i certificati emessi dalla propria infrastruttura a chiave pubblica (PKI) se la catena di certificati è installata nei dispositivi client. Intune può distribuire questi certificati ai dispositivi gestiti. Per i dispositivi non gestiti, è necessario installare manualmente questi certificati.

Non è consigliabile usare una CA radice privata. Anche la CA radice privata deve essere inoltrata ai computer client, che introduce molte problemi. 

### <a name="certificate-management"></a>Gestione dei certificati

Tutta la gestione dei certificati è attraverso le singole pagine dell'applicazione. Passare alla pagina del **proxy di applicazione** dell'applicazione per accedere al campo **certificato** .

È possibile utilizzare lo stesso certificato per più applicazioni. Se un certificato caricato funziona con un'altra applicazione, verrà applicato automaticamente. Non verrà chiesto di caricarlo di nuovo quando si aggiunge o si configura l'app. 

Quando un certificato scade, viene visualizzato un avviso che informa di caricare un altro certificato. Se il certificato viene revocato, è possibile che venga visualizzato un avviso di sicurezza quando si accede all'app. Per aggiornare il certificato per un'app, passare alla pagina **proxy di applicazione** per l'app, selezionare **certificato**e caricare un nuovo certificato. Se il certificato precedente non è usato da altre app, viene eliminato automaticamente. 

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md) alle app pubblicate con l'autenticazione di Azure AD.
* [Abilitare l'accesso condizionale](../conditional-access/technical-reference.md#cloud-apps-assignments) alle app pubblicate.

