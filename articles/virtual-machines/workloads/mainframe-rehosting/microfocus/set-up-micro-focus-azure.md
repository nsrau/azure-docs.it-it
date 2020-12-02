---
title: Installare Micro Focus Enterprise Server 5,0 e Enterprise Developer 5,0 in Azure | Microsoft Docs
description: Questo articolo illustra come installare Micro Focus Enterprise Server 5,0 e Enterprise Developer 5,0 in Microsoft Azure.
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
ms.openlocfilehash: d9e5f9b531fc28caf8f3162a70318927d40bb923
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483063"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Installare Micro Focus Enterprise Server 5,0 e Enterprise Developer 5,0 in Azure

Questo articolo illustra come configurare [Micro Focus Enterprise Server 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) e [micro focus Enterprise Developer 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) su Microsoft Azure.

Un carico di lavoro comune in Azure è un ambiente di sviluppo e test. Questo scenario è comune perché è così conveniente e semplice da distribuire e rimuovere. Con Enterprise Server, Micro Focus ha creato una delle più grandi piattaforme di riallocazione mainframe disponibili. Puoi eseguire carichi di lavoro z/OS su una piattaforma x86 meno costosa in Azure usando macchine virtuali (VM) Windows o Linux.

Questa installazione usa le VM di Azure che eseguono l'immagine di Windows Server 2016 da Azure Marketplace con Microsoft SQL Server 2017 già installato. Questa configurazione si applica anche a Azure Stack.

L'ambiente di sviluppo corrispondente per Enterprise Server è Enterprise Developer, che può essere eseguito su Microsoft Visual Studio 2017 o versione successiva, Visual Studio Community (gratuito per il download) o Eclipse. Questo articolo illustra come distribuirlo usando una macchina virtuale Windows Server 2016 fornita con Visual Studio 2017 o versione successiva installata.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare i prerequisiti seguenti:

-   Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

