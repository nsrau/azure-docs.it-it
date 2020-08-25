---
title: Opzioni di migrazione dei dati da dispositivi StorSimple serie 8000
description: Viene fornita una panoramica delle opzioni per la migrazione dei dati da StorSimple serie 8000.
services: storsimple
author: twooley
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: twooley
ms.openlocfilehash: 92ab570b3e06d67d7986a8103e9622e95eb57e7e
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783910"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opzioni per la migrazione dei dati da StorSimple serie 8000

> [!IMPORTANT]
> Il 31 dicembre 2022 la serie StorSimple 8000 raggiungerà lo stato di fine supporto (EOS). Si consiglia ai clienti della serie StorSimple 8000 di eseguire la migrazione a una delle alternative descritte nel documento.

La serie StorSimple 8000 sta raggiungendo la [fine del supporto](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) nel dicembre 2022. I clienti che eseguono StorSimple serie 8000 hanno la possibilità di eseguire l'aggiornamento ad altri servizi ibridi di Azure per la prima parte. Questo articolo descrive le opzioni ibride di Azure disponibili per la migrazione dei dati.

## <a name="migration-options"></a>Opzioni di migrazione

I clienti che usano StorSimple serie 8000 hanno opzioni di Azure o di terze parti.

### <a name="azure-options"></a>Opzioni di Azure

#### <a name="migrate-to-azure-file-sync"></a>Eseguire la migrazione a Sincronizzazione file di Azure

Questa nuovissima opzione di migrazione consente ai clienti di archiviare le condivisioni file dell'organizzazione nel File di Azure. Queste condivisioni file sono quindi centralizzate per l'accesso in locale tramite l'uso di Sincronizzazione file di Azure (AFS). AFS può essere distribuito in un host server di Windows. La migrazione effettiva dei dati viene quindi eseguita come una copia host o tramite lo strumento di migrazione.

Per altre informazioni su come eseguire la migrazione dei dati in Sincronizzazione file di Azure, vedere la pagina relativa alla [migrazione di StorSimple 8100 e 8600 a sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

### <a name="third-party-options"></a>Opzioni di terze parti

#### <a name="migrate-to-panzura-freedom-nas"></a>Eseguire la migrazione a Panzura Freedom NAS

StorSimple serie 5000-7000 e i clienti della serie StorSimple 8000 possono scegliere di eseguire la migrazione a Panzura Freedom NAS per conservarne i dati in Azure. La soluzione Panzura Freedom fornisce una soluzione NAS che si estende su Data Center, uffici, cloud pubblici e privati. La soluzione supporta flussi di lavoro relativi a dati locali, ibridi e nel cloud per client NFS e SMB e client per dispositivi mobili.

Questa migrazione è supportata da Panzura e per iniziare i clienti possono richiedere supporto per la migrazione dal [sito Web Panzura](https://panzura.com/migrate-storsimple-panzura/).

#### <a name="migrate-to-nasuni"></a>Eseguire la migrazione a Nasuni

Il passaggio dell'intero ambiente StorSimple a una piattaforma di servizi file stabile, sicura e ad alte prestazioni è facile con Nasuni. Nasuni offre la sicurezza e le prestazioni dell'archiviazione file locale, combinando la scalabilità e la durabilità di Azure.  In qualità di fornitore di software indipendente (ISV) di Azure, Nasuni offre tutti gli strumenti necessari per spostare i dati di StorSimple in una piattaforma moderna che consente di condividere e collaborare con i file in più posizioni.

Inizia subito: [sito Web Nasuni](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
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

R. Il [supporto di](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) StorSimple serie 8000 della serie è stato raggiunto nel dicembre 2022. La fine del supporto implica che Microsoft non sarà più in grado di fornire supporto per l'hardware e il software di questi dispositivi dopo il 2022 dicembre. Si consiglia di iniziare adesso la preparazione di un piano per la migrazione dei dati dai dispositivi.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Cosa accade ai dati archiviati in Azure?  

R. È possibile continuare a usare i dati in Azure quando si esegue la migrazione a un servizio più recente.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. Cosa accade ai dati archiviati localmente nel dispositivo StorSimple?

R. Possono essere copiati i dati nel dispositivo locale al servizio più recente, come descritto nei documenti di migrazione.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. Che cosa accade se si vuole proteggere il dispositivo StorSimple serie 8000?

R. Mentre i servizi potrebbero continuare a funzionare, Microsoft non sarà in grado di fornire supporto per hardware e software. La migrazione è fortemente consigliata per la continuità aziendale.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. Quali opzioni sono disponibili per la migrazione dei dati dai dispositivi StorSimple serie 8000?

R. A seconda dello scenario, gli utenti della serie StorSimple 8000 hanno le opzioni di migrazione seguenti:

* **Eseguire la migrazione a Sincronizzazione file di Azure**: usare questa opzione quando si desidera passare al formato nativo di Azure. È possibile usare Sincronizzazione file di Azure per la gestione centralizzata delle condivisioni file.

* **Altre opzioni**: è possibile contattare supporto tecnico Microsoft per discutere delle opzioni di migrazione non elencate qui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. La migrazione ad altre soluzioni di archiviazione è supportata?

R. Sì. La migrazione da altre soluzioni di archiviazione tramite copia host dei dati è supportata.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. La migrazione è supportata da Microsoft?

R. La migrazione dalla serie 8000 è un'operazione completamente supportata. Infatti, Microsoft consiglia di rivolgersi al supporto tecnico prima di iniziare la migrazione. La migrazione è attualmente un'operazione assistita. Se si intende eseguire la migrazione dei dati dal dispositivo StorSimple serie 8000, [contattare il supporto di StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. Qual è il modello di determinazione dei prezzi per la migrazione a Sincronizzazione file di Azure?

R. Quando si utilizza Sincronizzazione file di Azure, è possibile che vengano applicate le tariffe di sottoscrizione per il servizio. I clienti dovranno anche pagare i costi di archiviazione in corso. Per una stima, vedere [prezzi di AFS]( https://azure.microsoft.com/pricing/details/storage/files/) .

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. Quanto tempo ci vuole per completare una certificazione?

R. Il tempo necessario per la migrazione dei dati varia a seconda della quantità di dati e dall'opzione di aggiornamento selezionata.

## <a name="next-steps"></a>Passaggi successivi

* [Migrare i dati da una serie StorSimple 8000 a Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
