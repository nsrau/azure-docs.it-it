---
title: Panoramica di Microsoft Azure Data Box Heavy | Microsoft Docs
description: Viene descritto Azure Data Box, una soluzione ibrida che consente di trasferire enormi quantità di dati in Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 8ecef86841bcf13a469f9c0dc81f114bd54acdba
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946517"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Che cos'è Azure Data Box Heavy? (Anteprima)

La soluzione ibrida Microsoft Azure Data Box consente di inviare centinaia di terabyte di dati in Azure in modo rapido, economico e affidabile. Il trasferimento sicuro dei dati viene accelerato con la spedizione all'utente di un dispositivo proprietario con capacità di archiviazione di 1 PB. Il dispositivo è imballato in una scatola rigida per proteggere i dati durante il trasporto.

Data Box Heavy è attualmente in anteprima ed è possibile iscriversi per richiedere un dispositivo mediante il portale di Azure. Una volta ricevuto il dispositivo presso il data center, è possibile configurarlo mediante l'interfaccia utente Web locale. Copiare i dati dai server in uso nel dispositivo e rispedire il dispositivo ad Azure. Nel data center di Azure i dati vengono caricati automaticamente dal dispositivo in Azure. L'intero processo viene monitorato per tutta la durata dal servizio Data Box nel portale di Azure.


