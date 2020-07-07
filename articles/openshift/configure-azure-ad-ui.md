---
title: "Azure Red Hat OpenShift che esegue OpenShift 4: configurare l'autenticazione Azure Active Directory usando il portale di Azure e la console Web OpenShift"
description: Informazioni su come configurare l'autenticazione Azure Active Directory per un cluster Azure Red Hat OpenShift che esegue OpenShift 4 usando la console Web di portale di Azure e OpenShift
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 6b6248aac35c22b9ffd2cd95df41e84986356259
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205313"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Configurare l'autenticazione Azure Active Directory per un cluster Azure Red Hat OpenShift 4 (portale)

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.75 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Prima di iniziare

Creare l'URL di **callback OAuth** del cluster e annotarlo. Assicurarsi di sostituire **Aro-RG** con il nome del gruppo di risorse e **Aro-cluster** con il nome del cluster.

> [!NOTE]
> La `AAD` sezione nell'URL di callback OAuth deve corrispondere al nome del provider di identità OAuth che verrà configurato in un secondo momento.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Creare un'applicazione Azure Active Directory per l'autenticazione

Accedere al portale di Azure e passare al pannello [registrazioni app](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), quindi fare clic su **nuova registrazione** per creare una nuova applicazione.

Specificare un nome per l'applicazione, ad esempio **Aro-azuread-auth**, e compilare l' **URI di reindirizzamento** usando il valore dell'URL di callback OAuth recuperato in precedenza.

![Registrazione nuova applicazione](media/aro4-ad-registerapp.png)

Passare a **certificati & segreti** e fare clic su **nuovo segreto client** e specificare i dettagli. Prendere nota del valore della chiave, che verrà usato in una fase successiva. Non sarà più possibile recuperarlo.

![Creare un segreto](media/aro4-ad-clientsecret.png)

Passare alla **Panoramica** e prendere nota dell'ID dell' **applicazione (client)** e della **Directory (tenant)**. Saranno necessari in una fase successiva.

![Recuperare gli ID dell'applicazione (client) e della directory (tenant)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Configurare le attestazioni facoltative

Gli sviluppatori di applicazioni possono utilizzare [attestazioni facoltative](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) nelle applicazioni Azure ad per specificare le attestazioni desiderate nei token inviati alla propria applicazione.

Le attestazioni facoltative possono essere usate per:

* Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
* Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
* Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Si configurerà OpenShift per usare l'attestazione e si eseguirà il `email` fallback per `upn` impostare il nome utente preferito aggiungendo il `upn` come parte del token ID restituito da Azure Active Directory.

Passare a **configurazione token (anteprima)** e fare clic su **Aggiungi attestazione facoltativa**. Selezionare **ID** , quindi controllare le attestazioni di **posta elettronica** e **UPN** .

![Creare un segreto](media/aro4-ad-tokens.png)

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

È possibile trovare l'URL della console del cluster eseguendo il comando seguente, simile al seguente: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Avviare l'URL della console in un browser e accedere usando le credenziali `kubeadmin`.

Passare ad **Amministrazione**, fare clic su **Impostazioni cluster**, quindi selezionare la scheda **configurazione globale** . scorrere fino a selezionare **OAuth**.

Scorrere verso il basso per selezionare **Aggiungi** in **Identity Providers** e selezionare **OpenID Connect**.
![Selezionare OpenID Connect dall'elenco a discesa provider di identità](media/aro4-oauth-idpdrop.png)

Immettere il nome **AAD**, l' **ID client** come **ID applicazione** e il **segreto client**. Il formato dell' **URL dell'autorità emittente** è il seguente: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Sostituire il segnaposto con l'ID tenant recuperato in precedenza.

![Immettere i dettagli OAuth](media/aro4-oauth-idp-1.png)

Scorrere verso il basso fino alla sezione **Claims** e aggiornare il **nome utente preferito** per usare il valore dell'attestazione **UPN** .

![Immettere i dettagli delle attestazioni](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Verificare l'accesso tramite Azure Active Directory

Se ora si esegue la disconnessione della console Web di OpenShift e si tenta di eseguire di nuovo l'accesso, verrà visualizzata una nuova opzione per accedere con **AAD**. Potrebbe essere necessario attendere alcuni minuti.

![Schermata di accesso con Azure Active Directory opzione](media/aro4-login-2.png)
