---
title: Installare l'ambiente di sviluppo/test IBM zD & T in Azure | Microsoft Docs
description: Distribuire l'ambiente di sviluppo e test IBM Z (zD & T) nell'infrastruttura distribuita come servizio (IaaS) di macchine virtuali (VM) di Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025947"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installare l'ambiente di sviluppo/test IBM zD & T in Azure

Per creare un ambiente di sviluppo/test per carichi di lavoro mainframe nei sistemi IBM Z, è possibile distribuire l'ambiente di sviluppo e test IBM Z (zD & T) nell'infrastruttura distribuita come servizio (IaaS) di macchine virtuali (VM) di Azure.

Con zD & T, è possibile sfruttare i costi della piattaforma x86 per gli ambienti di sviluppo e test meno critici, quindi eseguire il push degli aggiornamenti in un ambiente di produzione di sistema Z. Per ulteriori informazioni, vedere le [istruzioni di installazione IBM ZD & T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure e Azure Stack supportano le versioni seguenti:

- zD & T Personal Edition
- zD & T Parallel Sysplex
- zD & T Enterprise Edition

Tutte le edizioni di zD & T vengono eseguite solo su sistemi x86 Linux, non su Windows Server. Enterprise Edition è supportato in Red Hat Enterprise Linux (RHEL) o Ubuntu/Debian. Le immagini di VM RHEL e Debian sono disponibili per Azure.

Questo articolo illustra come configurare zD & T Enterprise Edition in Azure, in modo da poter usare il server Web zD & T Enterprise Edition per la creazione e la gestione degli ambienti. L'installazione di zD & T non installa alcun ambiente. È necessario crearli separatamente come pacchetti di installazione. Ad esempio, gli sviluppatori di applicazioni che controllano le distribuzioni (ADCD) sono immagini di volumi di ambienti di test. Sono contenute in immagini zip nella distribuzione dei supporti disponibile in IBM. Vedere come [configurare un ambiente ADCD in Azure](demo.md).

Per ulteriori informazioni, vedere la [Panoramica di zD & T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) in IBM Knowledge Center.

Questo articolo illustra come configurare l'ambiente di sviluppo e test Z (zD & T) Enterprise Edition in Azure. È quindi possibile usare il server Web zD & T Enterprise Edition per creare e gestire ambienti basati su Z in Azure.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> IBM consente l'installazione di zD & T Enterprise Edition solo negli ambienti di sviluppo/test,*non* negli ambienti di produzione.

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- È necessario accedere al supporto, disponibile solo per i clienti e i partner IBM. Per ulteriori informazioni, contattare il rappresentante IBM oppure consultare le informazioni di contatto nel sito Web [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) .

- Un [server licenze](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Questa operazione è necessaria per l'accesso agli ambienti. Il modo in cui viene creato dipende dalla modalità di licenza del software IBM:

     - Il **server licenze basato su hardware** richiede un dispositivo hardware USB che contiene i token razionali necessari per accedere a tutte le parti del software. È necessario ottenere questo da IBM.

     - Per il **server licenze basate su software** è necessario configurare un server centralizzato per la gestione delle chiavi di licenza. Questo metodo è preferibile ed è necessario impostare le chiavi ricevute da IBM nel server di gestione.

## <a name="create-the-base-image-and-connect"></a>Creare l'immagine di base e connettersi

1. In portale di Azure [creare una macchina virtuale](/azure/virtual-machines/linux/quick-create-portal) con la configurazione del sistema operativo desiderata. Questo articolo presuppone una macchina virtuale B4ms (con 4 vCPU e 16 GB di memoria) che esegue Ubuntu 16,04.

2. Dopo aver creato la macchina virtuale, aprire le porte in ingresso 22 per SSH, 21 per FTP e 9443 per il server Web.

3. Ottenere le credenziali SSH visualizzate nel pannello **Panoramica** della macchina virtuale tramite il pulsante **Connetti** . Selezionare la scheda **SSH** e copiare il comando di accesso SSH negli Appunti.

4. Accedere a una [shell bash](/azure/cloud-shell/quickstart) dal computer locale e incollare il comando. Il formato sarà **SSH @ no__t-1user ID @ no__t-2 @ no__t-3 @ no__t-4IP Address @ no__t-5**. Quando vengono richieste le credenziali, immetterle per stabilire una connessione alla Home Directory.

## <a name="copy-the-installation-file-to-the-server"></a>Copiare il file di installazione nel server

Il file di installazione per il server Web è **ZDT @ no__t-1Install @ no__t-2EE\_V12.0.0.1.tgz**. È incluso nel supporto fornito da IBM. È necessario caricare questo file nella macchina virtuale Ubuntu.

1. Dalla riga di comando immettere il comando seguente per assicurarsi che tutti gli elementi siano aggiornati nell'immagine appena creata:

    ```
    sudo apt-get update
    ```

2. Creare la directory in cui eseguire l'installazione:

    ```
    mkdir ZDT
    ```

3. Copiare il file dal computer locale alla macchina virtuale:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Questo comando copia il file di installazione nella directory ZDT della Home Directory, che varia a seconda che il client esegua Windows o Linux.

## <a name="install-the-enterprise-edition"></a>Installare Enterprise Edition

1. Passare alla directory ZDT e decomprimere il file ZDT @ no__t-0Install @ no__t-1EE\_V12.0.0.1.tgz usando i comandi seguenti:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Eseguire il programma di installazione:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selezionare **1** per installare Enterprise Server.

4. Premere **invio** e leggere attentamente i contratti di licenza. Alla fine della licenza, immettere Yes ( **Sì** ) per continuare.

5. Quando viene richiesto di modificare la password per l'utente appena creato, **ibmsys1**, usare il comando **sudo passwd ibmsys1** e immettere la nuova password.

6. Per verificare se l'installazione è riuscita, immettere

    ```
    dpkg -l | grep zdtapp
    ```

7. Verificare che l'output contenga la stringa **zdtapp 12.0.0.0**, che indica che il pacchetto gas è stato installato correttamente

### <a name="starting-enterprise-edition"></a>Avvio di Enterprise Edition

Tenere presente che quando il server Web viene avviato, viene eseguito con l'ID utente zD & T creato durante il processo di installazione.

1. Per avviare il server Web, usare l'ID utente radice per eseguire il comando seguente:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copiare l'output dell'URL dallo script, simile al seguente:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Incollare l'URL in un Web browser per aprire il componente di gestione per l'installazione di zD & T.

## <a name="next-steps"></a>Passaggi successivi

[Configurare una distribuzione controllata per gli sviluppatori di applicazioni (ADCD) in IBM zD & T V1](./demo.md)
