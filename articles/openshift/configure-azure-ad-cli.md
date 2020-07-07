---
title: "Azure Red Hat OpenShift che esegue OpenShift 4: configurare l'autenticazione Azure Active Directory tramite la riga di comando"
description: Informazioni su come configurare l'autenticazione Azure Active Directory per un cluster Azure Red Hat OpenShift che esegue OpenShift 4 tramite la riga di comando
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 45da3034891e5a82fb8423adb6bcd5e867f9d4e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205001"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurare l'autenticazione Azure Active Directory per un cluster Azure Red Hat OpenShift 4 (CLI)

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.75 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Recuperare gli URL specifici del cluster che verranno usati per configurare l'applicazione Azure Active Directory.

Costruire l'URL di callback OAuth del cluster e archiviarlo in una variabile **oauthCallbackURL**. Assicurarsi di sostituire **Aro-RG** con il nome del gruppo di risorse e **Aro-cluster** con il nome del cluster.

> [!NOTE]
> La `AAD` sezione nell'URL di callback OAuth deve corrispondere al nome del provider di identità OAuth che verrà configurato in un secondo momento.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Creare un'applicazione Azure Active Directory per l'autenticazione

Creare un'applicazione Azure Active Directory e recuperare l'identificatore dell'applicazione creato. Sostituire **\<ClientSecret>** con una password sicura.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Si dovrebbe ottenere un risultato simile al seguente. Prendere nota di questo dato che è l' **AppID** necessario nei passaggi successivi.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Recuperare l'ID tenant della sottoscrizione a cui appartiene l'applicazione.

```azure
az account show --query tenantId -o tsv
```

Si dovrebbe ottenere un risultato simile al seguente. Prendere nota di questo **TenantId** , che sarà necessario nei passaggi successivi.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Creare un file manifesto per definire le attestazioni facoltative da includere nel token ID

Gli sviluppatori di applicazioni possono utilizzare [attestazioni facoltative](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) nelle applicazioni Azure ad per specificare le attestazioni desiderate nei token inviati alla propria applicazione.

Le attestazioni facoltative possono essere usate per:

- Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Si configurerà OpenShift per usare l'attestazione e si eseguirà il `email` fallback per `upn` impostare il nome utente preferito aggiungendo il `upn` come parte del token ID restituito da Azure Active Directory.

Creare una **manifest.jsnel** file per configurare l'applicazione Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Aggiornare il optionalClaims dell'applicazione Azure Active Directory con un manifesto

Sostituire **\<AppID>** con l'ID ottenuto in precedenza.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Aggiornare le autorizzazioni dell'ambito dell'applicazione Azure Active Directory

Per poter leggere le informazioni utente da Azure Active Directory, è necessario definire gli ambiti appropriati.

Sostituire **\<AppID>** con l'ID ottenuto in precedenza.

Aggiungere l'autorizzazione per l'ambito **Azure Active Directory Graph. User. Read** per abilitare l'accesso e la lettura del profilo utente.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> A meno che non si sia autenticato come amministratore globale per questo Azure Active Directory, è possibile ignorare il messaggio per concedere il consenso, dal momento che verrà richiesto di effettuare questa operazione una volta eseguito l'accesso al proprio account.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Assegnare utenti e gruppi al cluster (facoltativo)

Per impostazione predefinita, le applicazioni registrate in un tenant di Azure Active Directory (Azure AD) sono disponibili per tutti gli utenti del tenant che eseguono correttamente l'autenticazione. Azure AD consente agli amministratori e agli sviluppatori di tenant di limitare un'app a un set specifico di utenti o gruppi di sicurezza all'interno del tenant.

Seguire le istruzioni nella documentazione di Azure Active Directory per [assegnare utenti e gruppi all'app](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configurare l'autenticazione OpenID OpenShift

Recuperare le `kubeadmin` credenziali. Eseguire il comando seguente per trovare la password per l'utente `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

L'output di esempio seguente mostra che la password sarà in `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Accedere al server API del cluster OpenShift usando il comando seguente. La `$apiServer` variabile è stata impostata in [precedenza](). Sostituire **\<kubeadmin password>** con la password recuperata.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Creare un segreto OpenShift per archiviare il segreto dell'applicazione Azure Active Directory, sostituendo **\<ClientSecret>** con il segreto recuperato in precedenza.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```    

Creare un file **oidc. YAML** per configurare l'autenticazione OpenID di OpenShift in Azure Active Directory. Sostituire **\<AppID>** e **\<TenantId>** con i valori recuperati in precedenza.

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret: 
        name: openid-client-secret-azuread
      extraScopes: 
      - email
      - profile
      extraAuthorizeParameters: 
        include_granted_scopes: "true"
      claims:
        preferredUsername: 
        - email
        - upn
        name: 
        - name
        email: 
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

Applicare la configurazione al cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Viene restituita una risposta simile alla seguente.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verificare l'accesso tramite Azure Active Directory

Se ora si esegue la disconnessione della console Web di OpenShift e si tenta di eseguire nuovamente l'accesso, verrà visualizzata una nuova opzione per accedere con **AAD**. Potrebbe essere necessario attendere alcuni minuti.

![Schermata di accesso con Azure Active Directory opzione](media/aro4-login-2.png)
