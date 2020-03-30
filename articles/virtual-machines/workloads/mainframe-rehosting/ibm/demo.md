---
title: Impostare una distribuzione controllata da sviluppatori di applicazioni (ADCD) in IBM zD&T v1 Documenti Microsoft
description: Eseguire un ambiente di sviluppo e test IBM (zD&T) in macchine virtuali (VM) di Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841379"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Impostare una distribuzione controllata da sviluppatori di applicazioni (ADCD) in IBM zD&T v1

È possibile eseguire un ambiente IBM per lo sviluppo e l'ambiente di test (zD&T) in Macchine virtuali di Azure.You can run an IBM - Development and Test Environment (zD&T) environment in Azure Virtual Machines (VMs). Questo ambiente emula l'architettura della serie IBM. Può ospitare una varietà di sistemi operativi o installazioni della serie di serie (chiamati anche istanze o pacchetti) (chiamati anche istanze o pacchetti), che vengono resi disponibili attraverso pacchetti personalizzati chiamati IBM Application Developers Controlled Distributions (ADAD).

Questo articolo illustra come configurare un'istanza ADCD in un ambiente zD&T in Azure.This article shows you how to set up an ADCD instance in an zD&T environment on Azure. Gli ADCD creano implementazioni complete del sistema operativo della serie di serie per gli ambienti di sviluppo e test eseguiti in zD&T.

