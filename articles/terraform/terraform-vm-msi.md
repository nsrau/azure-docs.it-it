---
title: Usare un'immagine di Azure Marketplace per creare una macchina virtuale Terraform Linux con Identità del servizio gestito
description: Usare un'immagine di Azure Marketplace per creare una macchina virtuale Terraform Linux con Identità del servizio gestito e la gestione dello stato remoto per distribuire facilmente le risorse in Azure.
keywords: terraform, devops, MSI, macchina virtuale, stato remoto, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Usare un'immagine di Azure Marketplace per creare una macchina virtuale Terraform Linux con Identità del servizio gestito

Questo articolo illustra come usare un'[immagine di Terraform Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) per creare una `Ubuntu Linux VM (16.04 LTS)` con l'ultima versione di [Terraform](https://www.terraform.io/intro/index.html) installata e configurata mediante [Identità del servizio gestito](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Questa immagine configura anche un back-end remoto per abilitare la gestione dello [stato remoto](https://www.terraform.io/docs/state/remote.html) mediante Terraform. Grazie all'immagine di Terraform Marketplace è possibile imparare a usare Terraform in Azure in pochi minuti, senza dover installare Terraform e configurare l'autenticazione manualmente. 

Per questa immagine di macchina virtuale Terraform non sono previsti costi per il software. Si pagano solo le spese d'uso dell'hardware di Azure valutate in base alle dimensioni della macchina virtuale di cui si esegue il provisioning. Altre informazioni sui costi di calcolo sono disponibili nella [pagina dei prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Prerequisiti
Per poter creare una macchina virtuale Linux Terraform è necessario avere una sottoscrizione di Azure. Se non è già disponibile, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Creare la macchina virtuale Terraform 

Ecco i passaggi necessari per creare un'istanza di macchina virtuale Linux Terraform. 

1. Passare all'elenco [Crea una risorsa](https://ms.portal.azure.com/#create/hub) nel portale di Azure.
2. Cercare `Terraform` nella barra di ricerca di `Search the Marketplace`. Selezionare il modello `Terraform`. Selezionare il pulsante **Crea** nella scheda dei dettagli di Terraform in basso a destra.
![testo alternativo](media\terraformmsi.png)
3. Le sezioni seguenti forniscono gli input per ognuno dei passaggi (**elencati sulla destra**) della procedura guidata usata per creare la macchina virtuale Terraform Linux.  Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi.

## <a name="details-in-create-terraform-tab"></a>Dettagli nella scheda Crea Terraform

Ecco i dettagli che devono essere immessi nella scheda Crea Terraform.

a. **Nozioni di base**
    
* **Nome**: nome della macchina virtuale Terraform.
* **Nome utente**: primo ID di accesso dell'account.
* **Password**: la prima password dell'account. È possibile usare una chiave pubblica SSH invece di una password.
* **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale. È necessario disporre di privilegi di creazione delle risorse per questa sottoscrizione.
* **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o usarne uno esistente.
* **Location**: selezionare la posizione del data center più appropriata. In genere è il data center che include la maggior parte dei dati o è più vicino alla posizione fisica per l'accesso più veloce alla rete.

b. **Impostazioni aggiuntive**

* Dimensioni: dimensioni della macchina virtuale.
* Tipo di disco della macchina virtuale: scegliere tra SSD o HDD.

c. **Riepilogo di Terraform**

* Verificare che tutte le informazioni immesse siano corrette. 

d. **Acquista**

* Per avviare il provisioning, fare clic su Acquista. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio Dimensioni.

L'immagine della macchina virtuale Terraform esegue questa procedura:

* Crea una macchina virtuale con identità assegnata dal sistema in base all'immagine di Ubuntu 16.04 LTS
* Installa l'estensione MSI nella macchina virtuale per consentire il rilascio dei token OAuth per le risorse di Azure
* Assegna le autorizzazioni di Controllo degli accessi in base al ruolo all'identità gestita, concedendo i diritti di proprietario per il gruppo di risorse
* Crea una cartella per il modello Terraform (tfTemplate)
* Preconfigura lo stato remoto di Terraform con il back-end di Azure

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Come accedere alla macchina virtuale Linux Terraform e configurarla

Dopo aver creato la VM, è possibile accedervi tramite SSH. Usare le credenziali dell'account creato nella sezione Nozioni di base del passaggio 3 per l'interfaccia della shell di testo. In Windows è possibile scaricare uno strumento client SSH come [Putty](http://www.putty.org/).

Dopo aver usato `SSH` per stabilire la connessione alla macchina virtuale, è necessario assegnare le autorizzazioni di collaboratore per l'intera sottoscrizione all'identità del servizio gestito nella macchina virtuale. L'autorizzazione di collaboratore consente all'identità del servizio gestito nella macchina virtuale di usare Terraform per creare risorse al di fuori del gruppo di risorse della macchina virtuale. È possibile completare facilmente questa azione eseguendo uno script una sola volta. Il comando da usare è il seguente:

`. ~/tfEnv.sh`

Questo script usa il meccanismo di [accesso interattivo all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) per eseguire l'autenticazione con Azure e assegnare all'identità del servizio gestito della macchina virtuale l'autorizzazione di collaboratore sull'intera sottoscrizione. 

 Nella macchina virtuale è stato creato il back-end dello stato remoto di Terraform. Per abilitarlo nella distribuzione di Terraform, occorre copiare il file remoteState.tf dalla directory tfTemplate alla radice degli script di Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Altre informazioni sulla gestione dello stato remoto sono disponibili [qui](https://www.terraform.io/docs/state/remote.html). La chiave di accesso alle risorse di archiviazione è esposta in questo file e deve essere archiviata attentamente nel controllo del codice sorgente.  

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare una macchina virtuale Terraform Linux in Azure. Di seguito sono segnalate altre risorse di approfondimento su Terraform di Azure. 

 [Hub Terraform in Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentazione sul provider Terraform in Azure](http://aka.ms/terraform)  
 [Codice sorgente del provider Terraform in Azure](http://aka.ms/tfgit)  
 [Moduli del provider Terraform in Azure](http://aka.ms/tfmodules)
 

















