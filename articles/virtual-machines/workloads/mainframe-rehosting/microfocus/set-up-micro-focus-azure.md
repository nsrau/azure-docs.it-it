---
title: Installazione di Micro Focus Enterprise Server 4.0 ed Enterprise Developer 4.0 in Azure Documenti Microsoft
description: Riospitare i carichi di lavoro mainframe IBM z/OS usando l'ambiente di sviluppo e test Micro Focus nelle macchine virtuali (VM) di Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834563"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Installare Micro Focus Enterprise Server 4.0 ed Enterprise Developer 4.0 in Azure

Questo articolo illustra come configurare [Micro Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e Micro Focus Enterprise Developer 4.0 in Azure.This article shows how to set up Micro Focus Enterprise Server 4.0 and Micro Focus Enterprise Developer [4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) on Azure.

Un carico di lavoro comune in Azure è un ambiente di sviluppo e test. Questo scenario è comune perché è così conveniente e facile da distribuire e abbattere. Con Enterprise Server, Micro Focus ha creato una delle più grandi piattaforme di rehosting mainframe disponibili. È possibile eseguire carichi di lavoro z/OS su una piattaforma x86 meno costosa in Azure usando macchine virtuali (VM) Windows o Linux.You can run z/OS workloads on a less expensive x86 platform on Azure using either Windows or Linux virtual machines (VMs).

Questa configurazione usa le macchine virtuali di Azure che eseguono l'immagine di Windows Server 2016 da Azure Marketplace con Microsoft SQL Server 2017 già installato. Questa configurazione si applica anche ad Azure Stack.This setup also applies to Azure Stack.

L'ambiente di sviluppo corrispondente per Enterprise Server è Enterprise Developer, che viene eseguito in Microsoft Visual Studio 2017 o versioni successive, Visual Studio Community (scaricabile gratuitamente) o Eclipse. In questo articolo viene illustrato come distribuirlo utilizzando una macchina virtuale Windows Server 2016 fornita con Visual Studio 2017 o versione successiva installata.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, consulta questi prerequisiti:

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Il software Micro Focus e una licenza valida (o licenza di prova). Se sei un cliente Micro Focus esistente, contatta il tuo rappresentante Micro Focus. In caso contrario, [richiedere una versione di valutazione](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Ottenere la documentazione per [Enterprise Server ed Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> A best practice is to set up a site-to-site virtual private network (VPN) tunnel or a jumpbox so you can control access to the Azure VMs.

## <a name="install-enterprise-server"></a>Installare Enterprise Server

1. Per migliorare la sicurezza e la gestibilità, è consigliabile creare un nuovo gruppo di risorse solo per questo progetto, ad esempio **RGMicroFocusEntServer**. Usare la prima parte del nome in Azure per scegliere il tipo di risorsa per semplificarne l'individuarla in un elenco.

2. Creare una macchina virtuale. In Azure Marketplace selezionare la macchina virtuale e il sistema operativo desiderati. Di seguito è riportata una configurazione consigliata:Here's a recommended setup:

    - **Enterprise Server:** selezionare la macchina virtuale ES2 v3 (con 2 vCPU e 16 GB di memoria) con Windows Server 2016 e SQL Server 2017 installato. Questa immagine è disponibile in Azure Marketplace.This image is available from the Azure Marketplace. Enterprise Server può usare anche il database SQL di Azure.Enterprise Server can use Azure SQL Database as well.

    - **Enterprise Developer**: Selezionare B2ms VM (con 2 vCPU e 8 GB di memoria) con Windows 10 e Visual Studio installati. Questa immagine è disponibile in Azure Marketplace.This image is available from the Azure Marketplace.

3. Nella sezione **Nozioni di base,** inserisci il tuo nome utente e la password. Selezionare la **sottoscrizione** e la **posizione/area** da usare per le macchine virtuali. Selezionare **RGMicroFocusEntServer** per il gruppo di risorse.

4. Inserire entrambe le macchine virtuali nella stessa rete virtuale in modo che possano comunicare tra loro.

5. Accettare le impostazioni predefinite per il resto delle impostazioni. Ricordare il nome utente e la password creati per l'amministratore di queste macchine virtuali.

6. Una volta create le macchine virtuali, aprire le porte in ingresso 9003, 86 e 80 per HTTP e 3389 per RDP nel computer Enterprise Server e 3389 nel computer Developer.

7. Per accedere alla macchina virtuale Enterprise Server, nel portale di Azure selezionare la macchina virtuale ES2 v3. Passare alla sezione **Panoramica** e selezionare **Connetti** per avviare una sessione RDP. Accedere usando le credenziali create per la macchina virtuale.

8. Dalla sessione RDP, caricare i due file seguenti. Poiché si utilizza Windows, è possibile trascinare e rilasciare i file nella sessione RDP:

    - **es\_40.exe**, il file di installazione di Enterprise Server.

    - **mflic**, il file di licenza corrispondente: Enterprise Server non verrà caricato senza di esso.

9. Fare doppio clic sul file per avviare l'installazione. Nella prima finestra, selezionare il percorso di installazione e accettare il contratto di licenza con l'utente finale.

     ![Schermata di installazione di Micro Focus Enterprise Server](media/01-enterprise-server.png)

     Al termine dell'installazione, viene visualizzato il seguente messaggio:

     ![Schermata di installazione di Micro Focus Enterprise Server](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Verificare gli aggiornamenti

Dopo l'installazione, verificare la presenza di eventuali aggiornamenti aggiuntivi, ad esempio, insieme a Enterprise Server, in diversi prerequisiti, ad esempio .NET Redistributable e .NET Framework.

### <a name="upload-the-license"></a>Caricare la licenza

1. Avviare l'amministrazione delle licenze Micro Focus.

2. Fare clic su **Avvia** \> **amministrazione licenze**di Micro Focus **License Manager** \> , quindi fare clic sulla scheda **Installa.** Ad esempio, per un file, in File di **licenza**, individuare il file **mflic** caricato in precedenza nella macchina virtuale e selezionare **Installa licenze**.

     ![Finestra di dialogo Amministrazione licenza Micro Focus](media/03-enterprise-server.png)

3. Verificare che Enterprise Server sia caricato. Provare ad avviare il sito Amministrazione <http://localhost:86/> Enterprise Server da un browser utilizzando questo URL . Viene visualizzata la pagina Amministrazione Enterprise Server come illustrato.

     ![Pagina Amministrazione di Enterprise Server](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installare Enterprise Developer nel computer di sviluppo

1. Selezionare il gruppo di risorse creato in precedenza (ad esempio, **RGMicroFocusEntServer**), quindi selezionare l'immagine dello sviluppatore.

2. Per accedere alla macchina virtuale, passare alla sezione **Panoramica** e selezionare **Connetti**. Questo accesso avvia una sessione RDP. Accedere usando le credenziali create per la macchina virtuale.

3. Dalla sessione RDP, caricare i due file seguenti (trascinare e rilasciare se lo si desidera):

    - **edvs2017.exe**, il file di installazione di Enterprise Server.

    - **mflic**, il file di licenza corrispondente (Enterprise Developer non verrà caricato senza di esso).

4. Fare doppio clic sul file **edvs2017.exe** per avviare l'installazione. Nella prima finestra, selezionare il percorso di installazione e accettare il contratto di licenza con l'utente finale. Se lo si desidera, scegliere **Installa Rumba 9.5** per installare questo emulatore di terminale, di cui probabilmente necessario.

     ![Finestra di dialogo Installazione di Micro Focus Enterprise Developer for Visual Studio 2017](media/04-enterprise-server.png)

5. Al termine dell'installazione, viene visualizzato il seguente messaggio:

     ![Messaggio di installazione riuscita](media/05-enterprise-server.png)

6. Avviare Micro Focus License Manager come per Enterprise Server. Scegliere **Avvia** \> **amministrazione licenze**di License Manager \> Micro **Focus** e fare clic sulla scheda **Installa.**

7. Scegliere il tipo di formato di licenza da caricare: un file di licenza o un codice di licenza di 16 caratteri. Ad esempio, per un file, in File di **licenza**, individuare il file **mflic** caricato in precedenza nella macchina virtuale e selezionare **Installa licenze**.

     ![Finestra di dialogo Amministrazione licenza Micro Focus](media/07-enterprise-server.png)

Quando Enterprise Developer viene caricato, la distribuzione di un ambiente di sviluppo e test Micro Focus in Azure è completa.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'applicazione Bank Demo](./demo.md)
- [Eseguire Enterprise Server nei contenitori DockerRun Enterprise Server in Docker containers](./run-enterprise-server-container.md)
- [Migrazione delle applicazioni mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
