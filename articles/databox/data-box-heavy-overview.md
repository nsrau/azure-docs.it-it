---
title: Panoramica di Microsoft Azure Data Box Heavy | Microsoft Docs
description: Viene descritto Azure Data Box, una soluzione ibrida che consente di trasferire enormi quantità di dati in Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 06/19/2019
ms.author: alkohli
ms.openlocfilehash: 94ba0040b1de7e1f03825f1cd2ef2a6536343d96
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271749"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Che cos'è Azure Data Box Heavy? (anteprima)

Azure Data Box Heavy consente di inviare centinaia di terabyte di dati in Azure in modo rapido, economico e affidabile. I dati vengono trasferiti in Azure tramite la spedizione di un dispositivo Data Box Heavy con capacità di archiviazione di 1 PB, in cui è possibile inserire i dati da inviare a Microsoft. Il dispositivo è imballato in un involucro rigido per proteggere i dati durante il trasporto.

Data Box Heavy è attualmente disponibile in anteprima. È possibile iscriversi per richiedere un dispositivo tramite il portale di Azure. Una volta ricevuto il dispositivo presso il data center, è possibile configurarlo tramite l'interfaccia utente Web locale. Copiare i dati dai server in uso nel dispositivo e rispedire il dispositivo ad Azure. Nel data center di Azure i dati vengono caricati in uno o più account di archiviazione di Azure specifici dell'utente. È possibile tenere traccia dell'intero processo nel portale di Azure.


