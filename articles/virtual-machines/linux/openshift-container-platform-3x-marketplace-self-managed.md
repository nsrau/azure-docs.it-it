---
title: Distribuire l'offerta OpenShift container Platform 3,11 self-Managed Marketplace in Azure | Microsoft Docs
description: Distribuisci l'offerta OpenShift container Platform 3,11 self-Managed Marketplace in Azure.
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
ms.openlocfilehash: d5028ff6378fec5939aee3218071fe6f4eb1e843
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791622"
---
# <a name="configure-prerequisites"></a>Configurazione dei prerequisiti

Prima di usare l'offerta Marketplace per distribuire un cluster OpenShift container Platform 3,11 autonomo in Azure, è necessario configurare alcuni prerequisiti.  Per istruzioni su come creare una chiave SSH (senza passphrase), l'insieme di credenziali delle chiavi di Azure, il segreto dell'insieme di credenziali delle chiavi e un'entità servizio, vedere l'articolo relativo ai [prerequisiti di OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) .

 
## <a name="deploy-using-the-marketplace-offer"></a>Eseguire la distribuzione usando l'offerta Marketplace

Il modo più semplice per distribuire un cluster OpenShift container Platform 3,11 autonomo in Azure consiste nell'usare l' [offerta di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Questa opzione è la più semplice, ma offre anche funzionalità di personalizzazione limitate. L'offerta Marketplace distribuisce OpenShift container Platform 3.11.82 e include le opzioni di configurazione seguenti:

- **Nodi master**: tre (3) nodi master con tipo di istanza configurabile.
- **Nodi di infrastruttura**: tre (3) nodi di infrastruttura con tipo di istanza configurabile.
- **Nodes**: il numero di nodi (compresi tra 1 e 9) e il tipo di istanza sono configurabili.
- **Tipo di disco**: vengono usati dischi gestiti.
- **Rete**: supporto per la rete nuova o esistente e l'intervallo CIDR personalizzato.
- **Nomi comuni**: è possibile abilitare i nomi comuni.
- **Metriche**: è possibile abilitare la metrica Hawkular.
- **Registrazione**: è possibile abilitare la registrazione EFK.
- **Provider di servizi cloud di Azure**: abilitata per impostazione predefinita, può essere disabilitata.

Nell'angolo in alto a sinistra della portale di Azure fare clic su **Crea una risorsa**, immettere ' OpenShift container Platform ' nella casella di ricerca e premere INVIO.

   ![Nuova ricerca risorse](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

La pagina risultati si aprirà con **Red Hat OpenShift container Platform 3,11, gestito in modo autonomo** nell'elenco. 

   ![Nuovo risultato della ricerca delle risorse](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Fare clic sull'offerta per visualizzare i dettagli dell'offerta. Per distribuire questa offerta, fare clic su **Crea**. Verrà visualizzata l'interfaccia utente per immettere i parametri necessari. La prima schermata è il pannello **nozioni di base** .

   ![Pagina del titolo dell'offerta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Nozioni di base**

Per ottenere informazioni su uno dei parametri di input, passare il puntatore ***sul i accanto al*** nome del parametro.

Immettere i valori per i parametri di input e fare clic su **OK**.

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| Nome utente amministratore VM | Utente amministratore da creare in tutte le istanze di macchina virtuale |
| Chiave pubblica SSH per l'utente amministratore | Chiave pubblica SSH usata per accedere alla macchina virtuale: non deve avere una passphrase |
| Sottoscrizione | Sottoscrizione di Azure in cui distribuire il cluster |
| Gruppo di risorse | Creare un nuovo gruppo di risorse o selezionare un gruppo di risorse vuoto esistente per le risorse cluster |
| Località | Area di Azure in cui distribuire il cluster |

   ![Pannello nozioni di base sull'offerta](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Impostazioni infrastruttura**

Immettere i valori per i parametri di input e fare clic su **OK**.

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| Prefisso del nome del cluster OCP | Prefisso del cluster usato per configurare i nomi host per tutti i nodi. Da 1 a 20 caratteri |
| Dimensioni nodo master | Accettare le dimensioni predefinite della macchina virtuale oppure fare clic su **modifica dimensioni** per selezionare dimensioni diverse per la macchina virtuale.  Selezionare le dimensioni della VM appropriate per il carico di lavoro |
| Dimensioni del nodo dell'infrastruttura | Accettare le dimensioni predefinite della macchina virtuale oppure fare clic su **modifica dimensioni** per selezionare dimensioni diverse per la macchina virtuale.  Selezionare le dimensioni della VM appropriate per il carico di lavoro |
| Numero di nodi applicazione | Accettare le dimensioni predefinite della macchina virtuale oppure fare clic su **modifica dimensioni** per selezionare dimensioni diverse per la macchina virtuale.  Selezionare le dimensioni della VM appropriate per il carico di lavoro |
| Dimensioni nodo applicazione | Accettare le dimensioni predefinite della macchina virtuale oppure fare clic su **modifica dimensioni** per selezionare dimensioni diverse per la macchina virtuale.  Selezionare le dimensioni della VM appropriate per il carico di lavoro |
| Dimensioni host Bastion | Accettare le dimensioni predefinite della macchina virtuale oppure fare clic su **modifica dimensioni** per selezionare dimensioni diverse per la macchina virtuale.  Selezionare le dimensioni della VM appropriate per il carico di lavoro |
| Rete virtuale nuova o esistente | Creare un nuovo vNet (impostazione predefinita) o usare un vNet esistente |
| Scegliere Impostazioni CIDR predefinite o Personalizza intervallo IP (CIDR) | Accettare intervalli CIDR predefiniti o selezionare un **intervallo IP personalizzato** e immettere informazioni CIDR personalizzate.  Le impostazioni predefinite creeranno vNet con CIDR di 10.0.0.0/14, una subnet master con 10.1.0.0/16, una subnet infra con 10.2.0.0/16 e una subnet COMPUTE e CNS con 10.3.0.0/16 |
| Nome del gruppo di risorse Key Vault | Nome del gruppo di risorse che contiene il Key Vault |
| Nome dell'insieme di credenziali delle chiavi | Nome della Key Vault che contiene il segreto con la chiave privata SSH.  Sono consentiti solo caratteri alfanumerici e trattini e devono avere una lunghezza compresa tra 3 e 24 caratteri |
| Nome del segreto | Nome del segreto che contiene la chiave privata SSH.  Sono consentiti solo caratteri alfanumerici e trattini |

   ![Pannello infrastruttura offerta](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Modifica dimensioni**

Per selezionare dimensioni diverse per la macchina virtuale, fare clic su ***modifica dimensioni***.  Viene visualizzata la finestra di selezione della macchina virtuale.  Selezionare le dimensioni della macchina virtuale desiderate e fare clic su **Seleziona**.

   ![Selezionare le dimensioni della macchina virtuale](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rete virtuale esistente**

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| Nome rete virtuale esistente | Nome del vNet esistente |
| Nome della subnet per i nodi master | Nome della subnet esistente per i nodi master.  Deve contenere almeno 16 indirizzi IP e seguire la RFC 1918 |
| Nome della subnet per i nodi infra | Nome della subnet esistente per i nodi infra.  Deve contenere almeno 32 indirizzi IP e seguire la RFC 1918 |
| Nome della subnet per i nodi compute e CNS | Nome della subnet esistente per i nodi di calcolo e del sistema nervoso.  Deve contenere almeno 32 indirizzi IP e seguire la RFC 1918 |
| Gruppo di risorse per la rete virtuale esistente | Nome del gruppo di risorse che contiene il vNet esistente |

   ![Infrastruttura dell'offerta VNET esistente](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervallo IP personalizzato**

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| Intervallo di indirizzi per la rete virtuale | CIDR personalizzato per vNet |
| Intervallo di indirizzi per la subnet contenente i nodi master | CIDR personalizzato per la subnet master |
| Intervallo di indirizzi per la subnet contenente i nodi dell'infrastruttura | CIDR personalizzato per la subnet dell'infrastruttura |
| Intervallo di indirizzi per la subnet contenente i nodi compute e CNS | CIDR personalizzato per i nodi compute e CNS |

   ![Offerta di un intervallo IP personalizzato dell'infrastruttura](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**Piattaforma OpenShift container 3,11**

Immettere i valori per i parametri di input e fare clic su **OK** .

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| Password utente amministratore di OpenShift | Password per l'utente OpenShift iniziale.  Questo utente sarà anche l'amministratore del cluster |
| Conferma password utente amministratore OpenShift | Digitare nuovamente la password dell'utente amministratore di OpenShift |
| Nome utente di Red Hat Subscription Manager | Nome utente per accedere alla sottoscrizione o all'ID organizzazione di Red Hat.  Questa credenziale viene usata per registrare l'istanza di RHEL nella sottoscrizione e non verrà archiviata da Microsoft o Red Hat |
| Password utente di Red Hat Subscription Manager | Password per accedere alla sottoscrizione o alla chiave di attivazione di Red Hat.  Questa credenziale viene usata per registrare l'istanza di RHEL nella sottoscrizione e non verrà archiviata da Microsoft o Red Hat |
| ID pool OpenShift di gestione delle sottoscrizioni Red Hat | ID del pool che contiene i diritti della piattaforma del contenitore OpenShift. Assicurarsi di disporre di diritti sufficienti per la piattaforma OpenShift container per l'installazione del cluster |
| ID pool OpenShift di gestione delle sottoscrizioni Red Hat per i nodi broker/Master | ID del pool che contiene i diritti della piattaforma OpenShift container per i nodi broker/master. Assicurarsi di disporre di diritti sufficienti per la piattaforma OpenShift container per l'installazione del cluster. Se non si usa l'ID del pool di broker/Master, immettere l'ID del pool per i nodi dell'applicazione |
| Configurare il provider di servizi cloud di Azure | Configurare OpenShift per l'uso del provider di servizi cloud di Azure. Necessaria se si usa l'associazione dischi di Azure per i volumi permanenti.  Il valore predefinito è sì |
| GUID ID client dell'entità servizio Azure AD | Azure AD GUID ID client dell'entità servizio, noto anche come AppID. Necessaria solo se la configurazione del provider di servizi cloud di Azure è impostata su **Sì** |
| Azure AD Secret ID client dell'entità servizio | Azure AD Secret ID client dell'entità servizio. Necessaria solo se la configurazione del provider di servizi cloud di Azure è impostata su **Sì** |
 
   ![Pannello OpenShift offerta](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Impostazioni aggiuntive**

Il pannello impostazioni aggiuntive consente la configurazione del sistema nervoso per l'archiviazione, la registrazione, la metrica e il sottodominio del router GlusterFS.  Per impostazione predefinita, non viene installata nessuna di queste opzioni e verrà utilizzato nip.io come sottodominio del router a scopo di test. L'abilitazione di CNS installerà tre nodi di calcolo aggiuntivi con tre dischi collegati aggiuntivi che ospiteranno i pod GlusterFS.  

Immettere i valori per i parametri di input e fare clic su **OK** .

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| Configurare l'archiviazione nativa del contenitore | Installa lo SNC nel cluster OpenShift e lo Abilita come archiviazione. Sarà il valore predefinito se il provider di Azure è disabilitato |
| Configurare la registrazione del cluster | Installa la funzionalità di registrazione EFK nel cluster.  Ridimensionare i nodi infra in modo appropriato per ospitare i pod EFK |
| Configurare le metriche per il cluster | Installa le metriche Hawkular nel cluster OpenShift.  Ridimensionare i nodi infra in modo appropriato per ospitare i pod delle metriche Hawkular |
| Sottodominio router predefinito | Selezionare nipio per test o Custom per immettere il proprio sottodominio per la produzione |
 
   ![Pannello aggiuntivo offerta](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Impostazioni aggiuntive-parametri aggiuntivi**

| Parametro di input | Descrizione parametro |
|-----------------------|-----------------|
| NOMI comuni Dimensioni del nodo | Accettare le dimensioni predefinite del nodo oppure selezionare **modifica dimensioni** per selezionare una nuova dimensione della macchina virtuale |
| Immettere il sottodominio personalizzato | Il dominio di routing personalizzato da usare per esporre le applicazioni tramite il router nel cluster OpenShift.  Assicurarsi di creare la voce DNS con caratteri jolly appropriata] |
 
   ![Offrire un'installazione aggiuntiva del sistema nervoso](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Summary**

La convalida viene eseguita in questa fase per verificare che la quota di Core sia sufficiente per distribuire il numero totale di macchine virtuali selezionate per il cluster.  Esaminare tutti i parametri immessi.  Se gli input sono accettabili, fare clic su **OK** per continuare.

   ![Pannello Riepilogo offerta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Acquista**

Confermare le informazioni di contatto nella pagina Acquista e fare clic su **Acquista** per accettare le condizioni per l'utilizzo e avviare la distribuzione del cluster OpenShift container Platform.

   ![Pannello acquisto offerta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift

Al termine della distribuzione, recuperare la connessione dalla sezione di output della distribuzione. Connettersi alla console di OpenShift con il browser usando l' **URL della console di OpenShift**. è anche possibile usare SSH per l'host Bastion. Di seguito è riportato un esempio in cui il nome utente amministratore è clusteradmin e il nome di dominio completo DNS dell'indirizzo IP pubblico bastion è bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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