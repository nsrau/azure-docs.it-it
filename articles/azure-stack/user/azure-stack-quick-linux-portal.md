---
title: Azure dello Stack di avvio rapido - creazione macchina virtuale portale
description: Azure Stack Quick Start - creare una VM Linux tramite il portale
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 12/11/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1e1732f48de9f95e669d0282d120e48b5fe5f0ef
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Creare una macchina virtuale Linux con il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Macchine virtuali di Azure Stack può essere create tramite il portale di Azure Stack. Questo metodo fornisce un'interfaccia utente basata su browser per creare e configurare una macchina virtuale e tutte risorse correlate. Questa Guida introduttiva viene illustrato come creare una macchina virtuale Linux rapidamente e installarvi un server web.

## <a name="prerequisites"></a>Prerequisiti

* **Un'immagine Linux nel marketplace Azure Stack**

   Per impostazione predefinita, il marketplace Azure Stack non contiene un'immagine Linux. In tal caso, prima di creare una macchina virtuale Linux, verificare che l'operatore di Azure Stack ha scaricato la **Ubuntu Server 16.04 LTS** immagine usando la procedura descritta nel [scaricare elementi del marketplace da Azure in Azure Stack](../azure-stack-download-azure-marketplace-item.md) argomento.

* **Accesso a un client SSH**

   Se si utilizza il Kit di sviluppo dello Stack di Azure (ASDK), si potrebbe non avere accesso a un client SSH nel proprio ambiente. In questo caso, è possibile scegliere tra diversi pacchetti che includono un client SSH. Ad esempio, è possibile installare PuTTY che include un client SSH e il generatore di chiavi SSH (puttygen.exe). Per ulteriori informazioni sulle opzioni disponibili, vedere le operazioni seguenti correlati Azure articolo: [come chiavi SSH uso con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Questa Guida rapida Usa PuTTY per generare le chiavi SSH e connettersi alla macchina virtuale Linux. Per scaricare e installare PuTTY, passare a [http://www.putty.org/](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

È necessario una coppia di chiavi SSH per il completamento di questa Guida rapida. Se è già disponibile una coppia di chiavi SSH, questo passaggio può essere ignorato.

1. Passare alla cartella di installazione PuTTY (il percorso predefinito è ```C:\Program Files\PuTTY```) ed eseguire ```puttygen.exe```.
2. Nella finestra Generatore di chiavi PuTTY, assicurarsi di **tipo di chiave per generare** è impostato su **RSA**e **numero di bit in una chiave generata** è impostato su **2048**. Quando si è pronti, fare clic su **genera**.

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Per completare il processo di generazione delle chiavi, spostare il cursore del mouse all'interno della finestra Generatore di chiavi PuTTY.
4. Al termine di generazione delle chiavi, fare clic su **Salva la chiave pubblica** e **Salva la chiave privata** per salvare le chiavi pubbliche e private ai file.

   ![Chiavi puTTY](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Accedi al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack varia a seconda del prodotto cui Azure Stack si è connessi:

* Per Azure Stack Development Kit (ASDK) passare a: https://portal.local.azurestack.external.
* Per un sistema integrato dello Stack di Azure, andare all'URL fornito l'operatore di Stack di Azure.

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

1. Fare clic su **creare una risorsa** nell'angolo superiore sinistro del portale di Azure Stack.

2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Fare clic su **Crea**.

4. Digitare le informazioni della macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH**. Quando si incolla nella chiave pubblica SSH (che è stato salvato in un file in precedenza), prestare attenzione a rimuovere tutti gli spazi iniziali o finali. Al termine fare clic su **OK**.

   ![Nozioni fondamentali sulla macchina virtuale](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Selezionare **D1_V2** per la macchina virtuale.

   ![Dimensioni della macchina](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Nel **impostazioni** pagina, mantenere le impostazioni predefinite e fare clic su **OK**.

7. Nel **riepilogo** pagina, fare clic su **OK** per avviare la distribuzione della macchina virtuale.


## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

1. Fare clic su **Connetti** nella pagina della macchina virtuale. Verrà visualizzata una stringa di connessione SSH che può essere usata per connettersi alla macchina virtuale.

   ![Connettere la macchina virtuale](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Aprire PuTTY.
3. Nel **configurazione PuTTY** schermata **categoria**, espandere **SSH** e quindi fare clic su **Auth**. Fare clic su **Sfoglia** e selezionare il file di chiave privato che è stato salvato in precedenza.

   ![Chiave privata puTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. In **categoria**, scorrere verso l'alto e fare clic su **sessione**.
5. Nel **(nome Host o indirizzo IP)** incollare la stringa di connessione dal portale di Azure Stack che è stato illustrato in precedenza. In questo esempio, la stringa è ```asadmin@192.168.102.34```.
 
   ![Sessione puTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. Fare clic su **aprire** aprire una sessione per la macchina virtuale.

   ![Sessione Linus](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>Installare NGINX

Utilizzare il seguente script bash per aggiornare l'origine del pacchetto e installare il pacchetto NGINX più recente nella macchina virtuale. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Al termine, chiudere la sessione SSH e restituire la pagina di panoramica di macchina virtuale nel portale di Azure Stack.


## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Un gruppo di sicurezza di rete (NSG) consente il traffico in ingresso e in uscita. Quando viene creata una macchina virtuale dal portale di Azure Stack, viene creata una regola in ingresso sulla porta 22 per le connessioni SSH. Poiché questa macchina virtuale ospita un server web, una regola di gruppo deve essere creato per la porta 80.

1. Nella macchina virtuale **Panoramica** pagina fare clic sul nome del **gruppo di risorse**.
2. Selezionare il **il gruppo di sicurezza di rete** per la macchina virtuale. Il gruppo di sicurezza di rete può essere identificato tramite la colonna **Tipo**. 
3. Nel menu a sinistra, sotto **impostazioni**, fare clic su **sicurezza regole connessioni in entrata**.
4. Fare clic su **Aggiungi**.
5. In **Nome** digitare **http**. Assicurarsi che l'opzione **Intervallo di porte** sia impostata su 80 e l'opzione **Azione** sia impostata su **Consenti**. 
6. Fare clic su **OK**.


## <a name="view-the-nginx-welcome-page"></a>Visualizzare la pagina iniziale di NGINX

Con NGINX installato e la porta 80 aperta nella macchina virtuale, il server web ora è accessibile all'indirizzo IP pubblico della macchina virtuale. L'indirizzo IP pubblico è reperibile nella pagina Panoramica della macchina virtuale nel portale di Azure Stack.

Aprire un web browser e passare a ```http://<public IP address>```.

![Sito NGINX predefinito](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse da una pagina della macchina virtuale e fare clic su **eliminare**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva, è stato distribuito una semplice macchina virtuale Linux, una regola gruppo di sicurezza di rete e installato un server web. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).

