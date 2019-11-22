---
title: "Esercitazione: Creare una VM Linux con un'identità gestita dall'immagine di Azure Marketplace usando Terraform"
description: Creare una macchina virtuale Linux Terraform con un'identità gestita e la gestione remota dello stato usando un'immagine di Azure Marketplace
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 233012d6caf1280914a6d2439ae856d69570fff7
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838040"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Esercitazione: Creare una VM Linux con un'identità gestita dall'immagine di Azure Marketplace usando Terraform

Questo articolo illustra come usare un'[immagine di Terraform del Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) per creare una macchina virtuale Ubuntu Linux (16.04 LTS) con l'ultima versione di [Terraform](https://www.terraform.io/intro/index.html) installata e configurata tramite [identità gestite per risorse di Azure](/azure/active-directory/managed-service-identity/overview). Questa immagine configura anche un back-end remoto per abilitare la gestione dello [stato remoto](https://www.terraform.io/docs/state/remote.html) con Terraform. 

Grazie all'immagine di Terraform del Marketplace è possibile imparare a usare Terraform in Azure, senza dover installare e configurare Terraform manualmente. 

Per questa immagine di macchina virtuale Terraform non sono previsti costi per il software. Si pagano solo le tariffe di utilizzo dell'hardware di Azure in base alle dimensioni delle macchine virtuali di cui è stato effettuato il provisioning. 

Per altre informazioni sui costi di calcolo, vedere la [pagina dei prezzi delle macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Prerequisiti
Per poter creare una macchina virtuale Linux Terraform è necessario avere una sottoscrizione di Azure. Se non è già disponibile, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Creare la macchina virtuale Terraform 

Ecco i passaggi necessari per creare un'istanza di una macchina virtuale Linux Terraform: 

1. Nel portale di Azure passare all'elenco [Crea una risorsa](https://ms.portal.azure.com/#create/hub).

1. Nella barra di ricerca **Cerca nel Marketplace** cercare **Terraform**. 

1. Selezionare **Crea**. 

1. Le sezioni seguenti forniscono gli input per ognuno dei passaggi della procedura guidata usata per creare la macchina virtuale Linux Terraform. La sezione seguente elenca gli input necessari per configurare ognuno di questi passaggi.

## <a name="details-on-the-create-terraform-tab"></a>Dettagli nella scheda Crea Terraform

Immettere i dettagli seguenti nella scheda **Crea Terraform**:

1. **Nozioni di base**
    
   * **Nome**: il nome della macchina virtuale Terraform.
   * **Nome utente**: primo ID di accesso dell'account.
   * **Password**: prima password dell'account. È possibile usare una chiave pubblica SSH invece di una password.
   * **Sottoscrizione** sottoscrizione in cui il computer deve essere creato e fatturato. È necessario disporre di privilegi di creazione delle risorse per questa sottoscrizione.
   * **Gruppo di risorse**: Gruppo di risorse nuovo o esistente.
   * **Percorso**: data center più appropriato. In genere è il data center che include la maggior parte dei dati o quello più vicino alla posizione fisica per l'accesso più veloce alla rete.

2. **Impostazioni aggiuntive**

   * **Dimensione**: Dimensioni della macchina virtuale. 
   * **Tipo di disco VM**: unità SSD o HDD.

3. **Riepilogo di Terraform**

   * Verificare che tutte le informazioni immesse siano corrette. 

4. **Acquista**

   * Per avviare il processo di provisioning, selezionare **Acquista**. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede addebiti aggiuntivi oltre a quelli per il calcolo in base alle dimensioni del server scelte nel passaggio Dimensioni.

L'immagine della VM Terraform esegue questa procedura:

* Crea una macchina virtuale con identità assegnata dal sistema in base all'immagine di Ubuntu 16.04 LTS.
* Installa le identità gestite per l'estensione delle risorse di Azure nella VM per consentire l'emissione di token OAuth per le risorse di Azure.
* Assegna le autorizzazioni di Controllo degli accessi in base al ruolo all'identità gestita, concedendo i diritti di proprietario per il gruppo di risorse.
* Crea una cartella per il modello Terraform (tfTemplate).
* Preconfigura uno stato remoto di Terraform con il back-end di Azure.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Accedere a una VM Linux Terraform

Dopo aver creato la macchina virtuale, seguire questa procedura:

1. Accedere alla macchina virtuale tramite SSH. Usare le credenziali dell'account create nella sezione precedente. In Windows è possibile scaricare uno strumento client SSH come [Putty](https://www.putty.org/).

1. Concedere le autorizzazioni di collaboratore per l'intera sottoscrizione alle identità gestite per le risorse di Azure nella macchina virtuale. 

    L'autorizzazione di collaboratore consente alle identità gestite per le risorse di Azure nella VM di usare Terraform per creare risorse all'esterno del gruppo di risorse della VM. Eseguire questa operazione con lo script seguente: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Questo script usa il meccanismo di [accesso interattivo dell'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) per l'autenticazione con Azure. Questo processo assegna l'autorizzazione [Collaboratore di identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per l'intera sottoscrizione. 

1. La macchina virtuale ha un back-end di stato remoto Terraform. Per abilitarlo nella distribuzione di Terraform, è necessario copiare il file `remoteState.tf` nella radice degli script di Terraform.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Per altre informazioni sulla gestione remota dello stato, vedere [Stato remoto di Terraform](https://www.terraform.io/docs/state/remote.html). La chiave di accesso all'account di archiviazione viene esposta in questo file. Escluderla prima di eseguire il commit dei file di configurazione di Terraform nel controllo del codice sorgente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Vedere altre informazioni sull'uso di Terraform in Azure](/azure/terraform)