Come zD&T, gli ADCD sono disponibili solo per i clienti e i partner IBM e sono esclusivamente a scopo di sviluppo e test. Non devono essere utilizzati per gli ambienti di produzione. Numerosi pacchetti di installazione IBM sono disponibili per il download tramite [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) o [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- [L'ambiente zD&T][ibm-install-z] precedentemente configurato in Azure.The zD&T environment previously set up on Azure. In questo articolo si presuppone che si sta utilizzando la stessa immagine di macchina virtuale Ubuntu 16.04 creata in precedenza.

- Accesso ai supporti ADCD tramite IBM PartnerWorld o Passport Advantage.

- Un [server licenze](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Questa operazione è necessaria per eseguire IBM zD&T. Il modo in cui lo si crea dipende da come si concede in licenza il software da IBM:

  - **Il server licenze basato su hardware** richiede un dispositivo hardware USB contenente i token Rational necessari per accedere a tutte le parti del software. È necessario ottenere questo da IBM.

  - **Il server licenze basato su software** richiede la configurazione di un server centralizzato per la gestione delle chiavi di licenza. Questo metodo è preferibile e richiede di impostare le chiavi ricevute da IBM nel server di gestione.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Scaricare i pacchetti di installazione da Passport Advantage

È necessario l'accesso al supporto ADCD. I passaggi seguenti presuppongono che l'utente sia un cliente IBM e che sia possibile utilizzare Passport Advantage. I partner IBM possono utilizzare [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> In questo articolo si presuppone che un PC Windows viene utilizzato per accedere al portale di Azure e per scaricare il supporto IBM. Se si utilizza un desktop Mac o Ubuntu, i comandi e il processo per ottenere il supporto IBM potrebbero differire leggermente.

1. Accedere a [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selezionare **Download software** e **Accesso ai file multimediali**.

3. Selezionare **Offerta programma e numero di contratto**e fare clic su **Continua**.

4. Immettere la descrizione della parte o il numero di parte e fare clic su **Finder**.

5. Facoltativamente, fare clic sull'elenco degli ordini alfabetici per visualizzare e visualizzare il prodotto in base al nome.

6. Selezionare **Tutti i sistemi operativi** nel campo Sistema **operativo**e Tutte **le lingue** nel campo **Lingue**. Quindi, fare clic su **Vai**.

7. Fare clic su **Seleziona singoli file** per espandere l'elenco e visualizzare i singoli supporti da scaricare.

8. Verificare i pacchetti che si desidera scaricare, selezionare **Scarica**e quindi scaricare i file nella directory desiderata.

## <a name="upload-the-adcd-packages"></a>Caricare i pacchetti ADCD

Ora che si dispone dei pacchetti, è necessario caricarli nella macchina virtuale in Azure.Now that you have the package(s), you must upload them to your VM on Azure.

1. Nel portale di Azure avviare una sessione **ssh** con la macchina virtuale Ubuntu creata. Passare alla macchina virtuale, selezionare il pannello **Panoramica** e quindi selezionare **Connetti**.

2. Selezionare la scheda **SSH,** quindi copiare il comando ssh negli Appunti.

3. Accedere alla macchina virtuale utilizzando le credenziali e il [client SSH](/azure/virtual-machines/linux/use-remote-desktop) desiderato. Questa demo usa le estensioni Linux per Windows 10, che aggiunge una shell bash al prompt dei comandi di Windows.This demo uses the Linux extensions for Windows 10, which adds a bash shell to the Windows command prompt. PuTTY funziona altrettanto bene.

4. Una volta effettuato l'accesso, creare una directory per caricare i pacchetti IBM. Tieni presente che Linux fa distinzione tra maiuscole e minuscole. Ad esempio, questa demo presuppone che i pacchetti vengano caricati in:For example, this demo assumes the packages are uploaded to:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Caricare i file utilizzando un client SSH come[WinSCP](https://winscp.net/eng/index.php). Poiché SCP fa parte di SSH, utilizza la porta 22, che è ciò che utilizza SSH. Se il computer locale non è Windows, è possibile digitare il [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) nella sessione SSH.

6. Avviare il caricamento nella directory della macchina virtuale di Azure creata, che diventa l'archiviazione dell'immagine per zD&T.Initiate the upload to the Azure VM directory you created, which becomes the image storage for zD&T.

    > [!NOTE]
    > Assicurarsi che **ADCDTOOLS. XML** è incluso nel caricamento nella directory **home/MyUserID/'DT/adcd/nov2017'** Sarà necessario più avanti.

7. Attendere il caricamento dei file, che potrebbe richiedere del tempo a seconda della connessione ad Azure.Wait for the files to upload, which may take some time depending on your connection to Azure.

8. Una volta completati i caricamenti, passare alla directory dei volumi e decomprimere tutti i volumi **gz:**

    ```
        gunzip \*.gz
    ```
    
![Esplora file che mostra i volumi gz decompressi](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurare l'archiviazione delle immagini

Il passaggio successivo consiste nel configurare zD&T per utilizzare i pacchetti caricati. Il processo di archiviazione delle immagini all'interno di zD&T consente di montare e utilizzare le immagini. Può utilizzare SSH o FTP.

1. Avviare **il file zDTServer**. Per fare questo, è necessario essere al livello di radice. Immettere i due comandi seguenti nell'ordine indicato:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Prendere nota dell'output dell'URL da parte del comando e utilizzare questo URL per accedere al server Web. Sembra simile a:
     > https://(il nome della macchina virtuale o l'indirizzo IP):9443/
     >
     > Tenere presente che l'accesso Web utilizza la porta 9443. Consente di accedere al server Web. L'ID utente per l'&T è **zdtadmin** e la password è **password**.

    ![Schermata iniziale di IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Nella pagina **Guida introduttiva,** in **Configura,** selezionare **Archiviazione immagini.**

     ![Schermata introduttiva di IBM zD&T Enterprise Edition](media/03-quickstart.png)

4. Nella pagina **Configura archiviazione immagini** selezionare Protocollo di trasferimento file **SSH**.

5. In **Nome host**digitare **Localhost** e immettere il percorso della directory in cui sono state caricate le immagini. Ad esempio, /home/IDUtente/ SDT/adcd/nov2017/volumes.

6. Immettere **l'ID utente** e la **password** per la macchina virtuale. Non utilizzare l'ID utente e la password di&T.

7. Testare la connessione per assicurarsi di avere accesso e quindi selezionare **Salva** per salvare la configurazione.

## <a name="configure-the-target-environments"></a>Configurare gli ambienti di destinazione

Il passaggio successivo consiste nel configurare l'ambiente di destinazione zD&T. Questo ambiente ospitato emulato è il punto in cui vengono eseguite le immagini.

1. Nella pagina **Guida introduttiva,** in **Configura,** selezionare **Ambienti di destinazione.**

2. Nella pagina **Configura ambienti di destinazione** selezionare Aggiungi **destinazione**.

3. Selezionare **Linux**. IBM supporta due tipi di ambienti, Linux e Cloud (OpenStack), ma questa demo viene eseguita su Linux.

4. Nella pagina **Aggiungi ambiente di destinazione,** in **Nome host,** immettere **localhost**. Mantenere la **porta SSH** impostata su **22**.

5. Nella casella **Etichetta ambiente** di destinazione immettere un'etichetta, ad esempio **MyCICS.**

     ![Schermata Aggiungi ambiente di destinazione](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurare ADCD e distribuire

Dopo aver completato i passaggi di configurazione precedenti, è necessario configurare zD&T per utilizzare i pacchetti e l'ambiente di destinazione. Anche in questo caso, si utilizza il processo di archiviazione delle immagini in zD&T, che consente di montare e utilizzare le immagini. Può utilizzare SSH o FTP.

1. Nella pagina **Guida introduttiva,** in **Configura,** selezionare **ADCD**. Viene visualizzata una serie di istruzioni che indicano i passaggi che devono essere completati prima di poter montare un pacchetto ADCD. Questo spiega perché abbiamo chiamato la directory di destinazione come abbiamo fatto in precedenza.

2. Supponendo che tutte le immagini siano state caricate nelle directory corrette, fare clic sul collegamento **IMMAGINE da ADCD** visualizzato in basso a destra (mostrato nel passaggio 7 nella schermata seguente).

     ![IBM zD&T Enterprise Edition - Configurare lo schermo ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Creare l'immagine

Al termine del passaggio di configurazione precedente, viene visualizzata la pagina **Crea un'immagine utilizzando componenti ADCD.**

1. Selezionare il volume (in questo caso novembre 2017) per visualizzare i diversi pacchetti presenti in tale volume.

2. Per questa demo, selezionare **Customer Information Control System (CICS) - 5.3**.

3. Nella casella **Nome immagine** digitare un nome per l'immagine, ad esempio **Immagine MyCICS**.

4. Selezionare il pulsante **Crea immagine** in basso a destra.

     ![IBM zD&T Enterprise Edition - Creare un'immagine utilizzando la schermata Componenti ADCD](media/06-adcd.png)

5. Nella finestra visualizzata indica che l'immagine è stata distribuita correttamente, scegliere **Distribuisci immagini**.

6. Nella pagina **Distribuire un'immagine in un ambiente** di destinazione selezionare l'immagine creata nella pagina precedente ( Immagine**MyCICS**) e l'ambiente di destinazione creato in precedenza (**MyCICS**).

7. Nella schermata successiva specificare le credenziali per la macchina virtuale, ovvero non la credenziale ztadmin.

8. Nel riquadro Proprietà immettere il numero di **processori centrali (CP),** la quantità di memoria di **sistema (GB)** e la directory di **distribuzione** per l'immagine in esecuzione. Dal momento che questa è una demo, mantienila piccola.

9. Assicurarsi che la casella sia selezionata per il **comando Emetti automaticamente IPL su z/OS dopo la distribuzione**.

     ![Schermata Proprietà](media/07-properties.png)

10. Selezionare **Completa**.

11. Selezionare **Distribuisci immagine** dalla pagina **Distribuire un'immagine in un ambiente di destinazione.**

L'immagine può ora essere distribuita ed è pronta per essere montata da un emulatore di terminale 3270.

> [!NOTE]
> Se viene visualizzato un errore che indica che lo spazio su disco non è sufficiente, tenere presente che l'area richiede 151 Gb.

Congratulazioni! Si sta eseguendo un ambiente mainframe IBM in Azure.

## <a name="learn-more"></a>Altre informazioni

- [Migrazione del mainframe: miti e fatti](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale su Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistificazione della migrazione da mainframe ad AzureDemistifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
