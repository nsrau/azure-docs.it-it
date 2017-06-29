---
title: Usare gli agenti di macchine virtuali di Azure per l'integrazione continua con Jenkins
description: Agenti di macchine virtuali di Azure come slave di Jenkins
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: it-it
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Usare gli agenti di macchine virtuali di Azure per l'integrazione continua con Jenkins

Questa guida introduttiva mostra come usare il plug-in Agenti di macchine virtuali di Azure Jenkins per creare un agente Linux (Ubuntu) su richiesta in Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* Se non si dispone ancora di un'unità master Jenkins, è possibile iniziare con il [modello di soluzione](install-jenkins-solution-template.md). 
* Fare riferimento a [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) se non si dispone ancora di un'entità servizio di Azure.

## <a name="install-azure-vm-agents-plugin"></a>Installare il plug-in Agenti di macchine virtuali di Azure

Se si inizia con il [modello di soluzione](install-jenkins-solution-template.md), viene installato il plug-in Agente di macchine virtuali di Azure nell'unità master Jenkins.

In caso contrario, installare il plug-in **Agenti di macchine virtuali di Azure** dal dashboard di Jenkins.

## <a name="configure-the-plugin"></a>Configurare il plug-in

* Nel dashboard di Jenkins fare clic su **Manage Jenkins -> Configure System ->** (Gestisci Jenkins -> Configura sistema). Scorrere fino alla fine della pagina e individuare la sezione con l'elenco a discesa **Add new cloud** (Aggiungi nuovo cloud). Dal menu selezionare **Microsoft Azure VM Agents** (Agenti di macchine virtuali di Microsoft Azure).
* Selezionare un account esistente dall'elenco a discesa Azure Credentials (Credenziali di Azure).  Per aggiungere una nuova **entità servizio di Microsoft Azure**, immettere i valori seguenti: Subscription ID (ID sottoscrizione), Client ID (ID client), Client Secret (Segreto client) e OAuth 2.0 Token Endpoint (Endpoint di token OAuth 2.0).

![Credenziali di Azure](./media/jenkins-azure-vm-agents/service-principal.png)

* Fare clic su **Verify configuration** (Verifica configurazione) per assicurarsi che la configurazione del profilo sia corretta.
* Salvare la configurazione e continuare con il passaggio successivo.

## <a name="template-configuration"></a>Configurazione del modello

### <a name="general-configuration"></a>Configurazione generale
Configurare quindi un modello da usare per definire un agente di macchine virtuali di Azure. 

* Fare clic su **Add** (Aggiungi) per aggiungere un modello. 
* Specificare un nome per il nuovo modello. 
* Per l'etichetta, immettere "ubuntu". Questa etichetta viene usata durante la configurazione del processo.
* Selezionare l'area geografica desiderata dalla casella combinata.
* Selezionare le dimensioni desiderate per la macchina virtuale.
* Specificare il nome dell'account di archiviazione di Azure o lasciare vuota la casella per usare il nome predefinito "jenkinsarmst".
* Specificare il periodo di memorizzazione in minuti. Questa impostazione definisce il numero di minuti che Jenkins può attendere prima di eliminare automaticamente un agente inattivo. Immettere 0 se non si vuole eliminare automaticamente gli agenti inattivi.

![Configurazione generale](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Configurazione dell'immagine

Per creare un agente Linux (Ubuntu), selezionare **Image reference** (Riferimento immagine) e usare la configurazione seguente come esempio. Fare riferimento ad [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) per le immagini supportate di Azure più recenti.

* Image Publisher (Editore immagine): Canonical
* Image Offer (Offerta immagine): UbuntuServer
* Image Sku (Sku immagine): 14.04.5-LTS
* Image Version (Versione immagine): la più recente
* OS Type (Tipo di sistema operativo): Linux
* Launch Method (Metodo di avvio): SSH
* Specificare le credenziali di amministratore
* Per lo script di inizializzazione della macchina virtuale, immettere:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Configurazione dell'immagine](./media/jenkins-azure-vm-agents/image-config.png)

* Fare clic su **Verify Template** (Verifica modello) per verificare la configurazione.
* Fare clic su **Salva**.

## <a name="create-a-job-in-jenkins"></a>Creare un processo in Jenkins

* Nel dashboard di Jenkins fare clic su **New Item**. 
* Immettere un nome, selezionare **Freestyle project** (Progetto Freestyle) e fare clic su **OK**.
* Nella scheda **General** (Generale) selezionare "Restrict where project can be run" (Limitare l'area di esecuzione del progetto) e digitare "ubuntu" in Label Expression (Espressione etichetta). Nell'elenco a discesa verrà visualizzato "ubuntu".
* Fare clic su **Salva**.

![Configurazione del processo](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Compilare il nuovo progetto

* Tornare al dashboard di Jenkins.
* Fare clic con il pulsante destro del mouse sul nuovo processo creato, quindi fare clic su **Build now** (Compila ora). Verrà avviata la compilazione. 
* Una volta completata la compilazione, passare a **Console output** (Output console). La compilazione è stata eseguita in remoto in Azure.

![Output console](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Riferimento

* Video di Azure Friday: [Integrazione continua con Jenkins tramite gli agenti di macchine virtuali di Azure](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* Informazioni di supporto e opzioni di configurazione: [Wiki del plug-in Jenkins Agente di macchine virtuali di Azure](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


