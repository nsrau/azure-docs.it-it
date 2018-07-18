---
title: Azure Stack Guida introduttiva - creare un portale di VM
description: Azure Stack Quick Start - creare una VM Linux usando il portale
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
ms.openlocfilehash: c364a9ba5a9606967fa86262417ccd1ac3aeffc3
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866271"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Guida introduttiva: creare una macchina virtuale di server con il portale di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile creare una macchina virtuale Ubuntu Server 16.04 LTS con il portale di Azure Stack. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale. Questo articolo fornisce anche i passaggi per:

* Connettersi alla macchina virtuale con un client remoto.
* Installare un server web NGINX.
* Pulire le risorse.

## <a name="prerequisites"></a>Prerequisiti

* **Un'immagine Linux in Azure Stack marketplace**

   Per impostazione predefinita, il marketplace di Azure Stack non contiene un'immagine Linux. Prima di creare una macchina virtuale di server, assicurarsi che l'operatore di Azure Stack offre il **Ubuntu Server 16.04 LTS** immagine necessaria. L'operatore può usare la procedura descritta nel [Download di elementi di marketplace di Azure ad Azure Stack](../azure-stack-download-azure-marketplace-item.md) articolo.

* **Accesso a un client SSH**

   Se si usa Azure Stack Development Kit (ASDK), non si dispone dell'accesso a un client SSH. Se è necessario un client, esistono diversi pacchetti che includono un client SSH. Ad esempio, PuTTY include un client SSH e il generatore di chiavi SSH (puttygen.exe). Per altre informazioni sui pacchetti disponibili, leggere l'articolo di Azure seguente: [come usare le chiavi SSH con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Questa Guida introduttiva Usa PuTTY per generare le chiavi SSH e connettersi alla macchina virtuale Linux server. Per scaricare e installare PuTTY, passare a [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

È necessario una coppia di chiavi SSH per completare tutti i passaggi in questo articolo. Se si dispone di una coppia di chiavi SSH esistente, è possibile ignorare questo passaggio.

1. Passare alla cartella di installazione di PuTTY (il percorso predefinito è ```C:\Program Files\PuTTY```) ed eseguire ```puttygen.exe```.
2. Nella finestra di PuTTY Key Generator, verificare che il **tipo di chiave per generare** è impostata su **RSA**e il **numero di bit in una chiave generata** è impostata su **2048**. Quando si è pronti, fare clic su **genera**.

   ![Configurazione di puTTY Key Generator](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Per generare una chiave, spostare il cursore del mouse in modo casuale nella finestra di PuTTY Key Generator.
4. Al termine di generazione della chiave, fare clic su **Salva chiave pubblica** e quindi fare clic su **Save private key** per salvare le chiavi per i file.

   ![Risultati di puTTY Key Generator](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Accedere al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack dipende dal quale prodotto di Azure Stack ci si connette a:

* Per Azure Stack Development Kit (ASDK) passare a: https://portal.local.azurestack.external.
* Per un sistema integrato Azure Stack, passare all'URL che ha fornito l'operatore di Azure Stack.

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

1. Fare clic su **crea una risorsa** nell'angolo superiore sinistro del portale di Azure Stack.

2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Fare clic su **Crea**.

4. Digitare le informazioni della macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH**. Incollare la chiave pubblica SSH che è stato salvato e quindi fare clic su **OK**.

   >[!NOTE]
 Assicurarsi di che rimuovere tutti gli spazi iniziali o finali per essi della chiave.

   ![Nozioni di base del pannello - configurare la macchina virtuale](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selezionare **D1_V2** per la macchina virtuale.

   ![Pannello dimensioni - scegliere una dimensione di macchina virtuale](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Nel **le impostazioni** pagina, mantenere le impostazioni predefinite e fare clic su **OK**.

7. Nel **Summary** pagina, fare clic su **OK** per avviare la distribuzione della macchina virtuale.

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

1. Fare clic su **Connect** nella pagina macchina virtuale. Ciò consente di visualizzare una stringa di connessione SSH che è necessario connettersi alla macchina virtuale.

   ![Connettere la macchina virtuale](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Aprire PuTTY.
3. Nel **configurazione di PuTTY** schermata si userà il **categoria** scorrere verso l'alto o verso il basso la finestra. Scorrere verso il basso **SSH**, espandere **SSH**, quindi fare clic su **Auth**. Fare clic su **esplorare** e selezionare il file di chiave privata che è stato salvato.

   ![Selezionare la chiave privata PuTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Scorrere verso l'alto il **categoria** e quindi fare clic **sessione**.
5. Nel **(nome Host o indirizzo IP)** incollare la stringa di connessione visualizzata nel portale di Azure Stack. In questo esempio, la stringa è ```asadmin@192.168.102.34```.

   ![Stringa di connessione di configurazione di puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Fare clic su **aprire** per aprire una sessione per la macchina virtuale.

   ![Sessione di Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Installare il server web NGINX

Usare i comandi bash seguenti per aggiornare le origini dei pacchetti e installare il pacchetto NGINX più recente nella macchina virtuale.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Al termine dell'installazione di NGINX, chiudere la sessione SSH e aprire la pagina di panoramica della macchina virtuale nel portale di Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Un gruppo di sicurezza di rete (NSG) consente il traffico in ingresso e in uscita. Quando viene creata una macchina virtuale nel portale di Azure Stack, viene creata una regola in ingresso sulla porta 22 per connessioni SSH. Perché questa macchina virtuale ospita un server web, una regola di sicurezza di rete deve essere creato per consentire il traffico web sulla porta 80.

1. Nella macchina virtuale **Overview** pagina, fare clic sul nome delle **gruppo di risorse**.
2. Selezionare il **gruppo di sicurezza di rete** per la macchina virtuale. Il gruppo di sicurezza di rete può essere identificato tramite la colonna **Tipo**.
3. Nel menu a sinistra, sotto **le impostazioni**, fare clic su **regole di sicurezza in ingresso**.
4. Fare clic su **Aggiungi**.
5. In **Nome** digitare **http**. Assicurarsi che l'opzione **Intervallo di porte** sia impostata su 80 e l'opzione **Azione** sia impostata su **Consenti**.
6. Fare clic su **OK**.

## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Con NGINX installato e la porta 80 aperta sulla macchina virtuale, è possibile accedere al server web tramite l'indirizzo IP pubblico della macchina virtuale. (L'indirizzo IP pubblico viene visualizzato nella pagina di panoramica della macchina virtuale).

Aprire un web browser e passare a ```http://<public IP address>```.

![Pagina Completamento dell'installazione di server web NGINX](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Pulire le risorse

Pulire le risorse che non sono più necessari. Per eliminare la macchina virtuale e le relative risorse, selezionare il gruppo di risorse nella pagina macchina virtuale e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata distribuita una macchina virtuale di server Linux base con un server web. Per altre informazioni sulle macchine virtuali di Azure Stack, continuare [considerazioni sulle macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
