---
title: Eseguire Micro Focus Enterprise Server 4.0 in un contenitore Docker in macchine virtuali di AzureRun Micro Focus Enterprise Server 4.0 in a Docker Container on Azure Virtual Machines
description: Riospitare i carichi di lavoro mainframe IBM z/OS eseguendo Micro Focus Enterprise Server in un contenitore Docker in Macchine virtuali di Azure.Rehost your IBM z/OS mainframe workloads by running Micro Focus Enterprise Server in a Docker container on Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488463"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Eseguire Micro Focus Enterprise Server 4.0 in un contenitore Docker in AzureRun Micro Focus Enterprise Server 4.0 in a Docker Container on Azure

È possibile eseguire Micro Focus Enterprise Server 4.0 in un contenitore Docker in Azure.You can run Micro Focus Enterprise Server 4.0 in a Docker container on Azure. In questa esercitazione viene illustrato come. Utilizza la dimostrazione acctdemo CICS (Customer Information Control System) di Windows per Enterprise Server.

Docker aggiunge portabilità e isolamento alle applicazioni. Ad esempio, è possibile esportare un'immagine Docker da una macchina virtuale Windows per l'esecuzione in un'altra o da un repository a un server Windows con Docker.For example, you can export a Docker image from one Windows VM to run on another, or from a repository to a Windows server with Docker. L'immagine Docker viene eseguita nella nuova posizione con la stessa configurazione, senza dover installare Enterprise Server. Fa parte dell'immagine. Considerazioni sulle licenze ancora sono validi.

