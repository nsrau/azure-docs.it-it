---
title: Eseguire Micro Focus Enterprise Server 5,0 in un contenitore Docker in Azure | Microsoft Docs
description: Questo articolo illustra come eseguire Micro Focus Enterprise Server 5,0 in un contenitore Docker in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: bfd40d39907c4e69ded0fa257305d346ca261836
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319997"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Eseguire Micro Focus Enterprise Server 5,0 in un contenitore Docker in Azure

È possibile eseguire Micro Focus Enterprise Server 5,0 in un contenitore Docker in Azure. Questa esercitazione illustra come. Usa la dimostrazione acctdemo di Windows CICS (Customer Information Control System) per Enterprise Server.

Docker consente di aumentare la portabilità e l'isolamento delle applicazioni. Ad esempio, è possibile esportare un'immagine Docker da una macchina virtuale (VM) Windows per eseguirla in un altro o da un repository a un server Windows con Docker. L'immagine Docker viene eseguita nella nuova posizione con la stessa configurazione, senza dover installare Enterprise Server. Fa parte dell'immagine. Sono comunque valide le considerazioni sulle licenze.

Questa esercitazione installa la VM **Windows 2016 Datacenter con contenitori** da Azure Marketplace. Questa macchina virtuale include **Docker 18.09.0**. I passaggi seguenti illustrano come distribuire il contenitore, eseguirlo e connettersi ad esso con un emulatore 3270.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare i prerequisiti seguenti:

-   Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

