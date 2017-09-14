---
title: Configurare credenziali per l'autenticazione denominate | Microsoft Docs
description: "Informazioni su come fornire le credenziali che Visual Studio può usare per l'autenticazione delle richieste ad Azure per pubblicare un'applicazione in Azure da Visual Studio o monitorare un servizio cloud esistente."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: b75d190724da284324b0337e95a50a64902e19d8
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---
# <a name="set-up-named-authentication-credentials"></a>Configurare credenziali per l'autenticazione denominate
Per pubblicare un'applicazione in Azure da Visual Studio o monitorare un servizio cloud esistente, è necessario fornire le credenziali che Visual Studio può usare per l'autenticazione delle richieste inviate a Azure. In Visual Studio è possibile fornire queste credenziali accedendo a più posizioni. Ad esempio, da Esplora server è possibile aprire il menu di scelta rapida per il nodo **Azure** e scegliere **Connessione alla sottoscrizione di Microsoft Azure**. Dopo l'accesso le informazioni sulla sottoscrizione associate all'account Azure diverranno disponibili in Visual Studio. Non è necessario eseguire altre operazioni.

Gli strumenti di Azure supportano anche un modo meno recente di fornire le credenziali, attraverso il file di sottoscrizione, con estensione publishsettings. Questo articolo illustra tale metodo, ancora supportato in Azure SDK 2.2.

Per l'autenticazione in Azure sono richiesti gli elementi seguenti:

* ID sottoscrizione
* Un certificato X.509 v3 valido

> [!NOTE]
> La lunghezza della chiave del certificato X.509 v3 deve essere di almeno 2.048 bit. Azure rifiuta qualsiasi certificato che non soddisfa questo requisito o che sia non valido.
>
>

Visual Studio usa l'ID sottoscrizione e i dati del certificato come credenziali. Nel file di sottoscrizione, con estensione publishsettings, che contiene una chiave pubblica per il certificato, viene fatto riferimento alle credenziali appropriate. Il file di sottoscrizione può contenere le credenziali per più di una sottoscrizione.

Per modificare le informazioni sulla sottoscrizione è possibile usare la finestre di dialogo **Nuova sottoscrizione** o **Modifica sottoscrizione**, come illustrato più avanti in questo articolo.

Se si vuole creare un certificato, vedere le istruzioni contenute in [Creare e caricare un certificato di gestione per Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e quindi caricare manualmente il certificato nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Le credenziali richieste da Visual Studio per gestire i servizi cloud non sono le stesse necessarie per autenticare una richiesta per i servizi di archiviazione di Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importare, configurare o modificare le credenziali di autenticazione in Visual Studio

1. In Esplora server aprire il menu di scelta rapida per il nodo **Azure** e selezionare **Gestisci e filtra sottoscrizioni**.
2. Selezionare la scheda **Certificati** e quindi usare un metodo tra i seguenti:

    * Selezionare **Importa** per aprire la finestra di dialogo **Importa sottoscrizioni Microsoft Azure**. Qui è possibile scaricare il file delle sottoscrizioni per la sottoscrizione attualmente caricata, sfogliare il relativo percorso di download e quindi importarlo per l'uso in autenticazione.
    * Selezionare **Nuovo** per aprire la finestra di dialogo **Nuova sottoscrizione**. Qui è possibile configurare una nuova sottoscrizione per l'uso nell'autenticazione.
    * Selezionare **Modifica** (dopo aver scelto la sottoscrizione attiva) per aprire la finestra di dialogo **Modifica sottoscrizione**. Qui è possibile modificare una sottoscrizione esistente per l'uso nell'autenticazione. 

La procedura riportata di seguito presuppone che la finestra di dialogo **Nuova sottoscrizione** sia visualizzata.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Per configurare le credenziali di autenticazione in Visual Studio
1. Scegliere un certificato dall'elenco **Selezionare un certificato esistente per l'autenticazione**.
2. Selezionare il collegamento **Copia il percorso completo**. Il percorso del certificato, ovvero il file con estensione cer, viene copiato negli Appunti.

   > [!IMPORTANT]
   > Per pubblicare l'applicazione Azure da Visual Studio, è necessario caricare questo certificato nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Per caricare il certificato nel portale di Azure:

   a. Aprire il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Se richiesto, accedere al portale e quindi passare a **Impostazioni** > **Certificati di gestione**.
   
   c. Nel riquadro **Certificati di gestione** selezionare **Carica**.
   
   d. Selezionare la sottoscrizione di Azure, incollare il percorso completo del file con estensione cer appena creato e scegliere **Carica**.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di App Web](https://docs.microsoft.com/azure/app-service-web/)
* [Distribuire l'app nel Servizio app di Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy?toc=%2fazure%2fapp-service-api%2ftoc.json) 
* [Distribuzione di processi Web usando Visual Studio](https://docs.microsoft.com/en-us/azure/app-service-web/websites-dotnet-deploy-webjobs)
* [Creare e distribuire un servizio cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)
