---
title: Installare IBM zD & ambiente di sviluppo/test T su Azure | Microsoft Docs
description: Distribuire IBM Z Development e l'ambiente di Test (zD & T) nell'infrastruttura di macchine virtuali (VM) di Azure come servizio (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894526"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installare IBM zD & ambiente di sviluppo/test T su Azure

Per creare un ambiente di sviluppo/test per i carichi di lavoro di mainframe in IBM Z Systems, è possibile distribuire IBM Z Development e l'ambiente di Test (zD & T) nell'infrastruttura di macchine virtuali (VM) di Azure come servizio (IaaS).

Con zD & T, è possibile sfruttare il risparmio sui costi di x86 piattaforma per il meno critici ambienti di sviluppo e test, quindi spingere gli aggiornamenti eseguire il sistema Z nell'ambiente di produzione. Per altre informazioni, vedere la [istruzioni di installazione di IBM ZD & T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure e Azure Stack supporta le versioni seguenti:

- zD & T Personal Edition
- zD & T Sysplex parallele
- zD & T Enterprise Edition

Tutte le edizioni di a & zD eseguono solo su x86 sistemi Linux, non a Windows Server. Enterprise Edition è supportato in Red Hat Enterprise Linux (RHEL) o in Ubuntu/Debian. Le immagini RHEL sia macchine Virtuali Debian sono disponibili per Azure.

Questo articolo illustra come configurare zD & T Enterprise Edition in Azure in modo che è possibile usare il zD & server web T Enterprise Edition per la creazione e gestione di ambienti. Installazione di T & zD non installa tutti gli ambienti. È necessario creare separatamente come pacchetti di installazione. Ad esempio, distribuzioni controllati gli sviluppatori dell'applicazione (ADCD) sono immagini di volume degli ambienti di test. Sono contenuti nelle immagini con estensione zip nella distribuzione multimediale disponibile da IBM. Vedere come [configurare un ambiente ADCD in Azure](demo.md).

Per altre informazioni, vedere la [zD & T Panoramica](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) al centro della Knowledge Base IBM.

Questo articolo illustra come configurare lo sviluppo di Z e ambiente di Test (zD & T) Enterprise Edition in Azure. È quindi possibile utilizzare il zD & server web T Enterprise Edition per creare e gestire gli ambienti basati su Z in Azure.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> IBM consente zD & T Enterprise Edition venga installato in ambienti di sviluppo/test solo —*non* gli ambienti di produzione.

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- È necessario accedere al supporto, che è disponibile solo per i partner e clienti di IBM. Per altre informazioni, contattare il rappresentante di IBM o vedere le informazioni di contatto nel [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) sito Web.

- Oggetto [server di gestione licenze](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ciò è necessario per l'accesso agli ambienti. Il modo in cui si crea dipende dal modo in cui concedere in licenza il software IBM:

     - **Server di gestione licenze basate su hardware** richiede un dispositivo hardware USB che contiene il token razionali necessarie per accedere a tutte le parti del software. Ciò è necessario ottenere da IBM.

     - **Server di gestione licenze basati su software** richiede di configurare un server centralizzato per la gestione delle chiavi di licenza. Questo metodo è preferibile e richiede di configurare le chiavi che si riceve da IBM nel server di gestione.

## <a name="create-the-base-image-and-connect"></a>Creare l'immagine di base e connettersi

1. Nel portale di Azure [creare una macchina virtuale](/azure/virtual-machines/linux/quick-create-portal) con la configurazione del sistema operativo desiderato. Questo articolo presuppone che una macchina virtuale B4ms (con 4 Vcpu e 16 GB di memoria) che esegue Ubuntu 16.04.

2. Dopo aver creata la macchina virtuale, aprire le porte in ingresso 22 per SSH, 21 per FTP e 9443 per il server web.

3. Ottenere le credenziali SSH visualizzate sulla **Overview** pannello della macchina virtuale tramite il **Connect** pulsante. Selezionare il **SSH** e copiare il comando di accesso SSH negli Appunti.

4. Accedere a un [shell di Bash](/azure/cloud-shell/quickstart) dal computer locale e incollare il comando. Sarà nel formato **ssh\<id utente\>\@\<indirizzo IP\>**. Quando richiesto per le proprie credenziali, immettere in modo da stabilire una connessione alla directory Home.

## <a name="copy-the-installation-file-to-the-server"></a>Copiare il file di installazione nel server

Il file di installazione per il server web viene **ZDT\_installare\_EE\_V12.0.0.1.tgz**. È incluso nel supporto fornito da IBM. È necessario caricare questo file per le VM Ubuntu.

1. Dalla riga di comando, immettere il comando seguente per assicurarsi che tutto sia aggiornato dell'immagine appena creato:

    ```
    sudo apt-get update
    ```

2. Creare la directory di installazione per:

    ```
    mkdir ZDT
    ```

3. Copiare il file dal computer locale alla macchina virtuale:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Questo comando Copia il file di installazione nella directory ZDT nella directory Home, che varia a seconda del fatto che il client viene eseguito Windows o Linux.

## <a name="install-the-enterprise-edition"></a>Installa la Enterprise Edition

1. Passare alla directory ZDT e decomprimere il ZDT\_installare\_EE\_V12.0.0.1.tgz file usando i comandi seguenti:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Eseguire il programma di installazione:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selezionare **1** installare Enterprise Server.

4. Premere **invio** e leggere con attenzione i contratti di licenza. Alla fine della licenza, immettere **Sì** per procedere.

5. Quando viene richiesto di cambiare la password per l'utente appena creato **ibmsys1**, usare il comando **sudo passwd ibmsys1** e immettere la nuova password.

6. Per verificare se l'installazione sia riuscita immettere

    ```
    dpkg -l | grep zdtapp
    ```

7. Verificare che l'output contiene la stringa **zdtapp 12.0.0.0**, a indicare che la "benzina" del pacchetto è stato installato correttamente

### <a name="starting-enterprise-edition"></a>Avvio di Enterprise Edition

Tenere presente che quando viene avviato il server web, viene eseguito con l'ID utente di a & zD creato durante il processo di installazione.

1. Per avviare il server web, usare l'ID utente root per eseguire il comando seguente:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copiare l'URL di output dallo script, che è simile a:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Incollare l'URL in un web browser per aprire il componente di gestione per le zD & installazione T.

## <a name="next-steps"></a>Passaggi successivi

[Set di backup di un'applicazione agli sviluppatori controllato distribuzione (ADCD) in IBM zD & T v1](./demo.md)
