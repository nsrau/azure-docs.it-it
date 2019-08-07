---
title: Configurare una distribuzione controllata per gli sviluppatori di applicazioni (ADCD) in IBM zD & T V1 | Microsoft Docs
description: Eseguire un ambiente di sviluppo e test IBM Z (zD & T) in macchine virtuali (VM) di Azure.
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
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841379"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Configurare una distribuzione controllata per gli sviluppatori di applicazioni (ADCD) in IBM zD & T V1

È possibile eseguire un ambiente di sviluppo e test IBM Z (zD & T) in macchine virtuali (VM) di Azure. Questo ambiente emula l'architettura della serie IBM Z. Può ospitare un'ampia gamma di sistemi operativi o installazioni della serie Z, denominati anche pacchetti o istanze Z, che vengono resi disponibili tramite bundle personalizzati, denominati distribuzioni controllate di IBM Application Developers (ADCDs).

Questo articolo illustra come configurare un'istanza di ADCD in un ambiente zD & T in Azure. ADCDs creare implementazioni del sistema operativo serie Z complete per ambienti di sviluppo e test eseguiti in zD & T.

Come zD & T, ADCDs sono disponibili solo per i clienti e i partner IBM e sono esclusivamente a scopo di sviluppo e test. Non devono essere usati per gli ambienti di produzione. Molti pacchetti di installazione IBM sono disponibili per il download tramite [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) o [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- L' [ambiente zD & T][ibm-install-z] configurato in precedenza in Azure. Questo articolo presuppone che si stia usando la stessa immagine di macchina virtuale Ubuntu 16,04 creata in precedenza.

- Accesso al supporto ADCD tramite IBM PartnerWorld o Passport Advantage.

- Un [server licenze](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Questa operazione è necessaria per eseguire IBM zD & T. Il modo in cui viene creato dipende dalla modalità di licenza del software IBM:

  - Il **server licenze basato su hardware** richiede un dispositivo hardware USB che contiene i token razionali necessari per accedere a tutte le parti del software. È necessario ottenere questo da IBM.

  - Per il **server licenze basate su software** è necessario configurare un server centralizzato per la gestione delle chiavi di licenza. Questo metodo è preferibile ed è necessario impostare le chiavi ricevute da IBM nel server di gestione.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Scaricare i pacchetti di installazione da Passport Advantage

L'accesso al supporto ADCD è obbligatorio. I passaggi seguenti presuppongono che l'utente sia un cliente IBM e possa usare Passport Advantage. I partner IBM possono usare [IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> Questo articolo presuppone che venga usato un PC Windows per accedere a portale di Azure e scaricare il supporto IBM. Se si usa un desktop Mac o Ubuntu, i comandi e il processo per ottenere il supporto IBM potrebbero differire leggermente.

1. Accedere a [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selezionare **Download software** e **accesso ai supporti**.

3. Selezionare l' **offerta di programma e il numero di contratto**, quindi fare clic su **continua**.

4. Immettere la descrizione della parte o il numero di parte, quindi fare clic su **Finder**.

5. Facoltativamente, fare clic sull'elenco ordine alfabetico per visualizzare e visualizzare prodotto in base al nome.

6. Selezionare **tutti i sistemi operativi** nel **campo sistema operativo**e **tutte le lingue** nel **campo lingue**. Quindi, fare clic su **Vai**.

7. Fare clic su **Seleziona singoli file** per espandere l'elenco e visualizzare i singoli supporti da scaricare.

8. Verificare i pacchetti che si desidera scaricare, selezionare **Scarica**e quindi scaricare i file nella directory desiderata.

## <a name="upload-the-adcd-packages"></a>Carica il pacchetto/i ADCD

Ora che si dispone dei pacchetti, è necessario caricarli nella macchina virtuale in Azure.

1. Nel portale di Azure avviare una sessione **SSH** con la macchina virtuale Ubuntu creata. Passare alla macchina virtuale, selezionare il pannello **Panoramica** , quindi selezionare **Connetti**.

2. Selezionare la scheda **SSH** , quindi copiare il comando SSH negli Appunti.

3. Accedere alla VM usando le proprie credenziali e il [client SSH](/azure/virtual-machines/linux/use-remote-desktop) preferito. Questa demo usa le estensioni Linux per Windows 10, che aggiunge una shell bash al prompt dei comandi di Windows. PuTTy funziona anche in questo modo.

4. Quando si è connessi, creare una directory per caricare i pacchetti IBM. Tenere presente che Linux fa distinzione tra maiuscole e minuscole. Ad esempio, questa demo presuppone che i pacchetti vengano caricati in:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Caricare i file usando un client SSH, ad esempio[WinSCP](https://winscp.net/eng/index.php). Poiché SCP fa parte di SSH, usa la porta 22, che è l'uso di SSH. Se il computer locale non è Windows, è possibile digitare il [comando SCP](http://man7.org/linux/man-pages/man1/scp.1.html) nella sessione SSH.

6. Avviare il caricamento nella directory della macchina virtuale di Azure creata, che diventa l'archivio immagini per zD & T.

    > [!NOTE]
    > Assicurarsi che **ADCDTOOLS. Il codice XML** è incluso nel caricamento nella directory **Home/UserID/ZDT/ADCD/nov2017** . Sarà necessario più avanti.

7. Attendere che i file vengano caricati, operazione che può richiedere del tempo a seconda della connessione ad Azure.

8. Al termine del caricamento, passare alla directory volumi e decomprimere tutti i volumi **GZ** :

    ```
        gunzip \*.gz
    ```
    
![Esplora file che mostra i volumi GZ decompressi](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurare l'archiviazione di immagini

Il passaggio successivo consiste nel configurare zD & T per l'uso dei pacchetti caricati. Il processo di archiviazione delle immagini all'interno di zD & T consente di montare e usare le immagini. Può usare SSH o FTP.

1. Avviare il **zDTServer**. A tale scopo, è necessario essere a livello di radice. Immettere i due comandi seguenti nell'ordine:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Prendere nota dell'output dell'URL da parte del comando e utilizzare questo URL per accedere al server Web. L'aspetto è simile al seguente:
     > https://(nome o indirizzo IP della VM): 9443/ZDTMC/index. html
     >
     > Tenere presente che l'accesso Web usa la porta 9443. Utilizzare questo per accedere al server Web. L'ID utente per ZD & T è **zdtadmin** e la password è **password**.

    ![Schermata iniziale di IBM zD & T Enterprise Edition](media/02-welcome.png)

3. Nella pagina **avvio rapido** in **Configura**Selezionare **archiviazione immagini**.

     ![Schermata Avvio rapido IBM zD & T Enterprise Edition](media/03-quickstart.png)

4. Nella pagina **Configura archiviazione immagini** selezionare **SSH File Transfer Protocol**.

5. Per **nome host**digitare **localhost** e immettere il percorso della directory in cui sono state caricate le immagini. Ad esempio,/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Immettere l' **ID utente** e la **password** per la macchina virtuale. Non usare l'ID utente e la password ZD & T.

7. Testare la connessione per assicurarsi di avere accesso e quindi selezionare **Salva** per salvare la configurazione.

## <a name="configure-the-target-environments"></a>Configurare gli ambienti di destinazione

Il passaggio successivo consiste nel configurare l'ambiente di destinazione zD & T. Questo ambiente host emulato è il punto in cui vengono eseguite le immagini.

1. Nella pagina **avvio rapido** in **Configura**selezionare **ambienti di destinazione**.

2. Nella pagina **Configura ambienti di destinazione** selezionare **Aggiungi destinazione**.

3. Selezionare **Linux**. IBM supporta due tipi di ambienti, Linux e cloud (OpenStack), ma questa demo viene eseguita in Linux.

4. Nella pagina **Aggiungi ambiente di destinazione** , per **nome host**, immettere **localhost**. Mantieni la **porta SSH** impostata su **22**.

5. Nella casella **etichetta ambiente di destinazione** immettere un'etichetta, ad esempio **CICS.**

     ![Schermata Aggiungi ambiente di destinazione](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurare ADCD e distribuire

Dopo aver completato i passaggi di configurazione precedenti, è necessario configurare zD & T per usare i pacchetti e l'ambiente di destinazione. Anche in questo caso, si usa il processo di archiviazione delle immagini in zD & T, che consente di montare e usare le immagini. Può usare SSH o FTP.

1. Nella pagina **avvio rapido** , in **Configura**, selezionare **ADCD**. Viene visualizzata una serie di istruzioni che indicano i passaggi da completare prima di poter montare un pacchetto ADCD. Questo spiega perché la directory di destinazione è stata denominata come abbiamo fatto in precedenza.

2. Supponendo che tutte le immagini siano state caricate nelle directory corrette, fare clic sull' **immagine dal collegamento ADCD** visualizzata in basso a destra, come illustrato nel passaggio 7 nello screenshot seguente.

     ![IBM zD & T Enterprise Edition-configurare la schermata ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Creare l'immagine

Una volta completato il passaggio di configurazione precedente, viene visualizzata la pagina **Crea un'immagine usando i componenti di ADCD** .

1. Selezionare il volume (nov 2017 in questo caso) per visualizzare i diversi pacchetti presenti in tale volume.

2. Per questa demo selezionare **Customer Information Control System (CICS)-5,3**.

3. Nella casella **nome immagine** Digitare un nome per l'immagine, ad esempio l' **immagine CICS**.

4. Selezionare il pulsante **Crea immagine** in basso a destra.

     ![IBM zD & T Enterprise Edition-creare un'immagine usando la schermata componenti ADCD](media/06-adcd.png)

5. Nella finestra visualizzata, indicante che l'immagine è stata distribuita correttamente, scegliere **Distribuisci immagini**.

6. Nella pagina **Distribuisci un'immagine in un ambiente di destinazione** , selezionare l'immagine creata nella pagina precedente (**immagine CICS**) e l'ambiente di destinazione creato in precedenza (**CICS**).

7. Nella schermata successiva specificare le credenziali per la macchina virtuale, ovvero non le credenziali ztadmin.

8. Nel riquadro Proprietà immettere il numero di **processori centrali (CPS)** , la quantità di memoria di **sistema (GB)** e la directory di **distribuzione** per l'immagine in esecuzione. Poiché si tratta di una demo, è sufficiente mantenerla.

9. Assicurarsi che la casella sia selezionata per **rilasciare automaticamente il comando IPL a z/OS dopo la distribuzione**.

     ![Schermata delle proprietà](media/07-properties.png)

10. Selezionare **completa**.

11. Selezionare **Distribuisci immagine** dalla pagina **Distribuisci un'immagine in un ambiente di destinazione** .

Ora l'immagine può essere distribuita ed è pronta per essere montata da un emulatore di terminale 3270.

> [!NOTE]
> Se viene visualizzato un errore che informa che non si dispone di spazio su disco sufficiente, si noti che l'area richiede 151 GB.

Congratulazioni! Si sta ora eseguendo un ambiente IBM mainframe in Azure.

## <a name="learn-more"></a>Altre informazioni

- [Migrazione del mainframe: miti e fact](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Demistificazione della migrazione da mainframe ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
