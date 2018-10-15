---
title: Come installare HANA su SAP HANA di Azure (Istanze large) | Microsoft Docs
description: Come installare SAP HANA su SAP HANA di Azure (Istanze large).
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
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167649"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Esempio di installazione di SAP HANA in istanze Large di HANA

Questa sezione illustra come installare SAP HANA in un'unità di istanze Large di HANA. Lo stato iniziale è simile al seguente:

- Sono stati forniti a Microsoft tutti i dati necessari per la distribuzione di un'istanza Large di SAP HANA.
- È stata ricevuta un'istanza Large di SAP HANA da Microsoft.
- È stata creata una rete virtuale di Azure connessa alla rete locale.
- Il circuito ExpressRoute per istanze Large di HANA è stato connesso alla stessa rete virtuale di Azure.
- È stata installata una VM di Azure da usare come jumpbox per istanze Large di HANA.
- È stata verificata la connessione dal jumpbox all'unità di istanze Large di HANA e viceversa.
- È stata verificata l'installazione di tutte le patch e di tutti i pacchetti necessari.
- Sono state controllate le note e la documentazione SAP relative all'installazione di HANA nel sistema operativo in uso ed è stato verificato il supporto nella versione del sistema operativo per la versione di HANA specifica.

Le sequenze successive illustrano il download dei pacchetti di installazione di HANA nella VM jumpbox, che in questo caso è in esecuzione in un sistema operativo VM, quindi la copia dei pacchetti nell'unità di istanze Large di HANA e infine la sequenza del programma di installazione.

## <a name="download-of-the-sap-hana-installation-bits"></a>Download dei bit di installazione di SAP HANA
Poiché le unità di istanze Large di HANA non hanno alcuna connettività diretta a Internet, non è possibile scaricare direttamente i pacchetti di installazione da SAP alla VM di istanze Large di HANA. Per risolvere il problema relativo all'assenza di connettività diretta a Internet, è necessario il jumpbox. I pacchetti vengono scaricati nella VM jumpbox.

Per scaricare i pacchetti di installazione di HANA, è necessario un utente S o un altro utente SAP, che consente di accedere a SAP Marketplace. Completare questa sequenza di schermate dopo l'accesso:

