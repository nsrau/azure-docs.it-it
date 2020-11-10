---
title: Configurare il registro contenitori incorporato per Azure Red Hat OpenShift 4
description: Configurare il registro contenitori incorporato per Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414805"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configurare il registro contenitori incorporato per Azure Red Hat OpenShift 4

In questo articolo si configurerà il registro delle immagini del contenitore incorporato per un cluster Azure Red Hat OpenShift (ARO) 4. Si apprenderà come:

> [!div class="checklist"]
> * Configurare Azure AD
> * Configurare OpenID Connect
> * Accedere al registro immagini del contenitore predefinito

## <a name="prerequisites"></a>Prerequisiti

* Creare un cluster seguendo i passaggi descritti in [creare un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster). Assicurarsi di creare il cluster con l' `--pull-secret` argomento a `az aro create` .  Questa operazione è necessaria se si vuole configurare Azure AD per l'accesso, come richiesto da questo articolo.
* Connettersi al cluster attenendosi alla procedura descritta in [connettersi a un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Assicurarsi di seguire la procedura descritta in "installare l'interfaccia della riga di comando di OpenShift" perché il comando verrà usato `oc` più avanti in questo articolo.
   * Prendere nota dell'URL della console del cluster, simile a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . I valori per `<random>` e `<region>` verranno usati più avanti in questo articolo.
   * Prendere nota delle `kubeadmin` credenziali. Verranno usati più avanti in questo articolo.

## <a name="set-up-azure-active-directory"></a>Configurare Azure Active Directory

Azure Active Directory (Azure AD) implementa OpenID Connect (OIDC). OIDC consente di usare Azure AD per accedere al cluster ARO. Per configurare Azure AD, attenersi alla procedura riportata di seguito.

1. Configurare un tenant di Azure AD seguendo la procedura descritta nella [Guida introduttiva: configurare un tenant](/azure/active-directory/develop/quickstart-create-new-tenant). È possibile che l'account Azure abbia già un tenant. In tal caso, è possibile evitare di crearne uno se si dispone di privilegi sufficienti nel tenant per seguire i passaggi. Prendere nota dell' **ID tenant**. Questo valore verrà usato in seguito.
2. Creare almeno un utente Azure AD attenendosi alla procedura descritta in [aggiungere o eliminare utenti usando Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). È possibile usare questi account o il proprio per testare l'applicazione. Prendere nota degli indirizzi di posta elettronica e delle password di questi account per l'accesso.
3. Creare una nuova registrazione dell'applicazione nel tenant di Azure AD seguendo la procedura descritta nella [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](/azure/active-directory/develop/quickstart-register-app). 
   1. Ricordare la nota nei prerequisiti relativi ai valori per `<random>` e `<region>` . Usare questi valori per creare un URI in base alla formula seguente:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Quando si raggiunge il passaggio relativo al campo **URI di reindirizzamento** , immettere l'URI del passaggio precedente.
   1. Selezionare **Registra**.
   1. Nella pagina **Panoramica** dell'app, prendere nota del valore visualizzato per **ID applicazione (client)** , come illustrato di seguito.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Pagina Panoramica dell'applicazione Azure AD.":::

4. Creare un nuovo segreto client. 
   1. Nella registrazione dell'applicazione appena creata selezionare **certificati & Secrets** nel riquadro di spostamento a sinistra.
   1. Selezionare **Nuovo segreto client**.
   1. Fornire **una descrizione** e selezionare **Aggiungi**.
   1. Salvare il valore del **segreto client** generato. Questo valore verrà usato più avanti nell'articolo.

### <a name="add-openid-connect-identity-provider"></a>Aggiungi provider di identità OpenID Connect

ARO fornisce un registro contenitori incorporato.  Per accedervi, configurare un provider di identità (IDP) utilizzando Azure AD OIDC attenendosi alla seguente procedura.

1. Accedere alla console Web di OpenShift dal browser come `kubeadmin` .  Si tratta della stessa procedura usata quando si eseguono i passaggi in [esercitazione: connettersi a un cluster di Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. Nel riquadro di spostamento a sinistra selezionare **Amministrazione**  >  **Impostazioni cluster**.
1. Al centro della pagina selezionare **configurazione globale**.
1. Trovare **OAuth** nella colonna **risorsa di configurazione** della tabella e selezionarlo.
1. In **Identity Providers (provider di identità** ) selezionare **Aggiungi** e fare clic su **OpenID Connect**.
1. Impostare **nome** come **OpenID**.  Questo valore può essere già compilato.
1. Impostare l' **ID client** e il **segreto client** come valori del passaggio precedente.
1. Eseguire questo passaggio per trovare il valore per l' **URL dell'autorità emittente**.
   1. Sostituire **\<tenant-id>** con quello salvato durante la sezione **configurare Azure Active Directory** nell'URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Aprire l'URL nello stesso browser usato per interagire con il portale di Azure.
   1. Copiare il valore dell' **emittente** della proprietà nel corpo JSON restituito e incollarlo nella casella di testo labeled **URL (URL autorità di certificazione** ).  Il valore dell' **autorità emittente** sarà simile a `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Spostarsi nella parte inferiore della pagina e selezionare **Aggiungi**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect in OpenShift.":::
1. Disconnettersi dalla console Web di OpenShift selezionando il pulsante **Kube: admin** nella parte superiore destra della finestra del browser e scegliendo **Disconnetti**.

### <a name="access-the-built-in-container-image-registry"></a>Accedere al registro immagini del contenitore predefinito

Le istruzioni seguenti consentono di accedere al registro di sistema incorporato.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definire l'utente Azure AD come amministratore

1. Accedere alla console Web di OpenShift dal browser usando le credenziali di un utente Azure AD.

   1. Usare una funzionalità InPrivate, incognito o un'altra finestra del browser equivalente per accedere alla console.
   1. La finestra sarà diversa dopo aver abilitato OIDC.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Finestra di accesso abilitata per OpenID Connect.":::
   1. Selezionare **OpenID**

   > [!NOTE]
   > Prendere nota del nome utente e della password usati per accedere a questa pagina. Questo nome utente e la password funzioneranno come amministratore per altre azioni in questo e in altri articoli.
1. Accedere con l'interfaccia della riga di comando di OpenShift usando la procedura seguente.  Per la discussione, questo processo è noto come `oc login` .
   1. Nella parte superiore destra della console Web espandere il menu di scelta rapida dell'utente che ha eseguito l'accesso, quindi selezionare **Copy login Command**.
   1. Accedere a una nuova finestra della scheda con lo stesso utente, se necessario.
   1. Selezionare **Visualizza token**.
   1. Copiare il valore elencato sotto **login con questo token** negli Appunti ed eseguirlo in una shell, come illustrato di seguito.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Eseguire `oc whoami` nella console e prendere nota dell'output come **\<aad-user>** .  Questo valore verrà usato più avanti nell'articolo.
1. Disconnettersi dalla console Web di OpenShift. Selezionare il pulsante nella parte superiore destra della finestra del browser con il nome **\<aad-user>** e scegliere **Disconnetti**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Concedere al Azure AD utente i ruoli necessari per l'interazione del registro di sistema

1. Accedere alla console Web di OpenShift dal browser usando le `kubeadmin` credenziali.
1. Accedere all'interfaccia della riga di comando di OpenShift con il token per `kubeadmin` attenendosi alla procedura descritta `oc login` in precedenza, ma dopo aver eseguito l'accesso alla console Web con `kubeadmin` .
1. Eseguire i comandi seguenti per abilitare l'accesso al Registro incorporato per l' **utente AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   L'output dovrebbe essere simile al seguente.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   L'output dovrebbe essere simile al seguente.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   L'output dovrebbe essere simile al seguente.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   L'output dovrebbe essere simile al seguente.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
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

Usare il registro delle immagini del contenitore predefinito distribuendo un'applicazione in OpenShift.  Per le applicazioni Java, seguire le istruzioni per [distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).
