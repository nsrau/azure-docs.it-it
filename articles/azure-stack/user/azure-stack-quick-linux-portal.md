---
title: Azure dello Stack di avvio rapido - creazione macchina virtuale portale
description: Azure Stack Quick Start - creare una VM Linux tramite il portale
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Guida introduttiva: creare una macchina virtuale di server Linux con il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile creare una macchina virtuale Ubuntu Server 16.04 LTS tramite il portale di Azure Stack. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. In questo articolo offre inoltre la procedura per:

* Connettersi alla macchina virtuale con un client remoto.
* Installare un server web NGINX.
* Pulire le risorse.

## <a name="prerequisites"></a>Prerequisiti

* **Un'immagine Linux nel marketplace Azure Stack**

   Per impostazione predefinita, il marketplace Azure Stack non contiene un'immagine Linux. Prima di creare una macchina virtuale di Linux server, assicurarsi che l'operatore di Stack di Azure fornisce le **Ubuntu Server 16.04 LTS** immagine necessaria. L'operatore può utilizzare i passaggi descritti nel [scaricare elementi del marketplace da Azure a Azure Stack](../azure-stack-download-azure-marketplace-item.md) articolo.

* **Accesso a un client SSH**

   Se si utilizza il Kit di sviluppo dello Stack di Azure (ASDK), non è necessario l'accesso a un client SSH. Se è necessario un client, esistono diversi pacchetti che includono un client SSH. Ad esempio PuTTY include un client SSH e il generatore di chiavi SSH (puttygen.exe). Per ulteriori informazioni sui pacchetti disponibili, leggere l'articolo di Azure seguente: [come chiavi uso di SSH con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Questa Guida rapida Usa PuTTY per generare le chiavi SSH e connettersi alla macchina virtuale del server Linux. Per scaricare e installare PuTTY, passare a [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

È necessario una coppia di chiavi SSH per completare tutti i passaggi in questo articolo. Se si dispone di una coppia di chiavi SSH esistente, è possibile ignorare questo passaggio.

1. Passare alla cartella di installazione PuTTY (il percorso predefinito è ```C:\Program Files\PuTTY```) ed eseguire ```puttygen.exe```.
2. Nella finestra Generatore di chiavi PuTTY, assicurarsi di **tipo di chiave per generare** è impostato su **RSA**e **numero di bit in una chiave generata** è impostato su **2048**. Quando si è pronti, fare clic su **genera**.

   ![Configurazione puTTY generatore di chiavi](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Per generare una chiave, spostare il cursore del mouse in modo casuale nella finestra del generatore di chiavi PuTTY.
4. Quando viene completata la generazione di chiave, fare clic su **Salva la chiave pubblica** e quindi fare clic su **Salva la chiave privata** per salvare i file delle chiavi.

   ![Generatore di chiavi di puTTY risultati](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Accedi al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack varia a seconda del prodotto cui Azure Stack si è connessi:

* Per Azure Stack Development Kit (ASDK) passare a: https://portal.local.azurestack.external.
* Per un sistema integrato dello Stack di Azure, andare all'URL fornito l'operatore di Stack di Azure.

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

1. Fare clic su **creare una risorsa** nell'angolo superiore sinistro del portale di Azure Stack.

2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Fare clic su **Crea**.

4. Digitare le informazioni della macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH**. Incollare la chiave pubblica SSH che salvato e quindi fare clic su **OK**.

   >[!NOTE]
 Assicurarsi di che rimuovere tutti gli spazi iniziali o finali per sono chiavi.

   ![Nozioni di base del pannello - configurare la macchina virtuale](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selezionare **D1_V2** per la macchina virtuale.

   ![Dimensione pannello, scegliere una dimensione di macchina virtuale](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Nel **impostazioni** pagina, mantenere le impostazioni predefinite e fare clic su **OK**.

7. Nel **riepilogo** pagina, fare clic su **OK** per avviare la distribuzione della macchina virtuale.

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

1. Fare clic su **Connetti** nella pagina della macchina virtuale. Verrà visualizzata una stringa di connessione SSH che è necessario connettersi alla macchina virtuale.

   ![Connettere la macchina virtuale](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Aprire PuTTY.
3. Nel **configurazione di PuTTY** schermata si utilizzerà il **categoria** scorrere verso l'alto o verso il basso la finestra. Scorrere verso il basso **SSH**, espandere **SSH**, quindi fare clic su **Auth**. Fare clic su **Sfoglia** e selezionare il file di chiave privato che è stato salvato.

   ![Selezionare la chiave privata PuTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Scorrere verso l'alto il **categoria** finestra e quindi fare clic su **sessione**.
5. Nel **nome Host (o indirizzo IP)** casella, incollare la stringa di connessione visualizzata nel portale di Azure Stack. In questo esempio, la stringa è ```asadmin@192.168.102.34```.

   ![Stringa di connessione configurazione puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Fare clic su **aprire** per aprire una sessione per la macchina virtuale.

   ![Sessione di Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Installare il server web NGINX

Utilizzare i seguenti comandi bash per aggiornare l'origine del pacchetto e installare il pacchetto NGINX più recente nella macchina virtuale.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Al termine dell'installazione NGINX, chiudere la sessione SSH e aprire la pagina di panoramica della macchina virtuale nel portale di Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Un gruppo di sicurezza di rete (NSG) consente il traffico in ingresso e in uscita. Quando viene creata una macchina virtuale nel portale di Azure Stack, viene creata una regola in ingresso sulla porta 22 per le connessioni SSH. Poiché questa macchina virtuale ospita un server web, una regola di sicurezza deve essere creata per consentire il traffico web sulla porta 80.

1. Nella macchina virtuale **Panoramica** pagina fare clic sul nome del **gruppo di risorse**.
2. Selezionare il **il gruppo di sicurezza di rete** per la macchina virtuale. Il gruppo di sicurezza di rete può essere identificato tramite la colonna **Tipo**.
3. Nel menu a sinistra, sotto **impostazioni**, fare clic su **sicurezza regole connessioni in entrata**.
4. Fare clic su **Aggiungi**.
5. In **Nome** digitare **http**. Assicurarsi che l'opzione **Intervallo di porte** sia impostata su 80 e l'opzione **Azione** sia impostata su **Consenti**.
6. Fare clic su **OK**.

## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Con NGINX installato e la porta 80 aperta nella macchina virtuale, è possibile accedere al server web utilizzando l'indirizzo IP pubblico della macchina virtuale. (L'indirizzo IP pubblico viene visualizzato nella pagina Panoramica della macchina virtuale).

Aprire un web browser e passare a ```http://<public IP address>```.

![Pagina Completamento dell'installazione di server web NGINX](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse che non è necessario più. Per eliminare la macchina virtuale e le relative risorse, selezionare il gruppo di risorse nella pagina della macchina virtuale e quindi fare clic su **eliminare**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è distribuita una macchina virtuale di server Linux base con un server web. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
