---
title: Uso della macchina virtuale di data science con intelligenza artificiale geografica - Azure | Microsoft Docs
description: Come usare una macchina virtuale con intelligenza artificiale geografica in Azure.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma; bradsev;
ms.openlocfilehash: f87b56ad2b19b10bca1e797f0a5aef5c2a92a5dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Uso della macchina virtuale di data science con intelligenza artificiale geografica

Usare la VM di data science con intelligenza artificiale geografica per recuperare dati per l'analisi, eseguire il wrangling dei dati e compilare modelli per applicazioni per l'intelligenza artificiale che usano informazioni geospaziali. Dopo avere effettuato il provisioning della VM di data science con intelligenza artificiale geografica e l'accesso ad ArcGIS Pro con l'account ArcGIS, è possibile iniziare a interagire con ArcGIS Desktop e ArcGIS Online. È anche possibile accedere ad ArcGIS dalle interfacce di Python e da un bridge in linguaggio R preconfigurato nella VM di data science geografica. Per compilare applicazioni avanzate per l'intelligenza artificiale, combinarlo con i framework di apprendimento automatico e apprendimento avanzato e con altri software di data science disponibili nella VM di data science.  


## <a name="configuration-details"></a>Dettagli di configurazione

La libreria di Python, [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), usata per interfacciarsi con ArcGIS, viene installata nell'ambiente Conda della radice globale della VM di data science, che si trova in ```c:\anaconda```. 

- Se si esegue Python in un prompt dei comandi, eseguire ```activate``` per attivarlo nell'ambiente Python della radice Conda. 
- Se si usa un ambiente di sviluppo integrato o un notebook di Jupyter, è possibile selezionare l'ambiente o il kernel per assicurarsi di essere nell'ambiente Conda corretto. 

Il bridge R per ArcGIS viene installato come libreria R denominata [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) nell'istanza autonoma di Microsoft R Server principale in ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio e Jupyter sono già preconfigurati per usare questo ambiente R e avranno accesso alla libreria R ```arcgisbinding```. 


## <a name="geo-ai-data-science-vm-samples"></a>Esempi di macchina virtuale di data science con intelligenza artificiale geografica

Oltre agli esempi basati su framework di apprendimento automatico e apprendimento avanzato della VM di data science di base, nell'ambito della VM di data science con intelligenza artificiale geografica viene anche fornito un set di esempi geospaziali. Questi esempi consentono di iniziare rapidamente lo sviluppo di applicazioni per l'intelligenza artificiale usando i dati geospaziali e il software ArcGIS. 


1. [Introduzione all'analisi geospaziale con Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): esempio introduttivo che illustra come usare i dati geospaziali con l'interfaccia di Python per ArcGIS fornita dalla libreria [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm). Illustra anche come combinare il tradizionale apprendimento automatico con i dati geospaziali e visualizzare il risultato su una mappa in ArcGIS. 

2. [Introduzione all'analisi geospaziale con R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): esempio introduttivo che illustra come usare i dati geospaziali con l'interfaccia di R per ArcGIS fornita dalla libreria [arcgisbinding](https://github.com/R-ArcGIS/r-bridge). 

3. [Classificazione dell'uso del suolo a livello di pixel](https://github.com/Azure/pixel_level_land_classification): esercitazione che illustra come creare un modello di rete neurale profonda che accetta un'immagine aerea come input e restituisce un'etichetta relativa alla copertura del suolo. Esempi di etichette indicanti la copertura del suolo sono "coperto da vegetazione" o "acqua". Il modello restituisce tale etichetta per ogni pixel dell'immagine. Il modello viene compilato usando il framework di apprendimento avanzato [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) open source di Microsoft. L'esempio illustra anche come aumentare il numero di istanze del training su [Azure Batch per intelligenza artificiale](https://docs.microsoft.com/azure/batch-ai/) e usare le stime del modello nel software ArcGIS Pro. 


## <a name="next-steps"></a>Passaggi successivi

Per altri esempi che usano la VM di data science, vedere qui:

* [Esempi](dsvm-samples-and-walkthroughs.md)

