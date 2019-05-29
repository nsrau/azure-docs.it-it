---
title: Esercitazione - Creare un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Informazioni su come creare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: d8d767b97e335feeb31851c89a9b21eddf7157ea
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962217"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Esercitazione: Creare un cluster Azure Red Hat OpenShift

Questa è la prima di una serie di esercitazioni. Si apprenderà come creare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure, come ridimensionarlo e quindi eliminarlo per pulire le risorse.

Nella prima parte della serie, si apprenderà come:

> [!div class="checklist"]
> * Creare un cluster Azure Red Hat OpenShift

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster Azure Red Hat OpenShift
> * [Ridimensionare un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Eliminare un cluster Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prerequisiti

> [!IMPORTANT]
> Questa esercitazione richiede la versione 2.0.65 dell'interfaccia della riga di comando di Azure.
>    
> Prima di poter usare Azure Red Hat OpenShift, è necessario acquistare un minimo di 4 nodi di applicazione di Azure Red Hat OpenShift riservato, come descritto in [Configurare l'ambiente di sviluppo di Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Prima di iniziare questa esercitazione:

Verificare di aver [configurato l'ambiente di sviluppo](howto-setup-environment.md), incluse:
- Installare l'interfaccia della riga di comando più recente (versione 2.0.65 o successiva)
- Creazione di un tenant, se non ne si dispone
- Creazione di un oggetto di applicazione di Azure se non ne si dispone
- Creazione di un gruppo di sicurezza
- Creazione di un utente di Active Directory per accedere al cluster.

## <a name="step-1-sign-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Se si esegue l'interfaccia della riga di comando di Azure in locale, aprire una shell dei comandi Bash ed eseguire `az login` per accedere ad Azure.

```bash
az login
```

 Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Passaggio 2: Creare un cluster Azure Red Hat OpenShift

Nella finestra di comando di Bash impostare le variabili seguenti:

> [!IMPORTANT]
> Scegliere un nome per il cluster univoco e con sole lettere minuscole o la creazione avrà esito negativo.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Scegliere una località in cui creare il cluster. Per un elenco di aree di Azure che supportano OpenShift in Azure, vedere [Aree supportate](supported-resources.md#azure-regions). Ad esempio: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Impostare `APPID` sul valore salvato nel passaggio 5 di [Creare una registrazione di app Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Impostare 'GROUPID' sul valore salvato nel passaggio 10 di [Creare un gruppo di sicurezza Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Impostare `SECRET` sul valore salvato nel passaggio 8 di [Creare un segreto client](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Impostare `TENANT` sul valore dell'ID del tenant salvato nel passaggio 7 di [Creare un nuovo tenant](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Creare il gruppo di risorse per il cluster. Eseguire il comando seguente dalla stessa shell Bash usata per definire le variabili precedenti:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Facoltativo: connettere la rete virtuale del cluster a una rete virtuale esistente

Se non è necessario connettere la rete virtuale del cluster creato a una rete virtuale esistente tramite peering, ignorare questo passaggio.

Prima di tutto ottenere l'identificatore della rete virtuale esistente. L'identificatore avrà il formato: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Se non si conosce il nome della rete o il gruppo di risorse a cui appartiene la rete virtuale esistente, passare al [pannello Reti virtuali](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e fare clic sulla rete virtuale. Viene visualizzata la pagina Rete virtuale in cui saranno elencati il nome della rete e il gruppo di risorse a cui appartiene.

Definire una variabile VNET_ID usando il comando seguente dell'interfaccia della riga di comando in una shell BASH:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Ad esempio: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Creare il cluster

Ora è possibile creare un cluster. Il seguente crea il cluster nel tenant Azure AD specificato, specifica l'oggetto app di Azure AD e il segreto da usare come un'entità di sicurezza e il gruppo di sicurezza che contiene i membri che hanno accesso amministrativo al cluster.

Se **non** si esegue il peer di un cluster a una rete virtuale, usare il comando seguente:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Se **non** si esegue il peer di un cluster a una rete virtuale, usare il comando seguente che aggiunge il flag `--vnet-peer`:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Se viene restituito un errore che indica che il nome host non è disponibile, il nome del cluster potrebbe non essere univoco. Provare a eliminare la registrazione dell'app originale e a ripetere la procedura con un nome cluster differente in [Creare una nuova registrazione di app] (howto-aad-app-configuration.md#create-a-new-app-registration), omettendo il passaggio della creazione di un nuovo utente e gruppo di sicurezza.

Dopo alcuni minuti, `az openshift create` verrà completato.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Ottenere l'URL di accesso per il cluster

Per ottenere l'URL per l'accesso al cluster, eseguire questo comando:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Cercare `publicHostName` nell'output, ad esempio: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

L'URL di accesso per il cluster sarà `https://` seguito dal valore `publicHostName`.  Ad esempio: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Si userà questo URI nel passaggio successivo come parte dell'URI di reindirizzamento della registrazione dell'app.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Passaggio 3: Aggiornare l'URI di reindirizzamento della registrazione dell'app

Ora che si dispone dell'URL di accesso per il cluster, impostare l'interfaccia utente di reindirizzamento della registrazione dell'app:

1. Aprire il [pannello Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Fare clic sull'oggetto registrazione app.
3. Fare clic su **Aggiungi un URI di reindirizzamento**.
4. Assicurarsi che **TIPO** sia **Web** e impostare l'**URI DI REINDIRIZZAMENTO** usando il modello seguente: `https://<public host name>/oauth2callback/Azure%20AD`. Ad esempio: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Fare clic su **Save** (Salva).

## <a name="step-4-sign-in-to-the-openshift-console"></a>Passaggio 4: Accedere alla console OpenShift

È ora possibile accedere alla console OpenShift per il nuovo cluster. La [console Web OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) consente di visualizzare, esplorare e gestire i contenuti dei progetti OpenShift.

Sarà necessaria una nuova istanza del browser, che non abbia memorizzato nella cache l'identità usata normalmente per accedere al portale di Azure.

1. Aprire una finestra di tipo *Incognito* (Chrome) o *InPrivate* (Microsoft Edge).
2. Passare all'URL di accesso ottenuto in precedenza, ad esempio: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Accedere usando il nome utente creato nel passaggio 3 di [Creare un nuovo utente di Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Apparirà una finestra di dialogo **Autorizzazioni richieste**. Fare clic su **Acconsenti per conto dell'organizzazione** e quindi fare clic su **Accetta**.

A questo punto si è connessi alla console del cluster.

![Screenshot della console del cluster OpenShift](./media/aro-console.png)

 Per altre informazioni sull'[uso della console OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) per creare e compilare immagini, vedere la documentazione di [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html).

## <a name="step-5-install-the-openshift-cli"></a>Passaggio 5: Installare l'interfaccia della riga di comando di OpenShift

L'[interfaccia della riga di comando di OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (o *strumenti OC*) fornisce i comandi per gestire le applicazioni e le utilità di livello inferiore che consentono di interagire con i vari componenti del cluster OpenShift.

Nella console OpenShift fare clic sul punto interrogativo nell'angolo in alto a destra accanto al nome di accesso e selezionare **Command Line Tools** (Strumenti da riga di comando).  Seguire il collegamento **Latest Release** (Versione più recente) per scaricare e installare l'interfaccia della riga di comando oc supportata per Linux, MacOS o Windows.

> [!NOTE]
> Se l'icona del punto interrogativo nell'angolo in alto a destra non è visualizzata, selezionare *Service Catalog* (Catalogo di servizi) o *Application Console* (Console dell'applicazione) nell'elenco a discesa nell'angolo in alto a sinistra.
>
> In alternativa, è possibile [scaricare l'interfaccia della riga di comando oc](https://www.okd.io/download.html) direttamente.

La pagina **Command Line Tools** (Strumenti da riga di comando) fornisce un comando nel formato `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Fare clic sul pulsante *Copy to clipboard* (Copia negli Appunti) per copiare questo comando.  In una finestra del terminale [impostare il percorso](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) per includere l'installazione locale degli strumenti oc. Accedere quindi al cluster usando il comando dell'interfaccia della riga di comando di oc copiato.

Se non è possibile ottenere il valore del token seguendo la procedura precedente, ricavarlo da: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un cluster Azure Red Hat OpenShift

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Ridimensionare un cluster Azure Red Hat OpenShift](tutorial-scale-cluster.md)