> [!IMPORTANT]
> - Data Box Heavy è disponibile in anteprima. Prima di distribuire questa soluzione, esaminare le [condizioni per le anteprime di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Per richiedere un dispositivo, effettuare l'iscrizione nel [portale di anteprima](http://aka.ms/).
> - Durante l'anteprima, Data Box Heavy può essere spedito ai clienti negli Stati Uniti e nell'Unione europea. Per altre informazioni, vedere [Disponibilità a livello di area](#region-availability).

## <a name="use-cases"></a>Casi d'uso

Data Box Heavy è ideale per trasferire dati con dimensioni superiori a 500 TB in scenari con connettività di rete limitata o assente. Lo spostamento dei dati può avvenire una tantum, periodicamente o può essere un trasferimento di dati in blocco iniziale seguito da trasferimenti periodici. Ecco i vari scenari in cui è possibile usare Data Box Heavy per il trasferimento dei dati.

 - **Migrazione una tantum**: quando si spostano grandi quantità di dati locali in Azure. 
     - Spostamento di una libreria multimediale da nastri offline in Azure per creare una libreria multimediale online.
     - Migrazione della farm della VM, di SQL Server e di applicazioni ad Azure
     - Spostamento dei dati cronologici in Azure per un'analisi approfondita e la generazione di report tramite HDInsight

 - **Trasferimento in blocco iniziale**: quando si esegue un trasferimento in blocco iniziale usando Data Box Heavy (valore di inizializzazione) seguito da trasferimenti incrementali sulla rete. 
     - Ad esempio, i partner di soluzioni di backup, come Commvault e Data Box Heavy, vengono usati per spostare il backup cronologico iniziale di grandi dimensioni in Azure. Al termine, i dati incrementali verranno trasferiti tramite la rete nella risorsa di archiviazione di Azure.

 - **Caricamenti periodici**: quando vengono generate periodicamente grandi quantità di dati che devono essere spostate in Azure. Ad esempio nella prospezione di fonti energetiche, in cui viene generato contenuto video negli impianti di trivellazione e nelle installazioni di turbine a vento.      

## <a name="benefits"></a>Vantaggi

Data Box Heavy è progettato per spostare grandi quantità di dati in Azure senza alcun impatto sulla rete. La soluzione offre i vantaggi seguenti:

- **Velocità**: Data Box Heavy usa interfacce di rete a 40 Gbps ad alte prestazioni.

- **Sicurezza**: Data Box Heavy è dotato di sicurezza integrata per il dispositivo, i dati e il servizio.
    - Il dispositivo è imballato in modo resistente, fissato con viti ed etichette antimanomissione. 
    - I dati sul dispositivo sono sempre protetti con crittografia AES a 256 bit.
    - Il dispositivo può essere sbloccato solo con una password disponibile nel portale di Azure.
    - Il servizio è protetto dalle funzioni di sicurezza di Azure.
    - Dopo che i dati sono stati caricati in Azure, i dischi sul dispositivo verranno cancellati, in conformità con gli standard NIST 800-88r1.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Componenti

Data Box Heavy include i componenti seguenti:

* **Dispositivo Data Box Heavy**: un dispositivo fisico con imballaggio resistente per archiviare i dati in modo sicuro. Questo dispositivo è dotato di una capacità di archiviazione utilizzabile di 800 TB. 

    
* **Servizio Data Box**: un'estensione del portale di Azure che consente di gestire un dispositivo Data Box Heavy da un'interfaccia Web accessibile da diverse posizioni geografiche. Usare il servizio Data Box per eseguire le attività di amministrazione quotidiane del dispositivo Data Box Heavy. Le attività del servizio includono la creazione e la gestione degli ordini, la visualizzazione e la gestione degli avvisi e la gestione delle condivisioni.  

* **Interfaccia utente Web locale**: un'interfaccia utente basata sul Web usata per configurare il dispositivo in modo che possa connettersi alla rete locale e quindi registrare il dispositivo con il servizio Data Box. Usare l'interfaccia utente Web locale anche per arrestare e riavviare il dispositivo, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.


## <a name="the-workflow"></a>Il flusso di lavoro

Il flusso di lavoro tipico è costituito dai passaggi seguenti:

1. **Ordine**: creare un ordine nel portale di Azure, fornire informazioni sulla spedizione e l'account di archiviazione di Azure a cui i dati sono destinati. Se il dispositivo è disponibile, Azure lo prepara e lo spedisce con un ID di verifica della spedizione.

2. **Ricezione**: dopo avere ricevuto il dispositivo, collegare i cavi di alimentazione e di rete usando i cavi specificati. Accendere il dispositivo ed eseguire la connessione. Configurare la rete del dispositivo e montare le condivisioni nel computer host da cui si intende copiare i dati.

3. **Copia dei dati**: copiare i dati in condivisioni di Data Box Heavy.

4. **Reso**: preparare il dispositivo, spegnerlo e rispedirlo al data center di Azure.

5. **Caricamento**: i dati vengono copiati automaticamente dal dispositivo in Azure. I dischi del dispositivo vengono cancellati in modo sicuro secondo le linee guida del National Institute of Standards and Technology (NIST).

Durante questo processo si riceve una notifica tramite posta elettronica a ogni cambiamento di stato. 

## <a name="region-availability"></a>Aree di disponibilità

Data Box Heavy può trasferire i dati in base all'area in cui è distribuito il servizio, al paese in cui viene spedito il dispositivo e all'account di archiviazione di Azure di destinazione in cui verranno trasferiti i dati. 

- **Disponibilità del servizio**: per questa versione, Data Box Heavy è disponibile nelle aree seguenti:
    - Tutte le aree di cloud pubblico negli Stati Uniti: Stati Uniti centro-occidentali, Stati Uniti occidentali 2, Stati Uniti occidentali, Stati Uniti centro-meridionali, Stati Uniti centrali, Stati Uniti centro-settentrionali, Stati Uniti orientali e Stati Uniti orientali 2.
    - Unione europea: Europa occidentale ed Europa settentrionale.
    - Regno Unito: Regno Unito meridionale e Regno Unito occidentale.
    - Francia: Francia centrale e Francia meridionale.

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure in cui è disponibile il servizio. 

## <a name="sign-up"></a>Iscrizione

Data Box Heavy è disponibile in anteprima ed è necessario effettuare l'iscrizione. Seguire questa procedura per iscriversi a Data Box Heavy:

1. Accedere al portale di Azure all'indirizzo: https://aka.ms/azuredatabox.
2. Fare clic su **+** per creare una nuova risorsa. Cercare **Azure Data Box**. Selezionare il servizio **Azure Data Box**.

    <!--![The Data Box Heavy sign up 1]()-->

3. Fare clic su **Create**(Crea).

    <!--![The Data Box Heavy sign up 2]()-->

4. Selezionare la sottoscrizione che si vuole usare per l'anteprima di Data Box Heavy. Selezionare l'area in cui si intende distribuire la risorsa Data Box Heavy. Nell'opzione **Data Box Heavy** fare clic su **Iscrizione**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Rispondere alle domande relative al paese di residenza dei dati, all'intervallo di tempo, al servizio di Azure di destinazione per il trasferimento dei dati, alla larghezza di banda della rete e alla frequenza di trasferimento dei dati. Esaminare l'Informativa sulla privacy e le condizioni e selezionare la casella di controllo che consente a Microsoft di usare l'indirizzo di posta elettronica per contattare l'utente.

    <!--![The Data Box Heavy sign up 4]()-->

Dopo aver effettuato l'iscrizione e abilitato la sottoscrizione per l'anteprima, sarà possibile ordinare Data Box Heavy.




