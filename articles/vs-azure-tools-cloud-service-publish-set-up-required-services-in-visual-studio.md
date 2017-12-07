---
title: Preparare la pubblicazione o la distribuzione di un servizio cloud in Visual Studio | Microsoft Docs
description: Informazioni sulle procedure per la configurazione di servizi cloud e dell'account di archiviazione e per la configurazione dell'applicazione Azure.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: kraigb
ms.openlocfilehash: 5b2043ada193e5a08957656e153b58a61e7eba9e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Preparare la pubblicazione o la distribuzione di un servizio cloud in Visual Studio

Per pubblicare un progetto di servizio cloud, è necessario configurare i servizi seguenti come descritto in questo articolo:

* Un **servizio cloud** per l'esecuzione dei ruoli nell'ambiente Azure. 
* Un **account di archiviazione** che fornisce l'accesso ai servizi BLOB, code e tabelle.

## <a name="create-a-cloud-service"></a>Creare un servizio cloud

Un servizio cloud esegue i ruoli nell'ambiente Azure. È possibile creare un servizio cloud in Visual Studio oppure tramite il [portale di Azure](https://portal.azure.com/) come descritto nelle sezioni seguenti.

### <a name="create-a-cloud-service-from-visual-studio"></a>Creare un servizio cloud in Visual Studio

1. Con un progetto di servizio cloud creato in precedenza, fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.
1. Se necessario, accedere con l'account Microsoft o aziendale associato alla sottoscrizione di Azure, quindi selezionare **Avanti** per passare alla pagina **Impostazioni**.
1. Verrà visualizzata la finestra di dialogo **Crea servizio cloud e account di archiviazione** (in caso contrario, selezionare **Crea nuovo** dall'elenco **Servizio cloud**).
1. Immettere un nome per il servizio cloud senza distinzione tra maiuscole e minuscole. Il nome deve essere compreso nell'URL ed essere univoco. Scegliere anche un'area o un gruppo di affinità e selezionare un'opzione di replica.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Creare un servizio cloud tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Servizi cloud (versione classica)** sul lato sinistro della pagina.
1. Selezionare **+ Aggiungi**, quindi specificare le informazioni obbligatorie (nome DNS, sottoscrizione, gruppo di risorse e posizione). Non è necessario caricare un pacchetto in questo momento poiché questa operazione verrà eseguita successivamente in Visual Studio.
1. Fare clic su **Crea** per completare la procedura.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione fornisce l'accesso ai servizi BLOB, code e tabelle. È possibile creare un account di archiviazione tramite Visual Studio o il [portale di Azure](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Creare un account di archiviazione in Visual Studio

1. In **Esplora soluzioni**, con un progetto di servizio cloud creato in precedenza, individuare il nodo **Servizi connessi** all'interno di un progetto di ruolo, fare clic con il pulsante destro del mouse e scegliere **Aggiungi servizio connesso**. In Visual Studio 2015 fare clic con il pulsante destro del mouse sul nodo **Archiviazione** e scegliere **Crea account di archiviazione**.
1. Nell'elenco **Servizi connessi** che viene visualizzato selezionare **Archiviazione cloud con Archiviazione di Azure**.
1. Nella finestra di dialogo Archiviazione di Azure che viene visualizzata selezionare **+Crea un nuovo account di archiviazione**. Verrà visualizzata una finestra di dialogo in cui specificare la sottoscrizione, un nome per l'account, un piano tariffario, un gruppo di risorse e una posizione.
1. Al termine, fare clic su **Crea**. Il nuovo account di archiviazione verrà visualizzato nell'elenco degli account di archiviazione disponibili nella sottoscrizione.
1. Selezionare l'account e fare clic su **Aggiungi**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Creare un account di archiviazione tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **+ Nuovo** in alto a sinistra.
1. Selezionare **Archiviazione** in Azure Marketplace, quindi **Account di archiviazione: BLOB, File, Tabelle, Code** sul lato destro.
1. Specificare le informazioni obbligatorie (nome, modello di distribuzione e così via).
1. Fare clic su **Crea** per completare la procedura.

## <a name="configure-your-app-to-use-the-storage-account"></a>Configurare l'app per l'uso dell'account di archiviazione

Dopo aver creato un account di archiviazione e averne definito le connessioni in Visual Studio, vengono aggiornate automaticamente le configurazioni del servizio per il progetto, inclusi gli URL e le chiavi di accesso.

Se è stato creato un servizio cloud in Visual Studio usando **Aggiungi servizio connesso**, è possibile verificare le connessioni aprendo `ServiceConfiguration.Cloud.cscfg` e `ServiceConfiguration.Local.cscfg`.

Se è stato creato un servizio cloud tramite il portale di Azure, seguire la stessa procedura descritta in [Creare un account di archiviazione in Visual Studio](#create-a-storage-account-from-visual-studio) selezionando l'account esistente anziché creandone uno nuovo. Visual Studio aggiornerà quindi la configurazione per l'utente.

Per configurare le impostazioni manualmente, usare le pagine delle proprietà in Visual Studio per il ruolo applicabile nel progetto di servizio cloud (fare clic con il pulsante destro del mouse sul ruolo e scegliere **Proprietà**). Per altre informazioni, vedere [Configurazione di una stringa di connessione in un account di archiviazione](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Chiavi di accesso

Nel portale di Azure vengono visualizzati gli URL che è possibile usare per accedere alle risorse di ogni servizio di archiviazione di Azure, nonché le chiavi di accesso primaria e secondaria dell'account. Questi tasti possono essere usati per autenticare le richieste eseguite in relazione ai servizi di archiviazione.

La chiave di accesso secondaria fornisce lo stesso accesso all'account di archiviazione offerto da quella primaria e viene generata come backup nel caso in cui la chiave di accesso primaria dovesse essere compromessa. È anche consigliabile rigenerare regolarmente le chiavi di accesso. È possibile modificare l'impostazione di una stringa di connessione per usare la chiave secondaria mentre si rigenera quella primaria, quindi procedere nuovamente alla modifica per usare la chiave primaria rigenerata mentre si rigenera quella secondaria.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla pubblicazione di app in Azure da Visual Studio, vedere [Pubblicazione di un servizio Cloud con gli strumenti di Azure](vs-azure-tools-publishing-a-cloud-service.md).
