---
title: Creare un progetto in Azure con Jenkins e istanze di contenitori di Azure
description: Informazioni su come utilizzare il plug-in agente di contenitore di Azure per Jenkins per compilare un progetto in Azure con istanze di contenitori di Azure
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 269e936cb79ba4138285f5dbd326413d70d5924d
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Creare un progetto in Azure con Jenkins e istanze di contenitori di Azure

Istanze di contenitore di Azure è semplice per poter iniziare a in esecuzione senza la necessità di eseguire il provisioning di macchine virtuali o di adottare un servizio di livello superiore. Istanze di contenitore di Azure fornisce fatturazione al secondo in base alla capacità che necessario. rendendola un'opzione attraente per carichi di lavoro, ad esempio eseguire la compilazione.

Si apprenderà come:
> [!div class="checklist"]
> * Installare e configurare un server Jenkins in Azure
> * Installare e configurare il plug-in agenti di contenitore di Azure per Jenkins
> * Utilizzare istanze di contenitori di Azure per compilare il [Spring PetClinic l'applicazione di esempio](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure** - per ulteriori informazioni sulle opzioni di acquisto di Azure, vedere [come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [la versione di un mese valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Azure 2.0 CLI o Azure Cloud Shell** -installare uno dei seguenti prodotti in cui immettere i comandi di Azure:

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) -consente di eseguire i comandi di Azure da un comando o una finestra terminale.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - basato su Browser esperienza della shell. Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Installare un server Jenkins in Azure usando l'immagine del Jenkins Marketplace

Jenkins supporta un modello in cui si basa il Jenkins server delega il lavoro a uno o più agenti per una singola installazione di Jenkins per ospitare un numero elevato di progetti o per fornire ambienti diversi, necessaria per consentire o test. I passaggi in questa sezione consentono di installare e configurare un server Jenkins in Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Connettersi al server Jenkins in esecuzione in Azure

Dopo aver installato Jenkins in Azure, è necessario connettersi a Jenkins. I passaggi seguenti consentono di configurare una connessione SSH per la VM Jenkins in esecuzione in Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Aggiornare il DNS di Jenkins

Jenkins è necessario conoscere il proprio URL durante la creazione di collegamenti che fanno riferimento a se stessa. Ad esempio, l'URL deve essere utilizzato quando Jenkins invia messaggi di posta elettronica contenente collegamenti diretti per generare i risultati. 

In questa sezione illustra l'impostazione dell'URL di Jenkins.

1. Nel browser, passare al dashboard di Jenkins a `http://localhost:8080`.