> [!IMPORTANT]
> - Data Box Heavy è disponibile in anteprima. Prima di distribuire questa soluzione, esaminare le [condizioni per le anteprime di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> - Per richiedere un dispositivo, effettuare l'iscrizione nel [portale di anteprima](https://aka.ms/azuredatabox).
> - Durante l'anteprima, Data Box Heavy può essere spedito ai clienti negli Stati Uniti e nell'Unione europea. Per altre informazioni, vedere [Disponibilità a livello di area](#region-availability).

## <a name="use-cases"></a>Casi d'uso

Data Box Heavy offre la soluzione ideale per grandi quantità di dati, dell'ordine di centinaia di terabyte, quando la connettività di rete è insufficiente per caricare i dati in Azure. Lo spostamento dei dati può avvenire una tantum, periodicamente o può essere un trasferimento di dati in blocco iniziale seguito da trasferimenti periodici. Ecco i vari scenari in cui è possibile usare Data Box Heavy per il trasferimento dei dati.

 - **Migrazione una tantum**: quando si spostano grandi quantità di dati locali in Azure.
     - Spostare una libreria multimediale da nastri offline in Azure per creare una libreria multimediale online.
     - Eseguire la migrazione in Azure di una farm di macchine virtuali, di SQL Server e di applicazioni.
     - Spostare i dati cronologici in Azure per eseguire analisi approfondite e generare report tramite HDInsight.

 - **Trasferimento in blocco iniziale**: quando si esegue un trasferimento in blocco iniziale usando Data Box Heavy (valore di inizializzazione) seguito da trasferimenti incrementali sulla rete.
     - Ad esempio, Data Box Heavy e un partner di soluzioni di backup vengono usati per spostare in Azure il backup iniziale di una grande quantità di dati cronologici. Al termine, i dati incrementali verranno trasferiti tramite la rete nella risorsa di archiviazione di Azure.

 - **Caricamenti periodici**: quando vengono generate periodicamente grandi quantità di dati che devono essere spostate in Azure. Ad esempio nella prospezione di fonti energetiche, in cui viene generato contenuto video negli impianti di trivellazione e nelle installazioni di turbine a vento.

## <a name="benefits"></a>Vantaggi

Data Box Heavy è stato progettato per spostare grandi quantità di dati in Azure senza alcun impatto sulla rete del cliente. La soluzione offre i vantaggi seguenti:

- **Velocità**: Data Box Heavy usa interfacce di rete a 40 Gbps ad alte prestazioni.

- **Sicurezza**: Data Box Heavy è dotato di sicurezza integrata per il dispositivo, i dati e il servizio.
    - Il dispositivo è imballato in modo resistente, fissato con viti ed etichette antimanomissione.
    - I dati sul dispositivo sono sempre protetti con crittografia AES a 256 bit.
    - Il dispositivo può essere sbloccato solo con una password disponibile nel portale di Azure.
    - Il servizio è protetto dalle funzioni di sicurezza di Azure.
    - Dopo che i dati sono stati caricati in Azure, il contenuto dei dischi sul dispositivo viene cancellato, in conformità con gli standard 800-88r1 del National Institute of Standards and Technology (NIST).


## <a name="features-and-specifications"></a>Funzionalità e specifiche

In questa versione il dispositivo Data Box Heavy include le caratteristiche seguenti.

| Specifiche                                          | DESCRIZIONE              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | ~ 22 kg (500 libbre) <br>Dispositivo su ruote bloccate per il trasporto|
| Dimensioni                                              | Larghezza: 66 cm (26 pollici) Altezza: 71 cm (28 pollici) Lunghezza: 122 cm (48 pollici) |
| Spazio nel rack                                              | Non può essere montato su rack|
| Cavi necessari                                         | Sono inclusi 4 cavi di alimentazione da 120 V/10 A (NEMA 5-15) con messa a terra <br> Il dispositivo supporta fino a 240 V ed è dotato di connettori per cavi di alimentazione C-13 <br> Usare cavi di rete compatibili con [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Potenza                                                    | 4 unità di alimentazione incorporate, condivise tra i nodi del dispositivo <br> Consumo energetico tipico di 1.200 watt|
| Capacità di archiviazione                                        | Capacità nominale di circa 1 PB, 70 dischi da 14 TB ciascuno <br> Capacità utilizzabile di 770 TB|
| Numero di nodi                                          | 2 nodi indipendenti per ogni dispositivo (500 TB ciascuno) |
| Interfacce di rete per nodo                             | 4 interfacce di rete per ogni nodo <br><br> MGMT, DATA3 <ul><li> 2 interfacce da 1 GbE </li><li> MGMT è un'interfaccia per la gestione e la configurazione iniziale, non è configurabile dall'utente </li><li> DATA3 è un'interfaccia configurabile dall'utente e DHCP (Dynamic Host Configuration Protocol) per impostazione predefinita</li><li>Le interfacce di rete da 1 GbE possono essere configurate anche come interfacce da 10 GbE</li></ul>Interfacce dati DATA1 e DATA2 <ul><li>2 interfacce da 40 GbE </li><li> Configurabile dall'utente per DHCP (impostazione predefinita) o statici</li></ul>|


## <a name="components"></a>Componenti

Data Box Heavy include i componenti seguenti:

* **Dispositivo Data Box Heavy**: un dispositivo fisico con imballaggio resistente per archiviare i dati in modo sicuro. Questo dispositivo è dotato di una capacità di archiviazione utilizzabile di 770 TB.
    
* **Servizio Data Box**: un'estensione del portale di Azure che consente di gestire un dispositivo Data Box Heavy da un'interfaccia Web accessibile da diverse posizioni geografiche. Usare il servizio Data Box per gestire il dispositivo Data Box Heavy. Le attività del servizio includono la creazione e la gestione degli ordini, la visualizzazione e la gestione degli avvisi e la gestione delle condivisioni.  

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

Data Box Heavy può trasferire i dati in base all'area in cui è distribuito il servizio, al paese o all'area geografica in cui viene spedito il dispositivo e all'account di archiviazione di Azure di destinazione in cui verranno trasferiti i dati.

- **Disponibilità del servizio**: per questa versione, Data Box Heavy è disponibile nelle aree seguenti:
    - Tutte le aree di cloud pubblico negli Stati Uniti: Stati Uniti centro-occidentali, Stati Uniti occidentali 2, Stati Uniti occidentali, Stati Uniti centro-meridionali, Stati Uniti centrali, Stati Uniti centro-settentrionali, Stati Uniti orientali e Stati Uniti orientali 2.
    - Unione europea: Europa occidentale ed Europa settentrionale.
    - Regno Unito: Regno Unito meridionale e Regno Unito occidentale.
    - Francia: Francia centrale e Francia meridionale.

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure in cui è disponibile il servizio.

Per informazioni aggiornate sulla disponibilità delle aree geografiche per Data Box Heavy, visitare la pagina [Prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Iscrizione

Data Box Heavy è disponibile in anteprima ed è necessario effettuare l'iscrizione. Per iscriversi a Data Box Heavy, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo: https://aka.ms/azuredatabox.
2. Fare clic su **Crea una risorsa** per creare una nuova risorsa. Cercare **Azure Data Box**. Selezionare il servizio **Azure Data Box**.

    <!--![The Data Box Heavy sign up 1]()-->

3. Fare clic su **Create**(Crea).

    <!--![The Data Box Heavy sign up 2]()-->

4. Selezionare la sottoscrizione che si vuole usare per l'anteprima di Data Box Heavy. Selezionare l'area in cui si intende distribuire la risorsa Data Box Heavy. Nell'opzione **Data Box Heavy** fare clic su **Iscrizione**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Rispondere alle domande relative al paese o all'area geografica di residenza dei dati, all'intervallo di tempo, al servizio di Azure di destinazione per il trasferimento dei dati, alla larghezza di banda della rete e alla frequenza di trasferimento dei dati. Esaminare l'Informativa sulla privacy e le condizioni e selezionare la casella di controllo che consente a Microsoft di usare l'indirizzo di posta elettronica per contattare l'utente.

    <!--![The Data Box Heavy sign up 4]()-->

Dopo aver eseguito l'iscrizione e abilitato la sottoscrizione per l'anteprima, sarà possibile ordinare Data Box Heavy.

    
