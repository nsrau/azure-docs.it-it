---
title: Installare IBM zD&ambiente di sviluppo/test T in Azure. Documenti Microsoft
description: Distribuire l'ambiente di sviluppo e test IBM (zD&T) nell'infrastruttura di Azure Virtual Machine (VM) come servizio (IaaS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025947"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installare IBM zD&ambiente di sviluppo/test T in AzureInstall IBM zD&T dev/test environment on Azure

Per creare un ambiente di sviluppo/test per i carichi di lavoro mainframe su IBM Systems, è possibile distribuire IBM, Development and Test Environment (zD&T) nell'infrastruttura di tipo Azure Virtual Machine (VM) (IaaS).

Con zD&T, è possibile sfruttare i risparmi sui costi della piattaforma x86 per gli ambienti di sviluppo e test meno critici e quindi eseguire il push degli aggiornamenti in un ambiente di produzione di sistema. Per ulteriori informazioni, vedere le istruzioni di [installazione di IBM .D&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure e Azure Stack supportano le versioni seguenti:Azure and Azure Stack support the following versions:

- zD&T Personal Edition
- zD&Sysplex parallelo T
- zD&T Enterprise Edition

Tutte le edizioni di zD&T vengono eseguite solo su sistemi Linux x86, non su Windows Server. Enterprise Edition è supportato su Red Hat Enterprise Linux (RHEL) o Ubuntu/Debian. Le immagini RHEL e Debian VM sono disponibili per Azure.Both RHEL and Debian VM images are available for Azure.

Questo articolo illustra come configurare zD&T Enterprise Edition in Azure in modo da poter usare il server Web zD&T Enterprise Edition per la creazione e la gestione degli ambienti. L'installazione di zD&T non installa alcun ambiente. È necessario crearli separatamente come pacchetti di installazione. Ad esempio, Application Developers Controlled Distributions (ADCD) sono immagini di volume di ambienti di test. Essi sono contenuti in immagini zip sulla distribuzione dei supporti disponibili da IBM. Informazioni su come [configurare un ambiente ADCD in Azure.](demo.md)

Per ulteriori informazioni, vedere la [panoramica di zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) in IBM Knowledge Center.

Questo articolo illustra come configurare l'edizione Enterprise di Sviluppo e Test (zD&T) in Azure.This article shows you how to set up - Development and Test Environment (zD&T) Enterprise Edition on Azure. È quindi possibile usare il server Web zD&T Enterprise Edition per creare e gestire ambienti basati su z in Azure.Then you can use the zD&T Enterprise Edition web server to create and manage s-based environments on Azure.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> IBM consente l'installazione di zD&T Enterprise Edition solo in ambienti di sviluppo/test,*non* in ambienti di produzione.

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- È necessario accedere ai supporti, disponibili solo per i clienti e i partner IBM. Per ulteriori informazioni, contattare il rappresentante IBM o visualizzare le informazioni di contatto sul sito Web [zD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- Un [server licenze](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Questa operazione è necessaria per l'accesso agli ambienti. Il modo in cui lo si crea dipende da come si concede in licenza il software da IBM:

     - **Il server licenze basato su hardware** richiede un dispositivo hardware USB contenente i token Rational necessari per accedere a tutte le parti del software. È necessario ottenere questo da IBM.

     - **Il server licenze basato su software** richiede la configurazione di un server centralizzato per la gestione delle chiavi di licenza. Questo metodo è preferibile e richiede di impostare le chiavi ricevute da IBM nel server di gestione.

## <a name="create-the-base-image-and-connect"></a>Creare l'immagine di base e

1. Nel portale di Azure [creare una macchina virtuale](/azure/virtual-machines/linux/quick-create-portal) con la configurazione del sistema operativo desiderata. In questo articolo si presuppone una macchina virtuale B4ms (con 4 vCPU e 16 GB di memoria) che esegue Ubuntu 16.04.

2. Dopo aver creato la macchina virtuale, aprire le porte in ingresso 22 per SSH, 21 per FTP e 9443 per il server Web.

3. Ottenere le credenziali SSH visualizzate nel pannello **Panoramica** della macchina virtuale tramite il pulsante **Connetti.Get** the SSH credentials shown on the Overview blade of the VM via the Connect button. Selezionare la scheda **SSH** e copiare il comando di accesso SSH negli Appunti.

4. Accedere a una [shell Bash](/azure/cloud-shell/quickstart) dal PC locale e incollare il comando. Sarà nel formato **ssh\<indirizzo\>\@\<IP\>id utente**. Quando vengono richieste le credenziali, immetterle per stabilire una connessione alla home directory.

## <a name="copy-the-installation-file-to-the-server"></a>Copiare il file di installazione nel server

Il file di installazione per il server web è **\_l'installazione\_di EE\_V12.0.0.1.tgz**. È incluso nel supporto fornito da IBM. È necessario caricare questo file nella macchina virtuale Ubuntu.You must upload this file to your Ubuntu VM.

1. Dalla riga di comando, immettere il comando seguente per assicurarsi che tutto sia aggiornato nell'immagine appena creata:

    ```
    sudo apt-get update
    ```

2. Creare la directory in cui eseguire l'installazione:

    ```
    mkdir ZDT
    ```

3. Copiare il file dal computer locale nella macchina virtuale:Copy the file from your local machine to the VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Questo comando copia il file di installazione nella directory DT della Home directory, che varia a seconda che il client esegua Windows o Linux.

## <a name="install-the-enterprise-edition"></a>Installare l'edizione Enterprise

1. Andare alla directory di\_\_installazione eil file EE\_V12.0.0.1.tgz utilizzando i seguenti comandi:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Eseguire il programma di installazione:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selezionare **1** per installare Enterprise Server.

4. Premere **INVIO** e leggere attentamente i contratti di licenza. Al termine della licenza, immettere **Sì** per procedere.

5. Quando viene richiesto di modificare la password per l'utente appena creato, **ibmsys1**, utilizzare il comando **sudo passwd ibmsys1** e immettere la nuova password.

6. Per verificare se l'installazione è stata eseguita correttamente,

    ```
    dpkg -l | grep zdtapp
    ```

7. Verificare che l'output contenga la stringa **zdtapp 12.0.0.0**, che indica che il gas del pacchetto è stato installato correttamente

### <a name="starting-enterprise-edition"></a>Avvio di Enterprise Edition

Tenere presente che all'avvio del server Web, viene eseguito con l'ID utente zD&T creato durante il processo di installazione.

1. Per avviare il server Web, utilizzare l'ID utente radice per eseguire il comando seguente:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copiare l'output dell'URL dallo script, che ha l'aspetto di:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Incollare l'URL in un browser Web per aprire il componente di gestione per l'installazione di zD&T.

## <a name="next-steps"></a>Passaggi successivi

[Impostare una distribuzione controllata da sviluppatori di applicazioni (ADCD) in IBM zD&T v1](./demo.md)