Questa esercitazione installa la macchina virtuale **(VM) di Windows 2016 Datacenter with Containers** da Azure Marketplace. Questa macchina virtuale include **Docker 18.09.0.** La procedura seguente illustra come distribuire il contenitore, eseguirlo e quindi connettersi a esso con un emulatore 3270.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, consulta questi prerequisiti:

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Il software Micro Focus e una licenza valida (o licenza di prova). Se sei un cliente Micro Focus esistente, contatta il tuo rappresentante Micro Focus. In caso contrario, [richiedere una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > I file dimostrativi di Docker sono inclusi in Enterprise Server 4.0. Questa esercitazione\_\_usa ent\_server\_dockerfiles 4.0 windows.zip. Accedervi dalla stessa posizione in cui è stato effettuato l'accesso al file di installazione di Enterprise Server oppure passare a *Micro Focus* per iniziare.

- Documentazione per [Enterprise Server ed Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Proteggere il supporto\_dal\_file\_windows.zip\_4.0 del server ent. Proteggere il file di licenza ES-Docker-Prod-XXXXXXXX.mflic (necessario per creare le immagini Docker).

2. Creare la macchina virtuale A tale scopo, aprire il portale di Azure, selezionare **Crea una risorsa** in alto a sinistra e filtrare in base a Windows *Server*. Nei risultati selezionare **Windows Server 2016 Datacenter con contenitori**.

     ![Risultati della ricerca nel portale di AzureAzure portal search results](media/01-portal.png)

3. Per configurare le proprietà per la macchina virtuale, scegliere dettagli istanza:To configure the properties for the VM, choose instance details:

    1. Scegliere le dimensioni per la macchina virtuale. Per questa esercitazione, è consigliabile usare una macchina virtuale **DS2\_v2 standard** con 2 vCPU e 7 GB di memoria.

    2. Selezionare **l'area** e il gruppo di **risorse** in cui si desidera eseguire la distribuzione.

    3. Per **Opzioni disponibilità**, utilizzare l'impostazione predefinita.

    4. In **Nome utente**digitare l'account amministratore che si desidera utilizzare e la password.

    5. Assicurarsi che la **porta 3389 RDP** sia aperta. Solo questa porta deve essere esposta pubblicamente, pertanto è possibile accedere alla macchina virtuale. Accettare quindi tutti i valori predefiniti e fare clic su **Revisione e creazione**.

     ![Creare un riquadro della macchina virtualeCreate a virtual machine pane](media/container-02.png)

4. Attendere il completamento della distribuzione (un paio di minuti). Un messaggio indica che la macchina virtuale è stata creata.

5. Fare clic su **Vai alla risorsa** per passare al pannello **Panoramica** per la macchina virtuale.

6. A destra, fai clic sul pulsante **Connetti.** Le opzioni **Connetti a macchina virtuale** vengono visualizzate a destra.

7. Fare clic sul pulsante **Scarica file RDP** per scaricare il file RDP che consente di allegare alla macchina virtuale.

8. Al termine del download del file, aprirlo e digitare il nome utente e la password creati per la macchina virtuale.

     > [!NOTE]
     > Non usare le credenziali aziendali per accedere. (Il client RDP presuppone che si consiglia di utilizzare questi. Non è così.)

9.  Selezionare **Altre scelte**, quindi selezionare le credenziali della macchina virtuale.

A questo punto, la macchina virtuale è in esecuzione e collegata tramite RDP. L'accesso è stato effettuato e si è pronti per il passaggio successivo.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Creare una directory sandbox e caricare il file zip

1.  Creare una directory nella macchina virtuale in cui è possibile caricare i file demo e di licenza. Ad esempio, **C:\\Sandbox**.

2.  Caricare **\_ent\_server\_dockerfiles\_4.0 windows.zip** e il file **ES-Docker-Prod-XXXXXXXX.mflic** nella directory creata.

3.  Estrarre il contenuto del file zip nella directory di **Windows ent\_server\_dockerfiles\_4.0\_** creata dal processo di estrazione. Questa directory include un file leggio (come file .html e .txt) e due sottodirectory, **EnterpriseServer** ed **Examples**.

4.  Copy **ES-Docker-Prod-XXXXXXXX.mflic** nel\\server\\\_C: Sandbox ent\_dockerfiles\_4.0\_windows\\EnterpriseServer\_e\_\\C:\\\\Sandbox\\ent\_dockerfiles\_4.0 windows Esempi di directory CICS.

> [!NOTE]
> Assicurarsi di copiare il file di licenza in entrambe le directory. Sono necessari per l'istruzione di compilazione Docker per assicurarsi che le immagini siano concesse in licenza correttamente.

## <a name="check-docker-version-and-create-base-image"></a>Controllare la versione Docker e creare l'immagine di base

> [!IMPORTANT]
> La creazione dell'immagine Docker appropriata è un processo in due passaggi. Creare innanzitutto l'immagine di base di Enterprise Server 4.0.Quindi creare un'altra immagine per la piattaforma x64. Sebbene sia possibile creare un'immagine x86 (32 bit), utilizzare l'immagine a 64 bit.

1. Aprire un prompt dei comandi.

2. Verificare che Docker sia installato. Al prompt dei comandi digitare:

    ```
        docker version
    ```

     Ad esempio, la versione era 18.09.0 quando questo è stato scritto.

3. Per modificare la directory, **digitare cd ent_server_dockerfiles_4.0_windows EnterpriseServer**.

4. Digitare **bld.bat IacceptEULA** per avviare il processo di compilazione per l'immagine di base iniziale. Attendere alcuni minuti per l'esecuzione di questo processo. Nei risultati, notare le due immagini che sono state create, una per x64 e una per x86:

     ![Finestra dei comandi che mostra le immagini](media/container-04.png)

5. Per creare l'immagine finale per la dimostrazione CICS, passare alla directory CICS digitando **\\cd Sandbox\\\_server\_dockerfiles\_4.0\_windows\\Esempi\\CICS**.

6. Per creare l'immagine, **digitare bld.bat x64**. Attendere alcuni minuti per l'esecuzione del processo e il messaggio che informa che l'immagine è stata creata.

7. Digitare **le immagini docker** per visualizzare un elenco di tutte le immagini Docker installate nella macchina virtuale. Assicurarsi che **microfocus/es-acctdemo** sia uno di loro.

     ![Finestra di comando che mostra l'immagine es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Eseguire l'immagine 

1.  Per avviare Enterprise Server 4.0 e l'applicazione acctdemo, al prompt dei comandi digitare:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installare un emulatore di terminale 3270 come [x3270](http://x3270.bgp.nu/) e utilizzarlo per collegare, tramite la porta 9040, all'immagine in esecuzione.

3.  Ottenere l'indirizzo IP del contenitore acctdemo, in modo che Docker possa fungere da server DHCP per i contenitori gestiti:

    1.  Ottenere l'ID del contenitore in esecuzione. Digitare **Docker ps** al prompt dei comandi e prendere nota dell'ID (**22a0fe3159d0** in questo esempio). Salvarlo per il passaggio successivo.

    2.  Per ottenere l'indirizzo IP per il contenitore acctdemo, utilizzare l'ID contenitore del passaggio precedente come segue:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Ad esempio:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Prendere nota dell'indirizzo IP dell'immagine acctdemo. Ad esempio, l'indirizzo nell'output seguente è 172.19.202.52.For example, the address in the following output is 172.19.202.52.

     ![Finestra di comando che mostra l'indirizzo IP](media/container-06.png)

5. Montare l'immagine utilizzando l'emulatore. Configurare l'emulatore per l'utilizzo dell'indirizzo dell'immagine acctdemo e della porta 9040. Qui, è **172.19.202.52:9040**. Il tuo sarà simile. Viene visualizzata la schermata **Signon to CICS.**

    ![Accesso alla schermata CICS](media/container-07.png)

6. Accedere all'area CICS immettendo **SYSAD** per **USERID** e **SYSAD** per la **password**.

7. Cancellare lo schermo, utilizzando la mappa dei tasti dell'emulatore. Per x3270, selezionare l'opzione di menu **Mappa chiave.**

8. Per avviare l'applicazione acctdemo, digitare **ACCT**. Viene visualizzata la schermata iniziale dell'applicazione.

     ![Schermata Demo account](media/container-08.png)

9. Sperimentare con i tipi di account di visualizzazione. Ad esempio, digitare **D** per la richiesta e **11111** per il **ACCOUNT**. Altri numeri di account da provare sono 22222, 33333 e così via.

     ![Schermata Demo account](media/container-09.png)

10. Per visualizzare la console di amministrazione di Enterprise Server, passare al prompt dei comandi e **digitare start http:172.19.202.52:86**

     ![Console di amministrazione di Enterprise Server](media/container-010.png)

L'operazione è terminata. A questo punto si esegue e si gestisce un'applicazione CICS in un contenitore Docker.

## <a name="next-steps"></a>Passaggi successivi

- [Installare Micro Focus Enterprise Server 4.0 ed Enterprise Developer 4.0 in Azure](./set-up-micro-focus-azure.md)
- [Migrazione delle applicazioni mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
