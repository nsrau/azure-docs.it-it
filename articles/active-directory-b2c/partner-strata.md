---
title: Esercitazione per la configurazione di Azure Active Directory B2C con strati
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con whoIam per la verifica degli utenti
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: e3f067647eb7bdb33b06a9ebdefd8fdd0485e4c6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294260"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Esercitazione per estendere Azure AD B2C per proteggere le applicazioni locali usando gli strati

In questa esercitazione di esempio viene illustrato come integrare Azure Active Directory (AD) B2C con l'agente di [orchestrazione delle identità Maverics](https://www.strata.io/maverics-identity-orchestrator/)di strati.
L'agente di orchestrazione delle identità Maverics estende Azure AD B2C per proteggere le applicazioni locali. Si connette a qualsiasi sistema di identità, esegue in modo trasparente la migrazione di utenti e credenziali, sincronizza i criteri e le configurazioni ed estrae l'autenticazione e la gestione delle sessioni. L'uso di Strata Enterprise può passare rapidamente da Legacy a Azure AD B2C senza riscrivere le applicazioni. La soluzione offre i vantaggi seguenti:

- **Single Sign-On cliente (SSO) per app ibride locali** : Azure ad B2C supporta il servizio SSO con l'agente di orchestrazione delle identità Maverics. Gli utenti eseguono l'accesso con gli account ospitati in Azure AD B2C o provider di identità di social networking (IdP). Maverics estende l'accesso SSO alle app che sono state tradizionalmente protette da sistemi di identità legacy come Symantec SiteMinder.

- **Estendi l'accesso Single Sign-on basato su standard alle app senza riscriverle** : usare Azure ad B2C per gestire l'accesso degli utenti e abilitare SSO con i connettori SAML o OIDC di agente di orchestrazione identità Maverics.

- **Semplicità di configurazione** : Azure ad B2C offre una semplice interfaccia utente dettagliata per la connessione dei connettori SAML o OIDC di agente di orchestrazione Maverics Identity a Azure ad B2C.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- Un [tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

- Istanza di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) per archiviare i segreti utilizzati dall'agente di orchestrazione delle identità Maverics. Viene usato per connettersi a Azure AD B2C o ad altri provider di attributi, ad esempio una directory LDAP (Lightweight Directory Access Protocol) o un database.