Passare a [SAP Service Marketplace](https://support.sap.com/en/index.html), quindi fare clic su Download Software (Download di software) > Installations and Upgrade (Installazioni e aggiornamento) > By Alphabetical Index (In base all'indice alfabetico) > Under H – SAP HANA Platform Edition (In H - SAP HANA Platform Edition) > SAP HANA Platform Edition 2.0 > Installation (Installazione) > Download the following files (Scaricare i file seguenti).

![Scaricare i file di installazione di HANA](./media/hana-installation/image16_download_hana.PNG)

Nel caso della dimostrazione sono stati scaricati i pacchetti di installazione per SAP HANA 2.0. Nella VM jumpbox di Azure espandere i file compressi autoestraenti nella directory, come illustrato di seguito.

![Estrarre i file di installazione di HANA](./media/hana-installation/image17_extract_hana.PNG)

Dopo l'estrazione dei file compressi, copiare la directory creata dall'estrazione, nel caso precedente 51052030, nell'unità di istanze Large di HANA nel volume /hana/shared in una directory creata.

> [!Important]
> Non copiare i pacchetti di installazione nel volume LUN radice o di avvio, poiché lo spazio è limitato e deve essere usato anche da altri processi.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installare SAP HANA nell'unità di istanze Large di HANA
Per installare SAP HANA, è necessario accedere come utente radice. Solo l'utente radice ha autorizzazioni sufficienti per l'installazione di SAP HANA.
È prima di tutto necessario configurare le autorizzazioni per la directory copiata in /hana/shared. Le autorizzazioni da configurare sono analoghe a queste:

```
chmod –R 744 <Installation bits folder>
```

Se si vuole installare SAP HANA tramite l'interfaccia grafica, è necessario installare il pacchetto gtk2 istanze Large di HANA. Verificare se l'installazione è stata effettuata con il comando seguente:

```
rpm –qa | grep gtk2
```

Nei passaggi successivi viene illustrato il programma di installazione di SAP HANA con l'interfaccia utente grafica. Passare prima di tutto alla directory di installazione e quindi alla sottodirectory HDB_LCM_LINUX_X86_64. Inizia

```
./hdblcmgui 
```
da tale directory. Viene ora visualizzata una procedura guidata con una sequenza di schermate in cui è necessario fornire i dati per l'installazione. Nel caso illustrato viene eseguita l'installazione del server di database SAP HANA e dei componenti client di SAP HANA. Viene quindi selezionato il valore "SAP HANA Database" (Database SAP HANA), come mostrato di seguito.

![Selezionare HANA nell'installazione](./media/hana-installation/image18_hana_selection.PNG)

Nella schermata successiva viene selezionata l'opzione "Install New System" (Installa nuovo sistema).

![Selezionare una nuova installazione di HANA](./media/hana-installation/image19_select_new.PNG)

Dopo questo passaggio è necessario selezionare alcuni componenti aggiuntivi che possono essere installati oltre al server di database SAP HANA.

![Selezionare altri componenti di HANA](./media/hana-installation/image20_select_components.PNG)

Per le finalità di questa documentazione sono stati selezionati SAP HANA Client e SAP HANA Studio. È stata installata anche un'istanza con scalabilità verticale. Nella schermata successiva è quindi necessario scegliere "Single-Host System" (Sistema a singolo host). 

![Selezionare l'installazione con scalabilità verticale](./media/hana-installation/image21_single_host.PNG)

Nella schermata successiva è necessario specificare alcuni dati.

![Specificare il SID di SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Come ID di sistema (SID) di HANA è necessario specificare lo stesso SID fornito a Microsoft quando è stata ordinata la distribuzione di tipo istanze Large di HANA. La selezione di un SID diverso provoca un errore dell'installazione a causa di problemi di accesso ai volumi diversi.

Come directory di installazione è necessario usare la directory /hana/shared. Nel passaggio successivo è necessario specificare le posizioni dei file di dati HANA e dei file di log HANA.


![Specificare la posizione dei log HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Definire come file di dati e di log i volumi già dotati dei punti di montaggio che contengono il SID scelto nella schermata precedente. Se il SID non corrisponde a quello digitato nella schermata precedente, tornare indietro e modificare il SID specificando il valore disponibile nei punti di montaggio.

Nel passaggio successivo occorre verificare il nome host e, se necessario, correggerlo. 

![Verificare il nome host](./media/hana-installation/image24_review_host_name.PNG)

Nel passaggio successivo è necessario anche recuperare i dati forniti a Microsoft quando è stata ordinata la distribuzione di tipo istanze Large di HANA. 

![Specificare un UID e un GID per l'utente di sistema](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> È necessario specificare lo stesso ID di utente di sistema e lo stesso ID di gruppo utenti forniti a Microsoft quando è stata ordinata la distribuzione di unità. Se non si specificano esattamente gli stessi ID, non sarà possibile installare SAP HANA nell'unità di istanze Large di HANA.

Nelle due schermate successive, non riprodotte in questa documentazione, è necessario specificare la password per l'utente SYSTEM del database SAP HANA e la password per l'utente sapadm, che viene usata per l'agente host SAP, installato come parte dell'istanza di database SAP HANA.

Dopo la definizione della password, viene visualizzata una schermata di conferma. Verificare tutti i dati elencati e continuare con l'installazione. Viene visualizzata una schermata che indica lo stato dell'installazione, analoga a quella seguente.

![Verificare lo stato dell'installazione](./media/hana-installation/image27_show_progress.PNG)

Al termine dell'installazione dovrebbe essere visualizzata un'immagine simile alla seguente:

![L'installazione è stata completata](./media/hana-installation/image28_install_finished.PNG)

A questo punto l'istanza di SAP HANA dovrebbe essere attiva e pronta all'uso. Dovrebbe essere possibile connettersi all'istanza da SAP HANA Studio. Assicurarsi di verificare anche se sono disponibili patch recenti per SAP HANA e applicarle.


**Passaggi successivi**

- Consultare [Disponibilità elevata e ripristino di emergenza di SAP HANA in istanze Large di Azure](hana-overview-high-availability-disaster-recovery.md).

