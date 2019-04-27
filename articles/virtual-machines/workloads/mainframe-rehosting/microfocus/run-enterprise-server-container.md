---
title: Eseguire Micro Focus Enterprise Server 4.0 in un contenitore Docker in macchine virtuali di Azure
description: Riallocare i carichi di lavoro IBM mainframe z/OS tramite l'esecuzione di Micro Focus Enterprise Server in un contenitore Docker in macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61488463"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Eseguire Micro Focus Enterprise Server 4.0 in un contenitore Docker in Azure

È possibile eseguire Micro messa a fuoco Enterprise Server 4.0 in un contenitore Docker in Azure. Questa esercitazione spiega come fare. Usa la dimostrazione acctdemo Windows CICS (Customer Information controllo System) per il Server Enterprise.

Docker aggiunge isolamento e la portabilità alle applicazioni. Ad esempio, è possibile esportare un'immagine Docker da una macchina virtuale Windows per l'esecuzione in un altro, o da un repository in un server Windows con Docker. L'immagine Docker viene eseguito nella nuova posizione con la stessa configurazione, senza dover installare Enterprise Server. È parte dell'immagine. Considerazioni sulle licenze vengono mantenuti.

Questa esercitazione consente di installare il **Windows 2016 Datacenter con contenitori** macchina virtuale (VM) da Azure Marketplace. Questa macchina virtuale include **Docker 18.09.0**. La procedura seguente mostra come distribuire il contenitore, eseguirla e quindi connettersi a esso con un emulatore 3270.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, consultare questi prerequisiti:

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Il software di Micro Focus e una licenza valida (o licenza di valutazione). Se sei un cliente esistente di Micro Focus, contattare il rappresentante di Micro Focus. In caso contrario, [Richiedi una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > I file demo di Docker sono inclusi in Enterprise Server 4.0. Questa esercitazione Usa ent\_server\_dockerfile\_4.0\_windows.zip. Accessibile dalla stessa posizione che l'accesso è il file di installazione del Server dell'organizzazione o passare a *Micro Focus* per iniziare.

- La documentazione per [Server Enterprise e Developer Enterprise](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Proteggere il supporto da parte di ent\_server\_dockerfile\_4.0\_windows.zip file. Proteggere il file di licenza ES-Docker-Prod-XXXXXXXX.mflic (necessario per compilare le immagini Docker).

2. Creare la macchina virtuale A tale scopo, aprire Azure portale, selezionare **crea una risorsa** dall'alto a sinistra, filtra *server windows*. Nei risultati, selezionare **Windows Server 2016 Datacenter: con i contenitori**.

     ![Risultati della ricerca del portale di Azure](media/01-portal.png)

3. Per configurare le proprietà per la macchina virtuale, scegliere Dettagli di istanze:

    1. Scegliere le dimensioni per la macchina virtuale. Per questa esercitazione, è consigliabile usare un **DS2 Standard\_v2** della macchina virtuale con 2 Vcpu e 7 GB di memoria.

    2. Selezionare il **regione** e **gruppo di risorse** si vuole distribuire.

    3. Per la **opzioni di disponibilità**, utilizzare l'impostazione predefinita.

    4. Per la **Username**, digitare l'account amministratore da usare e la password.

    5. Assicurarsi che **porta RDP 3389** è aperto. Solo questa porta deve essere esposta pubblicamente, pertanto è possibile accedere alla macchina virtuale. Quindi accettare tutti i valori predefiniti e fare clic su **revisione + Crea**.

     ![Creare un riquadro della macchina virtuale](media/container-02.png)

4. Attendere il completamento (un paio di minuti) della distribuzione. Un messaggio indica che la macchina virtuale sia stata creata.

5. Fare clic su **Vai alla risorsa** per passare alle **Panoramica** pannello per la macchina virtuale.

6. A destra, scegliere il **Connect** pulsante. Il **Connetti a macchina virtuale** vengono visualizzate le opzioni sulla destra.

7. Scegliere il **Scarica File RDP** pulsante per scaricare il file RDP che consente di connettersi alla macchina virtuale.

8. Dopo che il file ha completato il download, aprire lo e digitare il nome utente e password creata per la macchina virtuale.

     > [!NOTE]
     > Non utilizzare le credenziali aziendali per l'accesso. (Il client RDP si presuppone che si desideri usarle. Non.)

9.  Selezionare **altre scelte**, quindi selezionare le credenziali VM.

A questo punto, la macchina virtuale sia in esecuzione e collegati tramite RDP. Hai effettuato l'accesso in e pronto per il passaggio successivo.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Creare una directory di sandbox e caricare il file zip

1.  Creare una directory nella macchina virtuale in cui è possibile caricare i file di licenza e demo. Ad esempio, **c:\\Sandbox**.

2.  Caricare **ent\_server\_dockerfile\_4.0\_windows.zip** e il **ES-Docker-Prod-XXXXXXXX.mflic** file nella directory creata.

3.  Estrarre il contenuto del file zip per il **ent\_server\_dockerfile\_4.0\_windows** directory creati dal processo di estrazione. Questa directory include un file Leggimi (come file con estensione txt e. HTML) e due sottodirectory **EnterpriseServer** e **esempi**.

4.  Copia **ES-Docker-Prod-XXXXXXXX.mflic** c:\\Sandbox\\ent\_server\_dockerfile\_4.0\_windows\\ C: ed EnterpriseServer\\Sandbox\\ent\_server\_dockerfile\_4.0\_windows\\esempi\\directory CICS.

> [!NOTE]
> Assicurarsi di che copiare il file di licenza per entrambe le directory. Sono necessarie per l'istruzione di compilazione di Docker per assicurarsi che le immagini di una licenza idonea.

## <a name="check-docker-version-and-create-base-image"></a>Controllare la versione di Docker e creare l'immagine di base

> [!IMPORTANT]
> Creazione dell'immagine Docker appropriato è un processo in due passaggi. Innanzitutto, creare l'immagine di base Server 4.0 Enterprise. Creare quindi un'altra immagine x64 per la piattaforma. Sebbene sia possibile creare un x86 (32 bit) dell'immagine, usare l'immagine a 64 bit.

1. Aprire un prompt dei comandi.

2. Verificare che Docker sia installato. Al prompt dei comandi digitare:

    ```
        docker version
    ```

     Ad esempio, la versione è stata 18.09.0 quando questo è stato scritto.

3. Per modificare la directory, digitare **\Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer cd**.

4. Tipo di **bld.bat IacceptEULA** per avviare il processo di compilazione per l'immagine di base iniziale. Attendere alcuni minuti eseguire questo processo. Nei risultati, si noti che le due immagini che sono stati creati, ovvero uno per x64 e uno per x86:

     ![Finestra di comando che mostra le immagini](media/container-04.png)

5. Per creare l'immagine finale per la dimostrazione CICS, passare alla directory CICS digitando **cd\\Sandbox\\ent\_server\_dockerfile\_4.0\_windows\\ Gli esempi\\CICS**.

6. Per creare l'immagine, digitare **bld.bat x64**. Attendere alcuni minuti per eseguire il processo e il messaggio che informa che l'immagine è stata creata.

7. Tipo di **immagini docker** per visualizzare un elenco di tutte le immagini Docker installate nella macchina virtuale. Assicurarsi che **microfocus/es-acctdemo** è uno di essi.

     ![Finestra di comando che Mostra immagine es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Eseguire l'immagine 

1.  Per avviare Enterprise Server 4.0 e dell'applicazione acctdemo, al prompt dei comandi digitare:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installare, ad esempio un emulatore terminal 3270 [x3270](http://x3270.bgp.nu/) e usarlo per connettere, tramite la porta 9040, all'immagine di cui è in esecuzione.

3.  Ottenere l'indirizzo IP del contenitore acctdemo, in modo che Docker può fungere da server DHCP per i contenitori gestiti:

    1.  Ottenere l'ID del contenitore in esecuzione. Tipo di **Docker ps** al prompt dei comandi e annotare l'ID (**22a0fe3159d0** in questo esempio). Salvare il file per il passaggio successivo.

    2.  Per ottenere l'indirizzo IP per il contenitore acctdemo, usare l'ID del contenitore nel passaggio precedente come indicato di seguito:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Ad esempio: 

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Annotare l'indirizzo IP per l'immagine acctdemo. Ad esempio, l'indirizzo nell'output seguente è 172.19.202.52.

     ![Finestra di comando con l'indirizzo IP](media/container-06.png)

5. Montare l'immagine usando l'emulatore. Configurare l'emulatore per usare l'indirizzo della porta 9040 e acctdemo immagine. In questo caso, ha **172.19.202.52:9040**. Sarà simile. Il **Signon a CICS** verrà visualizzata la schermata.

    ![Signon alla schermata CICS](media/container-07.png)

6. Accedere all'area CICS immettendo **SYSAD** per il **USERID** e **SYSAD** per il **Password**.

7. Cancellare lo schermo, utilizzando la mappatura dell'emulatore. Per x3270, selezionare la **mappatura** l'opzione di menu.

8. Per avviare l'applicazione acctdemo, digitare **ACCT**. Viene visualizzata la schermata iniziale per l'applicazione.

     ![Schermata account Demo](media/container-08.png)

9. Provare a usare i tipi di account visualizzato. Ad esempio, digitare **1!d** per la richiesta e **11111** per il **ACCOUNT**. Altri numeri di conto per provare sono 22222 33333 e così via.

     ![Schermata account Demo](media/container-09.png)

10. Per visualizzare la console di amministrazione di Server di livello aziendale, passare al prompt dei comandi e digitare **avviare http:172.19.202.52:86**

     ![Console di amministrazione di Server Enterprise](media/container-010.png)

L'operazione è terminata. Ora si è in esecuzione e gestione di un'applicazione CICS in un contenitore Docker.

## <a name="next-steps"></a>Passaggi successivi

- [Installare Micro Focus Enterprise Server 4.0 e gli sviluppatori aziendali 4.0 in Azure](./set-up-micro-focus-azure.md)
- [Migrazione delle applicazioni mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
