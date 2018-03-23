---
title: Eseguire il provisioning di una macchina virtuale con intelligenza artificiale geografica in Azure | Microsoft Docs
description: Come eseguire il provisioning di una macchina virtuale con intelligenza artificiale geografica in Azure.
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
ms.author: gokuma
ms.openlocfilehash: 2994ef858e960640d98ab2f7d02a401b11aa9e8f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Eseguire il provisioning di una macchina virtuale con intelligenza artificiale geografica in Azure 

La macchina virtuale di data science con intelligenza artificiale geografica (Geo-DSVM) è una speciale estensione della diffusa [macchina virtuale di data science di Azure](http://aka.ms/dsvm) configurata per combinare intelligenza artificiale e analisi geospaziale. L'analisi geospaziale nella macchina virtuale è basata sulla tecnologia [ArcGIS Pro](https://www.arcgis.com/features/index.html). La macchina virtuale di data science rende possibile il training rapido dei modelli di apprendimento automatico e dei modelli di apprendimento avanzato sulla base di dati arricchiti con informazioni geografiche. È supportata solo su DSVM Windows 2016. 

La macchina virtuale Geo-DSVM contiene diversi strumenti per l'intelligenza artificiale, tra cui:

- edizioni GPU dei framework di apprendimento avanzato più diffusi, ad esempio Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer 
- strumenti per l'acquisizione e la pre-elaborazione di immagini e dati testuali 
- strumenti per attività di sviluppo, ad esempio Microsoft R Server Developer Edition, Python Anaconda, Jupyter Notebook per Python e R, IDE per Python e R, database SQL
- software desktop ArcGIS Pro di ESRI e interfacce Python e R utilizzabili con i dati geospaziali provenienti da applicazioni di intelligenza artificiale. 


## <a name="create-your-geo-ai-data-science-vm"></a>Creare una macchina virtuale di data science con intelligenza artificiale geografica

Ecco la procedura per creare un'istanza della VM di data science con intelligenza artificiale geografica: 


1. Passare alla macchina virtuale nel [portale di Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Fare clic sul pulsante **Crea** nella parte inferiore per visualizzare una procedura guidata.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. La procedura guidata usata per creare la macchina virtuale Geo-DSVM richiede **input** per ognuno dei **quattro passaggi** elencati in ordine numerico sul lato destro della figura. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:



   - **Nozioni di base**

      1. **Nome**: nome del server di data science che si sta creando.

      2. **Nome utente**: ID di accesso dell'account amministratore.

      3. **Password**: password dell'account amministratore.

      4. **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.

      5. **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse di Azure oppure usarne uno **vuoto** esistente nella sottoscrizione.

      6. **Location**: selezionare la posizione del data center più appropriata. In genere è il data center contenente la maggior parte dei dati o più vicino alla posizione fisica per garantire la massima velocità di accesso alla rete. Se è necessario l'apprendimento avanzato su GPU, scegliere una posizione in Azure che contenga istanze di macchine virtuali GPU serie NC. Attualmente le posizioni che contengono macchine virtuali GPU sono: **Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale**. Per l'elenco più recente, vedere la pagina [Prodotti Azure per area](https://azure.microsoft.com/regions/services/) e cercare **serie NC** in **Calcolo**. 


   - **Impostazioni**: selezionare una macchina virtuale GPU serie NC se si intende eseguire l'apprendimento avanzato sulla macchina virtuale Geo-DSVM. In caso contrario, si può scegliere una delle istanze basate su CPU.  Creare un account di archiviazione per la macchina virtuale. 
   
   - **Riepilogo**: verificare che tutte le informazioni immesse siano corrette.

   - **Acquista**: fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni del servizio. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size** . 

>[!NOTE]
> Per il provisioning sono necessari circa 20-30 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Come accedere alla macchina virtuale di data science con intelligenza artificiale geografica

Una volta creata la VM, si è pronti per iniziare a usare gli strumenti installati e preconfigurati nella VM. Sono disponibili riquadri del menu di avvio e icone del desktop per molti strumenti. È possibile connettersi tramite desktop remoto con le credenziali dell'account amministratore configurato nella sezione **Nozioni di base** precedente. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Uso del software ArcGIS Pro installato nella macchina virtuale

Nella macchina virtuale Geo-DSVM è preinstallato il software ArcGIS Pro desktop e l'ambiente è preconfigurato per funzionare con tutti gli strumenti nella DSVM. Quando si avvia ArcGIS vengono chieste le credenziali di accesso all'account ArcGIS. Se si dispone già di un account ArcGIS e di licenze per il software, è possibile usare le credenziali esistenti.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

In caso contrario, è possibile iscriversi per ricevere un nuovo account ArcGIS e una licenza o ottenere una [versione di valutazione gratuita](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Dopo aver effettuato l'iscrizione per un account ArcGIS a pagamento o di valutazione gratuita, è possibile autorizzare ArcGIS Pro per l'account seguendo le istruzioni nella [documentazione introduttiva di ArcGIS Pro](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Una volta eseguito l'accesso ad ArcGIS Pro desktop con l'account ArcGIS, si è pronti per iniziare a usare gli strumenti di data science installati e configurati nella macchina virtuale per progetti di analisi geospaziale e apprendimento automatico.

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare la macchina virtuale di data science con intelligenza artificiale geografica facendo riferimento agli argomenti seguenti:

* [Usare la macchina virtuale di data science con intelligenza artificiale geografica](use-geo-ai-dsvm.md)