---
title: Installare Micro Focus Enterprise Server 4,0 e Enterprise Developer 4,0 in Azure | Microsoft Docs
description: Riospitare i carichi di lavoro mainframe IBM z/OS usando l'ambiente di sviluppo e test con Micro Focus in macchine virtuali (VM) di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834563"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installare Micro Focus Enterprise Server 4,0 e Enterprise Developer 4,0 in Azure

Questo articolo illustra come configurare [Micro Focus Enterprise Server 4,0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e [micro focus Enterprise Developer 4,0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) in Azure.

Un carico di lavoro comune in Azure è un ambiente di sviluppo e test. Questo scenario è comune perché è così conveniente e semplice da distribuire e rimuovere. Con Enterprise Server, Micro Focus ha creato una delle più grandi piattaforme di riallocazione mainframe disponibili. Puoi eseguire carichi di lavoro z/OS su una piattaforma x86 meno costosa in Azure usando macchine virtuali (VM) Windows o Linux.

Questa installazione usa le VM di Azure che eseguono l'immagine di Windows Server 2016 da Azure Marketplace con Microsoft SQL Server 2017 già installato. Questa configurazione si applica anche a Azure Stack.

L'ambiente di sviluppo corrispondente per Enterprise Server è Enterprise Developer, che può essere eseguito su Microsoft Visual Studio 2017 o versione successiva, Visual Studio Community (gratuito per il download) o Eclipse. Questo articolo illustra come distribuirlo usando una macchina virtuale Windows Server 2016 fornita con Visual Studio 2017 o versione successiva installata.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Il software di Micro Focus e una licenza valida (o una licenza di valutazione). Se sei un cliente di Micro Focus esistente, contatta il tuo rappresentante di Micro Focus. In caso contrario, [richiedere una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Ottenere la documentazione per [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Una procedura consigliata consiste nell'impostare un tunnel di rete privata virtuale (VPN) da sito a sito o un JumpBox per poter controllare l'accesso alle macchine virtuali di Azure.

## <a name="install-enterprise-server"></a>Installare Enterprise Server

1. Per migliorare la sicurezza e la gestibilità, è consigliabile creare un nuovo gruppo di risorse solo per questo progetto, ad esempio **RGMicroFocusEntServer**. Usare la prima parte del nome in Azure per scegliere il tipo di risorsa per facilitarne l'individuazione in un elenco.

2. Creare una macchina virtuale. Da Azure Marketplace selezionare la macchina virtuale e il sistema operativo desiderati. Ecco una configurazione consigliata:

    - **Server aziendale**: Selezionare VM ES2 V3 (con 2 vCPU e 16 GB di memoria) con Windows Server 2016 e SQL Server 2017 installato. Questa immagine è disponibile in Azure Marketplace. Enterprise Server può anche usare il database SQL di Azure.

    - **Sviluppatore aziendale**: Selezionare B2ms VM (con 2 vCPU e 8 GB di memoria) con Windows 10 e Visual Studio installato. Questa immagine è disponibile in Azure Marketplace.

3. Nella sezione **nozioni di base** immettere il nome utente e la password. Selezionare la **sottoscrizione** e la **località/area** da usare per le macchine virtuali. Selezionare **RGMicroFocusEntServer** per il gruppo di risorse.

4. Inserire entrambe le macchine virtuali nella stessa rete virtuale in modo che possano comunicare tra loro.

5. Accettare le impostazioni predefinite per le altre impostazioni. Ricordare il nome utente e la password creati per l'amministratore di queste macchine virtuali.

6. Quando le macchine virtuali sono state create, aprire le porte in ingresso 9003, 86 e 80 per HTTP e 3389 per RDP nel computer server aziendale e 3389 nel computer di sviluppo.

7. Per accedere alla macchina virtuale Enterprise Server, in portale di Azure selezionare la VM ES2 V3. Passare alla sezione **Panoramica** e selezionare **Connetti** per avviare una sessione RDP. Accedere usando le credenziali create per la macchina virtuale.

8. Dalla sessione RDP, caricare i due file seguenti. Poiché si sta usando Windows, è possibile trascinare e rilasciare i file nella sessione RDP:

    - **es\_40. exe**, il file di installazione di Enterprise Server.

    - **mflic**, il file di licenza corrispondente: Enterprise Server non verrà caricato senza di esso.

9. Fare doppio clic sul file per avviare l'installazione. Nella prima finestra selezionare il percorso di installazione e accettare il contratto di licenza con l'utente finale.

     ![Schermata di installazione di Micro Focus Enterprise Server](media/01-enterprise-server.png)

     Al termine dell'installazione, viene visualizzato il messaggio seguente:

     ![Schermata di installazione di Micro Focus Enterprise Server](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Verificare gli aggiornamenti

Dopo l'installazione, assicurarsi di verificare la presenza di eventuali aggiornamenti aggiuntivi, in quanto alcuni prerequisiti, ad esempio Microsoft C++ Redistributable e .NET Framework, vengono installati insieme a Enterprise Server.

### <a name="upload-the-license"></a>Caricare la licenza

1. Avviare l'amministrazione della licenza di Micro Focus.

2. Fare clic su **Avvia** \> **Micro Focus License Manager** \> **License**Manager, quindi fare clic sulla scheda **Installa** . Scegliere il tipo di formato di licenza da caricare: un file di licenza o un codice di licenza a 16 caratteri. Ad esempio, per un file, in **file di licenza**, passare al file **mflic** caricato in precedenza nella macchina virtuale e selezionare **Installa licenze**.

     ![Finestra di dialogo Amministrazione licenza Micro Focus](media/03-enterprise-server.png)

3. Verificare il caricamento di Enterprise Server. Provare ad avviare il sito di amministrazione di Enterprise Server da un browser <http://localhost:86/>utilizzando questo URL . Viene visualizzata la pagina Amministrazione server aziendale, come illustrato.

     ![Pagina di amministrazione di Enterprise Server](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installare Enterprise Developer nel computer di sviluppo

1. Selezionare il gruppo di risorse creato in precedenza (ad esempio, **RGMicroFocusEntServer**), quindi selezionare l'immagine per sviluppatori.

2. Per accedere alla macchina virtuale, passare alla sezione **Panoramica** e selezionare **Connetti**. Questo accesso avvia una sessione RDP. Accedere usando le credenziali create per la macchina virtuale.

3. Dalla sessione RDP, caricare i due file seguenti (trascinamento della selezione se si desidera):

    - **edvs2017. exe**, il file di installazione di Enterprise Server.

    - **mflic**, il file di licenza corrispondente (Enterprise Developer non verrà caricato senza di esso).

4. Fare doppio clic sul file **edvs2017. exe** per avviare l'installazione. Nella prima finestra selezionare il percorso di installazione e accettare il contratto di licenza con l'utente finale. Se lo si desidera, scegliere **Installa Rumba 9,5** per installare questo emulatore di terminale, che probabilmente sarà necessario.

     ![Finestra di dialogo di installazione di Micro Focus Enterprise Developer per Visual Studio 2017](media/04-enterprise-server.png)

5. Al termine dell'installazione, viene visualizzato il messaggio seguente:

     ![Messaggio di installazione riuscita](media/05-enterprise-server.png)

6. Avviare Micro Focus License Manager esattamente come per Enterprise Server. Scegliere **Avvia** \> **Micro Focus License Manager** \> **License Administration**, quindi fare clic sulla scheda **Installa** .

7. Scegliere il tipo di formato di licenza da caricare: un file di licenza o un codice di licenza a 16 caratteri. Ad esempio, per un file, in **file di licenza**, passare al file **mflic** caricato in precedenza nella macchina virtuale e selezionare **Installa licenze**.

     ![Finestra di dialogo Amministrazione licenza Micro Focus](media/07-enterprise-server.png)

Quando si caricano gli sviluppatori aziendali, è stata completata la distribuzione di un ambiente di test e sviluppo di Micro Focus in Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'applicazione demo Bank](./demo.md)
- [Eseguire Enterprise Server in contenitori Docker](./run-enterprise-server-container.md)
- [Migrazione delle applicazioni mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
