---
title: Effettuare il provisioning di una macchina virtuale di data science per l'apprendimento avanzato in Azure | Microsoft Docs
description: Configurare e creare una macchina virtuale di data science per l'apprendimento avanzato in Azure per l'analisi e l'apprendimento automatico.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 4c911937226591124404c95b73b82b78e76649ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Effettuare il provisioning di una macchina virtuale per l'apprendimento avanzato in Azure 

La macchina virtuale per l'apprendimento avanzato (DLVM, Deep Learning Virtual Machine) è una variante configurata in modo particolare della più diffusa [macchina virtuale per data science](http://aka.ms/dsvm) (DSVM, Data Science Virtual Machine). Tale variante semplifica l'uso delle istanze di VM basate su GPU per il training rapido dei modelli di apprendimento avanzato. Supportata dalla DSVM Windows 2016 o Ubuntu, la DLVM condivide le stesse immagini di macchina virtuale di base e quindi tutti i set di strumenti avanzati disponibili nella DSVM. 

La DLVM contiene diversi strumenti per intelligenza artificiale, incluse le edizioni GPU dei framework di apprendimento avanzato più diffusi, ad esempio Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer, nonché strumenti per l'acquisizione e la pre-elaborazione di immagini e dati testuali, strumenti per attività di modellazione e sviluppo di data science, ad esempio Microsoft R Server Developer Edition, Python Anaconda, Jupyter Notebook per Python e R, IDE per Python e R, database SQL e molti altri strumenti di data science e Machine Learning. 

## <a name="create-your-deep-learning-virtual-machine"></a>Creare una macchina virtuale per l'apprendimento avanzato
Ecco i passaggi necessari per creare un'istanza della macchina virtuale per l'apprendimento avanzato: 

1. Passare alla macchina virtuale nel [portale di Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Fare clic sul pulsante **Crea** nella parte inferiore per visualizzare una procedura guidata.![create-dlvm](./media/dlvm-provision-wizard.PNG)
3. La procedura guidata usata per creare la DLVM richiede **input** per ognuno dei **quattro passaggi** elencati in ordine numerico sul lato destro della figura. Di seguito sono riportati gli input necessari per configurare ciascuno di questi passaggi:
   
   1. **Nozioni di base**
      
      1. **Name**: nome del server di analisi scientifica dei dati che si sta creando.
      2. **Select OS type for the Deep Learning VM** (Selezionare il tipo di sistema operativo per la VM di apprendimento avanzato): scegliere Windows o Linux (per la DSVM di base Windows 2016 e Linux Ubuntu)
      2. **Nome utente**: ID di accesso dell'account amministratore.
      3. **Password**: password dell'account amministratore.
      4. **Sottoscrizione**: se si ha più di una sottoscrizione, selezionare quella in cui viene creata e fatturata la macchina virtuale.
      5. **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse di Azure oppure usarne uno **vuoto** esistente nella sottoscrizione.
      6. **Location**: selezionare la posizione del data center più appropriata. In genere è il data center contenente la maggior parte dei dati o più vicino alla posizione fisica per garantire la massima velocità di accesso alla rete. 
      
> [!NOTE]
> Poiché il provisioning della DLVM viene effettuato all'interno di istanze di macchine virtuali GPU Azure serie NC, è necessario scegliere una posizione in Azure che contenga GPU. Attualmente le posizioni che contengono macchine virtuali GPU sono: **Stati Uniti orientali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale**. Per l'elenco più recente, vedere la pagina [Prodotti Azure per area](https://azure.microsoft.com/en-us/regions/services/) e cercare **serie NC** in **Calcolo**. 

   2. **Impostazioni**: selezionare una macchina virtuale GPU serie NC che soddisfi i requisiti funzionali e i vincoli di costo. Creare un account di archiviazione per la macchina virtuale.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Riepilogo**: verificare che tutte le informazioni immesse siano corrette.
   5. **Acquista**: fare clic su **Acquista** per avviare il provisioning. Viene fornito un collegamento alle condizioni della transazione. La macchina virtuale non prevede costi aggiuntivi oltre a quelli per il calcolo delle dimensioni del server scelto nel passaggio **Size** . 

> [!NOTE]
> Per il provisioning sono necessari circa 10-20 minuti. Lo stato del provisioning viene visualizzato nel portale di Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Come accedere alla macchina virtuale per l'apprendimento avanzato

### <a name="windows-edition"></a>Edizione per Windows
Dopo aver creato la VM, è possibile connettersi tramite desktop remoto con le credenziali dell'account amministratore configurato nella sezione **Nozioni di base** precedente. 

### <a name="linux-edition"></a>Edizione per Linux

Dopo aver creato la VM, è possibile accedervi tramite SSH. Usare le credenziali dell'account creato nella sezione **Nozioni di base** del passaggio 3 per l'interfaccia della shell di testo. In un client Windows è possibile scaricare uno strumento client SSH come [Putty](http://www.putty.org). Se si preferisce un desktop con interfaccia grafica (X Windows System), è possibile usare X11 Forwarding su Putty o installare il client X2Go.

> [!NOTE]
> Nei test Microsoft il client X2Go ha fornito prestazioni migliori di X11 Forwarding. È quindi consigliabile usare il client X2Go per un'interfaccia desktop grafica.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Installazione e configurazione del client X2Go
Per la DLVM Linux è già stato effettuato il provisioning con il server X2Go. La DLVM è pronta ad accettare connessioni client. Per connettersi al desktop con interfaccia grafica della VM Linux, è necessario completare la procedura seguente nel client:

1. Scaricare e installare il client X2Go per la piattaforma client da [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Eseguire il client X2Go e selezionare **New Session**(Nuova sessione). Viene visualizzata una finestra di configurazione con più schede. Immettere i parametri di configurazione seguenti:
   * **Scheda Session**(Sessione):
     * **Host**: nome host o indirizzo IP della VM Linux di analisi scientifica dei dati.
     * **Login**(Accesso): nome utente di accesso della VM Linux.
     * **SSH Port**(Porta SSH): lasciare il valore predefinito 22.
     * **Session Type**(Tipo sessione): modificare il valore in **XFCE**. La DSVM Linux attualmente supporta solo l'ambiente desktop XFCE.
   * **Scheda Media**(Supporti): è possibile disattivare il supporto audio e la stampa client se non è necessario usarli.
   * **Shared folders**(Cartelle condivise): se si intende montare directory dei computer client nella VM Linux, aggiungere in questa scheda le directory dei computer client da condividere con la VM.

Dopo aver eseguito l'accesso alla VM con il client SSH o il desktop con interfaccia grafica XFCE tramite il client X2Go, è possibile iniziare a usare gli strumenti installati e configurati nella VM. In XFCE è possibile visualizzare i collegamenti di menu delle applicazioni e le icone del desktop per molti di questi strumenti.

Una volta creata la VM ed effettuato il provisioning, si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Sono disponibili riquadri del menu di avvio e icone del desktop per molti strumenti. 
