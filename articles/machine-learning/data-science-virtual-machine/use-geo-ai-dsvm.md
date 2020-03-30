---
title: Utilizzare l'IA geografica
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come usare la Data Science Virtual Machine Geo intelligenza artificiale per analizzare i dati e costruire modelli basati su dati geospaziali.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278424"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Uso della macchina virtuale di data science con intelligenza artificiale geografica

Usare la VM di data science con intelligenza artificiale geografica per recuperare dati per l'analisi, eseguire il wrangling dei dati e compilare modelli per applicazioni per l'intelligenza artificiale che usano informazioni geospaziali. Dopo aver eseguito il provisioning della macchina virtuale di analisi dei dati Geo AI e aver eseguito l'accesso ad ArcGIS Pro tramite l'account ArcGIS, è possibile iniziare a interagire con il desktop ArcGIS e gli ArcGIs online. È anche possibile accedere ad ArcGIS da interfacce Python e da un bridge del linguaggio R preconfigurato nella macchina virtuale di analisi dei dati geografici. Per creare applicazioni di analisi dei dati avanzate, combinare la macchina virtuale di analisi dei dati geografici con i framework di apprendimento automatico e deep-learning e altri software di data science disponibili in essa.  


## <a name="configuration-details"></a>Dettagli di configurazione

La libreria Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), utilizzata per l'interfaccia con ArcGIS, viene installata nell'ambiente ```c:\anaconda```conda radice globale della macchina virtuale data science disponibile in .

- Se si esegue Python al prompt ```activate``` dei comandi, eseguire per attivare l'ambiente Python radice conda.
- Se si usa un blocco appunti IDE o Jupyter, è possibile selezionare l'ambiente o il kernel per assicurarsi di essere nell'ambiente conda corretto.

Il bridge R ad ArcGIS viene installato come libreria R denominata [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) nell'istanza ```C:\Program Files\Microsoft\ML Server\R_SERVER```autonoma principale di Microsoft Machine Learning Server che si trova in . Visual Studio, RStudio e Jupyter sono già preconfigurati per l'utilizzo di questo ambiente R e avranno accesso alla libreria R.Visual Studio, RStudio, and Jupyter are already preconfigured to use this R environment and will have access to the ```arcgisbinding``` R library. 


## <a name="geo-ai-data-science-vm-samples"></a>Esempi di macchina virtuale di data science con intelligenza artificiale geografica

Oltre agli esempi basati sul framework di apprendimento automatico e di apprendimento approfondito della macchina virtuale di analisi scientifica dei dati di base, viene fornito anche un set di esempi geospaziali come parte della macchina virtuale di analisi dei dati dell'io a io GEO. Questi esempi consentono di avviare rapidamente lo sviluppo di applicazioni di ia-AI utilizzando i dati geospaziali e il software ArcGIS:


1. [Introduzione all'analisi geospaziale con Python:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)un esempio introduttivo che mostra come lavorare con i dati geospaziali tramite l'interfaccia Python per ArcGIS viene fornito dalla libreria [dell'arco.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Viene inoltre illustrato come combinare l'apprendimento automatico tradizionale con i dati geospaziali e quindi visualizzare il risultato su una mappa in ArcGIS.

2. [Introduzione all'analisi geospaziale con R:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)esempio introduttivo che illustra come usare i dati geospaziali usando l'interfaccia R per ArcGIS fornita dalla libreria [di archiviazione.](https://github.com/R-ArcGIS/r-bridge) 

3. [Classificazione dell'uso del suolo a livello di pixel](https://github.com/Azure/pixel_level_land_classification): esercitazione che illustra come creare un modello di rete neurale profonda che accetta un'immagine aerea come input e restituisce un'etichetta relativa alla copertura del suolo. Esempi di etichette di copertura del suolo sono le acque *boschie* e *le acque.* Il modello restituisce tale etichetta per ogni pixel dell'immagine. 


## <a name="next-steps"></a>Passaggi successivi

Per altri esempi che usano la VM di data science, vedere qui:

* [Esempi](dsvm-samples-and-walkthroughs.md)