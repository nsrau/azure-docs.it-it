---
title: Opzioni di migrazione dei dati dai dispositivi StorSimple serie 8000
description: Viene fornita una panoramica delle opzioni per eseguire la migrazione dei dati dalla serie StorSimple 8000.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438320"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opzioni per la migrazione dei dati dalla serie StorSimple 8000

> [!IMPORTANT]
> Il 31 dicembre 2022 la serie StorSimple 8000 raggiungerà lo stato di fine del supporto (EOS). È consigliabile che i clienti della serie StorSimple 8000 emighino a una delle alternative descritte nel documento.

La serie StorSimple 8000 sta raggiungendo la [fine del supporto](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) nel dicembre 2022. I clienti che eseguono serie StorSimple 8000 hanno un'opzione per eseguire l'aggiornamento ad altri servizi ibridi di prima parte di Azure.The customers who are running StorSimple 8000 series have an option to upgrade to other Azure first party hybrid services. Questo articolo descrive le opzioni ibride di Azure disponibili per la migrazione dei dati.

## <a name="migration-options"></a>Opzioni di migrazione

I clienti che usano la serie StorSimple 8000 dispongono di opzioni di Azure o di terze parti.

### <a name="azure-options"></a>Opzioni di Azure

#### <a name="migrate-to-azure-file-sync"></a>Eseguire la migrazione a Sincronizzazione file di Azure

Questa nuova opzione di migrazione consente ai clienti di archiviare le condivisioni file dell'organizzazione in File di Azure.This brand new migration option enables customers to store their organization's file shares in the Azure Files. Queste condivisioni file sono quindi centralizzate per l'accesso in locale tramite l'uso di Sincronizzazione file di Azure (AFS). AFS può essere distribuito in un host server di Windows. La migrazione effettiva dei dati viene quindi eseguita come una copia host o tramite lo strumento di migrazione.

Per altre informazioni su come eseguire la migrazione dei dati in Sincronizzazione file di Azure, vedere [StorSimple 8100 e 8600 migrazione a Sincronizzazione file](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)di Azure.For more information on how to migrate data to Azure File Sync, go to StorSimple 8100 and 8600 migration to Azure File Sync .

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migrazioni: domande frequenti

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. Quando i dispositivi della serie StorSimple 8000 raggiungono la fine del servizio?

R. La serie StorSimple 8000 raggiunge [la fine del supporto](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) nel dicembre 2022. La fine del supporto implica che Microsoft non sarà più in grado di fornire supporto sia per l'hardware che il software di questi dispositivi dopo dicembre 2022. Si consiglia di iniziare adesso la preparazione di un piano per la migrazione dei dati dai dispositivi.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Cosa accade ai dati archiviati in Azure?  

R. È possibile continuare a usare i dati in Azure quando si esegue la migrazione a un servizio più recente.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. Cosa accade ai dati archiviati localmente nel dispositivo StorSimple?

R. Possono essere copiati i dati nel dispositivo locale al servizio più recente, come descritto nei documenti di migrazione.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. Cosa succede se voglio mantenere il mio apparecchio della serie StorSimple 8000?

R. Mentre i servizi potrebbero continuare a funzionare, Microsoft non sarà in grado di fornire supporto per hardware e software. La migrazione è fortemente consigliata per la continuità aziendale.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. Quali opzioni sono disponibili per eseguire la migrazione dei dati dai dispositivi della serie StorSimple 8000?

R. A seconda del loro scenario, gli utenti della serie StorSimple 8000 hanno le seguenti opzioni di migrazione:

* **Eseguire la migrazione a Sincronizzazione file di Azure**: usare questa opzione quando si desidera passare al formato nativo di Azure. È possibile usare Sincronizzazione file di Azure per la gestione centralizzata delle condivisioni file.

* **Altre opzioni**: È possibile contattare il supporto tecnico Microsoft per discutere le opzioni di migrazione non elencate qui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. La migrazione ad altre soluzioni di archiviazione è supportata?

R. Sì. La migrazione da altre soluzioni di archiviazione tramite copia host dei dati è supportata.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. La migrazione è supportata da Microsoft?

R. La migrazione da serie 8000 è un'operazione completamente supportata. Infatti, Microsoft consiglia di rivolgersi al supporto tecnico prima di iniziare la migrazione. La migrazione è attualmente un'operazione assistita. Se si intende eseguire la migrazione dei dati dal dispositivo della serie StorSimple 8000, contattare il [supporto StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. Qual è il modello di determinazione dei prezzi per la migrazione a Sincronizzazione file di Azure?

R. Quando si usa Sincronizzazione file di Azure, possono essere applicati i costi di sottoscrizione per il servizio. I clienti dovranno anche pagare i costi di archiviazione in corso. Fare riferimento a [Prezzi AFS]( https://azure.microsoft.com/pricing/details/storage/files/) per una stima.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. Quanto tempo ci vuole per completare una certificazione?

R. Il tempo necessario per la migrazione dei dati varia a seconda della quantità di dati e dall'opzione di aggiornamento selezionata.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la migrazione dei dati da una serie StorSimple 8000 a Sincronizzazione file di AzureMigrate data from a StorSimple 8000 series to Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
