---
title: Distribuire l'offerta sul Marketplace autogestiti piattaforma contenitore OpenShift in Azure | Microsoft Docs
description: Distribuire l'offerta sul Marketplace autogestiti piattaforma contenitore OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 9b981924dcaf715dd1d05d452b756a40b63f8dac
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233092"
---
# <a name="configure-prerequisites"></a>Configurazione dei prerequisiti

Prima di utilizzare l'offerta del Marketplace per distribuire un cluster OpenShift Container Platform Auto-gestito in Azure, è necessario configurare alcuni prerequisiti.  Leggere il [prerequisiti di OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) articolo per istruzioni su come creare un ssh key (senza una passphrase), Azure insieme di credenziali delle chiavi, il segreto dell'insieme di credenziali chiave e un'entità servizio.

 
## <a name="deploy-using-the-marketplace-offer"></a>Eseguire la distribuzione usando l'offerta del Marketplace

Il modo più semplice per distribuire un cluster OpenShift Container Platform Auto-gestito in Azure è usare il [offerta di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Questa opzione è la più semplice, ma è limitata anche funzionalità di personalizzazione. L'offerta del Marketplace consente di distribuire OpenShift Container Platform 3.11.82 e include le opzioni di configurazione seguenti:

- **Nodi master**: tre (3) nodi master con tipo di istanza configurabile.
- **Nodi di infrastruttura**: tre (3) nodi di infrastruttura con tipo di istanza configurabile.
- **Nodi**: Il numero di nodi (compreso tra 1 e 9) e il tipo di istanza è configurabile.
- **Tipo di disco**: vengono usati dischi gestiti.
- **Rete**: Supporto per rete nuovi o esistenti e l'intervallo CIDR personalizzato.
- **NOMI COMUNI**: è possibile abilitare i nomi comuni.
- **Metriche**: È possibile abilitare le metriche hawkular.
- **Registrazione**: La registrazione EFK può essere abilitata.
- **Provider di servizi cloud di Azure**: Abilitato per impostazione predefinita, può essere disabilitata.

In alto a sinistra del portale di Azure, fare clic su **crea una risorsa**, immettere 'openshift container platform' nella casella di ricerca e premere INVIO.

   ![Nuova ricerca di risorse](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Verrà aperta la pagina di risultati con **Red Hat OpenShift contenitore piattaforma Self-Managed** nell'elenco. 

   ![Nuovo risultato di ricerca di risorse](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Scegliere l'offerta per visualizzare i dettagli dell'offerta. Per distribuire questa offerta, fare clic su **Create**. Verrà visualizzata l'interfaccia utente di immettere parametri necessari. La prima schermata è il **nozioni di base** pannello.

   ![Titolo pagina dell'offerta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Nozioni di base**

Per ottenere informazioni su uno dei parametri di input, passare il mouse sul ***ho*** accanto al nome del parametro.

Immettere i valori per i parametri di input e fare clic su **OK**.

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Nome utente amministratore della macchina virtuale | L'utente amministratore deve essere creato in tutte le istanze di macchina virtuale |
| SSH chiave pubblica per l'utente amministratore | Usato per accedere a una macchina virtuale: la chiave pubblica SSH non deve avere una passphrase |
| Sottoscrizione | Sottoscrizione di Azure per distribuire un cluster |
| Gruppo di risorse | Creare un nuovo gruppo di risorse o selezionare un gruppo di risorse vuoto esistente per le risorse del cluster |
| Località | Area di Azure per distribuire un cluster |

   ![Pannello nozioni di base dell'offerta](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Impostazioni dell'infrastruttura**

Immettere i valori per i parametri di input e fare clic su **OK**.

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Prefisso del nome Cluster OCP | Prefisso usato per configurare i nomi host per tutti i nodi del cluster. Tra 1 e 20 caratteri |
| Dimensioni del nodo master | Accettare la dimensione VM predefinito oppure fare clic su **modificare le dimensioni** per selezionare una dimensione di VM diversa.  Selezionare la dimensione della macchina virtuale appropriata per il carico di lavoro |
| Dimensioni del nodo dell'infrastruttura | Accettare la dimensione VM predefinito oppure fare clic su **modificare le dimensioni** per selezionare una dimensione di VM diversa.  Selezionare la dimensione della macchina virtuale appropriata per il carico di lavoro |
| Numero di nodi di applicazione | Accettare la dimensione VM predefinito oppure fare clic su **modificare le dimensioni** per selezionare una dimensione di VM diversa.  Selezionare la dimensione della macchina virtuale appropriata per il carico di lavoro |
| Dimensioni del nodo dell'applicazione | Accettare la dimensione VM predefinito oppure fare clic su **modificare le dimensioni** per selezionare una dimensione di VM diversa.  Selezionare la dimensione della macchina virtuale appropriata per il carico di lavoro |
| Dimensioni Bastion Host | Accettare la dimensione VM predefinito oppure fare clic su **modificare le dimensioni** per selezionare una dimensione di VM diversa.  Selezionare la dimensione della macchina virtuale appropriata per il carico di lavoro |
| Rete virtuale nuova o esistente | Creare una nuova rete virtuale (impostazione predefinita) o usare una rete virtuale esistente |
| Scegli impostazioni CIDR predefinite o personalizzale intervallo IP (CIDR) | Accettare gli intervalli CIDR predefinito o selezionare **intervallo di indirizzi IP personalizzati** e immettere le informazioni personalizzate CIDR.  Le impostazioni predefinite creerà vNet con CIDR di 10.0.0.0/14, subnet master con 10.1.0.0/16, infrastrutture-subnet con 10.2.0.0/16 e una subnet di calcolo e di nomi comuni con 10.3.0.0/16 |
| Nome gruppo di risorse dell'insieme di credenziali chiave | Il nome del gruppo di risorse che contiene l'insieme di credenziali chiave |
| Nome insieme di credenziali delle chiavi | Il nome dell'insieme di credenziali chiave che contiene la chiave privata con il ssh chiave privata.  Sono consentiti solo caratteri alfanumerici e trattini ed essere compresa tra 3 e 24 caratteri |
| Nome del segreto | Il nome del segreto che contiene il ssh chiave privata.  Sono consentiti solo caratteri alfanumerici e trattini |

   ![Pannello infrastruttura di offerta](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Modificare le dimensioni**

Per selezionare una dimensione di VM diversa, fare clic su ***modificare le dimensioni***.  Verrà aperta la finestra di selezione della macchina virtuale.  Selezionare le dimensioni di macchina virtuale desiderata, quindi scegliere **seleziona**.

   ![Selezionare dimensioni della macchina virtuale](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rete virtuale esistente**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Nome della rete virtuale esistente | Nome della rete virtuale esistente |
| Nome della subnet per i nodi master | Nome della subnet esistente per i nodi master.  Deve contenere almeno 16 indirizzi IP e seguire RFC 1918 |
| Nome della subnet per infrastrutture-nodi | Nome dell'esistente infrastruttura di subnet per i nodi.  Deve contenere almeno 32 indirizzi IP e seguire RFC 1918 |
| Nome della subnet per i nodi di calcolo e di nomi comuni | Nome della subnet esistente per i nodi di calcolo e di nomi comuni.  Deve contenere almeno 32 indirizzi IP e seguire RFC 1918 |
| Gruppo di risorse per la rete virtuale esistente | Nome del gruppo di risorse che contiene la rete virtuale esistente |

   ![Rete virtuale esistente di infrastruttura dell'offerta](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervallo di indirizzi IP personalizzati**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Intervallo di indirizzi della rete virtuale | Custom CIDR per la rete virtuale |
| Intervallo di indirizzi della subnet che contiene i nodi master | Custom CIDR per la subnet master |
| Intervallo di indirizzi della subnet che contiene i nodi di infrastruttura | Custom CIDR per la subnet dell'infrastruttura |
| Intervallo di indirizzi subnet che contiene i nodi di calcolo e di nomi comuni | CIDR personalizzati per i nodi di calcolo e di nomi comuni |

   ![Intervallo di indirizzi IP personalizzato di infrastruttura dell'offerta](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Immettere i valori per i parametri di Input e fare clic su **OK**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Password dell'utente amministratore di OpenShift | Password per l'utente di OpenShift iniziale.  Questo utente sarà anche l'amministratore del cluster |
| Conferma Password utente dell'amministratore di OpenShift | Digitare nuovamente la Password dell'utente amministratore di OpenShift |
| Nome di utente Red Hat Subscription Manager | Nome utente di accedere alla sottoscrizione di Red Hat o ID organizzazione.  Questa credenziale viene usata per registrare l'istanza RHEL alla sottoscrizione e non verrà archiviata da Microsoft o Red Hat |
| Red Hat Subscription Manager utente Password | Password per accedere alla sottoscrizione di Red Hat e chiave di attivazione.  Questa credenziale viene usata per registrare l'istanza RHEL alla sottoscrizione e non verrà archiviata da Microsoft o Red Hat |
| ID del Pool di Red Hat Subscription Manager OpenShift | ID del pool che contiene il diritto di OpenShift Container Platform. Verificare che si dispone di sufficienti diritti di OpenShift Container Platform per l'installazione del cluster |
| ID del Pool di OpenShift Red Hat Subscription Manager per Service Broker / nodi Master | ID che contiene i diritti di OpenShift Container Platform per Broker pool / nodi Master. Verificare che si dispone di sufficienti diritti di OpenShift Container Platform per l'installazione del cluster. Se non si utilizza Service broker / master ID pool di applicazioni, immettere l'ID del pool di nodi di applicazione |
| Configurare il Provider di Cloud di Azure | Configurare OpenShift per l'uso di Provider di Cloud di Azure. Necessario se usando dischi di Azure collega per volumi permanenti.  Valore predefinito è Sì |
| GUID dell'ID Client entità servizio AD Azure | Azure AD Service Principal Client ID GUID - noto anche come ID app. Necessario solo se configurare Provider Cloud di Azure impostata su **Sì** |
| Azure AD Service Principal Client ID Secret | Segreto di ID Client entità servizio AD Azure. Necessario solo se configurare Provider Cloud di Azure impostata su **Sì** |
 
   ![Pannello di offerta di OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Impostazioni aggiuntive**

Il pannello impostazioni aggiuntive consente la configurazione di nomi comuni per l'archiviazione glusterfs, la registrazione, metriche e Router Sub dominio.  Il valore predefinito non installa una qualsiasi di queste opzioni e userà nip.io come del sotto-dominio router per scopi di test. Abilitazione CNS installerà tre nodi di calcolo aggiuntivi con tre dischi collegati aggiuntivi che ospiteranno glusterfs POD.  

Immettere i valori per i parametri di Input e fare clic su **OK**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| Configurare l'archiviazione nativa contenitore (CNS) | Vengono installati CNS in di OpenShift e abilitarlo come risorsa di archiviazione del cluster. Sarà l'impostazione predefinita se il Provider di Azure è disabilitato |
| Configurare la registrazione di Cluster | Installa la funzionalità di registrazione EFK nel cluster.  Dimensioni infra nodi in modo appropriato per i POD EFK host |
| Configurare le metriche per il Cluster | Installa Hawkular metriche nel cluster OpenShift.  Dimensioni infra nodi in modo appropriato per i POD Hawkular metriche host |
| Default Router Sub domain | Selezionare nipio per il test o personalizzato per immettere il proprio dominio sub per la produzione |
 
   ![Pannello aggiuntivo dell'offerta](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Impostazioni aggiuntive - parametri aggiuntivi**

| Parametro di input | Descrizione dei parametri |
|-----------------------|-----------------|
| (CNS) Dimensioni del nodo | Accettare le dimensioni del nodo predefinito o selezionare **modificare le dimensioni** per selezionare una nuova dimensione di macchina virtuale |
| Immettere il sottodominio personalizzato | Il dominio di routing personalizzato da utilizzare per l'esposizione delle applicazioni tramite il router nel cluster OpenShift.  Assicurarsi di creare la voce DNS appropriata con caratteri jolly] |
 
   ![Offrono ulteriori cns installare](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Riepilogo**

La convalida viene eseguita in questa fase per controllare la quota di core è sufficiente distribuire il numero totale di macchine virtuali selezionate per il cluster.  Esaminare tutti i parametri che sono state immesse.  Se gli input sono accettabili, fare clic su **OK** per continuare.

   ![Pannello di riepilogo dell'offerta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Acquista**

Verificare le informazioni di contatto nella pagina di acquisto e fare clic su **acquisto** per accettare le condizioni d'uso e avviare la distribuzione del cluster OpenShift Container Platform.

   ![Pannello acquisto dell'offerta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift

Al termine della distribuzione, recuperare la connessione dalla sezione di output della distribuzione. Connettersi alla console di OpenShift con il browser usando il **URL della Console OpenShift**. È anche possibile SSH al Bastion host. Di seguito è riportato un esempio in cui il nome utente amministratore è clusteradmin e il nome di dominio completo DNS dell'indirizzo IP pubblico bastion è bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure](./openshift-troubleshooting.md)
- [Getting started with OpenShift Container Platform](https://docs.openshift.com/container-platform) (Introduzione a OpenShift Container Platform)