- Un'istanza dell'agente di [orchestrazione delle identità Maverics](https://www.strata.io/maverics-identity-orchestrator/) che viene installata e in esecuzione in una macchina virtuale di Azure o nel server locale di propria scelta. Per informazioni su come ottenere il software e accedere alla documentazione di installazione e configurazione, contattare [Strata](https://www.strata.io/contact/)

- Applicazione locale per la transizione da un sistema di identità legacy a Azure AD B2C.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione Maverics di strati include i componenti seguenti:

- **Azure ad B2C** : il server di autorizzazione responsabile della verifica delle credenziali dell'utente. Gli utenti autenticati possono accedere alle app locali usando un account locale archiviato nella directory Azure AD B2C.

- **Un IDP sociale o aziendale esterno** : può essere qualsiasi provider OpenID Connect, Facebook, Google o github. Vedere informazioni sull'uso di [IDP esterni](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) con Azure ad B2C.  

- Agente **di orchestrazione delle identità Maverics di strati** : il servizio che orchestra l'accesso utente e passa in modo trasparente l'identità alle app tramite intestazioni HTTP.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Immagine mostra l'architettura di un'integrazione di Azure AD B2C con Strata Maverics per consentire l'accesso alle app ibride](./media/partner-strata/strata-architecture-diagram.png)

| Passaggi | Descrizione |
|:-------|:---------------|
| 1. | L'utente effettua una richiesta di accesso all'applicazione ospitata locale. L'agente di orchestrazione delle identità Maverics delega la richiesta effettuata dall'utente all'applicazione.|
| 2. | L'agente di orchestrazione controlla lo stato di autenticazione dell'utente. Se non riceve un token di sessione o il token di sessione specificato non è valido, invia l'utente a Azure AD B2C per l'autenticazione.|
| 3. | Azure AD B2C Invia la richiesta di autenticazione all'IdP sociale configurato.|
| 4. | L'IdP richiede le credenziali dell'utente. A seconda dell'IdP, l'utente può richiedere l'autenticazione a più fattori (multi-factor authentication).|
| 5. | Il provider di identità invia nuovamente la risposta di autenticazione a Azure AD B2C. Facoltativamente, l'utente può creare un account locale nella directory Azure AD B2C durante questo passaggio.|
| 6. | Azure AD B2C Invia la richiesta dell'utente all'endpoint specificato durante la registrazione dell'app di orchestrazione nel tenant del Azure AD B2C.|
| 7. | L'agente di orchestrazione valuta i criteri di accesso e calcola i valori degli attributi da includere nelle intestazioni HTTP trasmesse all'app. Durante questo passaggio, l'agente di orchestrazione può chiamare ulteriori provider di attributi per recuperare le informazioni necessarie per impostare correttamente i valori dell'intestazione.|
| 8. | L'agente di orchestrazione imposta i valori dell'intestazione e Invia la richiesta all'app.|
| 9. | L'utente è ora autenticato e può accedere all'app.|

## <a name="get-maverics-identity-orchestrator"></a>Ottenere l'agente di orchestrazione delle identità Maverics

Per ottenere il software da usare per integrare l'app locale legacy con Azure AD B2C, contattare [strati](https://www.strata.io/contact/). Dopo aver ottenuto il software, attenersi alla procedura seguente per determinare i prerequisiti specifici dell'agente di orchestrazione ed eseguire i passaggi necessari per l'installazione e la configurazione.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Configurare il tenant di Azure AD B2C

1. **Registrare l'applicazione**

   a. [Registrare l'agente di orchestrazione come applicazione](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga) in Azure ad B2C tenant.
   >[!Note]
   >Quando si configura l'istanza dell'agente di orchestrazione, saranno necessari il nome e l'identificatore del tenant, l'ID client, il segreto client, le attestazioni configurate e l'URI di reindirizzamento.

   b. Concedere a Microsoft MS API Graph le autorizzazioni per le applicazioni. Per l'applicazione sono necessarie le autorizzazioni seguenti: `offline_access` , `openid` .

   c. Aggiungere un URI di reindirizzamento per l'applicazione. Questo URI corrisponderà al `oauthRedirectURL` parametro della configurazione del connettore Azure ad B2C dell'agente di orchestrazione, ad esempio `https://example.com/oidc-endpoint` .

2. **Creare un flusso utente** : creare un [segno e un flusso utente di accesso](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows).

3. **Aggiungere un** provider di identità: scegliere di effettuare l'accesso dell'utente con un account locale o un [IDP](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers)aziendale o sociale.

4. **Definire gli attributi utente** : definire gli attributi da raccogliere durante l'iscrizione.

5. **Specificare le attestazioni dell'applicazione** : specificare gli attributi da restituire all'applicazione tramite l'istanza dell'agente di orchestrazione. L'agente di orchestrazione utilizza gli attributi delle attestazioni restituite da Azure AD B2C e può recuperare attributi aggiuntivi da altri sistemi di identità connesse, ad esempio le directory LDAP e i database. Tali attributi vengono impostati nelle intestazioni HTTP e inviati all'applicazione upstream locale.

## <a name="configure-maverics-identity-orchestrator"></a>Configurare l'agente di orchestrazione delle identità Maverics

Nelle sezioni seguenti verranno illustrati i passaggi necessari per configurare l'istanza dell'agente di orchestrazione. Per assistenza e documentazione aggiuntive, contattare [strati](https://www.strata.io/contact/).

### <a name="maverics-identity-orchestrator-server-requirements"></a>Requisiti del server dell'agente di orchestrazione identità Maverics

È possibile eseguire l'istanza dell'agente di orchestrazione in qualsiasi server, in locale o in un'infrastruttura di cloud pubblico per provider come Azure, AWS o GCP.

- Sistema operativo: REHL 7,7 o versione successiva, CentOS 7 +

- Disco: 10 GB (piccola)

- Memoria: 16 GB

- Porte: 22 (SSH/SCP), 443, 80

- Accesso radice per le attività di installazione/amministrazione

- L'agente di orchestrazione identità Maverics viene eseguito come utente `maverics` in `systemd`

- Rete in uscita dal server che ospita l'agente di orchestrazione delle identità Maverics con la possibilità di raggiungere il tenant Azure AD.

### <a name="install-maverics-identity-orchestrator"></a>Installare Maverics Identity Orchestrator

1. Ottenere la versione più recente del pacchetto RPM di Maverics. Inserire il pacchetto nel sistema in cui si vuole installare Maverics. Se si sta copiando il file in un host remoto, [SCP](https://www.ssh.com/ssh/scp/) è uno strumento utile.

2. Per installare il pacchetto Maverics, eseguire il comando seguente sostituendo il nome file al posto di `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Per impostazione predefinita, Maverics viene installato nella directory `/usr/local/bin`.

3. Dopo l'installazione Maverics verrà eseguito come servizio in `systemd`.  Per verificare che il servizio Maverics sia in esecuzione, eseguire il comando seguente:

   `sudo service maverics status`

  Se l'installazione dell'agente di orchestrazione ha avuto esito positivo, verrà visualizzato un messaggio simile al seguente:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Se il servizio Maverics non viene avviato, eseguire il comando seguente per analizzare il problema:

   `journalctl --unit=maverics.service --reverse`

   La voce di log più recente verrà visualizzata all'inizio dell'output.

Dopo l'installazione di Maverics, nella directory `/etc/maverics` viene creato il file `maverics.yaml`.

Configurare l'agente di orchestrazione per proteggere l'applicazione. Integrazione con Azure AD B2C, archiviazione e recupero di segreti da [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Definire il percorso da cui l'agente di orchestrazione deve leggere la propria configurazione.

### <a name="supply-configuration-using-environment-variables"></a>Fornire la configurazione usando le variabili di ambiente

Fornire la configurazione alle istanze dell'agente di orchestrazione tramite le variabili di ambiente.

`MAVERICS_CONFIG`

Questa variabile di ambiente indica all'istanza dell'agente di orchestrazione i file di configurazione YAML da usare e dove trovarli durante l'avvio o il riavvio. Impostare la variabile di ambiente in `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Creare la configurazione TLS dell'agente di orchestrazione

Il `tls` campo in `maverics.yaml` dichiara le configurazioni di sicurezza a livello di trasporto che l'istanza dell'agente di orchestrazione utilizzerà. I connettori possono usare oggetti TLS e il server dell'agente di orchestrazione.

La chiave `maverics` è riservata al server di Orchestrator. Tutte le altre chiavi sono disponibili e possono essere usate per inserire un oggetto TLS in un determinato connettore.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Configurare il connettore Azure AD B2C

Gli agenti di orchestrazione usano i connettori per l'integrazione con i provider di autenticazione e attributi. In questo caso, il gateway app di questo agente di orchestrazione usa il connettore Azure AD B2C come provider di autenticazione e attributo. Azure AD B2C usa il provider di identità di social networking per l'autenticazione e quindi funge da provider di attributi per l'agente di orchestrazione, passando gli attributi nel set di attestazioni nelle intestazioni HTTP.  

Questa configurazione del connettore corrisponde all'app registrata nel tenant del Azure AD B2C.

1. Copiare l'ID client, il segreto e l'URI di reindirizzamento dalla configurazione dell'app nel tenant.

2. Assegnare un nome al connettore, illustrato qui come `azureADB2C` e impostare il connettore `type` su `azure` . Prendere nota del nome del connettore poiché questo valore viene usato in altri parametri di configurazione riportati di seguito.

3. Per questa integrazione, `authType` deve essere impostato su `oidc` .

4. Impostare l'ID client copiato nel passaggio 1 come valore per il `oauthClientID` parametro.

5. Impostare il segreto client copiato nel passaggio 1 come valore per il `oauthClientSecret` parametro.

6. Impostare l'URI di reindirizzamento copiato nel passaggio 1 come valore per il `oauthRedirectURL` parametro.

7. Il connettore Azure AD B2C OIDC usa l'endpoint OIDC noto per individuare i metadati, inclusi gli URL e le chiavi di firma. Impostare il valore di sull' `oidcWellKnownURL` endpoint del tenant.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Definire Azure AD B2C come provider di autenticazione

Un provider di autenticazione determina come eseguire l'autenticazione per un utente che non ha presentato una sessione valida come parte della richiesta di risorse dell'app. La configurazione nel tenant di Azure AD B2C determina come richiedere all'utente le credenziali e applicare criteri di autenticazione aggiuntivi. Ad esempio, per richiedere un secondo fattore per completare il processo di autenticazione e decidere quali attestazioni devono essere restituite al gateway app dell'agente di orchestrazione dopo che l'autenticazione ha avuto esito positivo.

Il valore di `authProvider` deve corrispondere al valore del connettore `name` .

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Proteggere l'app locale con un gateway app dell'agente di orchestrazione

La configurazione del gateway app dell'agente di orchestrazione dichiara come Azure AD B2C proteggere l'applicazione e come gli utenti devono accedere all'app.

1. Creare un nome per il gateway app. È possibile usare un nome descrittivo o un nome host completo come identificatore per l'app.

2. Impostare `location`. L'esempio seguente usa la radice dell'app `/` , tuttavia, può essere qualsiasi percorso URL dell'applicazione.

3. Definire l'applicazione protetta in `upstream` usando la convenzione host: Port: `https://example.com:8080` .

4. Impostare i valori per le pagine errori e non autorizzate.

5. Definire i nomi di intestazione e i valori di attributo HTTP che devono essere forniti all'applicazione per stabilire l'autenticazione e controllare l'accesso all'app. I nomi delle intestazioni sono arbitrari e in genere corrispondono alla configurazione dell'app. I valori degli attributi sono spazi dei nomi dal connettore che li fornisce. Nell'esempio seguente i valori restituiti da Azure AD B2C sono preceduti dal nome `azureADB2C` del connettore dove il suffisso è il nome dell'attributo che contiene il valore richiesto, ad esempio `given_name` .

6. Impostare i criteri da valutare e applicare. Sono definite tre azioni: `allowUnauthenticated` , `allowAnyAuthenticated` e `allowIfAny` . Ogni azione è associata a un oggetto `resource` e i criteri vengono valutati `resource` .

>[!NOTE]
>Sia `headers` che `policies` usano le estensioni del servizio JavaScript o GoLang per implementare una logica arbitraria che migliori significativamente le funzionalità predefinite.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Usare Azure Key Vault come provider di segreti

È importante proteggere i segreti usati dall'agente di orchestrazione per connettersi a Azure AD B2C e a qualsiasi altro sistema di identità. Per impostazione predefinita, Maverics utilizzerà i segreti in testo normale, `maverics.yaml` ma in questa esercitazione si userà Azure Key Vault come provider di segreti.

Seguire le istruzioni per [creare una nuova Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) che l'istanza dell'agente di orchestrazione userà come provider di segreti. Aggiungere i segreti all'insieme di credenziali e prendere nota del `SECRET NAME` dato a ogni segreto. Ad esempio: `AzureADB2CClientSecret`.

Per dichiarare un valore come segreto in un file di configurazione `maverics.yaml`, racchiudere il segreto tra parentesi angolari:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

Il valore specificato nelle parentesi acute deve corrispondere al `SECRET NAME` segreto specificato nell'Azure Key Vault.

Per caricare i segreti da Azure Key Vault, impostare la variabile `MAVERICS_SECRET_PROVIDER` di ambiente nel file `/etc/maverics/maverics.env` , con le credenziali trovate nel azure-credentials.jssu file, usando il modello seguente:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Mettere insieme tutti gli elementi

Ecco come verrà visualizzata la configurazione dell'agente di orchestrazione al termine delle configurazioni descritte in precedenza.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testare il flusso

1. Passare all'URL dell'applicazione locale, `https://example.com/sonar/dashboard` .

2. L'agente di orchestrazione deve reindirizzare alla pagina configurata nel flusso utente.

3. Selezionare l'IdP dall'elenco nella pagina.

4. Quando si viene reindirizzati al provider di identità, fornire le credenziali richieste, incluso un token di autenticazione a più fattori, se richiesto da tale IdP.

5. Una volta eseguita l'autenticazione, è necessario reindirizzare a Azure AD B2C, che inoltra la richiesta dell'app all'URI di reindirizzamento dell'agente di orchestrazione.

6. L'agente di orchestrazione valuta i criteri, calcola le intestazioni e invia l'utente all'applicazione upstream.  

7. Verrà visualizzata l'applicazione richiesta.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
