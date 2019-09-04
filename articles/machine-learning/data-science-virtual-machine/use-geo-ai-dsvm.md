---
title: Usare l'intelligenza artificiale geografica
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
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278424"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Uso della macchina virtuale di data science con intelligenza artificiale geografica

Usare la VM di data science con intelligenza artificiale geografica per recuperare dati per l'analisi, eseguire il wrangling dei dati e compilare modelli per applicazioni per l'intelligenza artificiale che usano informazioni geospaziali. Dopo aver effettuato il provisioning dell'intelligenza artificiale geografica Data Science VM e aver eseguito l'accesso a ArcGIS Pro tramite l'account ArcGIS, è possibile iniziare a interagire con ArcGIS Desktop e ArcGIs online. È anche possibile accedere a ArcGIS dalle interfacce Python e da un Bridge di linguaggio R preconfigurato nella Data Science VM geografica. Per creare applicazioni di intelligenza artificiale avanzate, combinare la Data Science VM geografica con i Framework di apprendimento automatico e Deep Learning e altri software data science disponibili.  


## <a name="configuration-details"></a>Dettagli configurazione

La libreria Python, [ArcPy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), che viene usata per interfacciarsi con ArcGIS, viene installata nell'ambiente conda radice globale del Data Science VM trovato in ```c:\anaconda```.

- Se si esegue Python al prompt dei comandi, eseguire ```activate``` per attivarlo nell'ambiente Python radice conda.
- Se si usa un notebook IDE o Jupyter, è possibile selezionare l'ambiente o il kernel per assicurarsi di trovarsi nell'ambiente conda corretto.

Il Bridge R per ArcGIS viene installato come libreria R denominata [ArcGIS](https://github.com/R-ArcGIS/r-bridge) nell'istanza autonoma Microsoft Machine Learning server che si trova in ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, rstudio e Jupyter sono già preconfigurati per l' ```arcgisbinding``` uso di questo ambiente r e avranno accesso alla libreria r. 


## <a name="geo-ai-data-science-vm-samples"></a>Esempi di macchina virtuale di data science con intelligenza artificiale geografica

Oltre agli esempi basati su Framework di apprendimento automatico e di apprendimento avanzato del Data Science VM di base, viene fornito anche un set di esempi geospaziali come parte della Data Science VM di intelligenza artificiale geografica. Questi esempi possono aiutare ad avviare lo sviluppo di applicazioni di intelligenza artificiale usando i dati geospaziali e il software ArcGIS:


1. [Introduzione all'analisi geospaziale con Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Un esempio introduttivo che illustra come usare i dati geospaziali tramite l'interfaccia Python per ArcGIS è fornito dalla libreria [ArcPy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Viene anche illustrato come combinare l'apprendimento automatico tradizionale con i dati geospaziali e quindi visualizzare il risultato su una mappa in ArcGIS.

2. [Introduzione all'analisi geospaziale con R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Esempio introduttivo che illustra come usare i dati geospaziali usando l'interfaccia R in ArcGIS fornito dalla libreria [ArcGIS](https://github.com/R-ArcGIS/r-bridge) . 

3. [Classificazione dell'uso del suolo a livello di pixel](https://github.com/Azure/pixel_level_land_classification): Esercitazione che illustra come creare un modello di rete neurale profonda che accetta un'immagine aerea come input e restituisce un'etichetta relativa alla copertura del suolo. Esempi di etichette di copertura fondiaria sono *foreste* e *acqua*. Il modello restituisce tale etichetta per ogni pixel dell'immagine. 


## <a name="next-steps"></a>Passaggi successivi

Per altri esempi che usano la VM di data science, vedere qui:

* [Esempi](dsvm-samples-and-walkthroughs.md)