-   Il software di Micro Focus e una licenza valida (o una licenza di valutazione). Se sei un cliente di Micro Focus esistente, contatta il tuo rappresentante di Micro Focus. In caso contrario, [richiedere una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

-   Ottenere la documentazione per [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/).

    > [!Note]
    > Sono disponibili alcune opzioni per il controllo dell'accesso alle macchine virtuali:
    > -   Una procedura consigliata consiste nell'impostare [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/).
    > -   Un tunnel [di rete privata virtuale (VPN) da sito a sito](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) .
    > -   Una macchina virtuale JumpBox.

## <a name="install-enterprise-server"></a>Installare Enterprise Server

1.  Per migliorare la sicurezza e la gestibilità, è consigliabile creare un nuovo gruppo di risorse solo per questo progetto, ad esempio **RGMicroFocusEntServer**. Usare la prima parte del nome in Azure per scegliere il tipo di risorsa per facilitarne l'individuazione in un elenco.

2.  Creare una macchina virtuale. Da Azure Marketplace selezionare la macchina virtuale e il sistema operativo desiderati. Ecco una configurazione consigliata:

    -   **Server aziendale:** Selezionare **VM ES2 V3** (con 2 vCPU e 16 GB di memoria) con Windows Server 2016 e SQL Server 2017 installato. Questa immagine è disponibile in Azure Marketplace. Enterprise Server può anche usare il database SQL di Azure.

    -   **Sviluppatore aziendale:** Selezionare **B2MS VM** (con 2 vCPU e 8 GB di memoria) con Windows 10 e Visual Studio installato. Questa immagine è disponibile in Azure Marketplace.

3.  Nella sezione **nozioni di base** immettere il nome utente e la password. Selezionare la **sottoscrizione** e la **località/area** da usare per le macchine virtuali. Selezionare **RGMicroFocusEntServer** per il gruppo di risorse.

4.  Inserire entrambe le macchine virtuali nella stessa rete virtuale in modo che possano comunicare tra loro.

5.  Accettare le impostazioni predefinite per le altre impostazioni. Ricordare il nome utente e la password creati per l'amministratore di queste macchine virtuali.

6.  Quando le macchine virtuali sono state create, aprire le porte in ingresso **9003, 86** e **80** per HTTP e **3389** per Remote Desktop Protocol (RDP) nel computer server aziendale e **3389** nel computer di sviluppo.

7.  Per accedere alla macchina virtuale Enterprise Server, in portale di Azure selezionare la VM ES2 V3. Passare alla sezione **Panoramica** e selezionare **Connetti** per avviare una sessione RDP. Accedere usando le credenziali create per la macchina virtuale.

8.  Dalla sessione RDP, caricare i due file seguenti. Poiché si sta usando Windows, è possibile trascinare e rilasciare i file nella sessione RDP:

    -   `es\_50.exe`, il file di installazione di Enterprise Server.

    -   `mflic`, il file di licenza corrispondente, ovvero Enterprise Server non verrà caricato senza di esso.

9.  Fare doppio clic sul file per avviare l'installazione. Nella prima finestra selezionare il percorso di installazione e accettare il contratto di licenza con l'utente finale.

    ![Screenshot mostra la finestra di dialogo Micro Focus Enterprise Server in cui è possibile avviare l'installazione.](media/install-image-1.png)

    Al termine dell'installazione, viene visualizzato il messaggio seguente:

    ![Screenshot mostra un messaggio di operazione completata nella finestra di dialogo Micro Focus Enterprise Server.](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Verificare gli aggiornamenti

Dopo l'installazione, assicurarsi di verificare la presenza di eventuali aggiornamenti aggiuntivi come un numero di prerequisiti, ad esempio Microsoft C++ Redistributable e .NET Framework, insieme a Enterprise Server.

### <a name="upload-the-license"></a>Caricare la licenza

1.  Avviare l'amministrazione della licenza di Micro Focus.

2.  Selezionare **Avvia** \> **Micro Focus License Manager License Manager** \> **License Administration**, quindi fare clic sulla scheda **Installa** . scegliere il tipo di formato di licenza da caricare: un file di licenza o un codice di licenza a 16 caratteri. Ad esempio, per un file, in **file di licenza**, passare al `mflic` file * caricato in precedenza nella macchina virtuale e selezionare **Installa licenze**.

    ![Screenshot mostra la finestra di dialogo Amministrazione licenza Micro Focus in cui è possibile selezionare Installa licenze.](media/install-image-3.png)

3.  Verificare il caricamento di Enterprise Server. Provare ad avviare il sito di amministrazione di Enterprise Server da un browser utilizzando questo URL: `http://localhost:86/` . Viene visualizzata la pagina Amministrazione server aziendale, come illustrato.

    ![Pagina di amministrazione di Enterprise Server](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installare Enterprise Developer nel computer di sviluppo

1.  Selezionare il gruppo di risorse creato in precedenza (ad esempio, **RGMicroFocusEntServer**), quindi selezionare l'immagine per sviluppatori.

2.  Per accedere alla macchina virtuale, passare alla sezione **Panoramica** e selezionare **Connetti**. Questo accesso avvia una sessione RDP. Accedere usando le credenziali create per la macchina virtuale.

3.  Dalla sessione RDP, caricare i due file seguenti (trascinamento della selezione se si desidera):

    -   `edvs2017.exe`, il file di installazione di Enterprise Server.

    -   `mflic`, il file di licenza corrispondente (Enterprise Developer non verrà caricato senza di esso).

4.  Fare doppio clic sul file **edvs2017.exe** per avviare l'installazione. Nella prima finestra selezionare il percorso di installazione e accettare il contratto di licenza con l'utente finale. Se lo si desidera, scegliere **Installa Rumba 9,5** per installare questo emulatore di terminale, che probabilmente sarà necessario.

    ![Finestra di dialogo di installazione di Micro Focus Enterprise Developer per Visual Studio 2017](media/install-image-5.png)

5.  Al termine dell'installazione, viene visualizzato il messaggio seguente:

    ![Messaggio di installazione riuscita](media/install-image-6.png)

6.  Avviare Micro Focus License Manager esattamente come per Enterprise Server. Scegliere **Avvia** \> **Micro Focus License Manager** \> **License Administration**, quindi fare clic sulla scheda **Installa** .

7.  Scegliere il tipo di formato di licenza da caricare: un file di licenza o un codice di licenza a 16 caratteri. Ad esempio, per un file, in **file di licenza**, passare al `mflic` file caricato in precedenza nella macchina virtuale e selezionare  **Installa licenze**.

    ![Finestra di dialogo Amministrazione licenza Micro Focus](media/install-image-7.png)

Quando si caricano gli sviluppatori aziendali, è stata completata la distribuzione di un ambiente di test e sviluppo di Micro Focus in Azure.

**Passaggi successivi**

-   [Configurare l'applicazione BankDemo](./demo.md)

-   [Eseguire Enterprise Server in contenitori Docker](./run-enterprise-server-container.md)

-   [Migrazione delle applicazioni mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)