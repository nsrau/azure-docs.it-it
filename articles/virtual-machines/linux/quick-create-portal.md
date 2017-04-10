---
title: Avvio rapido in Azure - Creare un portale di VM | Microsoft Docs
description: Avvio rapido in Azure - Creare un portale di VM
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 8697ae51b5007cb50ac67accb5dafe48914af8ee
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Creare una macchina virtuale Linux con il portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata sul browser per la creazione e la configurazione delle macchine virtuali e di tutte le risorse correlate. Questa guida introduttiva illustra la creazione di una macchina virtuale con il portale di Azure. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per completare questa guida introduttiva è necessaria una coppia di chiavi SSH. Se è già disponibile una coppia di chiavi SSH, questo passaggio può essere ignorato. Se si usa un computer Windows, seguire le istruzioni riportate [qui](ssh-from-windows.md). 

In una shell Bash eseguire questo comando e attenersi alle indicazioni visualizzate. L'output del comando include il nome del file di chiave pubblica. Il contenuto del file è necessario durante la creazione della macchina virtuale.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Calcolo** nel pannello **Nuovo**, selezionare **Ubuntu Server 16.04 LTS** nel pannello **Calcolo** e quindi fare clic sul pulsante **Crea**.

3. Compilare il modulo **Generale** della macchina virtuale. In **Tipo di autenticazione** selezionare **SSH**. Quando si incolla la **chiave pubblica SSH**, prestare attenzione a rimuovere gli eventuali spazi iniziali o finali. Per **Gruppo di risorse** creare un nuovo gruppo. Un gruppo di risorse è un contenitore logico in cui vengono create e gestite collettivamente le risorse di Azure. Al termine fare clic su **OK**.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/quick-create-portal/create-vm-portal-basic-blade.png)  

4. Scegliere una dimensione per la VM e fare clic su **Seleziona**. 

    ![Selezionare una dimensione per la VM nel pannello del portale](./media/quick-create-portal/create-vm-portal-size-blade.png)

5. Nel pannello delle impostazioni selezionare **Sì** in **Usa dischi gestiti**, mantenere i valori predefiniti per le altre impostazioni e fare clic su **OK**.

6. Nella pagina del riepilogo fare clic su **OK** per avviare la distribuzione della macchina virtuale.

7. Per monitorare lo stato di distribuzione fare clic sulla macchina virtuale. La VM è disponibile nel dashboard del portale di Azure o selezionando **Macchine virtuali** nel menu a sinistra. Dopo che la VM è stata creata, lo stato cambia da **Distribuzione in corso** a **In esecuzione**.


## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Per impostazione predefinita nelle macchine virtuali Linux distribuite in Azure sono consentite solo le connessioni SSH. Se si intende usare questa macchina virtuale come un server Web, è necessario aprire la porta 80 al traffico Web. Questo passaggio illustra come creare una regola del gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 80.

1. Nella sezione **Informazioni di base** del pannello della macchina virtuale fare clic sul nome del **gruppo di risorse**.
2. Nel pannello del gruppo di risorse fare clic su di **Gruppo di sicurezza di rete** nell'elenco di risorse. Il gruppo di sicurezza di rete deve avere lo stesso nome della macchina virtuale, con -nsg aggiunto alla fine.
3. Fare clic sull'intestazione **Regola di sicurezza in ingresso** per aprire l'elenco di regole in ingresso. Nell'elenco dovrebbe già essere presente una regola per RDP.
4. Fare clic su **+ Aggiungi** per aprire il pannello **Aggiungi regola di sicurezza in ingresso**.
5. In **Nome** digitare **nginx** e assicurarsi che l'**Intervallo di porte** sia impostato su 80 e l'**Azione** sia impostata su **Consenti**, quindi fare clic su **OK**.


## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione SSH con la macchina virtuale.

1. Fare clic sul pulsante **Connetti** nel pannello della macchina virtuale. Il pulsante Connetti mostra una stringa di connessione SSH che può essere usata per connettersi alla macchina virtuale.

    ![Portale 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Usare il comando seguente per creare una sessione SSH. Sostituire la stringa di connessione con quella copiata dal portale di Azure.

```bash 
ssh <replace with IP address>
```

## <a name="install-nginx"></a>Installare NGINX

Usare lo script bash seguente per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Dopo l'installazione di NGINX e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di NGINX. Assicurarsi di usare il valore `publicIpAddress` indicato in precedenza per visitare la pagina predefinita. 

![Sito NGINX predefinito](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo selezionare il gruppo di risorse nel pannello della macchina virtuale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

[Creare esercitazioni per macchine virtuali a disponibilità elevata](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