1. Selezionare **gestire Jenkins**.

    ![Gestire le opzioni di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **configurare sistema**.

    ![Gestire l'opzione di plug-in di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. In **Jenkins percorso**, immettere l'URL del server Jenkins.

1. Selezionare **Salva**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Aggiornare Jenkins per consentire di Java rete avviare protocollo (JNLP)

L'agente di Jenkins si connette al server Jenkins tramite Java rete avviare protocollo (JNLP). In questa sezione viene illustrato come specificare una porta per gli agenti JNLP da utilizzare durante la comunicazione con il server Jenkins.

1. Nel dashboard di Jenkins, selezionare **gestire Jenkins**.

    ![Gestire le opzioni di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **configurare la sicurezza globale**.

    ![Configurare la sicurezza globale nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. In **agenti**selezionare **Fixed**e immettere una porta. Cattura di schermata mostra un valore porta 12345, ad esempio. È necessario specificare una porta appropriata per l'ambiente.

    ![Aggiornare le impostazioni di sicurezza globali di Jenkins per consentire JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Selezionare **Salva**.

1. Mediante Azure CLI 2.0 o Cloud Shell, immettere il comando seguente per creare una regola in ingresso per il gruppo di sicurezza di rete di Jenkins:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Creare e aggiungere un'entità servizio di Azure per le credenziali di Jenkins

Per la distribuzione in Azure è necessaria un'entità servizio di Azure. I passaggi seguenti illustrano il processo di creazione di un'entità servizio (se non hai già uno) e l'aggiornamento di Jenkins con l'entità servizio.

1. Se si dispone già un'entità servizio (e conoscere il relativo ID sottoscrizione, tenant, appId e password), è possibile ignorare questo passaggio. Se è necessario creare un'entità di sicurezza, vedere l'articolo [creare l'entità servizio di Azure con Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Durante la creazione di entità, prendere nota dei valori per l'ID sottoscrizione, i tenant, appId e password.

1. Selezionare **credenziali**.

    ![Gestire l'opzione relativa alle credenziali nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. In **credenziali**selezionare **sistema**.

    ![Gestire l'opzione di credenziali di sistema nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Selezionare **credenziali globali (illimitate)**.

    ![Gestire l'opzione di credenziali di sistema globali nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Selezionare **aggiunta alcune credenziali**.

    ![Aggiungere le credenziali nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. Nel **tipo** elenco a discesa, seleziona **entità servizio di Microsoft Azure** a causa della pagina visualizzare i campi specifici per l'aggiunta di un'entità servizio. Quindi, specificare i valori richiesti come indicato di seguito:

    - **Ambito** -selezionare l'opzione per **Global (Jenkins, i nodi, elementi, tutti gli elementi figlio, ecc.)** .
    - **ID sottoscrizione** -utilizzare l'ID sottoscrizione di Azure specificato durante l'esecuzione di `az account set`.
    - **ID client** -utilizzare il `appId` restituito da `az ad sp create-for-rbac`.
    - **Segreto client** -utilizzare il `password` restituito da `az ad sp create-for-rbac`.
    - **ID tenant** -utilizzare il `tenant` restituito da `az ad sp create-for-rbac`.
    - **Ambiente Azure** : selezionare questa opzione `Azure`.
    - **ID** -immettere `myTestSp`. Questo valore viene utilizzato più avanti in questa esercitazione.
    - **Descrizione** : (facoltativo) immettere un valore di descrizione per questa entità.

    ![Specificare nuove proprietà dell'entità servizio nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Dopo aver immesso le informazioni che definisce l'entità, è possibile selezionare facoltativamente **dell'entità servizio verificare** per assicurarsi che tutto funzioni correttamente. Se l'entità servizio è definito correttamente, viene visualizzato un messaggio che informa, "conferma l'entità servizio di Microsoft Azure." di sotto di **descrizione** campo.

1. Al termine, selezionare **OK** per aggiungere l'entità a Jenkins. Il dashboard di Jenkins Visualizza l'entità appena aggiunto nel **credenziali globali** pagina.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Creare un gruppo di risorse di Azure per le istanze di contenitore di Azure

Istanze di Azure contenitore deve essere inserite in un gruppo di risorse di Azure. Un gruppo di risorse di Azure è un contenitore con risorse correlate per una soluzione di Azure.

Tramite la CLI di Azure 2.0 o Cloud Shell, immettere il comando seguente per creare un gruppo di risorse denominato `JenkinsAciResourceGroup` nel percorso `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Al termine, il `az group create` comando Visualizza i risultati in modo analogo all'esempio seguente:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Installare il plug-in agenti di contenitore di Azure per Jenkins

Se è già stato installato il plug-in agenti di contenitore di Azure, è possibile ignorare questa sezione.

Dopo aver creato il gruppo di risorse di Azure creato per l'agente di Jenkins, la procedura seguente viene illustrato come installare il plug-in agenti di contenitore di Azure:

1. Nel dashboard di Jenkins, selezionare **gestire Jenkins**.

    ![Gestire le opzioni di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **gestire plug-in**.

    ![Gestire l'opzione di plug-in di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Selezionare **disponibile**.

    ![Visualizza i plug-in di Jenkins disponibile nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Immettere `Azure Container Agents` nel **filtro** casella di testo. (L'elenco filtri quando si immette il testo).

    ![Filtrare i plug-in di Jenkins disponibili nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Selezionare la casella di controllo accanto al **Azure contenitore agenti** plug-in e una delle opzioni di installazione. Ai fini di questa dimostrazione, ho selezionato il **installare senza dover riavviare** opzione.

    ![Installare il plug-in Azure contenitore agenti dal dashboard Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Dopo aver selezionato l'opzione per installare il plugin(s) desiderato, il dashboard di Jenkins Visualizza una pagina che riporta in dettaglio lo stato di cui si esegue l'installazione.

    ![Stato dell'installazione di installare i plug-in Azure contenitore agenti dal dashboard Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Per tornare alla pagina principale del dashboard Jenkins, selezionare **tornare alla pagina principale**.

## <a name="configure-the-azure-container-agents-plugin"></a>Configurare il plug-in agenti di contenitore di Azure

Dopo aver installato il plug-in agenti di contenitore di Azure, questa sezione viene illustrato come configurare il plug-nel dashboard Jenkins.

1. Nel dashboard di Jenkins, selezionare **gestire Jenkins**.

    ![Gestire le opzioni di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **configurare sistema**.

    ![Gestire l'opzione di plug-in di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Individuare il **Cloud** sezione nella parte inferiore della pagina e dal **aggiungere un nuovo cloud** elenco a discesa, seleziona **istanza del contenitore di Azure**.

    ![Aggiungere un nuovo provider di cloud dal dashboard Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. Nel **istanza del contenitore di Azure** sezione, specificare i valori seguenti:

    - **Nome del cloud** - (facoltativi, come il valore predefinito per un nome generato automaticamente.) Specificare un nome per questa istanza. 
    - **Credenziali di Azure** : selezionare la freccia a discesa e quindi selezionare il `myTestSp` voce che identifica l'entità servizio di Azure creata in precedenza.
    - **Gruppo di risorse** : selezionare la freccia a discesa e quindi selezionare il `JenkinsAciResourceGroup` voce che identifica il gruppo di risorse di Azure contenitore istanza creata in precedenza.

    ![Definire le proprietà dell'istanza di contenitore di Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Selezionare il **Aggiungi modelli contenitore** freccia a discesa, quindi selezionare **Aci contenitore modello**.

1. Nel **Aci contenitore modello** sezione, specificare i valori seguenti:

    - **Nome** -immettere `ACI-container`.
    - **Le etichette** -immettere `ACI-container`.
    - **Immagine di docker** -immettere`cloudbees/jnlp-slave-with-java-build-tools`

    ![Definizione di proprietà dell'immagine di istanza di contenitore di Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Selezionare **Advanced** (Avanzate).

1. Selezionare il **strategia di memorizzazione** freccia a discesa, selezionare **strategia di memorizzazione inattività contenitore**. Se si seleziona questa opzione, Jenkins mantiene l'agente fino a quando non viene eseguito alcun nuovo processo dell'agente e che sia trascorso il tempo di inattività specificato.

    ![Definire la strategia di memorizzazione di istanza di contenitore di Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Selezionare **Salva**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Creare il processo di applicazione PetClinic Spring in Jenkins

Nella procedura seguente consente di creazione di un processo di Jenkins - come progetto freestyle - per compilare l'applicazione PetClinic molla.

1. Nel dashboard di Jenkins, selezionare **nuovo elemento**.

    ![Opzione di menu nuovo elemento nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Immettere `myPetClinicProject` per il nome dell'elemento e selezionare **progetto Freestyle**.

    ![Nuovo progetto freestyle nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Selezionare **OK**.

1. Selezionare il **generale** scheda e specificare i valori seguenti:

    - **Limitare in cui è possono eseguire progetti** -selezionare questa opzione.
    - **Etichetta espressione** -immettere `ACI-container`. Quando si chiude il campo, viene visualizzato un messaggio per confermare che l'etichetta viene servita dalla configurazione del cloud creato nel passaggio precedente.

    ![Impostazioni generali per un nuovo progetto freestyle nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Selezionare il **gestione del codice sorgente** scheda e specificare i valori seguenti:

    - **Gestione del codice sorgente** : selezionare questa opzione **Git**.
    - **URL del repository** -immettere l'URL seguente per il repository Spring PetClinic esempio applicazione GitHub: `https://github.com/spring-projects/spring-petclinic.git`.

1. Selezionare il **compilare** scheda ed eseguire le attività seguenti:

    - Selezionare il **istruzione di compilazione aggiungere** freccia a discesa, selezionare **richiama le destinazioni di primo livello Maven**.

    - Per **obiettivi**, immettere `package`.

1. Selezionare **salvare** per salvare la nuova definizione di progetto.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Compilare il processo di applicazione PetClinic Spring in Jenkins

Ora è possibile compilare il progetto. In questa sezione viene illustrato come compilare un progetto dal dashboard Jenkins.

1. Nel dashboard di Jenkins, selezionare `myPetClinicProject`.

    ![Selezionare il progetto per compilare dal dashboard Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Selezionare **compilare ora**. 

    ![Compilare il progetto dal dashboard Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Quando si avvia una compilazione in Jenkins, la compilazione viene accodata. Nel caso di un agente di contenitore di Azure, la compilazione non è possibile eseguire fino a quando l'agente di contenitore di Azure viene avviato e portato online. Fino ad allora, viene visualizzato un messaggio che indica il nome dell'agente e il fatto che la compilazione è in sospeso. (Questo processo richiede circa cinque minuti, ma è necessario solo la prima volta che si usa l'agente di compilazione. Le compilazioni successive sono molto più veloce a questo punto l'agente è in linea.)

    ![La compilazione viene accodata fino a quando l'agente viene creato e portato online.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    Una volta avviata la compilazione, una barra di stato polo barber indica che la compilazione è in esecuzione:

    ![Dopo aver visualizzato l'indicatore di stato polo barber, la compilazione è in esecuzione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Quando l'indicatore di stato polo barber scomparirà, selezionare la freccia accanto il numero di build. Dal menu di scelta rapida, selezionare **output di Console**.

    ![Fare clic sulla voce di menu Log della Console per visualizzare le informazioni di compilazione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Le informazioni del log console generate dal processo di compilazione. Per visualizzare tutte le informazioni di compilazione (incluse le informazioni relative alla compilazione in esecuzione in modalità remota sull'agente di Azure creato), selezionare **log completo**.

    ![Fare clic sul collegamento di riempimento del log per visualizzare le informazioni più dettagliate.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    Il riempimento del log consente di visualizzare le informazioni di compilazione più dettagliate:

    ![Il riempimento del log consente di visualizzare le informazioni di compilazione più dettagliate.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Per visualizzare la disposizione di compilazione, scorrere fino alla fine del log.

    ![La disposizione di compilazione vengono visualizzati nella parte inferiore del log di compilazione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Pulire le risorse di Azure

In questa esercitazione, è stato creato le risorse contenute all'interno di due gruppi di risorse di Azure: 
    - `JenkinsResourceGroup`-Contiene le risorse di Azure per il server Jenkins.
    - `JenkinsAciResourceGroup`-Contiene le risorse di Azure per l'agente di Jenkins.
    
Se non è necessario utilizzare una delle risorse in un gruppo di risorse di Azure, è possibile eliminare il gruppo di risorse tramite il `az group delete` comando come indicato di seguito (sostituendo il &lt;resourceGroup > con il nome del gruppo di risorse a cui si desidera Delete):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Visitare il Jenkins su hub di Azure per vedere gli articoli e i campioni più recenti](https://docs.microsoft.com/en-us/azure/jenkins/)