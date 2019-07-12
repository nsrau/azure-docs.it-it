---
title: Set di backup di un'applicazione agli sviluppatori controllato distribuzione (ADCD) in IBM zD & T v1 | Microsoft Docs
description: Eseguire un ambiente di ambiente di Test (zD & T) e IBM Z Development in macchine virtuali (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: d527b08f3610531bef8e98a11998942411651d27
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621358"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Set di backup di un'applicazione agli sviluppatori controllato distribuzione (ADCD) in IBM zD & T v1

È possibile eseguire un ambiente di ambiente di Test (zD & T) e IBM Z Development in macchine virtuali (VM). Questo ambiente emula l'architettura di IBM Z Series. È possibile ospitare una varietà di sistemi operativi Z o le installazioni (noto anche come istanze Z o pacchetti), che vengono resi disponibili tramite le aggregazioni di personalizzata chiamate IBM applicazione sviluppatori controllato distribuzioni (ADCDs).

Questo articolo illustra come configurare un'istanza ADCD un zD & ambiente T in Azure. ADCDs creare implementazioni di sistema operativo Z serie completate per ambienti di sviluppo e test in esecuzione in zD & T.

Ad esempio zD & T, ADCDs sono disponibili solo per i partner e clienti di IBM e sono esclusivamente per scopi di sviluppo e test. Non sono può essere utilizzato per gli ambienti di produzione. Sono disponibili per il download tramite numerosi pacchetti di installazione di IBM [Passport vantaggio](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) oppure [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Il [zD & ambiente T][ibm-install-z] impostata in precedenza in Azure. Questo articolo presuppone che si usa la stessa immagine di macchina virtuale di Ubuntu 16.04 creata in precedenza.

- Accesso ai supporti ADCD tramite IBM PartnerWorld o i vantaggi di Passport.

- Oggetto [server di gestione licenze](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Questa operazione è necessaria per l'esecuzione IBM zD & T. Il modo in cui si crea dipende dal modo in cui concedere in licenza il software IBM:

  - **Server di gestione licenze basate su hardware** richiede un dispositivo hardware USB che contiene il token razionali necessarie per accedere a tutte le parti del software. Ciò è necessario ottenere da IBM.

  - **Server di gestione licenze basati su software** richiede di configurare un server centralizzato per la gestione delle chiavi di licenza. Questo metodo è preferibile e richiede di configurare le chiavi che si riceve da IBM nel server di gestione.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Scaricare i pacchetti di installazione dal vantaggio Passport

È necessario l'accesso ai supporti ADCD. I passaggi seguenti presuppongono un clienti di IBM e possono usare i vantaggi di Passport. Possono usare i partner di IBM [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> Questo articolo presuppone che un PC Windows viene usato per accedere al portale di Azure e per scaricare il supporto di IBM. Se si sta usando un desktop Mac e Ubuntu, i comandi e processo per ottenere il supporto di IBM possono differire leggermente.

1. Accedere al [Passport vantaggio](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selezionare **download del Software** e **accesso multimediale**.

3. Selezionare **numero offerta e contratto del programma**, fare clic su **continua**.

4. Immettere la descrizione di parte o il numero di parte, quindi scegliere **Finder**.

5. Facoltativamente, fare clic sull'elenco ordine alfabetico per visualizzare la fai clic su in base al nome.

6. Selezionare **tutti i sistemi operativi** nel **campo del sistema operativo**, e **tutte le lingue** nel **campo linguaggi**. Fare quindi clic **Vai**.

7. Fare clic su **selezionare i singoli file** per espandere l'elenco e visualizzare il supporto specifico per il download.

8. Verificare i pacchetti che si desidera scaricare, selezionare **scaricare**e quindi scaricare i file nella directory desiderata.

## <a name="upload-the-adcd-packages"></a>Caricare i pacchetti ADCD

Dopo aver creato i pacchetti, è necessario caricarli alla macchina virtuale in Azure.

1. Nel portale di Azure, avviare una **ssh** sessione con la VM di Ubuntu creata. Passare alla macchina virtuale, selezionare la **Overview** pannello e quindi selezionare **Connect**.

2. Selezionare il **SSH** scheda e quindi copiare il comando ssh negli Appunti.

3. Accedere alla VM usando le credenziali e il [client SSH](/azure/virtual-machines/linux/use-remote-desktop) preferito. In questa demo Usa le estensioni di Linux per Windows 10, che aggiunge una shell bash al prompt dei comandi di Windows. PuTTY funziona altrettanto bene.

4. Quando l'accesso, creare una directory in cui caricare i pacchetti di IBM. Tenere presente che Linux viene fatta distinzione tra maiuscole e minuscole. Ad esempio, in questa demo presuppone che i pacchetti vengono caricati in:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Caricare i file usando un client SSH, ad esempio[WinSCP](https://winscp.net/eng/index.php). Poiché SCP è una parte del protocollo SSH, Usa la porta 22, che viene usato SSH. Se nel computer locale non è Windows, è possibile digitare il [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) nella sessione SSH.

6. Avviare il caricamento nella directory di macchina virtuale di Azure è stato creato, che diventa l'archiviazione di immagini per zD & T.

    > [!NOTE]
    > Verificare che l'opzione **ADCDTOOLS. XML** è incluso il caricamento per il **home/MyUserID/ZDT/adcd/nov2017** directory. Sarà necessario più avanti.

7. Attendere che i file da caricare, che potrebbero richiedere alcuni minuti a seconda della connessione ad Azure.

8. Dopo aver completato il caricamento, passare alla directory di volumi e decomprimere tutti i **gz** volumi:

    ```
        gunzip \*.gz
    ```
    
![Visualizzato in Esplora file decompressi volumi gz](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurare l'archiviazione di immagini

Il passaggio successivo consiste nel configurare zD & T per usare i pacchetti caricati. Processo di archiviazione dell'immagine all'interno di zD & T consente di montare e usare le immagini. È possibile usare SSH o FTP.

1. Avviare il **zDTServer**. A tale scopo, è necessario essere a livello di radice. Immettere i due comandi seguenti nell'ordine:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Prendere nota dell'URL di output dal comando e usare questo URL per accedere al server web. È simile a:
     > https://(Your VM name or IP Address):9443/ZDTMC/index.HTML
     >
     > Tenere presente che l'accesso web Usa la porta 9443. Questa scheda consente di accedere al server web. È l'ID utente di a & ZD **zdtadmin** e la password sia **password**.

    ![IBM zD & T Enterprise Edition schermata iniziale](media/02-welcome.png)

3. Nel **avvio rapido** nella pagina **Configure**, selezionare **archiviazione immagini**.

     ![IBM zD & schermata avvio rapido di T Enterprise Edition](media/03-quickstart.png)

4. Nel **configurare l'archiviazione delle immagini** pagina, selezionare **SSH File Transfer Protocol**.

5. Per la **nome Host**, digitare **Localhost** e immettere il percorso della directory per cui è stato caricato le immagini. Ad esempio, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Immettere il **ID utente** e **password** per la macchina virtuale. Non utilizzare il ZD & ID utente T e la password.

7. Testare la connessione per assicurarsi di avere accesso e quindi selezionare **salvare** per salvare la configurazione.

## <a name="configure-the-target-environments"></a>Configurare gli ambienti di destinazione

Il passaggio successivo consiste nel configurare l'ambiente di destinazione di T & zD. In questo ambiente emulato ospitato è in cui eseguono le immagini.

1. Nel **avvio rapido** nella pagina **Configure**, selezionare **gli ambienti di destinazione**.

2. Nel **configurare gli ambienti di destinazione** pagina, selezionare **Add Target**.

3. Selezionare **Linux**. IBM supporta due tipi di ambienti, Linux e Cloud(OpenStack), ma questa demo viene eseguita in Linux.

4. Nel **Aggiungi ambiente di destinazione** pagina, per **il nome Host**, immettere **localhost**. Mantieni **porta SSH** impostata su **22**.

5. Nel **etichetta ambiente di destinazione** immettere un'etichetta, ad esempio **MyCICS.**

     ![Aggiungere una schermata di ambiente di destinazione](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurare ADCD e distribuire

Dopo aver completato i passaggi di configurazione precedente, è necessario configurare zD & T di utilizzare l'ambiente di pacchetti e di destinazione. È anche in questo caso, usare il processo di archiviazione di immagini in zD & T, che consente di montare e usare le immagini. È possibile usare SSH o FTP.

1. Nel **avvio rapido** nella pagina **Configure**, selezionare **ADCD**. Un set di istruzioni visualizzato, che richiede i passaggi che devono essere completate prima che un pacchetto ADCD può essere montato. Questo spiega perché è denominato directory di destinazione il modo in cui che è stato fatto in precedenza.

2. Supponendo che tutte le immagini sono state caricate nelle directory corrette, fare clic sui **immagine da ADCD** collegamento visualizzato in basso a destra (illustrato nel passaggio 7 nella schermata seguente).

     ![IBM zD & T Enterprise Edition - schermata Configura ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Creare l'immagine

Una volta completato, il passaggio di configurazione precedenti il **creare un'immagine con componenti ADCD** verrà visualizzata la pagina.

1. Selezionare il volume (Nov 2017 in questo caso) per visualizzare diversi pacchetti che sono in tale volume.

2. Per questa demo, selezionare **cliente informazioni Control System (CICS) - 5.3**.

3. Nel **nome dell'immagine** , digitare un nome per l'immagine, ad esempio **MyCICS immagine**.

4. Selezionare il **Crea immagine** pulsante nell'angolo inferiore destro.

     ![IBM zD & T Enterprise Edition - creare un'immagine utilizzando componenti ADCD schermata](media/06-adcd.png)

5. Nella finestra visualizzata, indicante che l'immagine è stata distribuita correttamente, scegliere **distribuire immagini**.

6. Nel **distribuire un'immagine in un ambiente di destinazione** pagina, selezionare l'immagine creata nella pagina precedente (**immagine MyCICS**) e l'ambiente di destinazione creato in precedenza (**MyCICS**).

7. Nella schermata successiva, fornire le credenziali per la macchina virtuale (vale a dire, non il ztadmin credential).

8. Nel riquadro proprietà, immettere il numero di **processori centrali (CPs)** , la quantità **memoria di sistema (GB)** e il **directory di distribuzione** per l'immagine in esecuzione. Poiché si tratta di una demo, mantenere piccola.

9. Assicurarsi che la casella è selezionata per **distribuire automaticamente il problema IPL comando per z/OS dopo**.

     ![Schermata delle proprietà](media/07-properties.png)

10. Selezionare **completo**.

11. Selezionare **distribuzione dell'immagine** dalle **distribuire un'immagine in un ambiente di destinazione** pagina.

L'immagine è ora possibile distribuire ed è pronto per essere montati dall'emulatore di terminale 3270.

> [!NOTE]
> Se si riceve un errore indicante che non è sufficiente spazio su disco, si noti che l'area 151 Gb.

Congratulazioni! Ora si esegue un ambiente mainframe IBM in Azure.

## <a name="learn-more"></a>Altre informazioni

- [Migrazione del mainframe: miti dai fatti](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistificazione di mainframe alla migrazione ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
