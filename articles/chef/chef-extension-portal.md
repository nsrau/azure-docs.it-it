---
title: Installare il client Chef dal portale di Azure
description: Informazioni su come distribuire e configurare il client Chef dal portale di Azure
keywords: azure, chef, devops, client, installare, portale
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: 6e46133c598c44b314077f2d020852416d3d2745
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586360"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Installare il client Chef dal portale di Azure
È possibile aggiungere l'estensione client Chef direttamente su un computer Windows o Linux dal portale di Azure. Questo articolo illustra nel dettaglio il processo usando una macchina virtuale Linux nuova.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure:** se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

- **Chef**: se non si ha un account Chef attivo, iscriversi a una [prova gratuita di Chef ospitato](https://manage.chef.io/signup). Per proseguire con le istruzioni di questo articolo è necessario avere i valori seguenti dell'account Chef:
  - Chiave organization_validation
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>Installare l'estensione Chef in una nuova macchina virtuale Linux
In questa sezione si usa il portale di Azure per creare una macchina virtuale Linux. La procedura illustra anche come installare l'estensione Chef nella nuova macchina virtuale.

1. Passare al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare l'opzione **Macchine virtuali**. Se l'opzione **Macchine virtuali** non è disponibile, selezionare **Tutti i servizi** e quindi **Macchine virtuali**.

1. Nella scheda **Macchine virtuali** selezionare **Aggiungi**.

    ![Aggiungere una nuova macchina virtuale nel portale di Azure](./media/chef-extension-portal/add-vm.png)

1. Nella scheda **Calcolo** selezionare il sistema operativo desiderato. Per questa demo è selezionato **Server Ubuntu**.

1. Nella scheda **Server Ubuntu** selezionare **Server Ubuntu 16.04 LTS**.

    ![Quando si crea una macchina virtuale Ubuntu, specificare la versione necessaria](./media/chef-extension-portal/ubuntu-server-version.png)

1. Nella scheda **Server Ubuntu 16.04 LTS** selezionare **Crea**.

    ![Ubuntu dispone di informazioni aggiuntive sul prodotto](./media/chef-extension-portal/create-vm.png)

1. Nella scheda **Crea macchina virtuale** selezionare **Generale**.

1. Nella scheda **Generale** specificare i valori seguenti e quindi scegliere **OK**.

   - **Nome**: specificare un nome per la nuova macchina virtuale.
   - **Tipo di disco della macchina virtuale**: specificare **SSD** o **HDD** per il tipo di disco di archiviazione. Per altre informazioni sui tipi di disco di macchina virtuale in Azure, vedere l'articolo [Selezionare un tipo di disco](../virtual-machines/windows/disks-types.md).
   - **Nome utente**: immettere un nome utente con privilegi di amministratore nella macchina virtuale.
   - **Tipo di autenticazione**: selezionare **Password**. È anche possibile selezionare **Chiave pubblica SSH** e specificare un valore di chiave pubblica SSH. Ai fini di questa demo (e nelle schermate) è selezionata l'opzione **Password**.
   - **Password** e **Conferma password**: immettere una password per l'utente.
   - **Accedi con Azure Active Directory**: selezionare **Disattivato**.
   - **Sottoscrizione**: se sono disponibili più sottoscrizioni, selezionare la sottoscrizione di Azure desiderata.
   - **Gruppo di risorse**: immettere un nome per il gruppo di risorse.
   - **Località**: selezionare **Stati Uniti orientali**.

     ![Scheda Generale per la creazione di una macchina virtuale](./media/chef-extension-portal/add-vm-basics.png)

1. Selezionare la dimensione della macchina virtuale nella scheda **Scegli una dimensione** e quindi scegliere **Seleziona**.

1. Nella scheda **Impostazioni** la maggior parte dei valori è popolata in base ai valori selezionati nelle schede precedenti. Selezionare **Estensioni**.

     ![Le estensioni vengono aggiunte alle macchine virtuali tramite la scheda Impostazioni](./media/chef-extension-portal/add-vm-select-extensions.png)

1. Nella scheda **Estensioni** selezionare **Aggiungi estensione**.

     ![Selezionare Aggiungi estensione per aggiungere un'estensione a una macchina virtuale](./media/chef-extension-portal/add-vm-add-extension.png)

1. Nella scheda **Nuova risorsa** selezionare **Linux Chef Extension (1.2.3)** (Estensione Chef per Linux (1.2.3)).

     ![Chef include estensioni della macchina virtuale per Linux e Windows](./media/chef-extension-portal/select-linux-chef-extension.png)

1. Nella scheda **Linux Chef Extension** (Estensione Chef per Linux) selezionare **Crea**.

1. Nella scheda **Installa estensione** specificare i valori seguenti e quindi scegliere **OK**.

    - **URL del server Chef**: Immettere l'URL del server Chef che include il nome dell'organizzazione, ad esempio, *https://api.chef.io/organization/mycompany*.
    - **Nome nodo Chef:** immettere il nome del nodo Chef.
    - **Elenco di esecuzione:** immettere l'elenco di esecuzione Chef aggiunto alla macchina. Questo valore può essere lasciato vuoto.
    - **Nome client di convalida:** immettere il nome del client di convalida Chef.Validation client name: Enter the Chef Validation Client Name. ad esempio, `tarcher-validator`.
    - **Chiave di convalida**: selezionare un file contenente la chiave di convalida usata per il bootstrap delle macchine.
    - **File di configurazione client:** selezionare un file di configurazione per chef-client. Questo valore può essere lasciato vuoto.
    - **Chef Client version** (Versione client Chef): immettere la versione del client Chef da installare. Questo valore può essere lasciato vuoto, che installa la versione più recente.
    - **SSL Verification Mode** (Modalità di verifica SSL): selezionare **Nessuna** o **Peer**. *Nessuno* è stato selezionato per la demo.
    - **Chef Environment** (Ambiente Chef): specificare l'ambiente Chef del quale è membro il nodo corrente. Questo valore può essere lasciato vuoto.
    - **Segreto borsa dati crittografati** - Selezionare un file contenente il segreto per il contenitore di dati crittografati a cui questa macchina deve accedere. Questo valore può essere lasciato vuoto.
    - **Certificato SSL del** server Chef: selezionare il certificato SSL assegnato al server Chef. Questo valore può essere lasciato vuoto.

      ![Installazione del server Chef in una macchina virtuale Linux](./media/chef-extension-portal/install-extension.png)

1. Quando viene visualizzata la scheda **Estensioni,** selezionare **OK**.

1. Quando viene visualizzata la scheda **Impostazioni,** selezionare **OK**.

1. Quando viene visualizzata la scheda **Crea,** viene visualizzato un riepilogo delle opzioni selezionate e immesse. Verificare le informazioni e le **Condizioni per l'utilizzo**, quindi selezionare **Crea**.

Al completamento del processo di creazione e distribuzione della macchina virtuale con l'estensione Chef, una notifica indica l'esito positivo o negativo dell'operazione. Al completamento della creazione della nuova macchina virtuale nel portale di Azure, viene aperta automaticamente la pagina delle risorse della macchina virtuale.

![Installazione del server Chef in una macchina virtuale Linux](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Creare una macchina virtuale Windows in Azure usando Chef](chef-automation.md)