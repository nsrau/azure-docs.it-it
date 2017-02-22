---
title: Archiviazione di Azure per enti pubblici | Microsoft Docs
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e


---
# <a name="azure-government-storage"></a>Archiviazione di Azure per enti pubblici
## <a name="azure-storage"></a>Archiviazione di Azure
Per informazioni dettagliate su questo servizio e su come usarlo, vedere la [documentazione pubblica sull'Archiviazione di Azure](../storage/index.md).

### <a name="storage-service-availability-by-azure-government-region"></a>Disponibilità del servizio di archiviazione in base all'area di Azure

| Service | Governo degli Stati Uniti - Virginia | Governo degli Stati Uniti - Iowa | Note
| --- | --- | --- | --- |
| [Archiviazione BLOB] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [Archiviazione tabelle] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [Archiviazione code] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [Archiviazione file] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [Archiviazione BLOB per accesso frequente/sporadico] (../storage/storage-blob-storage-tiers.md) |ND |ND |
| [Crittografia servizio di archiviazione] (../storage/storage-service-encryption.md) |GA |GA |
| [Archiviazione Premium] (../storage/storage-premium-storage.md) |GA |ND | Ciò vale anche per le macchine virtuali serie DS. |
| [Importazione/Esportazione BLOB] (../storage/storage-import-export-service.md) |GA |GA |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |GA |GA |

### <a name="variations"></a>Varianti
Gli URL degli account di archiviazione in Azure per enti pubblici sono diversi:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Archiviazione BLOB |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Archiviazione di accodamento |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Archiviazione tabelle |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| Archiviazione file |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> Tutti gli script e il codice devono tenere conto dei dovuti endpoint.  Vedere [Configurare le stringhe di connessione di archiviazione di Azure](../storage/storage-configure-connection-string.md). 
>
>

Per ulteriori informazioni sulle API, vedere <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Cloud Storage Account Constructor</a> (Costruttore degli account di Archiviazione cloud).

Il suffisso dell'endpoint da usare in questi overload è core.usgovcloudapi.net

> [!NOTE]
> [Microsoft Azure Storage Explorer] (../vs-azure-tools-storage-manage-with-storage-explorer.md) al momento non supporta la [connessione a una sottoscrizione di Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription) tramite aggiunta di un account in Azure per enti pubblici. Usare altri metodi di [connessione a un account di archiviazione] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service).
Quando si effettua il [collegamento a un account di archiviazione esterno] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account), nel **passaggio 3**, selezionare **Altro (specificare di seguito)** come dominio di endpoint di archiviazione e specificare **core.usgovcloudapi.net** per Azure per enti pubblici.
>
>

> [!NOTE]
> Se viene visualizzato l'errore 53 "Impossibile trovare il percorso di rete" mentre si effettua il [montaggio della condivisione file] (... / storage/storage-dotnet-how-to-use-files.md#mount-the-file-share), questo errore può essere dovuto al firewall che blocca la porta in uscita. Provare a montare la condivisione di file nella macchina virtuale che risiede nella stessa sottoscrizione di Azure dell'account di archiviazione.
>
>

> [!NOTE]
> Per distribuire il servizio StorSimple Manager Service, usare gli URL https://portal.azure.us/ e https://manage.windowsazure.us/ rispettivamente per il portale di Azure e il portale classico. Per istruzioni sulla distribuzione dell'array virtuale StorSimple, vedere [requisiti di sistema per l'array virtuale StorSimple] (../storsimple/storsimple-ova-system-requirements.md) e per StorSimple serie 8000, vedere [requisiti di rete, disponibilità elevata e software StorSimple] (../storsimple/storsimple-system-requirements.md) e passare alla sezione della distribuzione nel riquadro di navigazione di sinistra. Per la documentazione generale di StorSimple, vedere [Cos'è StorSimple?] (../storsimple/index.md).
>
>

### <a name="considerations"></a>Considerazioni
Le informazioni seguenti identificano il limite di Azure per enti pubblici per Archiviazione di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| I dati immessi, archiviati ed elaborati in un prodotto di Archiviazione di Azure possono contenere dati soggetti al controllo all'esportazione. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure. |I metadati di Archiviazione di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione del prodotto di archiviazione.  Non immettere dati regolamentati o controllati nei campi relativi a gruppi di risorse, nomi delle distribuzioni, nomi delle risorse e tag delle risorse. |

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni e aggiornamenti, iscriversi al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici</a>.



<!--HONumber=Feb17_HO2-->


