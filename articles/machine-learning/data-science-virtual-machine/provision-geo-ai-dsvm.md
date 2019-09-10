---
title: "Guida introduttiva: Configurare l'intelligenza artificiale geografica"
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come creare e configurare la Data Science Virtual Machine con intelligenza artificiale geografica. La Data Science Virtual Machine con intelligenza artificiale geografica offre gli strumenti necessari per creare soluzioni di intelligenza artificiale e Machine Learning usando i dati geografici.
keywords: apprendimento avanzato, AI, strumenti di data science, macchina virtuale per data science, analisi geospaziale
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/05/2018
ms.openlocfilehash: 7afeec3f71cd1af30093801fedabf3f0357ae3d0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208052"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Guida introduttiva: Configurare una macchina virtuale con intelligenza artificiale geografica in Azure 

La Geo AI Data Science Virtual Machine (Geo-DSVM) è una speciale estensione della famosa [Azure Data Science Virtual Machine](https://aka.ms/dsvm) configurata specificamente per combinare intelligenza artificiale e analisi geospaziale. L'analisi geospaziale nella macchina virtuale è basata sulla tecnologia [ArcGIS Pro](https://www.arcgis.com/features/index.html). La Data Science Virtual Machine (DSVM) consente il training rapido dei modelli di Machine Learning e anche Deep Learning. Per sviluppare questi modelli, vengono usati dati arricchiti con informazioni geografiche. La Geo-DSVM è supportata solo nella DSVM Windows 2016. 

Gli strumenti di intelligenza artificiale inclusi nella Geo-DSVM includono quanto segue:

- Edizioni GPU dei framework di Deep Learning più diffusi, ad esempio Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 e Chainer
- Strumenti per l'acquisizione e la pre-elaborazione di immagini e dati testuali
- Strumenti per attività di sviluppo, come Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter Notebook per Python e R, IDE per Python e R e database SQL
- Software desktop ArcGIS Pro di ESRI, con interfacce Python e R utilizzabili con i dati geospaziali provenienti da applicazioni di intelligenza artificiale
 

## <a name="create-your-geo-ai-data-science-vm"></a>Creare una macchina virtuale di data science con intelligenza artificiale geografica

Per creare un'istanza della Geo AI Data Science VM, procedere come segue:

1. Andare all'elenco di macchine virtuali nel [portale di Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Selezionare **Crea** nella parte inferiore per generare una procedura guidata:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. La procedura guidata richiede un input per ognuno dei quattro passaggi. Per informazioni dettagliate su questo input, vedere la sezione seguente.

### <a name="wizard-details"></a>Dettagli della procedura guidata ###

**Nozioni di base**:

- **Nome**: Nome del server di data science che si sta creando.
    
- **User name** (Nome utente): ID di accesso dell'account amministratore.
    
- **Password**: password dell'account amministratore.
    
- **Sottoscrizione** Se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.
    
- **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse di Azure oppure usarne uno **vuoto** esistente nella sottoscrizione.
    
- **Località**: selezionare il data center più appropriato. In genere è quello che include la maggior parte dei dati o che è situato più vicino alla posizione fisica per l'accesso più veloce alla rete. Se si prevede di eseguire Deep Learning in una GPU, è necessario scegliere una delle posizioni di Azure che include istanze di VM GPU serie NC. Attualmente le posizioni sono: **Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale**. Per l'elenco più recente, vedere la pagina [Prodotti Azure per area](https://azure.microsoft.com/regions/services/) e cercare **serie NC** in **Calcolo**. 
    
    
**Impostazioni**: selezionare una delle dimensioni di macchine virtuali GPU serie NC se si prevede di eseguire processi di Deep Learning in una GPU nella Geo-DSVM. In caso contrario, si può scegliere una delle istanze basate su CPU. Creare un account di archiviazione per la macchina virtuale. 
       
**Riepilogo**: Verificare che tutte le informazioni immesse siano corrette.
    
**Acquisto**: Per avviare il processo di provisioning, fare clic su **Acquista**. Viene fornito un collegamento alle condizioni del servizio. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per le risorse di calcolo per le dimensioni del server scelte nel passaggio **Dimensioni**. 
 
 >[!NOTE]
 > Il provisioning dovrebbe richiedere circa 20-30 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Come accedere alla macchina virtuale di data science con intelligenza artificiale geografica

 Una volta creata la VM, è possibile iniziare a usare gli strumenti installati e preconfigurati al suo interno. Per molti strumenti, sono disponibili riquadri del menu Start e icone del desktop. È possibile accedere alla VM tramite Desktop remoto usando le credenziali dell'account amministratore configurato nella sezione **Informazioni di base**.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Uso del software ArcGIS Pro installato nella macchina virtuale

Nella Geo-DSVM è preinstallato il software ArcGIS Pro desktop e l'ambiente è preconfigurato per funzionare con tutti gli strumenti inclusi nella DSVM. Quando si avvia ArcGIS, vengono richieste le credenziali per l'account ArcGIS. Se si dispone già di un account ArcGIS e di licenze per il software, è possibile usare le credenziali esistenti.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

In caso contrario, è possibile iscriversi per ricevere un nuovo account ArcGIS e una licenza o ottenere una [versione di valutazione gratuita](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Dopo aver effettuato l'iscrizione per un account ArcGIS standard o di valutazione gratuita, è possibile autorizzare ArcGIS Pro per l'account seguendo le istruzioni riportate nella [documentazione introduttiva di ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Una volta eseguito l'accesso ad ArcGIS Pro desktop con l'account ArcGIS, è possibile iniziare a usare gli strumenti di data science installati e configurati nella VM per progetti di analisi geospaziale e Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare la Geo AI Data Science VM facendo riferimento alla risorsa seguente:

* [Usare la macchina virtuale di data science con intelligenza artificiale geografica](use-geo-ai-dsvm.md)
