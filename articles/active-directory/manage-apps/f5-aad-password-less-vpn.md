---
title: Azure AD l'accesso ibrido sicuro con la VPN F5 | Microsoft Docs
description: Esercitazione per l'integrazione di Azure Active Directory Single Sign-on (SSO) con F5 BIG-IP per la VPN senza password
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2961f3f01f6ea4398fab6144b34fcb4409cdd96f
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96318170"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Esercitazione per l'integrazione di Azure Active Directory Single Sign-On con F5 BIG-IP per VPN senza password

In questa esercitazione si apprenderà come integrare una soluzione BigIP BIG-IP basata su Secure Socket Layer (SSL-VPN) con Azure Active Directory (AD) per Secure Hybrid Access (SHA).

L'integrazione di una VPN di grandi IP SSL dimensioni con Azure AD offre [molti vantaggi principali](f5-aad-integration.md), tra cui:

- Migliore governance zero con [Azure ad di pre-autenticazione e autorizzazione](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)

- [Autenticazione senza password per il servizio VPN](https://www.microsoft.com/security/business/identity/passwordless)

- Gestire le identità e l'accesso da un singolo piano di controllo: il [portale di Azure](https://portal.azure.com/#home)

Nonostante questo notevole valore aggiunto, la VPN classica rimane tuttavia predicato alla nozione di un perimetro di rete, dove attendibile si trova all'interno e non attendibile all'esterno. Questo modello non è più efficace per ottenere una vera e propria relazione di trust con zero, perché le risorse aziendali non sono più limitate alle pareti di un data center aziendale, ma piuttosto che in ambienti con più cloud senza limiti fissi. Per questo motivo, si consiglia ai clienti di prendere in considerazione il passaggio a un approccio più basato sull'identità alla gestione [dell'accesso in base alle singole applicazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad).

## <a name="scenario-description"></a>Descrizione dello scenario

In questo scenario, l'istanza di APM (BIG-IP) del servizio VPN SSL viene configurata come provider di servizi SAML (SP) e Azure AD diventa il provider di identità SAML attendibile, garantendo la pre-autenticazione. L'accesso Single Sign-on (SSO) da Azure AD viene quindi fornito tramite l'autenticazione basata su attestazioni al BIG-IP APM, offrendo un'esperienza di accesso VPN senza problemi.

![Immagine che mostra l'architettura VPN SSL](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Tutte le stringhe o i valori di esempio a cui viene fatto riferimento in questa guida devono essere sostituiti con quelli per l'ambiente effettivo.

## <a name="prerequisites"></a>Prerequisiti

Tuttavia, l'esperienza precedente o la conoscenza di F5 BIG-IP non è necessaria, ma è necessario:

- Una [sottoscrizione Azure ad gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) o superiore

- Le identità utente devono essere [sincronizzate dalla directory locale](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) a Azure ad.

- Un account con [autorizzazioni](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator) di amministratore dell'applicazione Azure ad

- Un'infrastruttura BIG-IP esistente con routing del traffico client da e verso il BIG-IP o [distribuire un'edizione virtuale Big-IP in Azure](f5-bigip-deployment-guide.md).

- È necessario che sia presente un record per il servizio VPN pubblicato da BIG-IP in DNS pubblico o un file localhost del client di test durante il test

- È necessario eseguire il provisioning di BIG-IP con i certificati SSL necessari per la pubblicazione di servizi su HTTPS.

