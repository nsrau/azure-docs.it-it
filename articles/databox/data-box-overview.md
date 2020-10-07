---
title: Panoramica di Microsoft Azure Data Box | Microsoft Docs
description: Questo articolo illustra Azure Data Box, una soluzione cloud che consente di trasferire enormi quantità di dati in Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 9f559587e1abca7642edfc6278018d760b55e774
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "86206228"
---
# <a name="what-is-azure-data-box"></a>Che cos'è Azure Data Box?

La soluzione cloud Microsoft Azure Data Box consente di inviare e ricevere diversi terabyte di dati in Azure in modo rapido, affidabile e a costi contenuti. Il trasferimento sicuro dei dati viene accelerato con la spedizione all'utente di un dispositivo di archiviazione Data Box. Ogni dispositivo di archiviazione ha una capacità di archiviazione massima utilizzabile di 80 TB e viene consegnato al data center dell'utente tramite un corriere locale. Il dispositivo è imballato in una scatola rigida per proteggere i dati durante il trasporto.

È possibile ordinare il dispositivo Data Box tramite il portale di Azure, per importare o esportare dati da Azure. Dopo avere ricevuto il dispositivo, lo si può configurare rapidamente usando l'interfaccia utente Web locale. A seconda che i dati vengano importati o esportati, copiarli dai server al dispositivo o viceversa e rispedire il dispositivo ad Azure. I dati importati in Azure, nel data center di Azure, vengono caricati automaticamente dal dispositivo in Azure. L'intero processo viene monitorato per tutta la durata dal servizio Data Box nel portale di Azure.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]


## <a name="use-cases"></a>Casi d'uso

Data Box è ideale per trasferire dati con dimensioni superiori a 40 TB in scenari con connettività di rete limitata o assente. Lo spostamento dei dati può avvenire una tantum, periodicamente o può essere un trasferimento di dati in blocco iniziale seguito da trasferimenti periodici. 

Ecco i vari scenari in cui è possibile usare Data Box per importare dati in Azure.

 - **Migrazione una tantum**: quando si spostano grandi quantità di dati locali in Azure. 
     - Spostamento di una libreria multimediale da nastri offline in Azure per creare una libreria multimediale online.
     - Migrazione della farm della VM, di SQL Server e di applicazioni ad Azure
     - Spostamento dei dati cronologici in Azure per un'analisi approfondita e la generazione di report tramite HDInsight

 - **Trasferimento in blocco iniziale**: quando si esegue un trasferimento in blocco iniziale usando Data Box (valore di inizializzazione) seguito da trasferimenti incrementali sulla rete. 
     - Vengono ad esempio usati partner di soluzioni di backup, come Commvault e Data Box Heavy, per spostare il backup cronologico iniziale di grandi dimensioni in Azure. Al termine, i dati incrementali verranno trasferiti tramite la rete nella risorsa di archiviazione di Azure.

- **Caricamenti periodici**: quando vengono generate periodicamente grandi quantità di dati che devono essere spostate in Azure. Ad esempio nella prospezione di fonti energetiche, in cui viene generato contenuto video negli impianti di trivellazione e nelle installazioni di turbine a vento. 

Ecco i vari scenari in cui è possibile usare Data Box per esportare dati da Azure.

- **Ripristino di emergenza**: quando una copia dei dati di Azure viene ripristinata in una rete locale. Questa operazione viene in genere eseguita in scenari di ripristino di emergenza in cui vengono esportate grandi quantità di dati di Azure in Data Box. Microsoft spedisce quindi questo dispositivo Data Box e i dati vengono ripristinati in locale dopo un breve periodo di tempo. 

- **Requisiti di sicurezza**: quando è necessario avere la possibilità di esportare i dati da Azure a causa di requisiti legali o di sicurezza. Ad esempio, Archiviazione di Azure è disponibile nei cloud US Secret e Top Secret ed è possibile usare Data Box per esportare i dati da Azure. 

- **Eseguire la migrazione in locale o a un altro provider di servizi cloud**: quando si vogliono spostare tutti i dati di nuovo in locale o trasferirli a un altro provider di servizi cloud, esportare i dati tramite Data Box per eseguire la migrazione dei carichi di lavoro.


## <a name="benefits"></a>Vantaggi

Data Box è progettato per spostare in Azure grandi quantità di dati senza alcun impatto sulla rete. La soluzione offre i vantaggi seguenti:

