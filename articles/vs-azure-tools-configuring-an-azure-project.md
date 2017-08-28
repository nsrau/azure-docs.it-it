---
title: Configurare un progetto di servizio cloud di Azure con Visual Studio | Documentazione Microsoft
description: Informazioni su come configurare un progetto di servizio cloud di Azure in Visuali Studio, in base ai requisiti specifici per il progetto.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/06/2017
ms.author: kraigb
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 11a56418156a46e1fcef7b8d1c6003990000046f
ms.contentlocale: it-it
ms.lasthandoff: 03/27/2017

---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurare un progetto di servizio cloud di Azure con Visual Studio
È possibile configurare un progetto di servizio cloud di Azure, in base ai requisiti specifici per il progetto. È possibile impostare proprietà per il progetto per le categorie seguenti:

- **Pubblicare un servizio cloud in Azure** - È possibile impostare una proprietà per verificare che un servizio cloud esistente distribuito in Azure non venga eliminato inavvertitamente.
- **Eseguire un servizio cloud o eseguirne il debug nel computer locale** - È possibile selezionare una configurazione del servizio da usare e indicare se si vuole avviare l'emulatore di archiviazione di Azure.
- **Convalidare un pacchetto del servizio cloud quando viene creato** - È possibile decidere di trattare tutti gli avvisi come errori in modo da assicurarsi che il pacchetto del servizio cloud venga distribuito senza problemi. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Procedura per configurare un progetto di servizio cloud di Azure
1. Aprire o creare un progetto di servizio cloud in Visual Studio

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Proprietà** dal menu di scelta rapida.
   
1. Nella pagina delle proprietà del progetto, selezionare la scheda **Sviluppo**.

    ![Menu Proprietà del progetto](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Impostare **Chiedi conferma prima di eliminare una distribuzione esistente** su **True**. Questa impostazione aiuta a evitare l'eliminazione involontaria di una distribuzione esistente in Azure.

1. Per indicare la **Configurazione del servizio** da usare durante l'esecuzione o il debug del servizio cloud in locale, nell'elenco Configurazione servizio scegliere la configurazione del servizio. Per altre informazioni su come modificare una configurazione del servizio per un ruolo, vedere [Procedura: Configurare i ruoli di un servizio cloud di Azure con Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Per avviare l'emulatore di archiviazione di Azure durante l'esecuzione o il debug del servizio cloud in locale, in **Avvia l'emulatore di archiviazione di Microsoft Azure** scegliere **True**.

1. Per assicurarsi che non sia possibile pubblicare se sono presenti errori di convalida del pacchetto, in **Considera gli avvisi come errori** scegliere **True**.

1. Per assicurarsi che il ruolo Web usi la stessa porta a ogni avvio in locale in IIS Express, in **Usa le porte del progetto Web** scegliere **True**.

1. Dalla barra degli strumenti di Visual Studio selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
- [Configurare un progetto di servizio cloud di Azure tramite più configurazioni del servizio](vs-azure-tools-multiple-services-project-configurations.md)


