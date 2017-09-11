---
title: Configurazione delle credenziali per l'autenticazione denominate | Documentazione Microsoft
description: "Informazioni su come fornire le credenziali che Visual Studio può usare per l'autenticazione delle richieste inviate a Azure per pubblicare un'applicazione in Azure da Visual Studio o monitorare un servizio cloud esistente. "
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
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c486676a70e195ec85ad40540ea4b7caaa86bc48
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="setting-up-named-authentication-credentials"></a>Configurazione delle credenziali per l'autenticazione denominate
Per pubblicare un'applicazione in Azure da Visual Studio o monitorare un servizio cloud esistente, è necessario fornire le credenziali che Visual Studio può usare per l'autenticazione delle richieste inviate a Azure. In Visual Studio è possibile fornire queste credenziali accedendo a più posizioni. Ad esempio, da Esplora server è possibile aprire il menu di scelta rapida per il nodo **Azure** e scegliere **Connessione alla sottoscrizione di Microsoft Azure**. Dopo l'accesso, le informazioni sulla sottoscrizione associate all'account Azure sono disponibili in Visual Studio. Non è necessario eseguire altre operazioni.

Gli strumenti di Azure supportano anche un modo meno recente di fornire le credenziali, attraverso il file di sottoscrizione, con estensione publishsettings. Questo argomento illustra tale metodo, ancora supportato in Azure SDK 2.2.

Per l'autenticazione in Azure sono richiesti gli elementi seguenti:

* ID sottoscrizione
* Un certificato X.509 v3 valido

> [!NOTE]
> La lunghezza della chiave del certificato X.509 v3 deve essere di almeno 2048 bit. Azure rifiuterà qualsiasi certificato che non soddisfa questo requisito o che sia non valido.
>
>

Visual Studio usa l'ID sottoscrizione e i dati del certificato come credenziali. Nel file di sottoscrizione, con estensione publishsettings, che contiene una chiave pubblica per il certificato, viene fatto riferimento alle credenziali appropriate. Il file di sottoscrizione può contenere le credenziali per più di una sottoscrizione.

Per modificare le informazioni sulla sottoscrizione è possibile usare le finestre di dialogo **Nuova sottoscrizione e Modifica sottoscrizione** , come illustrato più avanti in questo argomento.

Se si vuole creare un certificato, vedere le istruzioni contenute in [Creare e caricare un certificato di gestione per Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e quindi caricare manualmente il certificato nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Le credenziali richieste da Visual Studio per gestire i servizi cloud non sono le stesse necessarie per autenticare una richiesta per i servizi di archiviazione di Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importare, configurare o modificare le credenziali di autenticazione in Visual Studio
È possibile importare, impostare o modificare le credenziali di autenticazione nella finestra di dialogo **Nuova sottoscrizione**, visualizzata dopo l'azione seguente:

* In **Esplora server** aprire il menu di scelta rapida per il nodo **Azure**, scegliere **Gestisci e filtra sottoscrizioni**, scegliere la scheda **Certificati**, quindi procedere in uno dei modi seguenti:

    * Scegliere **Importa** per aprire la finestra di dialogo Importa sottoscrizioni Microsoft Azure, in cui è possibile scaricare il file delle sottoscrizioni per la sottoscrizione attualmente caricata, sfogliare il relativo percorso di download, quindi importarlo per l'uso in autenticazione.
    * Scegliere **Nuovo** per aprire la finestra di dialogo Nuova sottoscrizione, in cui è possibile impostare una nuova sottoscrizione per l'uso in autenticazione.
    * Scegliere **Modifica** (dopo aver scelto la sottoscrizione attiva) per aprire la finestra di dialogo Modifica sottoscrizione, in cui è possibile modificare una sottoscrizione esistente per l'uso in autenticazione. 

La procedura riportata di seguito presuppone che la finestra di dialogo **Nuova sottoscrizione** sia visualizzata.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Per configurare le credenziali di autenticazione in Visual Studio
1. Scegliere un certificato dall'elenco **Selezionare un certificato esistente per l'autenticazione** .
2. Scegliere il collegamento **Copia il percorso completo**. Il percorso del certificato, ovvero il file con estensione cer, viene copiato negli Appunti.

   > [!IMPORTANT]
   > Per pubblicare l'applicazione Azure da Visual Studio, è necessario caricare questo certificato nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Per caricare il certificato nel portale di Azure:

   1. Aprire il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   2. Se richiesto, accedere al portale e quindi passare a **Impostazioni**, **Certificati di gestione**.
   3. Nel riquadro Certificati di gestione, scegliere **Carica**.
   4. Selezionare la sottoscrizione di Azure, incollare il percorso completo del file con estensione .cer appena creato e scegliere **Carica**.