- **Velocità**: Data Box usa interfacce di rete a 1 Gbps o 10 Gbps per spostare fino a 80 TB di dati in o da Azure.

- **Sicurezza**: Data Box è dotato di protezioni di sicurezza integrate per il dispositivo, i dati e il servizio.
  - Il dispositivo è imballato in una scatola rigida assicurata con viti e adesivi antimanomissione. 
  - I dati sul dispositivo sono sempre protetti con crittografia AES a 256 bit.
  - Il dispositivo può essere sbloccato solo con una password disponibile nel portale di Azure.
  - Il servizio è protetto dalle funzioni di sicurezza di Azure.
  - Dopo che i dati sono stati caricati in Azure per un ordine di importazione, i dischi sul dispositivo verranno cancellati, in conformità agli standard NIST 800-88r1. Per un ordine di esportazione, i dischi vengono cancellati quando il dispositivo raggiunge il data center di Azure.
    
    Per altre informazioni, vedere [Azure Data Box security and data protection](data-box-security.md) (Sicurezza e protezione dei dati in Azure Data Box).

## <a name="features-and-specifications"></a>Funzionalità e specifiche

In questa versione il dispositivo Data Box include le funzionalità seguenti.

| Specifiche                                          | Descrizione              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | < 22 kg (50 libbre)                |
| Dimensioni                                              | Dispositivo - Larghezza: 309,0 mm Altezza: 430,4 mm Profondità: 502,0 mm |            
| Spazio nel rack                                              | 7 U quando inserito nel rack sul proprio lato (non può essere montato su rack)|
| Cavi necessari                                         | 1 cavo di alimentazione (incluso) <br> 2 cavi RJ-45 <br> 2 cavi X SFP + in rame Twinax|
| Capacità di archiviazione                                        | La capacità utilizzabile del dispositivo da 100 TB è pari a 80 TB dopo la protezione RAID 5|
| Potenza nominale                                            | La potenza dell'unità di alimentazione è di 700 W. <br> In genere, l'unità consuma 375 W.|
| Interfacce di rete                                      | 2 interfacce 1 GbE: MGMT, DATA 3. <br> MGMT: per la gestione, non configurabile dall'utente, usata per la configurazione iniziale <br> DATA3: per i dati, configurabile dall'utente e dinamica per impostazione predefinita <br> Le interfacce MGMT e DATA 3 possono lavorare anche come 10 GbE <br> 2 interfacce 10 GbE: DATA 1, DATA 2 <br> Entrambe sono per i dati, possono essere configurate come statiche o dinamiche (impostazione predefinita) |
| Trasferimento dati                                      | Sono supportate sia l'importazione che l'esportazione.  |
| Supporti per il trasferimento dati                                     | RJ-45, SFP+ 10 GbE Ethernet in rame  |
| Sicurezza                                                | Scatola rigida con viti personalizzate antimanomissione <br> Adesivi antimanomissione posizionati nella parte inferiore del dispositivo|
| Velocità di trasferimento dei dati                                      | Fino a 80 TB in un giorno su interfaccia di rete 10 GbE        |
| Gestione                                              | Interfaccia utente Web locale: installazione e configurazione iniziali da eseguire una sola volta <br> Portale di Azure: gestione del dispositivo quotidiana        |

## <a name="data-box-components"></a>Componenti di Data Box

Data Box include i componenti seguenti:

* **Dispositivo Data Box**: un dispositivo fisico che offre uno spazio di archiviazione principale, gestisce la comunicazione con l'archiviazione nel cloud e garantisce la sicurezza e la riservatezza di tutti i dati che sono archiviati nel dispositivo. Il dispositivo Data Box ha una capacità di archiviazione utilizzabile di 80 TB. 

    ![Piano anteriore e posteriore di Data Box](media/data-box-overview/data-box-combined3.png)

    
* **Servizio Data Box**: un'estensione del portale di Azure che consente di gestire un dispositivo Data Box da un'interfaccia Web a cui è possibile accedere da diverse posizioni geografiche. Usare il servizio Data Box per eseguire le attività di amministrazione quotidiane del dispositivo Data Box. Le attività del servizio includono la creazione e la gestione degli ordini, la visualizzazione e la gestione degli avvisi e la gestione delle condivisioni.  

    ![Servizio Data Box nel portale di Azure](media/data-box-overview/data-box-service1.png)

    Per altre informazioni, vedere l'articolo relativo all'[uso del servizio Data Box per gestire il dispositivo Data Box](data-box-portal-ui-admin.md).

