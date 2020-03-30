---
title: Distribuire l'offerta OpenShift Container Platform 3.11 Self-Managed Marketplace in AzureDeploy OpenShift Container Platform 3.11 Self-Managed Marketplace Offer in Azure
description: Distribuire l'offerta OpenShift Container Platform 3.11 Self-Managed Marketplace in Azure.Deploy OpenShift Container Platform 3.11 Self-Managed Marketplace Offer in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 235efd746562ea4bd52b9cb57da0d8165d60de02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561321"
---
# <a name="configure-prerequisites"></a>Configurazione dei prerequisiti

Prima di usare l'offerta Marketplace per distribuire un cluster OpenShift Container Platform 3.11 autogestito in Azure, è necessario configurare alcuni prerequisiti.  Leggere l'articolo [sui prerequisiti OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) per istruzioni su come creare una chiave ssh (senza passphrase), un insieme di credenziali delle chiavi di Azure, un segreto dell'insieme di credenziali delle chiavi e un'entità servizio.

 
## <a name="deploy-using-the-marketplace-offer"></a>Distribuzione tramite l'offerta Marketplace

Il modo più semplice per distribuire un cluster OpenShift Container Platform 3.11 autogestito in Azure consiste nell'usare [l'offerta di Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Questa opzione è la più semplice, ma ha anche funzionalità di personalizzazione limitate. L'offerta Marketplace distribuisce OpenShift Container Platform 3.11.82 e include le seguenti opzioni di configurazione:

- **Nodi master**: tre (3) nodi master con tipo di istanza configurabile.
- **Nodi di infrastruttura**: tre (3) nodi di infrastruttura con tipo di istanza configurabile.
- **Nodi**: Il numero di nodi (tra 1 e 9) e il tipo di istanza sono configurabili.
- **Tipo di disco**: vengono usati dischi gestiti.
- **Networking**: Supporto per rete nuova o esistente e gamma CIDR personalizzata.
- **Nomi comuni**: è possibile abilitare i nomi comuni.
- **Metriche**: Le metriche Hawkular possono essere abilitate.
- **Registrazione**: La registrazione EFK può essere abilitata.
- **Provider cloud di Azure:** abilitato per impostazione predefinita, può essere disabilitato.