-   Il software di Micro Focus e una licenza valida (o una licenza di valutazione). Se sei un cliente di Micro Focus esistente, contatta il tuo rappresentante di Micro Focus. In caso contrario, [richiedere una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > I file di dimostrazione di Docker sono inclusi in Enterprise Server 5,0. Questa esercitazione USA ENT \_ server \_ dockerfile \_ 5,0 \_windows.zip. Accedervi dalla stessa posizione in cui è stato eseguito l'accesso al file di installazione di Enterprise Server o passare a *Micro Focus* per iniziare.

-   Documentazione per [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22).

## <a name="create-a-vm"></a>Creare una macchina virtuale

1.  Proteggere i supporti dal file ENT \_ server \_ dockerfile \_ 5,0 \_windows.zip. Proteggere il file di licenza ES-Docker-prod-XXXXXXXX. mflic (necessario per compilare le immagini Docker).

2.  Creare la macchina virtuale A tale scopo, aprire portale di Azure, selezionare **Crea una risorsa** nel menu in alto a sinistra e filtrare per *sistema operativo Windows Server*. Nei risultati selezionare **Windows Server.** Nella schermata successiva selezionare **Windows Server 2016 datacenter-with containers (contenitori)**.

    ![Screenshot dei risultati della ricerca portale di Azure](./media/run-image-1.png)

3.  Per configurare le proprietà per la VM, scegliere Dettagli istanza:

    1.  Scegliere le dimensioni per la macchina virtuale. Per questa esercitazione, prendere in considerazione l'uso di una VM **DS2 \_ v3 standard** con 2 vCPU e 16 GB di memoria.

    2.  Selezionare l' **area** e il **gruppo di risorse** in cui si vuole eseguire la distribuzione.

    3.  Per le **Opzioni di disponibilità**, usare l'impostazione predefinita.

    4.  Per **nome utente**, digitare l'account amministratore che si desidera utilizzare e la password.

    5.  Verificare che sia aperta la **porta 3389 RDP** . Solo questa porta deve essere esposta pubblicamente, quindi è possibile accedere alla macchina virtuale. Accettare quindi tutti i valori predefiniti e fare clic su **Verifica + crea**.

    ![Screenshot del riquadro creare una macchina virtuale](./media/run-image-2.png)

4.  Attendere il completamento della distribuzione (un paio di minuti). Un messaggio indica che la macchina virtuale è stata creata.

5.  Selezionare **Vai alla risorsa** per passare al pannello **Panoramica** della macchina virtuale.

6.  A destra selezionare **Connetti**. Le opzioni **Connetti a macchina virtuale** vengono visualizzate a destra.

7.  Selezionare il pulsante **Scarica file RDP** per scaricare il file RDP (Remote Desktop Protocol) che consente di connettersi alla macchina virtuale.

8.  Al termine del download del file, aprirlo e digitare il nome utente e la password creati per la macchina virtuale.

    > [!Note]    
    > Non usare le credenziali aziendali per accedere. Il client RDP presuppone che sia necessario usare questi. Non lo è.

9.  Selezionare **altre opzioni**e quindi selezionare le credenziali della macchina virtuale.

A questo punto, la macchina virtuale è in esecuzione e collegata tramite RDP. È stato eseguito l'accesso e si è pronti per il passaggio successivo.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Creare una directory sandbox e caricare il file zip

1.  Creare una directory nella macchina virtuale in cui è possibile caricare i file di licenza e demo. Ad esempio, **C: \\ sandbox**.

2.  Caricare **il \_ Server ent \_ dockerfile \_ 5,0 \_windows.zip** e il file **es-Docker-prod-xxxxxxxx. mflic** nella directory creata.

3.  Estrarre il contenuto del file zip nella directory del ** \_ Server ent \_ dockerfile \_ 5,0 di \_ Windows** creata dal processo di estrazione. Questa directory include un file Leggimi (file con estensione HTML e txt) e due sottodirectory, **EnterpriseServer** ed **esempi**.

4.  Copiare **es-Docker-prod-xxxxxxxx. mflic** in c: \\ sandbox \\ ENT \_ server \_ dockerfile \_ 5,0 \_ Windows \\ EnterpriseServer and c: \\ sandbox \\ ENT \_ server \_ dockerfile \_ 5,0 \_ Windows \\ examples \\ directory CICS.  
      
    > [!Note]
    > Assicurarsi di copiare il file di licenza in entrambe le directory. Sono necessari per il passaggio di compilazione Docker per assicurarsi che le immagini siano correttamente concesse in licenza.

## <a name="check-docker-version-and-create-base-image"></a>Controllare la versione di Docker e creare un'immagine di base

> [!Important]  
> La creazione dell'immagine Docker appropriata è un processo in due passaggi. Per prima cosa, creare l'immagine di base di Enterprise Server 5,0. Quindi creare un'altra immagine per la piattaforma x64. Sebbene sia possibile creare un'immagine x86 (32 bit), usare l'immagine a 64 bit.

1.  Aprire un prompt dei comandi.

2.  Verificare che Docker sia installato. Al prompt dei comandi digitare: **Docker Version**  
    Ad esempio, la versione è 18.09.0 quando è stata scritta.

3.  Per modificare la directory, digitare:  
    **CD \\ Sandbox \\ ENT \_ server \_ dockerfile \_ 5,0 \_ Windows \\ EnterpriseServer**.

4.  Digitare **bld.bat IacceptEULA** per avviare il processo di compilazione per l'immagine di base iniziale. Attendere alcuni minuti per l'esecuzione del processo. Nei risultati si notino le due immagini create, una per x64 e una per x86:

    ![Visualizzazione di immagini finestra di comando](./media/run-image-3.png)

5.  Per creare l'immagine finale per la dimostrazione CICS, passare alla directory CICS digitando **CD \\ sandbox \\ ent \_ server \_ dockerfile \_ 5,0 \_ Windows \\ examples \\ CICS**.

6.  Per creare l'immagine, digitare **bld.bat x64**. Attendere alcuni minuti per l'esecuzione del processo e il messaggio che informa che l'immagine è stata creata.

7.  Digitare **Docker images** per visualizzare un elenco di tutte le immagini Docker installate nella macchina virtuale. Assicurarsi che **microfocus/es-acctdemo** sia uno di essi.

    ![Finestra di comando che mostra l'immagine es-acctdemo](./media/run-image-4.png)

## <a name="run-the-image"></a>Eseguire l'immagine

1.  Per avviare Enterprise Server 5,0 e l'applicazione acctdemo, al prompt dei comandi digitare:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Installare un emulatore di terminale 3270, ad esempio [x3270](http://x3270.bgp.nu/) , e usarlo per il collegamento, tramite la porta 9040, all'immagine in esecuzione.

2.  Ottenere l'indirizzo IP del contenitore acctdemo in modo che Docker possa fungere da server Dynamic Host Configuration Protocol (DHCP) per i contenitori gestiti:

    1.  Ottenere l'ID del contenitore in esecuzione. Digitare **Docker PS** al prompt dei comandi e prendere nota dell'ID (**22a0fe3159d0** in questo esempio). Salvarlo per il passaggio successivo.

    2.  Per ottenere l'indirizzo IP per il contenitore acctdemo, usare l'ID del contenitore nel passaggio precedente, come indicato di seguito:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Ad esempio:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Prendere nota dell'indirizzo IP per l'immagine acctdemo. Ad esempio, l'indirizzo nell'output seguente è 172.19.202.52.

    ![Screenshot del finestra di comando che mostra l'indirizzo IP](./media/run-image-5.png)

5. Montare l'immagine usando l'emulatore. Configurare l'emulatore per usare l'indirizzo dell'immagine acctdemo e la porta 9040. Qui è **172.19.202.52:9040**. Il tuo sarà simile. Verrà visualizzata la schermata **Accedi a CICS** .

    ![Screenshot di Sign-on per CICS](./media/run-image-6.png)

6. Accedere all'area CICS immettendo **SYSAD** per **userid** e **SYSAD** per la **password**.

7. Deselezionare la schermata usando la tastiera dell'emulatore. Per x3270 selezionare l'opzione di menu relativa alla **tastiera** .

8. Per avviare l'applicazione acctdemo, digitare **Acct**. Viene visualizzata la schermata iniziale per l'applicazione.

     ![Screenshot della demo dell'account](./media/run-image-7.png)

9. Provare a visualizzare i tipi di account. Ad esempio, digitare **D** per la richiesta e **11111** per l' **account**. Altri numeri di account da provare sono 22222, 33333 e così via.

    ![Screenshot della demo dell'account](./media/run-image-8.png)

10. Per visualizzare la console di amministrazione di Enterprise Server, passare al prompt dei comandi e digitare **Start http: 172.19.202.52:86**.

    ![Console di amministrazione di Enterprise Server](media/run-image-9.png)

L'operazione è terminata. A questo punto si sta eseguendo e gestendo un'applicazione CICS in un contenitore docker.

## <a name="next-steps"></a>Passaggi successivi

-   [Installare Micro Focus Enterprise Server 5,0 e Enterprise Developer 5,0 in Azure](./set-up-micro-focus-azure.md)

-   [Migrazione delle applicazioni mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
