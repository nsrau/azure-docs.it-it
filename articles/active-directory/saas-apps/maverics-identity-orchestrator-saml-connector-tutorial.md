---
title: "Esercitazione: Integrare l'accesso Single Sign-On (SSO) di Azure Active Directory con Maverics Identity Orchestrator SAML Connector | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: fbab2bbaa47090ff4bd7fb99495912bd1f645b61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758140"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Esercitazione: Integrare l'accesso Single Sign-On di Azure AD con Maverics Identity Orchestrator SAML Connector

Strata consente di integrare facilmente applicazioni locali con Azure Active Directory (Azure AD) per l'autenticazione e il controllo di accesso.

Questo articolo illustra come configurare Maverics Identity Orchestrator per:
* Eseguire la migrazione incrementale degli utenti da un sistema di gestione delle identità locale ad Azure AD durante l'accesso a un'applicazione locale legacy.
* Instradare ad Azure AD le richieste di accesso da un prodotto per la gestione degli accessi Web legacy, ad esempio CA SiteMinder o Oracle Access Manager.
* Autenticare gli utenti in applicazioni locali protette tramite intestazioni HTTP o cookie di sessione proprietari dopo aver autenticato l'utente con Azure AD.

Strata fornisce software che è possibile distribuire in locale o nel cloud. Consente di individuare, connettere e orchestrare i vari provider di identità in modo da creare un sistema di gestione delle identità distribuita per aziende ibride e multi-cloud.

Questa esercitazione illustra come eseguire la migrazione di un'applicazione Web locale attualmente protetta da un prodotto per la gestione degli accessi Web legacy (CA SiteMinder) in modo da usare Azure AD per l'autenticazione e il controllo di accesso. I passaggi principali sono indicati di seguito.
1. Installare Maverics Identity Orchestrator.
2. Registrare l'applicazione aziendale in Azure AD e configurarla per l'uso di Maverics Azure AD SAML Zero Code Connector per l'accesso SSO (Single Sign-On) basato su SAML.
3. Integrare Maverics con SiteMinder e l'archivio utenti LDAP (Lightweight Directory Access Protocol).
4. Configurare un insieme di credenziali delle chiavi di Azure e configurare Maverics per usarlo come provider di gestione dei segreti.
5. Illustrare la migrazione degli utenti e l'astrazione della sessione usando Maverics per fornire l'accesso a un'applicazione Web Java locale.