Acquisire familiarità con la [terminologia di F5 Big-IP](https://www.f5.com/services/resources/glossary) consente inoltre di comprendere i vari componenti a cui si fa riferimento nell'esercitazione.

>[!NOTE]
>Azure è in continua evoluzione, quindi non stupirsi se si riscontrano sfumature tra le istruzioni in questa guida e ciò che viene visualizzato nella portale di Azure. Le schermate sono di tipo BIG-IP versione 15, tuttavia, rimangono relativamente simili a quelle della versione 13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Aggiungere F5 BIG-IP dalla raccolta di Azure AD

La configurazione di una relazione di trust federativa SAML tra BIG-IP consente all'Azure AD BIG-IP di passare la preautenticazione e l' [accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) ai Azure ad, prima di concedere l'accesso al servizio VPN pubblicato.

1. Accedere al portale di Azure AD usando un account con diritti di amministratore dell'applicazione

2. Dal riquadro di spostamento a sinistra selezionare il **servizio Azure Active Directory**

3. Passare ad **applicazioni aziendali** e dalla barra multifunzione superiore selezionare **nuova applicazione**.

4. Cercare F5 nella raccolta e selezionare **F5 Big-IP APM Azure ad Integration**.

5. Specificare un nome per l'applicazione, seguito da **Aggiungi/crea** per aggiungerlo al tenant. L'utente può visualizzare il nome come icona nei portali delle applicazioni di Azure e Office 365. Il nome deve riflettere il servizio specifico. Ad esempio, VPN.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

1. Con le nuove proprietà dell'applicazione F5 in visualizzazione, passare a **Gestisci**  >  **Single Sign-on**

2. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**. Ignorare la richiesta di salvataggio delle impostazioni di Single Sign-On selezionando **No, verrà salvato in un secondo momento**.

3. Nel menu **imposta Single Sign-on con SAML** selezionare l'icona della penna per la **configurazione SAML di base** per fornire i dettagli seguenti:

   - Sostituire l' **URL dell'identificatore** predefinito con l'URL per il servizio pubblicazione Big-IP. ad esempio, `https://ssl-vpn.contoso.com`

   - Eseguire la stessa operazione con la casella di testo **URL di risposta** , incluso il percorso dell'endpoint SAML. ad esempio, `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - In questa configurazione, l'applicazione funzionerà in modalità avviata da IDP, dove Azure AD rilascia l'utente con un'asserzione SAML prima del reindirizzamento al servizio BIG-IP SAML. Per le app che non supportano la modalità avviata da IDP, specificare l' **URL di accesso** per il servizio Big-IP SAML. Ad esempio: `https://ssl-vpn.contoso.com`.

   - Per l'URL di disconnessione, immettere l'endpoint SLO (BIG-IP APM Single logout) pre-sospeso dall'intestazione host del servizio da pubblicare. ad esempio, `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Fornendo un URL SLO si garantisce che una sessione utente venga terminata a entrambe le estremità, BIG-IP e Azure AD, dopo che l'utente si è disconnesso. BIG-IP APM fornisce anche un' [opzione](https://support.f5.com/csp/article/K12056) per terminare tutte le sessioni quando si chiama un URL specifico dell'applicazione.

![Image Mostra la configurazione SAML di base](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>Da le TMO V16 l'endpoint SLO SAML è stato modificato in/SAML/SP/profile/redirect/SLO

4. Selezionare **Salva** prima di uscire dal menu di configurazione SAML e ignorare il prompt dei test SSO.

Osservare le proprietà della sezione **attributi utente & attestazioni** , in quanto Azure ad li emetterà agli utenti per l'autenticazione di Big-IP APM.

![Immagine che mostra le attestazioni degli attributi utente](media/f5-sso-vpn/user-attributes-claims.png)

È possibile aggiungere altre attestazioni specifiche che il servizio pubblicato da BIG-IP potrebbe prevedere, mentre si noti che tutte le attestazioni definite in aggiunta al set predefinito verranno emesse solo se esistono in Azure AD, come attributi popolati. Allo stesso modo, i ruoli della directory o le appartenenze ai [gruppi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) devono anche essere definiti in base a un oggetto utente in Azure ad prima che possano essere rilasciati come attestazioni.

![Immagine che mostra il collegamento per il download dei metadati federativi](media/f5-sso-vpn/saml-signing-certificate.png)

I certificati di firma SAML creati da Azure AD hanno una durata di tre anni, pertanto sarà necessario gestire con Azure AD informazioni aggiuntive pubblicate.

### <a name="azure-ad-authorization"></a>Autorizzazione Azure AD

Per impostazione predefinita, Azure AD rilascerà i token solo agli utenti a cui è stato concesso l'accesso a un servizio.

1. Sempre nella visualizzazione di configurazione dell'applicazione selezionare **utenti e gruppi** .

2. Selezionare **+ Aggiungi utente** e nel menu Aggiungi assegnazione selezionare **utenti e gruppi** .

3. Nella finestra di dialogo **utenti e gruppi** aggiungere i gruppi di utenti autorizzati ad accedere alla VPN, quindi **selezionare**  >  **assegna** .

![Immagine che mostra l'aggiunta di un collegamento utente ](media/f5-sso-vpn/add-user-link.png)

4. Questa operazione completa il Azure AD parte del trust federativo di SAML. È ora possibile configurare l'APM BIG-IP per pubblicare il servizio VPN SSL e configurarlo con un set di proprietà corrispondente per completare la relazione di trust, per la preautenticazione SAML.

## <a name="big-ip-apm-configuration"></a>Configurazione di BIG-IP APM

### <a name="saml-federation"></a>Federazione SAML

La sezione seguente crea il provider di servizi SAML BIG-IP e gli oggetti IDP SAML corrispondenti necessari per completare la Federazione del servizio VPN con Azure AD.

1. Passare ad **Access**  >  **Federation**  >  **SAML service provider**  >  **local SP Services** e selezionare **Crea**

![Image Mostra la configurazione di BIG-IP SAML](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Immettere un **nome** e lo stesso valore di **ID entità** definito in precedenza Azure ad e il nome di dominio completo dell'host che verrà usato per connettersi all'applicazione

![Immagine che mostra la creazione di un nuovo servizio SP SAML](media/f5-sso-vpn/create-new-saml-sp.png)

   Le impostazioni del nome SP sono necessarie solo se l'ID entità non corrisponde esattamente alla parte del **nome** host dell'URL pubblicato o se non è nel formato di URL normale basato sul nome host. Specificare lo schema esterno e il nome host dell'applicazione da pubblicare se l'ID entità è `urn:ssl-vpn:contosoonline` .

3. Scorrere verso il basso per selezionare il nuovo **oggetto SAML SP** e selezionare **Bind/UNBIND IDP Connectors**.

![Immagine che mostra la creazione della Federazione con il servizio SP locale](media/f5-sso-vpn/federation-local-sp-service.png)

4. Selezionare **Crea nuovo IDP Connector** e dal menu a discesa selezionare **dai metadati**

![Immagine che Mostra Crea nuovo connettore IDP](media/f5-sso-vpn/create-new-idp-connector.png)

5. Individuare il file XML di metadati della Federazione scaricato in precedenza e fornire un **nome del provider di identità** per l'oggetto APM che rappresenterà l'IDP SAML esterno

6. Selezionare **Aggiungi nuova riga** per selezionare il nuovo Azure ad connettore IDP esterno.

![Immagine che mostra il connettore IDP esterno](media/f5-sso-vpn/external-idp-connector.png)

7. Selezionare **Update (Aggiorna** ) per associare l'oggetto SAML SP all'oggetto IDP SAML e quindi fare clic su **OK**.

![Immagine che mostra IDP SAML con SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Configurazione di Webtop

I passaggi seguenti consentono di offrire la VPN SSL agli utenti tramite il portale web proprietario di BIG-IP.

1. Passare a **accedere** agli  >  **Webtops**  >  **elenchi di Webtop** Webtops e selezionare **Crea**.

2. Assegnare un nome al portale e impostare il tipo su **full**. Ad esempio: `Contoso_webtop`.

3. Modificare le preferenze rimanenti, quindi selezionare **fine**.

![Immagine che mostra la configurazione di Webtop](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>Configurazione VPN

La funzionalità VPN è costituita da diversi elementi, ognuno dei quali controlla un aspetto diverso dell'intero servizio.

1. Passare ad **Access**  >  **Connectivity/VPN**  >  **Network Access (VPN)**  >  **IPv4 lease pools** e selezionare **create**.

2. Consente di specificare un nome per il pool di indirizzi IP allocato ai client VPN. Ad esempio, Contoso_vpn_pool

3. Impostare tipo su **intervallo di indirizzi IP** e specificare un indirizzo IP iniziale e finale, seguito da **Aggiungi** e **terminato**.

![Immagine che mostra la configurazione VPN](media/f5-sso-vpn/vpn-configuration.png)

Un elenco di accesso alla rete effettua il provisioning del servizio con le impostazioni IP e DNS dal pool VPN, le autorizzazioni di routing utente e può anche avviare le applicazioni, se necessario.

1. Passare ad **accesso**  >  **connettività/VPN: accesso alla rete (VPN)**  >  **elenchi di accesso alla rete** e selezionare **Crea**.

2. Specificare un nome per l'elenco di accesso alla VPN e la didascalia, ad esempio contoso-VPN seguito da **fine**.

![Immagine mostra la configurazione VPN nell'elenco di accesso alla rete](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Dalla barra multifunzione superiore selezionare **impostazioni di rete** e aggiungere le impostazioni seguenti:

   • **Versione IP supportata**: IPv4

   • **Pool di lease IPv4**: selezionare il pool VPN creato in precedenza, ad esempio Contoso_vpn_pool

![Immagine che mostra il pool VPN di contoso](media/f5-sso-vpn/contoso-vpn-pool.png)

   È possibile usare le opzioni di impostazioni client per applicare restrizioni sulla modalità di indirizzamento del traffico client quando viene stabilita una VPN.

4. Selezionare **fine** e passare alla scheda DNS/hosts per aggiungere le impostazioni:

   • **Server dei nomi primari IPv4**: IP del server DNS dell'ambiente

   • **Suffisso di dominio predefinito DNS**: il suffisso di dominio per questa connessione VPN specifica. Ad esempio, contoso.com

![Immagine che mostra il suffisso di dominio predefinito](media/f5-sso-vpn/domain-suffix.png)

[F5 articolo](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) fornisce informazioni dettagliate sulla modifica delle impostazioni rimanenti in base alle proprie preferenze.

È ora necessario un profilo di connessione BIG-IP per configurare le impostazioni per ogni tipo di client VPN che il servizio VPN deve supportare. Ad esempio, Windows, OSX e Android.

1. Passare ad **accedere** ai profili connettività  >  **/VPN**  >  **Connectivity**  >  **Profiles** e selezionare **Aggiungi**.

2. Specificare un nome di profilo e impostare il profilo padre su **/Common/Connectivity**, ad esempio Contoso_VPN_Profile.

![Immagine che Mostra Crea nuovo profilo di connettività](media/f5-sso-vpn/create-connectivity-profile.png)

La [documentazione](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) di BigIP fornisce altri dettagli sul supporto client.

## <a name="access-profile-configuration"></a>Configurazione del profilo di accesso

Con gli oggetti VPN configurati, è necessario un criterio di accesso per abilitare il servizio per l'autenticazione SAML.

1. Passare ad **accedere** ai profili  >  **/criteri** di  >  **accesso (criteri per sessione)** e selezionare **Crea**

2. Specificare un nome di profilo e per il tipo di profilo selezionare **tutti**, ad esempio Contoso_network_access

3. Scorrere verso il basso per aggiungere almeno una lingua all'elenco **lingue accettate** e selezionare **completato**

![Immagine che mostra le proprietà generali](media/f5-sso-vpn/general-properties.png)

4. Selezionare **modifica** nel campo Per-Session criteri del nuovo profilo di accesso per avviare l'editor dei criteri visivi in una scheda del browser separata.

![Immagine che mostra i criteri per sessione](media/f5-sso-vpn/per-session-policy.png)

5. Selezionare il **+** segno e nella finestra popup selezionare autenticazione SAML **autenticazione**  >  **SAML Auth**  >  **Aggiungi elemento**.

6. Nella configurazione di SAML authentication SP selezionare l'oggetto SP SAML della VPN creato in precedenza, seguito da **Save**.

![Immagine che mostra l'autenticazione SAML](media/f5-sso-vpn/saml-authentication.png)

7. Selezionare **+** per il ramo riuscito di autenticazione SAML.

8. Dalla scheda assegnazione selezionare **assegnazione risorse avanzata** seguita da **Aggiungi elemento**

![Immagine che mostra l'assegnazione di risorse avanzata](media/f5-sso-vpn/advance-resource-assign.png)

9. Nella finestra popup selezionare **nuova voce** , quindi **Aggiungi/Elimina**.

10. Nella finestra figlio selezionare accesso alla **rete** e quindi selezionare il profilo di accesso alla rete creato in precedenza.

![Immagine che mostra l'aggiunta della nuova voce di accesso alla rete](media/f5-sso-vpn/add-new-entry.png)

11. Passare alla scheda **Webtop** e aggiungere l'oggetto Webtop creato in precedenza.

![Immagine che mostra l'aggiunta di un oggetto Webtop](media/f5-sso-vpn/add-webtop-object.png)

12. Selezionare **aggiornamento** seguito da **Salva**.

13. Selezionare il collegamento nella casella Nega superiore per modificare il ramo completato in modo da **consentire** il **salvataggio**.

![Immagine che mostra il nuovo editor dei criteri visivi](media/f5-sso-vpn/vizual-policy-editor.png)

14. Per eseguire il commit di queste impostazioni, selezionare **Applica criteri di accesso** e chiudere la scheda Editor dei criteri visivi.

![Immagine che mostra il nuovo gestore di criteri di accesso](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Pubblicare il servizio VPN

Con tutte le impostazioni, APM ora richiede un server virtuale front-end per l'ascolto dei client che si connettono alla VPN.

1. Selezionare elenco server virtuali **del traffico locale**  >  **Virtual Servers**  >  **Virtual Server List** e selezionare **Crea**.

2. Specificare un **nome** per il server virtuale VPN, ad esempio **VPN_Listener**.

3. Fornire al server virtuale un **indirizzo IP di destinazione** non usato con routing sul posto per ricevere il traffico client

4. Impostare la porta del servizio su **443 HTTPS** e verificare che lo stato sia **abilitato**

![Immagine che mostra il nuovo server virtuale](media/f5-sso-vpn/new-virtual-server.png)

5. Impostare il **profilo http** su http e aggiungere il profilo SSL (client) per il certificato SSL pubblico di cui è stato effettuato il provisioning come parte dei prerequisiti.

![Immagine che mostra il profilo SSL](media/f5-sso-vpn/ssl-profile.png)

6. In criteri di accesso impostare il profilo di **accesso** e il **profilo di connettività** per usare gli oggetti VPN creati.

![Immagine che mostra i criteri di accesso](media/f5-sso-vpn/access-policy.png)

7. Al termine, selezionare **fine** .

8.  Il servizio VPN SSL è ora pubblicato e accessibile tramite SHA, direttamente tramite l'URL o tramite i portali dell'applicazione Microsoft.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Fine delle password, vai alla password](https://www.microsoft.com/security/business/identity/passwordless)

- [Informazioni sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Zero Trust Framework per abilitare il lavoro remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Cinque passaggi per l'integrazione completa dell'applicazione con Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad)

## <a name="next-steps"></a>Passaggi successivi

Aprire un browser in un client Windows remoto e passare all'URL del **servizio VPN Big-IP**. Dopo l'autenticazione per Azure AD, verranno visualizzati il portale e l'utilità di avvio VPN di BIG-IP Webtop.

![Immagine che mostra l'utilità di avvio VPN](media/f5-sso-vpn/vpn-launcher.png)

Selezionando il riquadro VPN viene installato il client Edge BIG-IP e viene stabilita una connessione VPN configurata per SHA.
L'applicazione VPN F5 dovrebbe essere visibile anche come risorsa di destinazione in Azure AD l'accesso condizionale. Vedere le [linee guida](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) per la creazione di criteri di accesso condizionale e anche per consentire agli utenti di Azure ad [l'autenticazione senza password](https://www.microsoft.com/security/business/identity/passwordless).