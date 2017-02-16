---
title: Creazione di un progetto Azure con Visual Studio | Documentazione Microsoft
description: Creazione di un progetto Azure con Visual Studio
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d933117cf513436e04c79a335998f2ddba848dc4


---
# <a name="creating-an-azure-project-with-visual-studio"></a>Creazione di un progetto Azure con Visual Studio
Gli strumenti di Azure per Visual Studio forniscono un modello per la creazione di un servizio cloud per Azure e consentono inoltre di configurare, eseguire il debug e distribuire il servizio cloud in Azure.

Una soluzione di servizio cloud di Azure contiene i tipi di progetto seguenti:

* **Progetto Azure**
  
    Il progetto Azure contiene le associazioni ai progetti di ruolo nella soluzione. Include anche i file di definizione e di configurazione del servizio. Il file di definizione del servizio definisce le impostazioni di runtime per l'applicazione e include i ruoli richiesti, gli endpoint e le dimensioni della macchina virtuale. Il file di configurazione del servizio configura il numero delle istanze di un ruolo eseguite e i valori delle impostazioni definiti per un ruolo. Per altre informazioni su queste impostazioni, vedere [Procedura: Configurare i ruoli di un servizio cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
* **Progetto di ruolo Web**
  
    Un ruolo di lavoro esegue l'elaborazione in background, può comunicare con i servizi di archiviazione e con altri servizi basati su Internet. Un ruolo di lavoro può disporre di qualsiasi numero di endpoint HTTP, HTTPS o TCP.
  
  * **Ruolo Web ASP.NET**: per la compilazione di un'applicazione ASP.NET con un front-end Web
  * **Ruolo Web ASP.NET MVC5**
  * **Ruolo Web ASP.NET MVC4**
  * **Ruolo Web ASP.NET MVC3**
  * **Ruolo Web servizio WCF**: per la compilazione di un servizio WCF
  * **Ruolo Web dell'applicazione aziendale di Silverlight** (richiede Visual Studio 2012)
* **Ruolo di lavoro cache**
  
    Un ruolo che offre all'applicazione una cache dedicata.
* **Ruolo di lavoro con coda del bus di servizio**
  
    La coda del bus di servizio fornisce la funzionalità di accodamento dei messaggi nella comunicazione con il processo di lavoro. Per altre informazioni, vedere [Come usare le code del bus di servizio](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Per creare un progetto di servizio cloud di Azure in Visual Studio
1. Avviare Microsoft Visual Studio come amministratore.
2. Nella barra dei menu scegliere **File**, **Nuovo**, **Progetto**.
3. Nel riquadro **Tipi di progetto** scegliere **Cloud** dai nodi del modello di progetto Visual C# o Visual Basic.
4. Nel riquadro **Modelli** scegliere **Servizio cloud di Azure**.
5. Specificare la versione di .NET Framework che si vuole usare per sviluppare il progetto.
6. Immettere un nome e un percorso per il progetto e un nome per la soluzione. Fare clic su **OK** .
7. Nella finestra di dialogo **Nuovo progetto Azure** scegliere i ruoli da aggiungere e fare clic sul pulsante con la freccia destra per aggiungerli alla soluzione. È possibile aggiungere tutti i ruoli necessari.
8. Per rinominare un ruolo aggiunto al progetto, posizionarsi sul ruolo nella finestra di dialogo **Nuovo progetto Azure** e scegliere l'icona **Rinomina** a destra del ruolo. Una volta aggiunto, un ruolo può essere rinominato anche nella soluzione.




<!--HONumber=Nov16_HO3-->


