---
title: Guida introduttiva - Creare una macchina virtuale Linux nel portale di Azure | Microsoft Docs
description: Questa guida introduttiva illustra come usare il portale di Azure per creare una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 18ac0291bff2c0fbfffdd5dfa3097f8a6acb561f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Guida introduttiva: Creare una macchina virtuale Linux nel portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata su browser per creare le macchine virtuali e le risorse associate. Questa guida introduttiva illustra come usare il portale di Azure per distribuire una macchina virtuale Linux in Azure che esegue Ubuntu. Per visualizzare la macchina virtuale in azione, viene quindi usato SSH per accedere alla macchina virtuale e installare il server Web NGINX.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per completare questa guida introduttiva è necessaria una coppia di chiavi SSH. Se è già disponibile una coppia di chiavi SSH, questo passaggio può essere ignorato.

Per creare una coppia di chiavi SSH e accedere alle macchine virtuali Linux, eseguire il comando seguente da una shell Bash e seguire le istruzioni visualizzate. Ad esempio, è possibile usare [Azure Cloud Shell](../../cloud-shell/overview.md) o il [sottosistema di Windows per Linux](/windows/wsl/install-win10). L'output del comando include il nome del file di chiave pubblica. Copiare il contenuto del file di chiave pubblica (`cat ~/.ssh/id_rsa.pub`) negli Appunti:

```bash
ssh-keygen -t rsa -b 2048
```

Per informazioni più dettagliate su come creare coppie di chiavi SSH, incluso l'uso di PuTTy, vedere [Come usare le chiavi SSH con Windows in Azure](ssh-from-windows.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com

## <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace, cercare e selezionare **Ubuntu Server 16.04 LTS** di Canonical e quindi scegliere **Crea**.

3. Specificare un nome di macchina virtuale, ad esempio *macchinavirtuale*, lasciare il tipo di disco *SSD* e quindi specificare un nome utente, ad esempio *utenteazure*.

4. . Per **Tipo di autenticazione** selezionare **Chiave pubblica SSH** e quindi incollare la chiave pubblica nella casella di testo. Prestare attenzione a rimuovere gli eventuali spazi iniziali o finali nella chiave pubblica.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/quick-create-portal/create-vm-portal-basic-blade.png)

5. Scegliere **Crea nuovo** per creare un nuovo gruppo di risorse e quindi specificare un nome, ad esempio *GruppoRisorse*. Scegliere la **Località** desiderata e quindi selezionare **OK**.

4. Selezionare una dimensione per la VM. È possibile filtrare in base a *Tipo di calcolo* oppure a *Tipo di disco*, ad esempio. Le dimensioni consigliate per le macchine virtuali sono *D2s_v3*.

    ![Screenshot che mostra le dimensioni delle VM](./media/quick-create-portal/create-linux-vm-portal-sizes.png)

5. In **Impostazioni**  accettare tutte le impostazioni predefinite e selezionare **OK**.

6. Nella pagina di riepilogo selezionare **Crea** per avviare la distribuzione della macchina virtuale.

7. La macchina virtuale viene aggiunta al dashboard del portale di Azure. Una volta completata la distribuzione verrà automaticamente aperto il riepilogo della macchina virtuale.

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Creare una connessione SSH con la macchina virtuale.

1. Selezionare il pulsante **Connetti** nella pagina di panoramica per la macchina virtuale. 

    ![Portale 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Nella pagina **Connetti a macchina virtuale** mantenere le opzioni predefinite per la connessione con nome DNS sulla porta 22. In **Accedi con l'account locale della macchina virtuale** viene visualizzato un comando di connessione. Fare clic sul pulsante per copiare il comando. Di seguito è mostrato un esempio di comando di connessione SSH:

    ```bash
    ssh azureuser@myvm-123abc.eastus.cloudapp.azure.com
    ```

3. Incollare il comando di connessione SSH in una shell, ad esempio Azure Cloud Shell o Bash in Ubuntu in Windows per creare la connessione. 

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web NGINX. Per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente, eseguire i comandi seguenti dalla sessione SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Al termine, usare `exit` per chiudere la sessione SSH e tornare alle proprietà della macchina virtuale nel portale di Azure.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Un gruppo di sicurezza di rete (NSG) protegge il traffico in ingresso e in uscita. Quando si crea una VM nel portale di Azure, viene creata una regola in ingresso sulla porta 22 per le connessioni SSH. Questa macchina virtuale ospita un server Web, quindi è necessario creare una regola del gruppo di sicurezza di rete per la porta 80.

1. Nella pagina di panoramica per la macchina virtuale selezionare **Rete**.
2. Viene visualizzato l'elenco delle regole ingresso e in uscita esistenti. Scegliere **Aggiungi regola porta in ingresso**.
3. Selezionare l'opzione **Base** nella parte superiore e quindi scegliere *HTTP* nell'elenco dei servizi disponibili. Vengono forniti automaticamente la porta 80, una priorità e un nome.
4. Per creare la regola, selezionare **Aggiungi**.

## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Con NGINX installato e la porta 80 aperta per la macchina virtuale, è ora possibile accedere al server Web da Internet. Aprire un Web browser e immettere l'indirizzo IP pubblico della VM. L'indirizzo IP pubblico è reperibile nella pagina di panoramica per la macchina virtuale o all'inizio della pagina *Rete* in cui viene aggiunta la regola di porta in ingresso.

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, sono stati creati un gruppo di sicurezza di rete e una regola ed è stato installato un server Web. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Linux.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Linux in Azure](./tutorial-manage-vm.md)