Per altre istruzioni di installazione e configurazione, visitare il [sito Web di Strata](https://www.strata.io).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
- Sottoscrizione di Maverics Identity Orchestrator SAML Connector abilitata per l'accesso SSO. Per ottenere il software Maverics, contattare l'[ufficio vendite di Strata](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Installare Maverics Identity Orchestrator

Per iniziare con l'installazione di Maverics Identity Orchestrator, vedere le [istruzioni di installazione](https://www.strata.io).

### <a name="system-requirements"></a>Requisiti di sistema
* Sistemi operativi supportati
  * RHEL 7+
  * CentOS 7+

* Dependencies
  * systemd

### <a name="installation"></a>Installazione

1. Ottenere il pacchetto Maverics Redhat Package Manager (RPM) più recente. Copiare il pacchetto nel sistema in cui si vuole installare il software Maverics.

2. Installare il pacchetto Maverics, sostituendo `maverics.rpm` con il nome del file personale.

    `sudo rpm -Uvf maverics.rpm`

3. Dopo l'installazione, Maverics verrà eseguito come servizio in `systemd`. Per verificare che il servizio sia in esecuzione, eseguire il comando seguente:

    `sudo systemctl status maverics`

Per impostazione predefinita, Maverics viene installato nella directory */usr/local/bin*.

Dopo aver installato Maverics viene creato il file *maverics.yaml* predefinito nella directory */etc/maverics*. Prima di modificare la configurazione in modo da includere `workflows` e `connectors`, il file di configurazione è simile al seguente:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Opzioni di configurazione
### <a name="version"></a>Versione
Nel campo `version` viene dichiarata la versione del file di configurazione in uso. Se la versione non è specificata, verrà usata la versione della configurazione più recente.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
In `listenAddress` viene dichiarato l'indirizzo su cui Orchestrator sarà in ascolto. Se la sezione dell'indirizzo relativa all'host è vuota, Orchestrator sarà in ascolto su tutti gli indirizzi IP unicast e anycast disponibili del sistema locale. Se la sezione dell'indirizzo relativa alla porta è vuota, viene scelto automaticamente un numero di porta.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Nel campo `tls` viene dichiarato un mapping di oggetti TLS (Transport Layer Security). Gli oggetti TLS possono essere usati dai connettori e dal server di Orchestrator. Per tutte le opzioni TLS disponibili, vedere la documentazione relativa al pacchetto di `transport`.

Microsoft Azure richiede la comunicazione su TLS quando si usa l'accesso SSO basato su SAML. Per informazioni sulla generazione di certificati, visitare il [sito Web Let's Encrypt](https://letsencrypt.org/getting-started/).

La chiave `maverics` è riservata al server di Orchestrator. Tutte le altre chiavi sono disponibili e possono essere usate per inserire un oggetto TLS in un determinato connettore.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>File di inclusione

È possibile definire `connectors` e `workflows` in file di configurazione distinti e farvi riferimento nel file *maverics.yaml* usando `includeFiles`, come nell'esempio seguente:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Questa esercitazione usa un singolo file di configurazione *maverics.yaml*.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Usare Azure Key Vault come provider di segreti

### <a name="manage-secrets"></a>Gestire i segreti

Per caricare i segreti, Maverics può integrarsi con varie soluzioni di gestione dei segreti. Le integrazioni correnti includono un file, Hashicorp Vault e Azure Key Vault. Se non viene specificata alcuna soluzione per la gestione dei segreti, per impostazione predefinita Maverics carica i segreti in testo normale dal file *maverics.yaml*.

Per dichiarare un valore come segreto in un file di configurazione *maverics.yaml*, racchiudere il segreto tra parentesi angolari:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Caricare i segreti da un file

1. Per caricare i segreti da un file, aggiungere la variabile di ambiente `MAVERICS_SECRET_PROVIDER` nel file */etc/maverics/maverics.env* con:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Riavviare quindi il servizio Maverics eseguendo:

   `sudo systemctl restart maverics`

Nel file *secrets.yaml* è possibile aggiungere un numero qualsiasi di elementi `secrets`.

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Configurare un insieme di credenziali delle chiavi di Azure

È possibile configurare un insieme di credenziali delle chiavi di Azure usando il portale di Azure o l'interfaccia della riga di comando di Azure.

**Usare il portale di Azure**
1. Accedere al [portale di Azure](https://portal.azure.com).
1. [Creare un nuovo insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault).
1. [Aggiungere i segreti all'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).
1. [Registrare un'applicazione in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).
1. [Autorizzare un'applicazione a usare un segreto](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).

**Utilizzare l’interfaccia della riga di comando di Azure**

1. Aprire l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e quindi immettere il comando seguente:

    ```shell
    az login
    ```

1. Creare un nuovo insieme di credenziali delle chiavi eseguendo il comando seguente:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Aggiungere i segreti all'insieme di credenziali delle chiavi eseguendo il comando seguente:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Registrare un'applicazione in Azure AD eseguendo il comando seguente:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Autorizzare un'applicazione a usare un segreto eseguendo il comando seguente:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Per caricare i segreti dall'insieme di credenziali delle chiavi di Azure, impostare la variabile di ambiente `MAVERICS_SECRET_PROVIDER` nel file */etc/maverics/maverics.env* usando le credenziali presenti nel file *azure-credentials.json* nel formato seguente:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Riavviare il servizio Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configurare l'applicazione in Azure AD per l'accesso SSO basato su SAML

1. Nel tenant di Azure AD passare ad **Applicazioni aziendali**, cercare **Maverics Identity Orchestrator SAML Connector** e quindi selezionare l'applicazione.

1. Nel riquadro **Proprietà** di Maverics Identity Orchestrator SAML Connector impostare **Assegnazione utenti obbligatoria** su **No** per abilitare l'applicazione per gli utenti di cui è stata appena eseguita la migrazione.

1. Nel riquadro **Panoramica** di Maverics Identity Orchestrator SAML Connector selezionare **Configura l'accesso Single Sign-On**  e quindi selezionare **SAML**.

1. Nel riquadro **SAML-based sign on** (Accesso basato su SAML) di Maverics Identity Orchestrator SAML Connector modificare **Configurazione SAML di base** selezionando il pulsante **Modifica** (icona a forma di matita).

   ![Screenshot del pulsante Modifica per "Configurazione SAML di base".](common/edit-urls.png)

1. Immettere l'**ID entità** digitando un URL nel formato seguente: `https://<SUBDOMAIN>.maverics.org`. L'ID entità deve essere univoco tra le app nel tenant. Salvare il valore immesso qui per includerlo nella configurazione di Maverics.

1. Immettere **URL di risposta** nel formato seguente: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

1. Immettere **URL di accesso** nel formato seguente: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. 

1. Selezionare **Salva**.

1. Nella sezione **Certificato di firma SAML** selezionare il pulsante **Copia** per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Screenshot del pulsante Copia per "Certificato di firma SAML".](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Configurare Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD Connector supporta OpenID Connect e SAML Connect. Per configurare il connettore, eseguire le operazioni seguenti: 

1. Per abilitare l'accesso Single Sign-On basato su SAML, impostare `authType: saml`.

1. Creare il valore per `samlMetadataURL` nel formato seguente: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.

1. Definire l'URL a cui verrà reindirizzato Azure nell'app dopo l'accesso degli utenti con le credenziali di Azure. Usare il formato seguente: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`.

1. Copiare il valore di EntityID configurato in precedenza: `samlEntityID: https://<SUBDOMAIN>.maverics.org`.

1. Copiare il valore dall'URL di risposta che Azure AD userà per pubblicare la risposta SAML: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`.

1. Generare una chiave di firma JWT (JSON Web Token), che viene usata per proteggere le informazioni sulla sessione di Maverics Identity Orchestrator usando lo strumento [OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Copiare la risposta nella proprietà di configurazione `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`.

## <a name="attributes-and-attribute-mapping"></a>Attributi e mapping degli attributi
Il mapping degli attributi consente di definire il mapping degli attributi utente di una directory utente locale di origine in un tenant di Azure AD dopo la configurazione dell'utente.

Gli attributi determinano i dati utente che possono essere restituiti a un'applicazione in un'attestazione, passati ai cookie di sessione o passati all'applicazione nelle variabili di intestazione HTTP.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Configurare il file YAML di Maverics Identity Orchestrator Azure AD SAML Connector

La configurazione di Maverics Identity Orchestrator Azure AD SAML Connector sarà simile alla seguente:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Eseguire la migrazione degli utenti a un tenant di Azure AD

Seguire questa configurazione per eseguire la migrazione incrementale degli utenti da un prodotto per la gestione degli accessi Web, ad esempio CA SiteMinder, Oracle Access Manager o IBM Tivoli. È anche possibile eseguirne la migrazione da una directory LDAP (Lightweight Directory Access Protocol) o da un database SQL.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configurare le autorizzazioni dell'applicazione in Azure AD per creare gli utenti

1. Nel tenant di Azure AD passare a `App registrations` e selezionare l'applicazione **Maverics Identity Orchestrator SAML Connector**.

1. Nella riquadro **Maverics Identity Orchestrator SAML Connector | Certificati e segreti** selezionare `New client secret` e quindi selezionare un'opzione di scadenza. Selezionare il pulsante **Copia** per copiare il segreto e salvarlo nel computer.

1. Nel riquadro **Maverics Identity Orchestrator SAML Connector | Autorizzazioni API** selezionare **Aggiungi autorizzazione** e quindi nel riquadro **Richiedi le autorizzazioni dell'API** selezionare **Microsoft Graph** e **Autorizzazioni applicazione**. 

1. Nella schermata successiva selezionare **User.ReadWrite.All** e quindi selezionare **Aggiungi autorizzazioni**. 

1. Di nuovo nel riquadro **Autorizzazioni API** selezionare **Fornisci il consenso amministratore**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Configurare il file YAML di Maverics Identity Orchestrator SAML Connector per la migrazione degli utenti

Per abilitare il flusso di lavoro di migrazione degli utenti, aggiungere queste proprietà aggiuntive al file di configurazione:
1. Immettere l'**URL di Azure Graph** nel formato seguente: `graphURL: https://graph.microsoft.com`.
1. Immettere l'**URL del token OAuth** nel formato seguente: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`.
1. Immettere il segreto client generato in precedenza nel formato seguente: `oauthClientSecret: <CLIENT SECRET>`.


Il file di configurazione finale di Maverics Identity Orchestrator Azure AD SAML Connector sarà simile al seguente:

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Configurare Maverics Zero Code Connector per SiteMinder

Usare il connettore SiteMinder per eseguire la migrazione degli utenti a un tenant di Azure AD. Gli utenti accedono ad applicazioni locali legacy protette da SiteMinder usando le nuove identità e credenziali di Azure AD create.

Per questa esercitazione SiteMinder è stato configurato in modo da proteggere l'applicazione legacy con l'autenticazione basata su moduli e usando il cookie `SMSESSION`. Per l'integrazione con un'app che utilizza informazioni di autenticazione e sessione tramite intestazioni HTTP, è necessario aggiungere la configurazione dell'emulazione dell'intestazione al connettore.

In questo esempio si esegue il mapping dell'attributo `username` all'intestazione HTTP `SM_USER`:

```yaml
  headers:
    SM_USER: username
```

Impostare `proxyPass` sulla posizione a cui vengono inviate le richieste proxy. Si tratta in genere dell'host dell'applicazione protetta.

Il valore di `loginPage` deve corrispondere all'URL del modulo di accesso attualmente usato da SiteMinder per il reindirizzamento degli utenti per l'autenticazione.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Configurare Maverics Zero Code Connector per LDAP

Quando le applicazioni sono protette da un prodotto di gestione dell'accesso Web (WAM) come SiteMinder, gli attributi e le identità utente vengono in genere archiviati in una directory LDAP.

Questa configurazione del connettore dimostra come connettersi alla directory LDAP. Il connettore è configurato come archivio utenti per SiteMinder in modo che sia possibile raccogliere le informazioni corrette sul profilo utente durante il flusso di lavoro di migrazione e creare un utente corrispondente in Azure AD.

* `baseDN` specifica il percorso nella directory in cui eseguire la ricerca LDAP.

* `url` corrisponde all'indirizzo e alla porta del server LDAP a cui connettersi.

* `serviceAccountUsername` è il nome utente usato per la connessione al server LDAP, generalmente espresso come DN di binding, ad esempio `CN=Directory Manager`.

* `serviceAccountPassword` è la password usata per la connessione al server LDAP. Questo valore viene archiviato nell'istanza dell'insieme di credenziali delle chiavi di Azure configurata in precedenza.  

* `userAttributes` definisce l'elenco di attributi correlati all'utente da recuperare. In seguito viene eseguito il mapping di questi attributi agli attributi Azure AD corrispondenti.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>Configurare il flusso di lavoro di migrazione

La configurazione del flusso di lavoro di migrazione determina in che modo Maverics esegue la migrazione degli utenti da SiteMinder o LDAP ad Azure AD.

Questo flusso di lavoro:
- Usa il connettore SiteMinder per usare un proxy per l'account di accesso SiteMinder. Le credenziali utente vengono convalidate tramite l'autenticazione SiteMinder e quindi passate ai passaggi successivi del flusso di lavoro.
- Recupera gli attributi del profilo utente dall'archivio utenti di SiteMinder.
- Effettua una richiesta all'API Microsoft Graph per creare l'utente nel tenant di Azure AD.

Per configurare il flusso di lavoro di migrazione, eseguire le operazioni seguenti:

1. Assegnare un nome al flusso di lavoro, ad esempio **Migrazione da SiteMinder ad Azure AD**.
1. Specificare il valore di `endpoint`, ovvero un percorso HTTP in cui il flusso di lavoro viene esposto, che attiva le azioni (`actions`) del flusso di lavoro in risposta alle richieste. Il valore di `endpoint` in genere corrisponde all'app per cui viene usato un proxy, ad esempio `/my_app`. Il valore deve includere sia le barre iniziali che quelle finali.
1. Aggiungere il valore di `actions` appropriato al flusso di lavoro.

   a. Definire il metodo `login` per il connettore SiteMinder. Il valore del connettore deve corrispondere al valore del nome nella configurazione del connettore.

   b. Definire il metodo `getprofile` per il connettore LDAP.

   c.  Definire il metodo `createuser` per il connettore AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Verificare il flusso di lavoro di migrazione

1. Se il servizio Maverics non è già in esecuzione, avviarlo eseguendo il comando seguente:  

   `sudo systemctl start maverics`

1. Passare all'URL di accesso con proxy: `http://host.company.com/my_app`.
1. Fornire le credenziali utente usate per accedere all'applicazione mentre è protetta da SiteMinder.
4. Passare a **Home** > **Utenti | Tutti gli utenti** per verificare se l'utente è stato creato nel tenant di Azure AD.  

### <a name="configure-the-session-abstraction-workflow"></a>Configurare il flusso di lavoro di astrazione della sessione

Il flusso di lavoro di astrazione della sessione consente di spostare l'autenticazione e il controllo di accesso per l'applicazione Web locale legacy al tenant di Azure AD.

Il connettore Azure usa il metodo `login` per reindirizzare l'utente all'URL di accesso, presupponendo che non esista alcuna sessione.

Dopo l'autenticazione, il token di sessione creato come risultato viene passato a Maverics. Il metodo `emulate` del connettore SiteMinder viene usato per emulare la sessione basata su cookie o la sessione basata su intestazione e quindi decorare la richiesta con gli eventuali attributi aggiuntivi richiesti dall'applicazione.

1. Assegnare un nome al flusso di lavoro, ad esempio **Astrazione di sessione SiteMinder**.
1. Specificare il valore di `endpoint`, che corrisponde all'app per cui viene usato un proxy. Il valore deve includere sia la barra iniziale che quella finale, ad esempio `/my_app/`.
1. Aggiungere il valore di `actions` appropriato al flusso di lavoro.

   a. Definire il metodo `login` per il connettore di Azure. Il valore di `connector` deve corrispondere al valore di `name` nella configurazione del connettore.

   b. Definire il metodo `emulate` per il connettore SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Verificare il flusso di lavoro di astrazione della sessione

1. Passare all'URL dell'applicazione per cui è stato usato un proxy: `https://<AZURECOMPANY.COM>/<MY_APP>`. 
    
    L'utente verrà reindirizzato alla pagina di accesso per cui è stato usato un proxy.

1. Immettere le credenziali dell'utente Azure AD.

   L'utente dovrebbe essere reindirizzato all'applicazione come se l'autenticazione fosse stata eseguita direttamente da SiteMinder.
