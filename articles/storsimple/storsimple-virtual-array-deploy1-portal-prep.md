---
title: Preparazione del portale per l'array virtuale StorSimple | Documentazione Microsoft
description: La prima esercitazione per la distribuzione dell'array virtuale StorSimple include la preparazione del portale di Azure
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3d0801053721f98ce7a2b0fcbe3c65da8dbdd8d3
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Distribuire l'array virtuale StorSimple: preparare il portale di Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Panoramica

Questo è il primo articolo della serie di esercitazioni necessarie per la distribuzione completa dell'array virtuale come file server o server iSCSI tramite il modello di Resource Manager. Questo articolo illustra la preparazione necessaria per creare e configurare il servizio Gestione dispositivi StorSimple prima di eseguire il provisioning di un array virtuale. Questo articolo contiene anche i collegamenti a un elenco di controllo configurazione della distribuzione e i prerequisiti di configurazione.

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. Si consiglia di consultare l'elenco di controllo configurazione della distribuzione prima di iniziare. La preparazione del portale richiede meno di 10 minuti.

Le informazioni pubblicate in questo articolo si applicano alla distribuzione di array virtuali StorSimple nel portale di Azure e nel cloud di Microsoft Azure per enti pubblici.

### <a name="get-started"></a>Introduzione
Il flusso di lavoro di distribuzione consiste nella preparazione del portale, nel provisioning di un array virtuale nell'ambiente virtualizzato e nel completamento dell'installazione. Per iniziare la distribuzione dell'array virtuale StorSimple come file server o server iSCSI, è necessario fare riferimento alle seguenti risorse catalogate.

#### <a name="deployment-articles"></a>Articoli sulla distribuzione

Consultare gli articoli seguenti nella sequenza indicata per distribuire l'array virtuale StorSimple.

