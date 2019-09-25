---
title: Usare un'immagine di Azure Marketplace per creare una macchina virtuale Terraform Linux con un'identità gestita
description: Usare un'immagine di Azure Marketplace per creare una macchina virtuale Terraform Linux con un'identità gestita e la funzione di gestione dello stato remoto per distribuire facilmente le risorse in Azure.
services: terraform
ms.service: azure
keywords: terraform, devops, MSI, macchina virtuale, stato remoto, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 835170448db8709812ca62ac67f8659b3b6a2807
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173050"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Usare un'immagine di Azure Marketplace per creare una macchina virtuale Terraform Linux con identità gestite per risorse di Azure

Questo articolo illustra come usare un'[immagine di Terraform del Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) per creare una macchina virtuale Ubuntu Linux (16.04 LTS) con l'ultima versione di [Terraform](https://www.terraform.io/intro/index.html) installata e configurata tramite [identità gestite per risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Questa immagine configura anche un back-end remoto per abilitare la gestione dello [stato remoto](https://www.terraform.io/docs/state/remote.html) con Terraform. 

Grazie all'immagine di Terraform del Marketplace è possibile imparare a usare Terraform in Azure, senza dover installare e configurare Terraform manualmente. 

Per questa immagine di macchina virtuale Terraform non sono previsti costi per il software. Si pagano solo le spese di utilizzo dell'hardware di Azure valutate in base alle dimensioni della macchina virtuale di cui viene effettuato il provisioning. Per altre informazioni sui costi di calcolo, vedere la [pagina dei prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Prerequisiti
Per poter creare una macchina virtuale Linux Terraform è necessario avere una sottoscrizione di Azure. Se non è già disponibile, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Creare la macchina virtuale Terraform 

Ecco i passaggi necessari per creare un'istanza di una macchina virtuale Linux Terraform: 

1. Nel portale di Azure passare all'elenco [Crea una risorsa](https://ms.portal.azure.com/#create/hub).

2. Nella barra di ricerca **Cerca nel Marketplace** cercare **Terraform**. Selezionare il modello **Terraform**. 

3. Nella scheda dei dettagli di Terraform in basso a destra selezionare il pulsante **Crea**.

    ![Creare una macchina virtuale Terraform](media/terraformmsi.png)

4. Le sezioni seguenti forniscono gli input per ognuno dei passaggi della procedura guidata usata per creare la macchina virtuale Terraform Linux. La sezione seguente elenca gli input necessari per configurare ognuno di questi passaggi.

## <a name="details-on-the-create-terraform-tab"></a>Dettagli nella scheda Crea Terraform

Immettere i dettagli seguenti nella scheda **Crea Terraform**:

1. **Nozioni di base**
    
   * **Nome**: nome della macchina virtuale Terraform.
   * **Nome utente**: primo ID di accesso dell'account.
   * **Password**: prima password dell'account. È possibile usare una chiave pubblica SSH invece di una password.
   * **Sottoscrizione** sottoscrizione in cui il computer deve essere creato e fatturato. È necessario disporre di privilegi di creazione delle risorse per questa sottoscrizione.
   * **Gruppo di risorse**: Gruppo di risorse nuovo o esistente.
   * **Percorso**: data center più appropriato. In genere è il data center che include la maggior parte dei dati o quello più vicino alla posizione fisica per l'accesso più veloce alla rete.

2. **Impostazioni aggiuntive**

   * **Dimensione**: Dimensione della macchina virtuale. 
   * **Tipo di disco VM**: unità SSD o HDD.

3. **Riepilogo di Terraform**

   * Verificare che tutte le informazioni immesse siano corrette. 

4. **Acquista**

   * Per avviare il processo di provisioning, selezionare **Acquista**. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelte nel passaggio Dimensioni.

L'immagine della macchina virtuale Terraform esegue questa procedura:

* Crea una macchina virtuale con identità assegnata dal sistema in base all'immagine di Ubuntu 16.04 LTS.
* Installa l'estensione MSI nella macchina virtuale per consentire il rilascio dei token OAuth per le risorse di Azure.
* Assegna le autorizzazioni di Controllo degli accessi in base al ruolo all'identità gestita, concedendo i diritti di proprietario per il gruppo di risorse.
* Crea una cartella per il modello Terraform (tfTemplate).
* Preconfigura uno stato remoto di Terraform con il back-end di Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Accedere a una macchina virtuale Linux Terraform e configurarla

Dopo avere creato la VM, è possibile accedervi tramite SSH. Usare le credenziali dell'account creato nella sezione "Nozioni di base" del passaggio 3 per l'interfaccia della shell di testo. In Windows è possibile scaricare uno strumento client SSH come [Putty](https://www.putty.org/).

Dopo avere usato SSH per stabilire la connessione alla macchina virtuale, è necessario assegnare le autorizzazioni di collaboratore per l'intera sottoscrizione a identità gestite per risorse di Azure nella macchina virtuale. 

L'autorizzazione di collaboratore consente all'identità del servizio gestito nella macchina virtuale di usare Terraform per creare risorse al di fuori del gruppo di risorse della macchina virtuale. È possibile completare facilmente questa azione eseguendo uno script una sola volta. Usare il comando seguente:

`. ~/tfEnv.sh`

Questo script usa il meccanismo di [accesso interattivo all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) per eseguire l'autenticazione con Azure e assegnare all'identità gestita della macchina virtuale l'autorizzazione di collaboratore per l'intera sottoscrizione. 

 La macchina virtuale ha un back-end di stato remoto Terraform. Per abilitarlo nella distribuzione di Terraform, copiare il file remoteState.tf dalla directory tfTemplate alla radice degli script di Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Per altre informazioni sulla gestione dello stato remoto, vedere [questa pagina sullo stato remoto di Terraform](https://www.terraform.io/docs/state/remote.html). La chiave di accesso alle risorse di archiviazione viene esposta in questo file e deve essere esclusa prima di eseguire il commit dei file di configurazione di Terraform nel controllo del codice sorgente.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare una macchina virtuale Terraform Linux in Azure. Di seguito sono segnalate altre risorse di approfondimento su Terraform di Azure: 

 [Hub Terraform in Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure provider documentation (Documentazione sul provider Terraform in Azure)](https://aka.ms/terraform)  
 [Terraform Azure provider source (Codice sorgente del provider Terraform in Azure)](https://aka.ms/tfgit)  
 [Terraform Azure modules (Moduli del provider Terraform in Azure)](https://aka.ms/tfmodules)
 

















