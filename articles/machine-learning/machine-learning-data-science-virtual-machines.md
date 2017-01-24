---
title: Macchine virtuali per l&quot;analisi scientifica dei dati in Azure | Documentazione Microsoft
description: Configurare una macchina virtuale per l&quot;analisi scientifica dei dati
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 95e1fa87-794a-4d03-80a4-af4f3f3ac31e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: xibingao;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: 4de0683f2f58cc598a5c37e74a30b7e17aa7fd0d


---
# <a name="data-science-virtual-machines-in-azure"></a>Macchine virtuali per l'analisi scientifica dei dati in Azure
Vengono fornite istruzioni che descrivono come configurare una VM di Azure e una VM di Azure con il servizio SQL come server di IPython Notebook. La macchina virtuale Windows è configurata con strumenti di supporto come IPython Notebook, Azure Storage Explorer e AzCopy, nonché altre utilità per progetti di analisi scientifica dei dati. Ad esempio, Esplora archivi Azure e AzCopy forniscono modi efficaci per caricare dati nella memoria di Azure dal computer locale o per scaricarli dalla memoria nel computer locale. 

Questo menu include collegamenti ad argomenti che descrivono come configurare i diversi ambienti di analisi scientifica dei dati usati dal [processo di analisi scientifica dei dati per i team (TDSP)](data-science-process-overview.md).

[!INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

È possibile eseguire il provisioning di diversi tipi di macchine virtuali di Azure e configurarle per usarle come parte di un ambiente di analisi scientifica dei dati basato su cloud. La scelta della versione di macchina virtuale da usare dipende dal tipo e dalla quantità di dati da modellare con l'apprendimento automatico e dalla destinazione di quei dati nel cloud. 

* Per informazioni aggiuntive sulle questioni da prendere in considerazione per prendere questa decisione, vedere [Pianificazione dell'ambiente di analisi scientifica dei dati di Azure Machine Learning](machine-learning-data-science-plan-your-environment.md). 
* Per un catalogo di alcuni degli scenari che potrebbero verificarsi quando si esegue l'analisi avanzata, vedere l'argomento relativo agli [scenari di Advanced Analytics Process and Technology in Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md)

Sono disponibili due set di istruzioni:

* [Configurazione di una macchina virtuale di Azure per l'analisi scientifica dei dati](machine-learning-data-science-setup-virtual-machine.md) viene illustrato come eseguire il provisioning di una macchina virtuale di Azure con IPython Notebook e altri strumenti usati per l'analisi scientifica dei dati nei casi in cui è possibile usare una forma di risorsa di archiviazione di Azure diversa da SQL per archiviare i dati.
* In [Configurazione di una macchina virtuale di SQL Server di Azure per l'analisi scientifica dei dati](machine-learning-data-science-setup-sql-server-virtual-machine.md) viene illustrato come eseguire il provisioning di una macchina virtuale di Azure SQL Server con IPython Notebook e altri strumenti usati per l'analisi scientifica dei dati nei casi in cui è possibile usare un database SQL per archiviare i dati.

Dopo il provisioning e la configurazione, queste macchine virtuali possono essere usate come server di IPython Notebook per la navigazione e l'elaborazione dei dati e per altre attività legate ad Azure Machine Learning e al processo di analisi scientifica dei dati per i team. I passaggi successivi del processo di analisi scientifica dei dati sono illustrati nell'articolo dedicato al [percorso di apprendimento del processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e possono includere i passaggi per lo spostamento dei dati in SQL Server o HDInsight, la loro elaborazione e campionamento in preparazione dell'apprendimento dai dati con Azure Machine Learning.

> [!NOTE]
> Macchine virtuali di Azure è disponibile con **pagamento a consumo**. Per assicurarsi di non subire addebiti quando non si usa la macchina virtuale, lo stato deve essere impostato su **Arrestato (deallocato)** dal [portale di Azure classico](http://manage.windowsazure.com/). Per istruzioni dettagliate su come deallocare la macchina virtuale, vedere [Arresto e deallocazione della macchina virtuale quando non in uso](machine-learning-data-science-setup-virtual-machine.md#shutdown).
> 
> 




<!--HONumber=Dec16_HO3-->


