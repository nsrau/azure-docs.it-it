---
title: Esercitazione - Creare un cluster Azure Red Hat OpenShift | Microsoft Docs
description: Informazioni su come creare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/08/2019
ms.openlocfilehash: baada8a5238725456ca4a2ec7e8257c229066115
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466188"
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

Prima di iniziare questa esercitazione:

Verificare di aver [configurato l'ambiente di sviluppo](howto-setup-environment.md), incluse:
- Installare l'interfaccia della riga di comando più recente (versione 2.0.64 o successiva)
- Creazione di un tenant
- Creazione di un oggetto applicazione di Azure
- Creazione di un utente di Active Directory usato per accedere alle app in esecuzione nel cluster.

## <a name="step-1-sign-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Se si esegue l'interfaccia della riga di comando di Azure in locale, aprire una shell dei comandi Bash ed eseguire `az login` per accedere ad Azure.

```bash
az login
```

 Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Passaggio 2: Creare un cluster Azure Red Hat OpenShift

Nella finestra di comando di Bash impostare le variabili seguenti:

> [!IMPORTANT]
> Il nome del cluster deve contenere solo lettere minuscole o la creazione del cluster avrà esito negativo.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Usare per il cluster lo stesso nome scelto nel passaggio 6 di [Creare una nuova registrazione di app](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Scegliere una località in cui creare il cluster. Per un elenco di aree di Azure che supportano OpenShift in Azure, vedere [Aree supportate](supported-resources.md#azure-regions). Ad esempio: `LOCATION=eastus`.

Impostare `FQDN` sul nome completo del cluster. Questo nome è composto dal nome del cluster, dalla località e da `.cloudapp.azure.com` aggiunto alla fine. Si tratta dello stesso URL di accesso creato nel passaggio 6 di [Creare una nuova registrazione di app](howto-aad-app-configuration.md#create-a-new-app-registration). Ad esempio:   

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Impostare `APPID` sul valore salvato nel passaggio 9 di [Creare una nuova registrazione di app](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Impostare `SECRET` sul valore salvato nel passaggio 6 di [Creare un segreto client](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Impostare `TENANT` sul valore dell'ID del tenant salvato nel passaggio 7 di [Creare un nuovo tenant](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Creare il gruppo di risorse per il cluster. Eseguire il comando seguente dalla shell Bash usata per definire le variabili precedenti:

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

Ora è possibile creare un cluster.

 Se non si ha intenzione di connettere la rete virtuale del cluster a una rete virtuale esistente, omettere il parametro `--vnet-peer-id $VNET_ID` finale nell'esempio seguente.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Dopo alcuni minuti, `az openshift create` verrà completato correttamente e restituirà una risposta JSON contenente i dettagli del cluster.

> [!NOTE]
> Se viene restituito un errore che indica che il nome host non è disponibile, il nome del cluster potrebbe non essere univoco. Provare a eliminare la registrazione dell'app originale e a ripetere la procedura descritta in [Creare una nuova registrazione di app] (howto-aad-app-configuration.md#create-a-new-app-registration) (omettendo il passaggio finale della creazione di un nuovo utente, perché ne è già stato creato uno) con un nome del cluster diverso.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Passaggio 3: Accedere alla console OpenShift

È ora possibile accedere alla console OpenShift per il nuovo cluster. La [console Web OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) consente di visualizzare, esplorare e gestire i contenuti dei progetti OpenShift.

Accedere usando il [nuovo utente di Azure AD](howto-aad-app-configuration.md#create-a-new-active-directory-user) creato per il test. A tale scopo, sarà necessaria una nuova istanza del browser, che non abbia memorizzato nella cache l'identità usata normalmente per accedere al portale di Azure.

1. Aprire una finestra di tipo *Incognito* (Chrome) o *InPrivate* (Microsoft Edge).
2. Passare all'URL di accesso creato nel passaggio 6 di [Creare una nuova registrazione di app](howto-aad-app-configuration.md#create-a-new-app-registration). Ad esempio: https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> La console OpenShift usa un certificato autofirmato.
> Quando richiesto nel browser, ignorare l'avviso e accettare il certificato "non attendibile".

Accedere con l'utente e la password creati in [Creare un nuovo utente di Active Directory](howto-aad-app-configuration.md#create-a-new-active-directory-user). Quando viene visualizzata la finestra di dialogo **Autorizzazioni richieste**, selezionare **Acconsenti per conto dell'organizzazione** e quindi **Accetta**.

A questo punto si è connessi alla console del cluster.

![Screenshot della console del cluster OpenShift](./media/aro-console.png)

 Per altre informazioni sull'[uso della console OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) per creare e compilare immagini, vedere la documentazione di [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html).

## <a name="step-4-install-the-openshift-cli"></a>Passaggio 4: Installare l'interfaccia della riga di comando di OpenShift

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