---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Maverics Identity Orchestrator SAML Connector | Microsoft Docs"
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
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518929"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Maverics Identity Orchestrator SAML Connector

## <a name="introduction"></a>Introduzione

Strata consente di integrare facilmente applicazioni locali con Azure AD per l'autenticazione e il controllo di accesso.

Questa guida illustra come configurare Maverics Identity Orchestrator&trade; per:
* Eseguire la migrazione incrementale degli utenti da un sistema di gestione delle identità locale ad Azure AD durante l'accesso a un'applicazione locale legacy.
* Instradare le richieste di accesso da un prodotto per la gestione degli accessi Web legacy, ad esempio Oracle Access Manager o CA SiteMinder, ad Azure AD.
* Autenticare gli utenti in applicazioni locali protette tramite intestazioni HTTP o cookie di sessione proprietari dopo aver autenticato l'utente con Azure AD.

Strati fornisce software distribuito in locale o nel cloud per individuare, connettere e orchestrare i vari provider di identità in modo da creare un sistema di gestione delle identità distribuita per aziende ibride e multi-cloud.

Questa esercitazione illustra come eseguire la migrazione di un'applicazione Web locale attualmente protetta da un prodotto per la gestione degli accessi Web legacy (CA SiteMinder) in modo da usare Azure AD per l'autenticazione e il controllo di accesso.
1. Installare Maverics Identity Orchestrator&trade;
2. Registrare l'applicazione aziendale con Azure AD e configurarla per l'uso di Maverics Azure AD SAML Zero Code Connector&trade; per l'accesso SSO basato su SAML.
3. Integrare Maverics con SiteMinder e l'archivio utenti LDAP.
4. Installare Azure Key Vault e configurare Maverics per usarlo come provider di gestione dei segreti.
5. Illustrare la migrazione degli utenti e l'astrazione della sessione usando Maverics per fornire l'accesso a un'applicazione Web Java locale.

Per altre istruzioni di installazione e configurazione, vedere la pagina https://strata.io/docs

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
- Sottoscrizione di Maverics Identity Orchestrator SAML Connector abilitata per l'accesso Single Sign-On (SSO). Per ottenere il software Maverics, scrivere all'indirizzo sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Installare Maverics Identity Orchestrator&trade;

Per iniziare con l'installazione di Maverics Identity Orchestrator, vedere le istruzioni di installazione disponibili alla pagina https://strata.io/docs

## <a name="system-requirements"></a>Requisiti di sistema
### <a name="supported-operating-systems"></a>Sistemi operativi supportati
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Dependencies
* systemd

## <a name="installation"></a>Installazione

1. Ottenere la versione più recente del pacchetto RPM di Maverics. Copiare il pacchetto nel sistema in cui si vuole installare il software Maverics.

2. Installare il pacchetto Maverics, sostituendo `maverics.rpm` con il nome del file personale.

    `sudo rpm -Uvf maverics.rpm`

3. Dopo l'installazione Maverics verrà eseguito come servizio in `systemd`. Per verificare che il servizio sia in esecuzione, eseguire il comando seguente.

    `sudo systemctl status maverics`

Per impostazione predefinita, Maverics viene installato nella directory `/usr/local/bin`.

Dopo l'installazione di Maverics, nella directory `/etc/maverics` viene creato il file `maverics.yaml`. Prima di modificare la configurazione in modo da includere `workflows` e `connectors`, il file di configurazione è simile al seguente:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Opzioni di configurazione
### <a name="version"></a>Versione
Nel campo `version` viene dichiarata la versione del file di configurazione in uso. Se non è specificata, verrà usata la versione della configurazione più recente.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Indirizzo di ascolto
In `listenAddress` viene dichiarato l'indirizzo su cui Orchestrator sarà in ascolto. Se la sezione dell'indirizzo relativa all'host è vuota, Orchestrator sarà in ascolto su tutti gli indirizzi IP unicast e anycast disponibili del sistema locale. Se la sezione dell'indirizzo relativa alla porta è vuota, verrà scelto automaticamente un numero di porta.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

Nel campo `tls` viene dichiarato un mapping di oggetti TLS (Transport Layer Security). Gli oggetti TLS possono essere usati dai connettori e dal server di Orchestrator. Per tutte le opzioni TLS disponibili, vedere la documentazione relativa al pacchetto di `transport`.