| **#** | **In questo passaggio** | **A questo scopo:** | **Usare questi documenti.** |
| --- | --- | --- | --- |
| 1. |**Eseguire la configurazione del portale di Azure** |Creare e configurare il servizio Gestione dispositivi StorSimple prima di eseguire il provisioning di un array virtuale StorSimple. |[Preparare il portale](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Eseguire il provisioning dell'array virtuale** |Per Hyper-V, eseguire il provisioning e connettersi all'array virtuale StorSimple su un sistema host che esegue Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. <br></br> <br></br> Per VMware, eseguire il provisioning e connettersi a un array virtuale StorSimple in un sistema host che esegue VMware ESXi 5.5 e versioni successive.<br></br> |[Eseguire il provisioning di un array virtuale in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Eseguire il provisioning di un array virtuale in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Configurare l'array virtuale** |Per il file server, eseguire l'installazione iniziale, registrare il file server StorSimple e completare l'installazione del dispositivo. È quindi possibile eseguire il provisioning delle condivisioni SMB. <br></br> <br></br> Per il server iSCSI, eseguire l'installazione iniziale, registrare il server iSCSI StorSimple e completare l'installazione del dispositivo. È quindi possibile eseguire il provisioning dei volumi iSCSI. |[Configurare l'array virtuale come file server](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Configurare l'array virtuale come server iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="configuration-checklist"></a>Elenco di controllo configurazione

L'elenco di controllo configurazione descrive le informazioni che è necessario raccogliere prima di configurare il software nell'array virtuale StorSimple. La preparazione di queste informazioni in anticipo consente di semplificare il processo di distribuzione del dispositivo StorSimple nell'ambiente. In base alla distribuzione dell'array virtuale StorSimple come file server o server iSCSI, è necessario disporre di uno dei seguenti elenchi di controllo.

* Scaricare l' [elenco di controllo configurazione StorSimple Virtual Array come file server](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Scaricare l' [elenco di controllo configurazione StorSimple Virtual Array come server iSCSI](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Prerequisiti

Ecco i prerequisiti di configurazione per il servizio Gestione dispositivi StorSimple, l'array virtuale StorSimple e la rete del datacenter.

### <a name="for-the-storsimple-device-manager-service"></a>Per il servizio Gestione dispositivi StorSimple

Prima di iniziare, verificare che:

* Si dispone dell'account Microsoft con credenziali di accesso.
* Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
* La sottoscrizione di Microsoft Azure deve essere abilitata per il servizio Gestione dispositivi StorSimple.

### <a name="for-the-storsimple-virtual-array"></a>Per l'array virtuale StorSimple

Prima di distribuire un array virtuale, è necessario:

* Avere accesso a un sistema host che esegue Hyper-V in Windows Server 2008 R2 o versioni successive o VMware (ESXi 5.5 o versioni successive) da poter usare per il provisioning di un dispositivo.
* Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning dell'array virtuale:
  
  * Un minimo di 4 memorie centrali.
  * Almeno 8 GB di RAM. Se si prevede di configurare la matrice virtuale come file server, 8 GB supportano 2 milioni di file. Sono necessari 16 GB di RAM per supportare 2-4 milioni di file.
  * Un'interfaccia di rete.
  * Un disco virtuale da 500 GB per i dati di sistema.

### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

* La rete nel data center viene configurata in base ai requisiti di rete per il dispositivo StorSimple. Per ulteriori informazioni, vedere l'articolo relativo ai [requisiti di sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* L'array virtuale StorSimple dispone di una larghezza di banda Internet dedicata a 5 Mbps (o superiore) sempre disponibile. La larghezza di banda non deve essere condivisa con altre applicazioni.

## <a name="step-by-step-preparation"></a>Preparazione dettagliata

Seguire le istruzioni dettagliate riportate sotto per preparare il portale per il servizio Gestione dispositivi StorSimple.

## <a name="step-1-create-a-new-service"></a>Passaggio 1: Creare un nuovo servizio

Una singola istanza del servizio Gestione dispositivi StorSimple può gestire più array virtuali StorSimple. Eseguire i passaggi seguenti per creare una nuova istanza del servizio Gestione dispositivi StorSimple. Se si dispone di un servizio Gestione dispositivi StorSimple esistente per gestire gli array virtuali, ignorare questo passaggio e andare a [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio, sarà necessario creare almeno un account di archiviazione dopo avere creato un servizio.
> 
> * Se non è stato creato automaticamente un account di archiviazione, andare a [Configurare un nuovo account di archiviazione per il servizio](#optional-step-configure-a-new-storage-account-for-the-service) per istruzioni dettagliate.
> * Se è stata abilitata la creazione automatica di un account di archiviazione, andare al [Passaggio 2: Ottenere la chiave di registrazione del servizio](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passaggio 2: Ottenere la chiave di registrazione del servizio

Quando il servizio Gestione dispositivi StorSimple è attivo e in esecuzione, è necessario ottenere la chiave di registrazione. Questa chiave viene utilizzata per registrare e connettere il dispositivo StorSimple con il servizio.

Eseguire la procedura seguente nel [portale di Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> La chiave di registrazione del servizio viene usata per tutti i dispositivi Gestione dispositivi StorSimple da registrare con il servizio Gestione dispositivi StorSimple personale.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Passaggio 3: Scaricare l'immagine dell'array virtuale

Una volta in possesso della chiave di registrazione, è necessario scaricare l'immagine dell'array virtuale appropriato per eseguire il provisioning di un array virtuale nel sistema host. Le immagini degli array virtuali sono specifiche del sistema operativo e si possono scaricare dalla pagina Avvio rapido nel portale di Azure.

> [!IMPORTANT]
> Il software in esecuzione nell'array virtuale StorSimple può essere usato solo in combinazione con il servizio Gestione dispositivi StorSimple.
> 
> 

Eseguire la procedura seguente nel [portale di Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Per ottenere l'immagine dell'array virtuale

1. Accedere al [portale di Azure](https://portal.azure.com/). 
2. Nel portale di Azure fare clic su **Sfoglia > Gestione dispositivi StorSimple**.
3. Selezionare un servizio Gestione dispositivi StorSimple esistente. Nel pannello **Gestione dispositivi StorSimple** fare clic su **Avvio rapido**. 
4. Fare clic sul collegamento corrispondente all'immagine che si desidera scaricare dall'Area download Microsoft. I file di immagine sono pari a circa 4,8 GB.
   
   * VHDX per Hyper-V in Windows Server 2012 e versioni successive
   * VHD per Hyper-V in Windows Server 2008 R2 e versioni successive
   * VMDK per VMWare ESXi 5.5 e versioni successive
5. Scaricare e decomprimere il file in un'unità locale, prendendo nota della posizione del file decompresso.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Passaggio facoltativo: configurare un nuovo account di archiviazione per il servizio

Si tratta di un passaggio facoltativo che è necessario eseguire solo se non è stata abilitata la creazione automatica di un account di archiviazione con il servizio.

Se è necessario creare un account di archiviazione di Azure in un'area diversa, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per istruzioni dettagliate.

Eseguire i passaggi seguenti nel [portale di Azure](https://ms.portal.azure.com/), nella pagina del servizio Gestione dispositivi StorSimple, per aggiungere un account di archiviazione di Microsoft Azure esistente.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Per aggiungere una credenziale dell'account di archiviazione con la stessa sottoscrizione di Azure del servizio Gestione dispositivi

1. Passare al servizio Gestione dispositivi, selezionare e fare doppio clic. Si apre il pannello **Panoramica**.
2. Selezionare **Credenziali dell'account di archiviazione** nella sezione **Configurazione**.
3. Fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi un account di archiviazione** eseguire le operazioni seguenti:
   
    1. Per **Sottoscrizione** selezionare **Corrente**.
   
    2. Specificare il nome dell'account di archiviazione di Azure.
   
    3. Selezionare**Abilita** per creare un canale sicuro per la comunicazione di rete tra il dispositivo StorSimple e il cloud. Selezionare **Disabilita** solo se si opera all'interno di un cloud privato.
   
    4. Fare clic su **Aggiungi**. Quando la creazione dell'account di archiviazione viene completata, si riceve una notifica.<br></br>
   
     ![Aggiungere le credenziali di un account di archiviazione esistente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Passaggio successivo

Il passaggio successivo riguarda il provisioning di una macchina virtuale per l'array virtuale StorSimple. A seconda del sistema operativo host, vedere le istruzioni dettagliate in:

* [Eseguire il provisioning di un array virtuale StorSimple in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Eseguire il provisioning di un array virtuale StorSimple in VMware](storsimple-virtual-array-deploy2-provision-vmware.md)


