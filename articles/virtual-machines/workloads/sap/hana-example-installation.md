---
title: Come installare HANA in SAP HANA di Azure (istanze Large) | Microsoft Docs
description: Come installare HANA in SAP HANA di Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4629894933507bda7359fb034c4079d38100029
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231407"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installare HANA in SAP HANA di Azure (istanze Large)

Prima di installare HANA in SAP HANA di Azure (istanze Large) è necessario eseguire queste operazioni:
- Fornire a Microsoft tutti i dati necessari per la distribuzione in un'unità di SAP HANA in istanze Large.
- Ricevere l'unità di SAP HANA in istanze Large da Microsoft.
- Creare una rete virtuale di Azure che si connette alla rete locale.
- Connettere il circuito ExpressRoute per HANA in istanze Large alla stessa rete virtuale di Azure.
- Installare una macchina virtuale di Azure da usare come jumpbox per HANA in istanze Large.
- Verificare la connessione dalla macchina virtuale jumpbox all'unità di HANA in istanze Large e viceversa.
- Verificare l'installazione di tutte le patch e di tutti i pacchetti necessari.
- Leggere le note SAP e la documentazione sull'installazione di HANA nel sistema operativo in uso. Verificare che la versione di HANA selezionata sia supportata nella versione del sistema operativo.

La sezione seguente mostra un esempio di download dei pacchetti di installazione di HANA nella macchina virtuale jumpbox. In questo caso, il sistema operativo è Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Scaricare i bit di installazione di SAP HANA
Le unità di HANA in istanze Large non sono connesse direttamente a Internet. Non è possibile scaricare direttamente i pacchetti di installazione da SAP alla macchina virtuale di HANA in istanze Large. In alternativa, scaricare i pacchetti nella macchina virtuale jumpbox.

Per eseguire questa operazione, è necessario un utente S o un altro utente SAP, che consente di accedere a SAP Marketplace.

1. Eseguire l'accesso e passare a [SAP Service Marketplace](https://support.sap.com/en/index.html). Selezionare **Download Software** (Download del software)  > **Installations and Upgrades** (Installazioni e aggiornamenti)  > **By Alphabetical Index** (In ordine alfabetico). Selezionare la lettera H e quindi **SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installation** (Installazione). Scaricare i file come illustrato nello screenshot seguente.

   ![Screenshot dei file da scaricare](./media/hana-installation/image16_download_hana.PNG)

2. In questo esempio sono stati scaricati i pacchetti di installazione di SAP HANA 2.0. Nella macchina virtuale jumpbox di Azure espandere i file compressi autoestraenti nella directory, come illustrato di seguito.

   ![Screenshot di file compresso autoestraente](./media/hana-installation/image17_extract_hana.PNG)

3. Quando i file compressi sono stati estratti, copiare la directory creata dall'estrazione (in questo caso, 51052030). Copiare la directory dal volume /hana/shared dell'unità di HANA in istanze Large in una directory creata personalmente.

   > [!Important]
   > Non copiare i pacchetti di installazione nel volume LUN radice o di avvio perché lo spazio è limitato e deve essere usato anche da altri processi.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installare SAP HANA nell'unità di HANA in istanze Large
Per installare SAP HANA, accedere come utente ROOT. Solo l'utente ROOT ha autorizzazioni sufficienti per l'installazione di SAP HANA. Impostare le autorizzazioni per la directory copiata in /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Se si vuole installare SAP HANA tramite l'interfaccia utente grafica, è necessario che nelle unità di HANA in istanze Large sia installato il pacchetto gtk2. Per verificare se è installato, eseguire il comando seguente:

```
rpm –qa | grep gtk2
```

Nei passaggi successivi viene illustrato il programma di installazione di SAP HANA con l'interfaccia utente grafica.

Passare alla directory di installazione e quindi alla sottodirectory HDB_LCM_LINUX_X86_64. 

Da tale directory, avviare:

```
./hdblcmgui 
```
A questo punto, viene visualizzata una sequenza di schermate in cui si specificano i dati per l'installazione. In questo esempio viene eseguita l'installazione del server di database SAP HANA e dei componenti client di SAP HANA. Viene quindi selezionato il componente **SAP HANA Database**.

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con SAP HANA Database selezionato](./media/hana-installation/image18_hana_selection.PNG)

Nella schermata successiva selezionare **Install New System** (Installa nuovo sistema).

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con Install New System selezionato](./media/hana-installation/image19_select_new.PNG)

Esaminare quindi i vari componenti aggiuntivi che è possibile installare.

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con l'elenco dei componenti aggiuntivi](./media/hana-installation/image20_select_components.PNG)

In questo caso, si sono scelti SAP HANA Client e SAP HANA Studio. Si installa anche un'istanza con scalabilità verticale. Scegliere quindi **Single-Host System** (Sistema host singolo). 

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con Single Host System selezionato](./media/hana-installation/image21_single_host.PNG)

Specificare quindi alcuni dati.

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con i campi delle proprietà di sistema da definire](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Come ID di sistema (SID) di HANA è necessario specificare lo stesso SID fornito a Microsoft quando è stata ordinata la distribuzione di HANA in istanze Large. Se si sceglie un SID diverso, l'installazione non riuscirà a causa di problemi di accesso ai diversi volumi.

Come percorso di installazione usare la directory /hana/shared. Nel passaggio successivo specificare i percorsi dei file di dati HANA e dei file di log HANA.


![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con i campi relativi ai file di dati e log](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Il SID specificato al momento della definizione delle proprietà di sistema (due schermate fa) deve corrispondere al SID dei punti di montaggio. Se non vi è corrispondenza, tornare indietro e modificare il SID specificando il valore del SID dei punti di montaggio.

Nel passaggio successivo verificare il nome host e, se necessario, correggerlo. 

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con il nome host](./media/hana-installation/image24_review_host_name.PNG)

Nel passaggio successivo è necessario anche recuperare i dati forniti a Microsoft quando è stata ordinata la distribuzione di HANA in istanze Large. 

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con i campi dell'amministratore di sistema da definire](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Per **System Administrator User ID** (ID utente amministratore di sistema) e **ID of User Group** (ID gruppo di utenti) specificare gli stessi valori forniti a Microsoft quando si ordina la distribuzione di unità. Se non si specificano gli stessi ID, non sarà possibile installare SAP HANA nell'unità di HANA in istanze Large.

Le due schermate successive non sono illustrate qui. Consentono di specificare la password per l'utente SYSTEM del database SAP HANA e la password per l'utente sapadm. Quest'ultimo viene usato per l'agente Host SAP che viene installato come parte dell'istanza del database SAP HANA.

Dopo la definizione della password, viene visualizzata una schermata di conferma. Verificare tutti i dati elencati e continuare con l'installazione. A questo punto viene visualizzata una schermata che indica lo stato dell'installazione, come la seguente:

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA con gli indicatori di stato dell'installazione](./media/hana-installation/image27_show_progress.PNG)

Al termine dell'installazione, verrà visualizzata una schermata simile alla seguente:

![Screenshot della schermata di gestione del ciclo di vita di SAP HANA indicante il completamento dell'installazione](./media/hana-installation/image28_install_finished.PNG)

A questo punto l'istanza di SAP HANA dovrebbe essere attiva e pronta all'uso. Dovrebbe essere possibile connettersi all'istanza da SAP HANA Studio. Assicurarsi anche di cercare e applicare gli aggiornamenti più recenti.


## <a name="next-steps"></a>Passaggi successivi

- [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md)

