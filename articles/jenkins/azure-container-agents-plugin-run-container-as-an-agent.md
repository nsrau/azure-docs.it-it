---
title: Creare un progetto in Azure con Jenkins e Istanze di contenitore di Azure
description: Questo articolo fornisce informazioni su come usare il plug-in Azure Container Agents per Jenkins per creare un progetto in Azure con Istanze di contenitore di Azure
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
ms.openlocfilehash: 04a60bf021ec6e265a3880264386ad32ec7e8177
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Creare un progetto in Azure con Jenkins e Istanze di contenitore di Azure

Istanze di contenitore di Azure permette di essere subito operativi senza dover effettuare il provisioning di macchine virtuali o adottare un servizio di livello superiore. Istanze di contenitore di Azure consente la fatturazione al secondo in base alla capacità necessaria ed è quindi un'opzione ideale per i carichi di lavoro temporanei, ad esempio l'esecuzione di una compilazione.

Si apprenderà come:
> [!div class="checklist"]
> * Installare e configurare un server Jenkins in Azure
> * Installare e configurare il plug-in Azure Container Agents per Jenkins
> * Usare Istanze di contenitore di Azure per compilare l'[applicazione di esempio Spring PetClinic](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure** - Per informazioni sulle opzioni di acquisto di Azure, vedere [Come acquistare Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

- **Interfaccia della riga di comando di Azure 2.0 o Azure Cloud Shell** - Installare uno dei prodotti seguenti in cui immettere i comandi di Azure:

    - [Interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) - Consente di eseguire i comandi di Azure da una finestra di comando o del terminale.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - Esperienza di shell basata su browser. Cloud Shell consente l'accesso a un'esperienza di riga di comando basata su browser realizzata pensando alle attività di gestione di Azure management.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Installare un server Jenkins in Azure usando l'immagine di Jenkins Marketplace

Jenkins supporta un modello in cui il server Jenkins delega il lavoro a uno o più agenti per consentire a una singola installazione di Jenkins di ospitare un numero elevato di progetti o di fornire ambienti diversi necessari per le compilazioni o i test. I passaggi in questa sezione consentono di installare e configurare un server Jenkins in Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Connettersi al server Jenkins in esecuzione in Azure

Dopo avere installato Jenkins in Azure, è necessario connettersi a Jenkins. I passaggi seguenti consentono di configurare una connessione SSH alla macchina virtuale Jenkins in esecuzione in Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Aggiornare il DNS di Jenkins

Jenkins deve conoscere il proprio URL quando crea collegamenti che fanno riferimento a se stesso. L'URL deve ad esempio essere usato quando Jenkins invia messaggi di posta elettronica contenenti collegamenti diretti ai risultati della compilazione. 

Questa sezione illustra l'impostazione dell'URL di Jenkins.

1. Nel browser passare al dashboard di Jenkins in `http://localhost:8080`.

