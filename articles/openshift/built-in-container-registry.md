---
title: Configurare il registro contenitori predefinito per Azure Red Hat OpenShift 4
description: Configurare il registro contenitori predefinito per Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9ed53721b66dc03bad24e0510e0c8a970c61aec1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492421"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configurare il registro contenitori predefinito per Azure Red Hat OpenShift 4

Azure Red Hat OpenShift fornisce un registro di immagini contenitore integrato denominato [OpenShift container Registry (OCR)](https://docs.openshift.com/aro/4/registry/architecture-component-imageregistry.html) che consente di effettuare automaticamente il provisioning di nuovi repository di immagini su richiesta. In questo modo gli utenti dispongono di una posizione predefinita per le compilazioni delle applicazioni per eseguire il push delle immagini risultanti.

In questo articolo si configurerà il registro delle immagini del contenitore incorporato per un cluster Azure Red Hat OpenShift (ARO) 4. Si apprenderà come:

> [!div class="checklist"]
> * Configurare Azure AD
> * Configurare OpenID Connect
> * Accedere al registro immagini del contenitore predefinito

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster ARO esistente. Se è necessario un cluster ARO, vedere l'esercitazione su ARO [creare un cluster Azure Red Hat OpenShift 4](./tutorial-create-cluster.md). Assicurarsi di creare il cluster con l' `--pull-secret` argomento a `az aro create` .  Questa operazione è necessaria per configurare l'autenticazione Azure Active Directory e il registro contenitori incorporato.

Dopo aver creato il cluster, connettersi al cluster attenendosi alla procedura descritta in [connettersi a un cluster di Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Assicurarsi di seguire la procedura descritta in "installare l'interfaccia della riga di comando di OpenShift" perché il comando verrà usato `oc` più avanti in questo articolo.
   * Prendere nota dell'URL della console del cluster, simile a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . I valori per `<random>` e `<region>` verranno usati più avanti in questo articolo.
   * Prendere nota delle `kubeadmin` credenziali. Verranno usati anche più avanti in questo articolo.

### <a name="configure-azure-active-directory-authentication"></a>Configurare l'autenticazione di Azure Active Directory 

Azure Active Directory (Azure AD) implementa OpenID Connect (OIDC). OIDC consente di usare Azure AD per accedere al cluster ARO. Per configurare il cluster, seguire la procedura descritta in [configurare l'autenticazione Azure Active Directory](configure-azure-ad-cli.md) .

## <a name="access-the-built-in-container-image-registry"></a>Accedere al registro immagini del contenitore predefinito

Ora che sono stati configurati i metodi di autenticazione per il cluster ARO, è possibile abilitare l'accesso al registro di sistema predefinito.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definire l'utente Azure AD come amministratore

1. Accedere alla console Web di OpenShift dal browser usando le credenziali di un utente Azure AD. Si utilizzerà l'autenticazione OpenID di OpenShift su Azure Active Directory per usare OpenID per definire l'amministratore.

   1. Usare una funzionalità InPrivate, incognito o un'altra finestra del browser equivalente per accedere alla console. La finestra sarà diversa dopo aver abilitato OIDC.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Finestra di accesso abilitata per OpenID Connect.":::
   1. Selezionare **OpenID**

   > [!NOTE]
   > Prendere nota del nome utente e della password usati per accedere a questa pagina. Questo nome utente e la password funzioneranno come amministratore per altre azioni in questo e in altri articoli.
2. Accedere con l'interfaccia della riga di comando di OpenShift usando la procedura seguente.  Per la discussione, questo processo è noto come `oc login` .
   1. Nella parte superiore destra della console Web espandere il menu di scelta rapida dell'utente che ha eseguito l'accesso, quindi selezionare **Copy login Command**.
   2. Accedere a una nuova finestra della scheda con lo stesso utente, se necessario.
   3. Selezionare **Visualizza token**.
   4. Copiare il valore elencato sotto **login con questo token** negli Appunti ed eseguirlo in una shell, come illustrato di seguito.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Eseguire `oc whoami` nella console e prendere nota dell'output come **\<aad-user>** .  Questo valore verrà usato più avanti nell'articolo.
4. Disconnettersi dalla console Web di OpenShift. Selezionare il pulsante nella parte superiore destra della finestra del browser con il nome **\<aad-user>** e scegliere **Disconnetti**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Concedere al Azure AD utente i ruoli necessari per l'interazione del registro di sistema

1. Accedere alla console Web di OpenShift dal browser usando le `kubeadmin` credenziali.
1. Accedere all'interfaccia della riga di comando di OpenShift con il token per `kubeadmin` attenendosi alla procedura descritta `oc login` in precedenza, ma dopo aver eseguito l'accesso alla console Web con `kubeadmin` .
1. Eseguire i comandi seguenti per abilitare l'accesso al Registro incorporato per l' **utente AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Ottenere l'URL del registro contenitori

Usare il `oc get route` comando come illustrato di seguito per ottenere l'URL del registro contenitori.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Si noti l'output della console di **container Registry URL**. Verrà usato come nome completo del registro di sistema per questa guida e per quelli successivi.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato il registro delle immagini contenitore incorporato, è possibile iniziare distribuendo un'applicazione in OpenShift. Per le applicazioni Java, vedere [distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).