* **Interfaccia utente Web locale**: un'interfaccia utente basata sul Web usata per configurare il dispositivo in modo che possa connettersi alla rete locale e quindi registrare il dispositivo con il servizio Data Box. Usare l'interfaccia utente Web locale anche per arrestare e riavviare il dispositivo Data Box, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.

    ![Interfaccia utente Web locale di Data Box](media/data-box-overview/data-box-local-web-ui.png)

    Per informazioni sull'uso dell'interfaccia utente basata sul Web, vedere l'articolo relativo all'[uso dell'interfaccia utente basata sul Web per amministrare Data Box](data-box-portal-ui-admin.md).

## <a name="the-workflow"></a>Il flusso di lavoro

Il flusso di importazione tipico è costituito dai passaggi seguenti:

1. **Ordine**: creare un ordine nel portale di Azure, fornire informazioni sulla spedizione e l'account di archiviazione di Azure a cui i dati sono destinati. Se il dispositivo è disponibile, Azure lo prepara e lo spedisce con un ID di verifica della spedizione.

2. **Ricezione**: dopo avere ricevuto il dispositivo, collegare i cavi di alimentazione e di rete usando i cavi specificati. Accendere il dispositivo ed eseguire la connessione. Configurare la rete del dispositivo e montare le condivisioni nel computer host da cui si intende copiare i dati.

3. **Copia dei dati**: copiare i dati nelle condivisioni di Data Box.

4. **Reso**: preparare il dispositivo, spegnerlo e rispedirlo al data center di Azure.

5. **Caricamento**: i dati vengono copiati automaticamente dal dispositivo in Azure. I dischi del dispositivo vengono cancellati in modo sicuro secondo le linee guida del National Institute of Standards and Technology (NIST).

Durante questo processo si riceve una notifica tramite posta elettronica a ogni cambiamento di stato. Per informazioni dettagliate sul flusso, passare a [Deploy Data Box in Azure portal](data-box-deploy-ordered.md) (Distribuire Data Box nel portale di Azure).


Il flusso di esportazione tipico è costituito dai passaggi seguenti:

1. **Ordine**: creare un ordine di esportazione nel portale di Azure, fornire informazioni sulla spedizione e l'account di archiviazione di Azure di origine per i dati. Se il dispositivo è disponibile, Azure lo prepara. I dati vengono copiati dall'account di archiviazione di Azure al dispositivo Data Box. Al termine della copia dei dati, Microsoft spedisce il dispositivo con un ID di monitoraggio della spedizione.

2. **Ricezione**: dopo avere ricevuto il dispositivo, collegare i cavi di alimentazione e di rete usando i cavi specificati. Accendere il dispositivo ed eseguire la connessione. Configurare la rete del dispositivo e montare le condivisioni nel computer host in cui copiare i dati.

3. **Copia dei dati**: copiare i dati dalle condivisioni di Data Box ai server dati locali.

4. **Reso**: preparare il dispositivo, spegnerlo e rispedirlo al data center di Azure.

5. **Cancellazione dei dati**: i dischi del dispositivo vengono cancellati in modo sicuro secondo le linee guida del National Institute of Standards and Technology (NIST).

Durante il processo di esportazione, si riceve una notifica tramite posta elettronica a ogni cambiamento di stato. Per informazioni dettagliate sul flusso, passare a [Deploy Data Box in Azure portal](data-box-deploy-export-ordered.md) (Distribuire Data Box nel portale di Azure).

## <a name="region-availability"></a>Aree di disponibilità

Data Box può trasferire i dati in base all'area in cui è distribuito il servizio, al paese o all'area geografica in cui viene spedito il dispositivo e all'account di archiviazione di Azure di destinazione in cui verranno trasferiti i dati. 

### <a name="for-import"></a>Per l'importazione

- **Disponibilità del servizio**: quando si usa Data Box per ordini di importazione o esportazione, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) per informazioni sulla disponibilità a livello di area. La funzionalità di esportazione in Data Box è attualmente in fase di anteprima. 

    Per gli ordini di importazione, è anche possibile distribuire Data Box nel cloud di Azure per enti pubblici. Per altre informazioni, vedere [What is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Informazioni su Azure per enti pubblici). 

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure in cui è disponibile il servizio.


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema per Data Box](data-box-system-requirements.md).
- Comprendere i [limiti di Data Box](data-box-limits.md).
- Distribuire rapidamente [Azure Data Box](data-box-quickstart-portal.md) nel portale di Azure.