1. Selezionare **Manage Jenkins** (Gestisci Jenkins).

    ![Opzioni di gestione di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **Configure System** (Configura sistema).

    ![Opzioni di gestione dei plug-in di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. In **Jenkins Location** (Percorso Jenkins) immettere l'URL del server Jenkins.

1. Selezionare **Salva**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Aggiornare Jenkins per consentire il protocollo JNLP (Java Network Launch Protocol)

L'agente di Jenkins si connette al server Jenkins tramite il protocollo JNLP (Java Network Launch Protocol). Questa sezione illustra come specificare una porta che gli agenti JNLP devono usare quando comunicano con il server Jenkins.

1. Nel dashboard di Jenkins selezionare **Manage Jenkins** (Gestisci Jenkins).

    ![Opzioni di gestione di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **Configure Global Security** (Configura sicurezza globale).

    ![Configurare la sicurezza globale nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. In **Agents** (Agenti) selezionare **Fixed** (Fisso) e immettere una porta. Lo screenshot mostra un valore di porta 12345 di esempio. È necessario specificare una porta appropriata per l'ambiente.

    ![Aggiornare le impostazioni di sicurezza globali di Jenkins per consentire il protocollo JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Selezionare **Salva**.

1. Usando l'interfaccia della riga di comando di Azure 2.0 o Cloud Shell, immettere il comando seguente per creare una regola in ingresso per il gruppo di sicurezza di rete di Jenkins:

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

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Creare un'entità servizio di Azure e aggiungerla alle credenziali di Jenkins

Per la distribuzione in Azure è necessaria un'entità servizio di Azure. I passaggi seguenti illustrano la procedura di creazione di un'entità servizio (se non ne è già presente una) e l'aggiornamento di Jenkins con l'entità servizio.

1. Se si ha già un'entità servizio (e si conoscono i relativi valori di ID sottoscrizione, tenant, AppID e password), è possibile ignorare questo passaggio. Se è necessario creare un'entità servizio, vedere l'articolo [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) (Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0). Quando si crea l'entità servizio, prendere nota dei valori di ID sottoscrizione, tenant, AppID e password.

1. Selezionare **Credentials** (Credenziali).

    ![Opzioni di gestione delle credenziali nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. In **Credentials** (Credenziali) selezionare **System** (Sistema).

    ![Opzioni di gestione delle credenziali di sistema nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Selezionare **Global credentials (unrestricted)** (Credenziali globali - senza restrizioni).

    ![Opzioni di gestione delle credenziali di sistema globali nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Selezionare **Adding some credentials** (Aggiunta di credenziali).

    ![Aggiungere credenziali nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. Nell'elenco a discesa **Kind** (Tipo) selezionare **Microsoft Azure Service Principal** (Entità servizio di Microsoft Azure) per visualizzare sulla pagina i campi specifici per l'aggiunta di un'entità servizio. Specificare quindi i valori richiesti come indicato di seguito:

    - **Scope** (Ambito) - Selezionare l'opzione **Global (Jenkins, nodes, items, all child items, etc.)** (Globale - Jenkins, nodi, elementi, tutti gli elementi figlio, ecc.).
    - **Subscription ID** (ID sottoscrizione) - Usare l'ID sottoscrizione di Azure specificato durante l'esecuzione di `az account set`.
    - **Client ID** (ID client) - Usare il valore `appId` restituito da `az ad sp create-for-rbac`.
    - **Client Secret** (Segreto client) - Usare il valore `password` restituito da `az ad sp create-for-rbac`.
    - **Tenant ID** (ID tenant) - Usare il valore `tenant` restituito da `az ad sp create-for-rbac`.
    - **Azure Environment** (Ambiente Azure) - Selezionare `Azure`.
    - **ID** - Immettere `myTestSp`. Questo valore viene usato anche più avanti in questa esercitazione.
    - **Description** (Descrizione) - (Facoltativo) Immettere una descrizione per l'entità.

    ![Specificare le proprietà della nuova entità servizio nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Una volta immesse le informazioni che definiscono l'entità, è possibile selezionare **Verify Service Principal** (Verifica entità servizio) per assicurarsi che tutto funzioni correttamente. Se l'entità servizio è definita correttamente, viene visualizzato un messaggio che indica che l'entità servizio di Microsoft Azure è stata verificata sotto il campo **Description** (Descrizione).

1. Al termine, selezionare **OK** per aggiungere l'entità a Jenkins. Il dashboard di Jenkins mostra l'entità appena aggiunta nella pagina **Global Credentials** (Credenziali globali).

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Creare un gruppo di risorse di Azure per Istanze di contenitore di Azure

Il servizio Istanze di contenitore di Azure deve trovarsi in un gruppo di risorse di Azure. Un gruppo di risorse di Azure è un contenitore con risorse correlate per una soluzione di Azure.

Usando l'interfaccia della riga di comando di Azure 2.0 o Cloud Shell, immettere il comando seguente per creare un gruppo di risorse denominato `JenkinsAciResourceGroup` nell'area `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Al termine, il comando `az group create` mostra i risultati in modo analogo all'esempio seguente:

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

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Installare il plug-in Azure Container Agents per Jenkins

Se il plug-in Azure Container Agents è già stato installato, è possibile ignorare questa sezione.

Dopo aver creato il gruppo di risorse di Azure per l'agente di Jenkins, la procedura seguente illustra come installare il plug-in Azure Container Agents:

1. Nel dashboard di Jenkins selezionare **Manage Jenkins** (Gestisci Jenkins).

    ![Opzioni di gestione di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **Manage Plugins** (Gestisci plug-in).

    ![Opzioni di gestione dei plug-in di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Selezionare **Available** (Disponibili).

    ![Opzione di visualizzazione dei plug-in di Jenkins disponibili nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Immettere `Azure Container Agents` nella casella di testo **Filter** (Filtro). Quando si immette il testo, l'elenco viene filtrato.

    ![Filtrare i plug-in di Jenkins disponibili nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Selezionare la casella di controllo accanto al plug-in **Azure Container Agents** e scegliere una delle opzioni di installazione. Ai fini di questa dimostrazione, è stata selezionata l'opzione **Install without restart** (Installa senza riavviare).

    ![Installare il plug-in Azure Container Agents dal dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Dopo aver selezionato l'opzione per installare i plug-in desiderati, il dashboard di Jenkins mostra una pagina che riporta in dettaglio lo stato di ciò che si sta installando.

    ![Stato dell'installazione dei plug-in Azure Container Agents nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Per tornare alla pagina principale del dashboard di Jenkins, selezionare **Go back to the top page** (Torna a inizio pagina).

## <a name="configure-the-azure-container-agents-plugin"></a>Configurare il plug-in Azure Container Agents

Dopo avere installato il plug-in Azure Container Agents, questa sezione illustra come configurare il plug-in con il dashboard di Jenkins.

1. Nel dashboard di Jenkins selezionare **Manage Jenkins** (Gestisci Jenkins).

    ![Opzioni di gestione di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Selezionare **Configure System** (Configura sistema).

    ![Opzioni di gestione dei plug-in di Jenkins nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Individuare la sezione **Cloud** nella parte inferiore della pagina e nell'elenco a discesa **Add a new cloud** (Aggiungi nuovo cloud) selezionare **Azure Container Instance** (Istanza di contenitore di Azure).

    ![Aggiungere un nuovo provider di servizi cloud dal dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. Nella sezione **Azure Container Instance** (Istanza di contenitore di Azure) specificare i valori seguenti:

    - **Cloud name** (Nome cloud) - (Facoltativo in quanto per impostazione predefinita viene usato un nome generato automaticamente) Specificare un nome per l'istanza. 
    - **Azure Credential** (Credenziali di Azure) - Selezionare la freccia a discesa e quindi selezionare la voce `myTestSp` che identifica l'entità servizio di Azure creata in precedenza.
    - **Resource Group** (Gruppo di risorse) - Selezionare la freccia a discesa e quindi selezionare la voce `JenkinsAciResourceGroup` che identifica il gruppo di risorse dell'istanza di contenitore di Azure creato in precedenza.

    ![Definizione delle proprietà dell'istanza di contenitore di Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Selezionare la freccia a discesa **Add Container Template** (Aggiungi modello di contenitore) e quindi selezionare **Aci Container Template** (Modello di contenitore Aci).

1. Nella sezione **Aci container Template** (Modello di contenitore Aci) specificare i valori seguenti:

    - **Name** (Nome) - Immettere `ACI-container`.
    - **Labels** (Etichette) - Immettere `ACI-container`.
    - **Docker Image** (Immagine Docker) - Immettere `cloudbees/jnlp-slave-with-java-build-tools`

    ![Definizione delle proprietà dell'immagine dell'istanza di contenitore di Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Selezionare **Advanced** (Avanzate).

1. Selezionare la freccia a discesa **Retention Strategy** (Strategia di conservazione) e selezionare **Container Idle Retention Strategy** (Strategia di conservazione inattività contenitore). Se si seleziona questa opzione, Jenkins mantiene l'agente attivo fino a quando non viene eseguito alcun nuovo processo nell'agente e il tempo di inattività specificato è trascorso.

    ![Definizione della strategia di conservazione dell'istanza di contenitore di Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Selezionare **Salva**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Creare il processo dell'applicazione Spring PetClinic in Jenkins

I passaggi seguenti illustrano la procedura di creazione di un processo di Jenkins, come progetto Freestyle, per compilare l'applicazione Spring PetClinic.

1. Nel dashboard di Jenkins selezionare **New Item** (Nuovo elemento).

    ![Opzione di menu New Item (Nuovo elemento) nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Immettere `myPetClinicProject` come nome dell'elemento e selezionare **Freestyle project** (Progetto Freestyle).

    ![Nuovo progetto Freestyle nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Selezionare **OK**.

1. Selezionare la scheda **General** (Generale) e specificare i valori seguenti:

    - **Restrict where project can be run** (Limita posizione di esecuzione del progetto) - Selezionare questa opzione.
    - **Label Expression** (Espressione etichetta) - Immettere `ACI-container`. Quando si chiude il campo, viene visualizzato un messaggio che conferma che l'etichetta viene fornita dalla configurazione cloud creata nel passaggio precedente.

    ![Impostazioni generali per un nuovo progetto Freestyle nel dashboard di Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Selezionare la scheda **Source Code Management** (Gestione codice sorgente) e specificare i valori seguenti:

    - **Source Code Management** (Gestione codice sorgente) - Selezionare **Git**.
    - **Repository URL** (URL del repository) - Immettere l'URL seguente per il repository GitHub dell'applicazione di esempio Spring PetClinic: `https://github.com/spring-projects/spring-petclinic.git`.

1. Selezionare la scheda **Build** (Compilazione) ed eseguire le attività seguenti:

    - Selezionare la freccia a discesa **Add build step** (Aggiungi passaggio di compilazione) e selezionare **Invoke top-level Maven targets** (Richiama destinazioni Maven di livello principale).

    - Per **Goals** (Obiettivi) immettere `package`.

1. Selezionare **Save** (Salva) per salvare la definizione del nuovo progetto.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Compilare il processo dell'applicazione Spring PetClinic in Jenkins

Ora è possibile compilare il progetto. Questa sezione illustra come compilare un progetto dal dashboard di Jenkins.

1. Nel dashboard di Jenkins selezionare `myPetClinicProject`.

    ![Selezionare il progetto da compilare nel dashboard di Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Selezionare **Build now** (Compila). 

    ![Compilare il progetto dal dashboard di Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Quando si avvia una compilazione in Jenkins, la compilazione viene messa in coda. Nel caso di un agente contenitore di Azure, la compilazione non può essere eseguita fino a quando l'agente contenitore di Azure non viene avviato e portato online. Fino a quel momento, viene visualizzato un messaggio che indica il nome dell'agente e il fatto che la compilazione è in sospeso. Questo processo richiede circa cinque minuti, ma è necessario solo la prima volta che si usa l'agente per una compilazione. Le compilazioni successive sono molto più veloci in quanto a quel punto l'agente è online.

    ![La compilazione viene messa in coda fino a quando l'agente non viene creato e portato online.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    Una volta avviata la compilazione, un indicatore di stato a strisce diagonali indica che la compilazione è in esecuzione:

    ![Quando l'indicatore di stato a strisce diagonali viene visualizzato, la compilazione è in esecuzione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Quando l'indicatore di stato a strisce diagonali sparisce, selezionare la freccia accanto al numero di build. Dal menu di scelta rapida selezionare **Console output** (Output console).

    ![Fare clic sulla voce di menu Console Log (Log console) per visualizzare le informazioni di compilazione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Le informazioni del log della console vengono generate dal processo di compilazione. Per visualizzare tutte le informazioni di compilazione (incluse le informazioni relative alla compilazione in esecuzione in remoto nell'agente di Azure creato), selezionare **Full log** (Log completo).

    ![Fare clic sul collegamento al log completo per visualizzare informazioni più dettagliate sulla compilazione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    Il log completo mostra informazioni più dettagliate sulla compilazione:

    ![Il log completo mostra informazioni più dettagliate sulla compilazione.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Per visualizzare le informazioni finali sulla compilazione, scorrere fino alla parte finale del log.

    ![Le informazioni finali sulla compilazione sono visualizzate nella parte finale del log.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Pulire le risorse di Azure

In questa esercitazione sono state create risorse incluse in due gruppi di risorse di Azure: 
    - `JenkinsResourceGroup` - Contiene le risorse di Azure per il server Jenkins.
    - `JenkinsAciResourceGroup` - Contiene le risorse di Azure per l'agente Jenkins.
    
Se non è più necessario usare le risorse in un gruppo di risorse di Azure, è possibile eliminare il gruppo di risorse tramite il comando `az group delete` come illustrato di seguito (sostituendo il segnaposto &lt;resourceGroup> con il nome del gruppo di risorse da eliminare):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Visitare l'hub Jenkins in Azure per vedere gli articoli e gli esempi più recenti](https://docs.microsoft.com/azure/jenkins/)