---
title: Installare Micro Focus Enterprise Server 4.0 e gli sviluppatori aziendali 4.0 in Azure | Microsoft Docs
description: Rehosting IBM z/OS mainframe carichi di lavoro con lo sviluppo di Micro Focus e testare l'ambiente in macchine virtuali di Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 46b135aabaefb3a94e4470927297fb696f216b7a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192531"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installare Micro Focus Enterprise Server 4.0 e gli sviluppatori aziendali 4.0 in Azure

Questo articolo illustra come configurare [Micro messa a fuoco Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e [Micro messa a fuoco Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) in Azure.

Un carico di lavoro comune in Azure è un ambiente di sviluppo e test perché è in modo conveniente e facile da distribuire e Deconfigurazione. Con Enterprise Server, Micro Focus ha creato una delle più grandi mainframe riallocazione piattaforme disponibili. È possibile eseguire carichi di lavoro z/OS in un x86 meno costoso platform in Azure usando macchine virtuali (VM) Windows o Linux.

Questa configurazione usa macchine virtuali di Azure che eseguono l'immagine di Windows Server 2016 da Azure Marketplace con Microsoft SQL Server 2017 già installato. Questa impostazione si applica anche ad Azure Stack.

L'ambiente di sviluppo corrispondente per Enterprise Server è Enterprise Developer, eseguita in entrambi Microsoft Visual Studio 2017, Visual Studio Community (gratuita per il download), o Eclipse. Questo articolo illustra come eseguire la distribuzione usando una macchina virtuale Windows Server 2016 che viene fornito con Visual Studio 2017 installato.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, consultare questi prerequisiti:

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Il software di Micro Focus e una licenza valida (o licenza di valutazione). Se sei un cliente esistente di Micro Focus, contattare il rappresentante di Micro Focus. In caso contrario, [Richiedi una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Scarica la documentazione per [Server Enterprise e Developer Enterprise](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Una procedura consigliata consiste nell'impostare backup di un tunnel rete privata virtuale (VPN) site-to-site o un jumpbox in modo che è possibile controllare l'accesso alle VM di Azure.

## <a name="install-enterprise-server"></a>Installare il Server Enterprise

1. Per una migliore sicurezza e gestibilità, è consigliabile creare un nuovo gruppo di risorse solo per questo progetto, ad esempio, **RGMicroFocusEntServer**. Per designare il tipo di risorsa per renderlo più facile da trovare in un elenco, usare la prima parte del nome in Azure.

2. Creare una macchina virtuale. Da Azure Marketplace, selezionare la macchina virtuale e sistema operativo desiderato. Ecco una configurazione consigliata:

    - **Server aziendale**: Selezionare macchina virtuale v3 ES2 (con 2 Vcpu e 16 GB di memoria) con Windows Server 2016 e SQL Server 2017 installato. Questa immagine è disponibile da Azure Marketplace. Server dell'organizzazione possono usare Database SQL di Azure.

    - **Gli sviluppatori aziendali**: Selezionare macchina virtuale B2ms (con 2 Vcpu e 8 GB di memoria) con Windows 10 e installato Visual Studio. Questa immagine è disponibile da Azure Marketplace.

3. Nel **nozioni di base** pannello, immettere il nome utente e password. Selezionare il **abbonamento** e **località/area geografica** desiderato da utilizzare per le macchine virtuali. Selezionare **RGMicroFocusEntServer** per il gruppo di risorse.

4. Inserire entrambe le macchine virtuali nella stessa rete virtuale in modo che possano comunicare tra loro.

5. Accettare le impostazioni predefinite per il resto delle impostazioni. Ricordare il nome utente e password creati per l'amministratore di queste macchine virtuali.

6. Quando le macchine virtuali sono state create, aprire porte in ingresso 9003, x86 e 80 per HTTP e 3389 per RDP nel computer Server Enterprise e 3389 sul computer per lo sviluppo.

7. Per accedere alla macchina virtuale Enterprise Server, nel portale di Azure, selezionare la macchina virtuale v3 ES2. Andare alla **Overview** pannello e selezionare **Connect** per avviare una sessione RDP. Accedere con le credenziali create per la macchina virtuale.

8. Dalla sessione RDP, caricare i due file seguenti. Poiché si tratta di Windows, pertanto è possibile trascinare e rilasciare i file nella sessione RDP:

    - **es\_40. exe**, file di installazione del Server Enterprise.

    - **mflic**, il file di licenza corrispondenti, Server Enterprise non verrà caricata senza di essa.

9. Fare doppio clic sul file per avviare l'installazione. Nella prima finestra, selezionare il percorso di installazione e accettare il contratto di licenza utente finale.

     ![Schermata di messa a fuoco Enterprise Server Setup Micro](media/01-enterprise-server.png)

     Una volta completato il programma di installazione, viene visualizzato il messaggio seguente:

     ![Schermata di messa a fuoco Enterprise Server Setup Micro](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Verifica disponibilità aggiornamenti

Dopo l'installazione, assicurarsi di verificare la presenza di eventuali aggiornamenti aggiuntivi dopo un numero di prerequisiti, ad esempio il pacchetto ridistribuibile di Microsoft C++ e .NET Framework vengono installate insieme alla Enterprise Server.

### <a name="upload-the-license"></a>Caricare la licenza

1. Avviare l'amministrazione di contratti di Micro Focus.

2. Fare clic su **avviare** \> **Micro Focus License Manager** \> **amministrazione di contratti**, quindi fare clic su di **installare** scheda. Scegliere il tipo di formato della licenza da caricare: un file di licenza o un codice di licenza di 16 caratteri. Ad esempio, per un file, in **file di licenza**, individuare il **mflic** file caricato in precedenza per la macchina virtuale e selezionare **Installa licenze**.

     ![Micro-finestra di dialogo di amministrazione di contratti messa a fuoco](media/03-enterprise-server.png)

3. Verificare il caricamento di Enterprise Server. Provare ad avviare il sito di amministrazione di Server di livello aziendale da un browser con questo URL <http://localhost:86/> . Come illustrato, verrà visualizzata la pagina di amministrazione di Server di livello aziendale.

     ![Pagina di amministrazione del Server Enterprise](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installare gli sviluppatori aziendali su computer per lo sviluppo

1. Selezionare il gruppo di risorse creato in precedenza (ad esempio, **RGMicroFocusEntServer**), quindi selezionare l'immagine per gli sviluppatori.

2. Per accedere alla macchina virtuale, vedere la **Panoramica** pannello e selezionare **Connect**. Verrà avviata una sessione RDP. Accedere con le credenziali create per la macchina virtuale.

3. Dalla sessione RDP, caricare i due file seguenti (trascinamento della selezione se si desidera):

    - **edvs2017.exe**, file di installazione del Server Enterprise.

    - **mflic**, il corrispondente file di licenza (sviluppatore aziendale non verrà caricata senza di essa).

4. Fare doppio clic il **edvs2017.exe** file per avviare l'installazione. Nella prima finestra, selezionare il percorso di installazione e accettare il contratto di licenza utente finale. Se desidera, scegliere **9.5 Rumba installare** per installare l'emulatore di terminale, sarà probabilmente necessario.

     ![Micro messa a fuoco Enterprise Developer per la finestra di dialogo di installazione di Visual Studio 2017](media/04-enterprise-server.png)

5. Al termine dell'installazione, viene visualizzato il messaggio seguente:

     ![Messaggio di esito positivo di installazione](media/05-enterprise-server.png)

6. Avviare Micro Focus License Manager allo stesso modo per Enterprise Server. Scegli **avviare** \> **Micro Focus License Manager** \> **amministrazione di contratti**e fare clic su di **installare**scheda.

7. Scegliere il tipo di formato della licenza da caricare: un file di licenza o un codice di licenza di 16 caratteri. Ad esempio, per un file, in **file di licenza**, individuare il **mflic** file caricato in precedenza per la macchina virtuale e selezionare **Installa licenze**.

     ![Micro-finestra di dialogo di amministrazione di contratti messa a fuoco](/edia/07-enterprise-server.png)

Quando viene caricata per gli sviluppatori aziendali, la distribuzione di un ambiente di sviluppo e test di Micro Focus in Azure è stata completata.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'applicazione Demo Bank](./demo.md)