Nell'angolo superiore sinistro del portale di Azure fare clic su **Crea una risorsa**, immettere 'piattaforma contenitore openshift' nella casella di ricerca e premere INVIO.

   ![Nuova ricerca di risorse](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

La pagina Risultati si aprirà con **Red Hat OpenShift Container Platform 3.11 Self-Managed** nell'elenco. 

   ![Nuovo risultato della ricerca risorse](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Fai clic sull'offerta per visualizzare i dettagli dell'offerta. Per distribuire questa offerta, fare clic su **Crea**. Apparirà l'interfaccia utente per immettere i parametri necessari. La prima schermata è il pannello **Basics.**

   ![Pagina del titolo dell'offerta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Nozioni di base**

Per ottenere informazioni su uno qualsiasi dei parametri di input, passare il mouse sulla ***i*** accanto al nome del parametro.

Immettere i valori per i parametri di input e fare clic su **OK**.

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Nome utente amministratore macchina virtuale | L'utente amministratore da creare in tutte le istanze della macchina virtuale |
| Chiave pubblica SSH per l'utente amministratore | Chiave pubblica SSH utilizzata per accedere alla macchina virtuale - non deve avere una passphrase |
| Subscription | Sottoscrizione di Azure per distribuire il cluster |
| Gruppo di risorse | Creare un nuovo gruppo di risorse o selezionare un gruppo di risorse vuoto esistente per le risorse clusterCreate a new resource group or select an existing empty resource group for cluster resources |
| Location | Area di Azure in cui distribuire il clusterAzure region to deploy cluster into |

   ![Lama dell'offerta di base](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Impostazioni infrastruttura**

Immettere i valori per i parametri di input e fare clic su **OK**.

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Prefisso nome cluster OCP | Prefisso cluster utilizzato per configurare i nomi host per tutti i nodi. Tra 1 e 20 caratteri |
| Dimensioni nodo master | Accettare le dimensioni predefinite della macchina virtuale o fare clic su **Cambia dimensione** per selezionare una dimensione di macchina virtuale diversa.  Selezionare le dimensioni appropriate della macchina virtuale per il carico di lavoroSelect appropriate VM size for your work load |
| Dimensione nodo infrastruttura | Accettare le dimensioni predefinite della macchina virtuale o fare clic su **Cambia dimensione** per selezionare una dimensione di macchina virtuale diversa.  Selezionare le dimensioni appropriate della macchina virtuale per il carico di lavoroSelect appropriate VM size for your work load |
| Numero di nodi dell'applicazione | Accettare le dimensioni predefinite della macchina virtuale o fare clic su **Cambia dimensione** per selezionare una dimensione di macchina virtuale diversa.  Selezionare le dimensioni appropriate della macchina virtuale per il carico di lavoroSelect appropriate VM size for your work load |
| Dimensioni nodo applicazione | Accettare le dimensioni predefinite della macchina virtuale o fare clic su **Cambia dimensione** per selezionare una dimensione di macchina virtuale diversa.  Selezionare le dimensioni appropriate della macchina virtuale per il carico di lavoroSelect appropriate VM size for your work load |
| Dimensioni host Bastion | Accettare le dimensioni predefinite della macchina virtuale o fare clic su **Cambia dimensione** per selezionare una dimensione di macchina virtuale diversa.  Selezionare le dimensioni appropriate della macchina virtuale per il carico di lavoroSelect appropriate VM size for your work load |
| Rete virtuale nuova o esistente | Creare una nuova rete virtuale (predefinita) o utilizzare una vNet esistenteCreate a new vNet (Default) or use an existing vNet |
| Scegliere Impostazioni CIDR predefinite o personalizzare l'intervallo IP (CIDR) | Accettare gli intervalli CIDR predefiniti o selezionare **Intervallo IP personalizzato** e immettere informazioni CIDR personalizzate.  Le impostazioni predefinite creeranno vNet con CIDR di 10.0.0.0/14, subnet master con 10.1.0.0/16, subnet infra con 10.2.0.0/16 e subnet compute e cns con 10.3.0.0/16 |
| Nome del gruppo di risorse dell'insieme di credenziali delle chiaviKey Vault Resource Group Name | Nome del gruppo di risorse che contiene l'insieme di credenziali delle chiavi |
| Nome dell'insieme di credenziali delle chiavi | Nome dell'insieme di credenziali delle chiavi che contiene il segreto con la chiave privata ssh.  Sono consentiti solo caratteri alfanumerici e trattini e devono essere compresi tra 3 e 24 caratteri |
| Nome segreto | Nome del segreto che contiene la chiave privata ssh.  Sono consentiti solo caratteri alfanumerici e trattini |

   ![Offrire il blade dell'infrastruttura](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Modificare le dimensioni**

Per selezionare una dimensione di macchina virtuale diversa, fare clic su ***Cambia dimensione***.  Si aprirà la finestra di selezione della macchina virtuale.  Selezionare la dimensione della macchina virtuale desiderata e fare clic su **Seleziona**.

   ![Seleziona dimensioni macchina virtuale](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rete virtuale esistente**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Nome rete virtuale esistente | Nome della rete virtuale esistente |
| Nome della subnet per i nodi masterSubnet name for master nodes | Nome della subnet esistente per i nodi master.  Deve contenere almeno 16 indirizzi IP e seguire RFC 1918 |
| Nome della subnet per i nodi infrad | Nome della subnet esistente per i nodi infra.Name of existing subnet for infra nodes.  Deve contenere almeno 32 indirizzi IP e seguire RFC 1918 |
| Nome della subnet per i nodi compute e cnsSubnet name for compute and cns nodes | Nome della subnet esistente per i nodi compute e cns.  Deve contenere almeno 32 indirizzi IP e seguire RFC 1918 |
| Gruppo di risorse per la rete virtuale esistenteResource Group for the existing Virtual Network | Nome del gruppo di risorse che contiene la rete virtuale esistenteName of resource group that contains the existing vNet |

   ![Offrire l'infrastruttura vnet esistente](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervallo IP personalizzato**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Intervallo di indirizzi per la rete virtualeAddress Range for the Virtual Network | CIDR personalizzato per vNet |
| Intervallo di indirizzi per la subnet contenente i nodi masterAddress Range for the subnet containing the master nodes | CIDR personalizzato per la subnet master |
| Intervallo di indirizzi per la subnet contenente i nodi dell'infrastrutturaAddress Range for the subnet containing the infrastructure nodes | CIDR personalizzato per la subnet dell'infrastruttura |
| Intervallo di indirizzi per la subnet contenente i nodi compute e cnsAddress Range for subnet containing the compute and cns nodes | CIDR personalizzato per i nodi compute e cns |

   ![Offrire l'intervallo IP personalizzato dell'infrastruttura](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Immettere i valori per i parametri di input e fare clic su **OK**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| OpenShift Password utente amministratore | Password per l'utente OpenShift iniziale.  Questo utente sarà anche l'amministratore del cluster |
| Conferma password utente amministratore OpenShift | Digitare nuovamente la password dell'utente amministratore openShift |
| Nome utente di Red Hat Subscription Manager | Nome utente per accedere all'abbonamento Red Hat o all'ID organizzazione.  Questa credenziale viene utilizzata per registrare l'istanza RHEL nella sottoscrizione e non verrà archiviata da Microsoft o Red Hat |
| Red Hat Sottoscrizione Manager Password utente | Password per accedere all'abbonamento Red Hat o alla chiave di attivazione.  Questa credenziale viene utilizzata per registrare l'istanza RHEL nella sottoscrizione e non verrà archiviata da Microsoft o Red Hat |
| Red Hat Subscription Manager OpenShift Pool ID | ID pool che contiene il diritto OpenShift Container Platform. Assicurarsi di disporre di diritti sufficienti di OpenShift Container Platform per l'installazione del cluster |
| Red Hat Subscription Manager OpenShift Pool ID per broker / nodi master | ID pool che contiene i diritti OpenShift Container Platform per i nodi Broker/Master. Assicurarsi di disporre di diritti sufficienti di OpenShift Container Platform per l'installazione del cluster. Se non si utilizza il broker/ID pool master, immettere l'ID del pool per i nodi dell'applicazione |
| Configurare il provider cloud di AzureConfigure Azure Cloud Provider | Configurare OpenShift per l'uso di Azure Cloud Provider.Configure OpenShift to use Azure Cloud Provider. Necessario se si usa il collegamento del disco di Azure per i volumi persistenti.  Il valore predefinito è Sì |
| Azure AD Service Principal Client ID GUID | GUID dell'ID client dell'entità servizio di Azure AD, noto anche come AppID. Necessario solo se Configura il provider cloud di Azure impostato su **Sì** |
| Azure AD Service Principal Client ID Secret | Segreto ID client dell'entità servizio di Azure AD. Necessario solo se Configura il provider cloud di Azure impostato su **Sì** |
 
   ![Offerta lama OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Impostazioni aggiuntive**

Il pannello Impostazioni aggiuntive consente la configurazione di CNS per il dominio glusterfs storage, Logging, Metrics e Router Sub.  L'impostazione predefinita non installa nessuna di queste opzioni e utilizzerà nip.io come sottodominio del router a scopo di test. L'abilitazione di CNS installerà tre nodi di calcolo aggiuntivi con tre dischi collegati aggiuntivi che ospiteranno pod glusterfs.  

Immettere i valori per i parametri di input e fare clic su **OK**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Configurare l'archiviazione nativa contenitore (CNS)Configure Container Native Storage (CNS) | Installa CNS nel cluster OpenShift e abilitarlo come risorsa di archiviazione. Sarà predefinito se il provider di Azure è disabilitato |
| Configurare la registrazione del cluster | Installa la funzionalità di registrazione di EFK nel cluster.  Dimensione dei nodi in modo appropriato per ospitare i pod EFK |
| Configurare le metriche per il clusterConfigure Metrics for the Cluster | Installa le metriche Hawkular nel cluster OpenShift.  Dimensione dei nodi in modo appropriato per ospitare i pod delle metriche Hawkular |
| Sottodominio router predefinito | Selezionare nipio per il test o personalizzato per immettere il proprio sottodominio per la produzione |
 
   ![Offrire lama aggiuntiva](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Impostazioni aggiuntive - Parametri aggiuntivi**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| (CNS) Dimensione nodo | Accettare le dimensioni predefinite del nodo o selezionare Modifica dimensioni per selezionare una nuova dimensione della macchina virtualeAccept the default node size or select **Change size** to select a new VM size |
| Immettere il sottodominio personalizzato | Dominio di routing personalizzato da utilizzare per l'esposizione di applicazioni tramite il router nel cluster OpenShift.  Assicurarsi di creare la voce DNS con caratteri jolly appropriata] |
 
   ![Offrire installazione cns aggiuntiva](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Riepilogo**

La convalida viene eseguita in questa fase per verificare che la quota principale sia sufficiente per distribuire il numero totale di macchine virtuali selezionate per il cluster.  Esaminare tutti i parametri immessi.  Se gli input sono accettabili, fare clic **su OK** per continuare.

   ![Pannello riepilogativo dell'offerta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Acquistare**

Confermare le informazioni di contatto nella pagina Acquista e fare clic su **Acquista** per accettare le condizioni per l'utilizzo e avviare la distribuzione del cluster OpenShift Container Platform.

   ![Pannello di acquisto dell'offerta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift

Al termine della distribuzione, recuperare la connessione dalla sezione di output della distribuzione. Connettersi alla console OpenShift con il browser utilizzando **l'URL della console OpenShift**. è anche possibile SSH all'host Bastion. Di seguito è riportato un esempio in cui il nome utente amministratore è clusteradmin e il nome di dominio completo DNS dell'indirizzo IP pubblico bastion è bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-container-platform-3x-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Getting started with OpenShift Container Platform](https://docs.openshift.com) (Introduzione a OpenShift Container Platform)
- 