Microsoft Azure richiede l'uso del protocollo di comunicazione TLS quando si usa l'accesso SSO basato su SAML. Per altre informazioni relative alla generazione dei certificati, vedere[qui](https://letsencrypt.org/getting-started/).

La chiave `maverics` è riservata al server di Orchestrator. Tutte le altre chiavi sono disponibili e possono essere usate per inserire un oggetto TLS in un determinato connettore.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>File di inclusione

È possibile definire `connectors` e `workflows` nei relativi file di configurazione e farvi riferimento da `maverics.yaml` usando `includeFiles` come nell'esempio seguente.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

In questa esercitazione viene usato un singolo file di configurazione `maverics.yaml`.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Uso di Azure Key Vault come provider di segreti

### <a name="secret-management"></a>Gestione dei segreti

Maverics può integrarsi con diverse soluzioni di gestione dei segreti per caricare i segreti. Le integrazioni correnti includono un file, Hashicorp Vault e Azure Key Vault. Se non viene specificata alcuna soluzione per la gestione dei segreti, per impostazione predefinita Maverics userà il caricamento dei segreti in testo normale all'esterno di `maverics.yaml`.
Per dichiarare un valore come segreto in un file di configurazione `maverics.yaml`, racchiudere il segreto tra parentesi angolari:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>File

Per caricare segreti da un file, aggiungere la variabile di ambiente `MAVERICS_SECRET_PROVIDER` nel file `/etc/maverics/maverics.env`, con:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Riavviare quindi il servizio maverics: `sudo systemctl restart maverics`

Nel file `secrets.yaml` è possibile aggiungere un numero qualsiasi di elementi `secrets`.
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

La procedura seguente illustra come configurare un'istanza di Azure Key Vault, usando il [portale di Azure](https://portal.azure.com) o l'[dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):

1. [Eseguire l'accesso](https://portal.azure.com) usando il portale di Azure oppure usando il comando dell'interfaccia della riga di comando:
    ```shell
    az login
    ```

2. [Creare un nuovo insieme di credenziali usando il portale](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) o il comando dell'interfaccia della riga di comando:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Aggiungere i segreti a Key Vault usando il portale](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) o un comando dell'interfaccia della riga di comando:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Registrare un'applicazione con Azure Active Directory usando il portale di Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) o un comando dell'interfaccia della riga di comando:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autorizzare un'applicazione a usare un segreto usando il portale di Azure](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) o un comando dell'interfaccia della riga di comando:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Per caricare i segreti da Azure Key Vault, impostare la variabile di ambiente `MAVERICS_SECRET_PROVIDER` nel file `/etc/maverics/maverics.env` con le credenziali trovate nel file azure-credentials.json usando il formato seguente: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Riavviare quindi il servizio maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configurare l'applicazione in Azure AD per l'accesso SSO basato su SAML

1. Nel tenant di Azure Active Directory passare a `Enterprise applications`, cercare `Maverics Identity Orchestrator SAML Connector` e selezionarlo.

2. Nella pagina delle proprietà di 'Maverics Identity Orchestrator SAML Connector' impostare `User assignment required?` su No per abilitare l'applicazione per gli utenti di cui è stata appena eseguita la migrazione.

3. Nella pagina della panoramica di 'Maverics Identity Orchestrator SAML Connector' selezionare `Setup single sign-on` e quindi `SAML`.

4. Nella pagina dell'accesso Single Sign-On basato su SAML di 'Maverics Identity Orchestrator SAML Connector' modificare la configurazione SAML di base.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

5. Impostare il valore di `Entity ID` digitando un URL nel formato seguente: `https://<SUBDOMAIN>.maverics.org`. Il valore di `Entity ID` deve essere univoco tra le app nel tenant. Salvare il valore immesso qui per includerlo nella configurazione di Maverics.

6. Impostare il valore dell'URL di risposta nel formato seguente: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

7. Impostare il valore dell'URL di accesso nel formato seguente: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`. Fare clic su Salva.

8. Passare alla sezione Certificato di firma SAML e fare clic sul pulsante Copia per copiare l''URL dei metadati di federazione dell'app e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Configurazione di Maverics Identity Orchestrator Azure AD SAML Connector

Maverics Identity Orchestrator Azure AD SAML Connector supporta: 
- OpenID Connect
- SAML Connect 

1. Per abilitare l'accesso Single Sign-On basato su SAML, impostare `authType: saml`.

1. Creare il valore per `samlMetadataURL`: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Definire ora l'URL a cui verrà reindirizzato Azure nell'app dopo che è stato eseguito l'accesso con le credenziali di Azure.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Copiare il valore dall'ID entità configurato in precedenza: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Copiare il valore dall'URL di risposta che Azure AD userà per pubblicare la risposta SAML.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Generare una chiave di firma del token JWT, usata per proteggere le informazioni della sessione di Maverics Identity Orchestrator&trade;, usando lo [strumento OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Copiare la risposta nella proprietà di configurazione `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Attributi e mapping degli attributi
Mapping di attributi consente di definire il mapping degli attributi utente di una directory utente locale di origine in Azure AD quando si effettua il provisioning degli utenti.

Gli attributi determinano i dati utente che possono essere restituiti a un'applicazione in un'attestazione, passati ai cookie di sessione o passati all'applicazione nelle variabili di intestazione HTTP.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Configurare il file YAML di Maverics Identity Orchestrator Azure AD SAML Connector

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

## <a name="migrate-users-to-azure-ad"></a>Eseguire la migrazione di utenti ad Azure AD

Seguire questa configurazione per eseguire la migrazione incrementale degli utenti da un prodotto per la gestione degli accessi Web, ad esempio CA SiteMinder, Oracle Access Manager o IBM Tivoli, una directory LDAP o un database SQL.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configurare le autorizzazioni dell'applicazione in Azure AD per creare gli utenti

1. Nel tenant di Azure Active Directory passare a `App registrations` e selezionare l'applicazione 'Maverics Identity Orchestrator SAML Connector'.

2. Nella pagina dei certificati e segreti di 'Maverics Identity Orchestrator SAML Connector' selezionare `New client secret` e quindi scegliere un'opzione di scadenza. Fare clic sul pulsante Copia per copiare il segreto e salvarlo nel computer.

3. Nella pagina delle autorizzazioni API di 'Maverics Identity Orchestrator SAML Connector' selezionare `Add permission`, quindi in Richiedi le autorizzazioni dell'API selezionare `Microsoft Graph` e infine `Application permissions`. Nella schermata successiva selezionare `User.ReadWrite.All` e quindi `Add permissions`. Questa operazione consentirà di tornare alle autorizzazioni per le API in cui selezionare `Grant admin consent`.


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Configurare il file YAML di Maverics Identity Orchestrator SAML Connector per la migrazione degli utenti

Per abilitare il flusso di lavoro di migrazione degli utenti, aggiungere queste proprietà nel file di configurazione:
1. Impostare l'URL di Azure Graph: `graphURL: https://graph.microsoft.com`
1. Impostare l'URL del token OAuth nel formato seguente: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Impostare il segreto client generato in precedenza: `oauthClientSecret: <CLIENT SECRET>`


La configurazione finale di Maverics Identity Orchestrator Azure AD SAML Connector sarà simile alla seguente:
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

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Configurare Maverics Zero Code Connector&trade; per SiteMinder

Il connettore SiteMinder consente di eseguire la migrazione degli utenti in Azure AD e permette agli utenti di accedere alle applicazioni locali legacy protette da SiteMinder usando le identità e le credenziali di Azure AD appena create.

Per questa esercitazione SiteMinder è stato configurato in modo da proteggere l'applicazione legacy con l'autenticazione basata su moduli e usando il cookie `SMSESSION`. Per l'integrazione con un'app che utilizza autenticazione e sessione tramite intestazioni HTTP, è necessario aggiungere la configurazione dell'emulazione dell'intestazione al connettore.

In questo esempio si esegue il mapping dell'attributo `username` all'intestazione HTTP `SM_USER`:
```yaml
  headers:
    SM_USER: username
```

Impostare `proxyPass` sul percorso a cui vengono inviate le richieste proxy. Si tratta in genere dell'host dell'applicazione protetta.

Il valore di `loginPage` deve corrispondere all'URL del modulo di accesso attualmente usato da SiteMinder per il reindirizzamento degli utenti per l'autenticazione.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Configurare the Maverics Zero Code Connector&trade; per LDAP

Quando le applicazioni sono protette da un prodotto WAM, ad esempio SiteMinder, gli attributi e le identità utente vengono in genere archiviati in una directory LDAP.

Questa configurazione di connettore illustra come connettersi alla directory LDAP configurata come archivio utenti per SiteMinder in modo che sia possibile raccogliere le informazioni corrette sul profilo utente durante il flusso di lavoro di migrazione e creare un utente corrispondente in Azure AD.

* `baseDN` specifica il percorso nella directory in cui eseguire la ricerca LDAP.

* `url` corrisponde all'indirizzo e alla porta del server LDAP a cui connettersi.

* `serviceAccountUsername` è il nome utente usato per la connessione al server LDAP, generalmente espresso come DN di binding, ad esempio `CN=Directory Manager`.

* `serviceAccountPassword` è la password usata per la connessione al server LDAP. Questo valore viene archiviato nell'istanza di Azure Key Vault configurata in precedenza.  

* `userAttributes` definisce l'elenco di attributi correlati all'utente per cui eseguire una query. In seguito viene eseguito il mapping di questi attributi agli attributi Azure AD corrispondenti.

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

## <a name="configure-the-migration-workflow"></a>Configurare il flusso di lavoro di migrazione

La configurazione del flusso di lavoro di migrazione determina in che modo Maverics eseguirà la migrazione degli utenti da SiteMinder/LDAP ad Azure AD.

Questo flusso di lavoro:
- Usa il connettore SiteMinder per usare un proxy per l'account di accesso SiteMinder. Le credenziali utente vengono convalidate tramite l'autenticazione SiteMinder e quindi passate ai passaggi successivi del flusso di lavoro.
- Recupera gli attributi del profilo utente dall'archivio utenti di SiteMinder.
- Effettua una richiesta all'API Microsoft Graph per creare l'utente nel tenant di Azure AD.

Passaggi:
1. Assegnare un nome al flusso di lavoro, ad esempio Migrazione da SiteMinder ad Azure AD.
2. Specificare il valore di `endpoint`, ovvero un percorso HTTP in cui il flusso di lavoro viene esposto, che attiva il valore di `actions` del flusso di lavoro in risposta alle richieste. Il valore di `endpoint` in genere corrisponde all'app per cui viene usato un proxy, ad esempio `/my_app`. Il valore deve includere sia le barre iniziali che quelle finali.
3. Aggiungere il valore di `actions` appropriato al flusso di lavoro.
    - Definire il metodo `login` per il connettore SiteMinder. Il valore del connettore deve corrispondere al valore del nome nella configurazione del connettore.
     - Definire il metodo `getprofile` per il connettore LDAP.
     - Definire il valore di `createuser` per il connettore AzureAD.

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

1. Se il servizio Maverics non è già in esecuzione, avviarlo eseguendo il comando seguente: `sudo systemctl start maverics`

2. Passare all'URL di accesso con proxy: `http://host.company.com/my_app`.
3. Fornire le credenziali utente usate per accedere all'applicazione quando è protetta da SiteMinder.
4. Passare a Home > Utenti | Tutti gli utenti per verificare se l'utente è stato creato nel tenant di Azure AD.  

## <a name="configure-the-session-abstraction-workflow"></a>Configurare il flusso di lavoro di astrazione della sessione

Il flusso di lavoro di astrazione della sessione consente di spostare l'autenticazione e il controllo di accesso per l'applicazione Web locale legacy ad Azure AD.

Il connettore Azure usa il metodo `login` per reindirizzare l'utente all'URL di accesso, presupponendo che non esista alcuna sessione.

Dopo l'autenticazione, il token di sessione creato viene passato a Maverics e viene usato il metodo `emulate` del connettore SiteMinder per emulare la sessione basata su cookie e/o la sessione basata sull'intestazione e quindi decorare la richiesta con gli eventuali attributi aggiuntivi richiesti dall'applicazione.

1. Assegnare un nome al flusso di lavoro, ad esempio Astrazione di sessione SiteMinder.
2. Specificare il valore di `endpoint`, che in genere corrisponde all'app per cui viene usato un proxy. Il valore deve includere sia le barre iniziali che quelle finali, ad esempio `/my_app/`.
3. Aggiungere il valore di `actions` appropriato al flusso di lavoro.
    - Definire il metodo `login` per il connettore di Azure. Il valore di `connector` deve corrispondere al valore di `name` nella configurazione del connettore.
    - Definire il metodo `emulate` per il connettore SiteMinder.

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

1. Passare all'URL dell'applicazione per cui è stato usato un proxy: `https://<AZURECOMPANY.COM>/<MY_APP>`. L'utente verrà reindirizzato alla pagina di accesso per cui è stato usato un proxy.
2. Immettere le credenziali dell'utente Azure AD.
3. L'utente dovrebbe essere reindirizzato all'applicazione come se l'autenticazione fosse stata eseguita direttamente da SiteMinder.
