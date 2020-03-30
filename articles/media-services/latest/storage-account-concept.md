---
title: Account di archiviazione di Azure
titleSuffix: Azure Media Services
description: Informazioni su come creare un account di archiviazione di Azure da usare con Servizi multimediali di Azure.Learn how to create an Azure storage account to use with Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499830"
---
# <a name="azure-storage-accounts"></a>Account di archiviazione di Azure

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali.

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare gli account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare costi aggiuntivi di latenza e uscita dei dati.

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). Gli account BLOB solo non sono consentiti come **primaria**.

Si consiglia di utilizzare GPv2, in modo da poter sfruttare le funzionalità e le prestazioni più recenti. Per altre informazioni sugli account di archiviazione, vedere la [panoramica degli account di Archiviazione di Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Solo il livello di accesso a caldo è supportato per l'uso con Servizi multimediali di Azure, anche se gli altri livelli di accesso possono essere usati per ridurre i costi di archiviazione per il contenuto che non viene usato attivamente.

È possibile scegliere sKU diversi per l'account di archiviazione. Per altre informazioni, vedere [account di archiviazione](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Tuttavia, quando si sceglie uno SKU per la produzione, è necessario considerare `--sku Standard_RAGRS`, che fornisce la replica geografica per la continuità aziendale.

## <a name="assets-in-a-storage-account"></a>Asset in un account di archiviazione

In Servizi multimediali v3, le API di archiviazione vengono usate per caricare file in asset. Per altre informazioni, vedere Asset in Servizi multimediali di [Azure versione 3.](assets-concept.md)

> [!Note]
> Non tentare di modificare il contenuto dei contenitori BLOB generati da Media Services SDK senza usare le API di Servizi multimediali.

## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere le risorse inattivi, le risorse devono essere crittografate dalla crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali versione 3:

|Opzione di crittografia|Descrizione|Servizi multimediali v3|
|---|---|---|
|Crittografia di archiviazione di Servizi multimediali| Crittografia AES-256, chiave gestita da Servizi multimediali. |Non supportato. <sup>(1) Per quanto mi fa parte,</sup>|
|[Crittografia del servizio di archiviazione per i dati inattiviStorage service encryption for data at rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente.|Supportato.|
|[Crittografia lato client di archiviazioneStorage client-side encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta dall'archiviazione di Azure, chiave gestita dal cliente in Key Vault.Client-side encryption offered by Azure storage, key managed by customer in Key Vault.|Non supportato.|

<sup>1</sup> In Servizi multimediali v3, la crittografia di archiviazione (crittografia AES-256) è supportata solo per la compatibilità con le versioni precedenti quando gli asset sono stati creati con Servizi multimediali v2, il che significa che v3 funziona con gli asset crittografati di archiviazione esistenti ma non consente la creazione di nuovi asset.

## <a name="storage-account-errors"></a>Errori dell'account di archiviazioneStorage account errors

Lo stato "Disconnesso" per un account Servizi multimediali indica che l'account non ha più accesso a uno o più degli account di archiviazione collegati a causa di una modifica alle chiavi di accesso alle risorse di archiviazione. Sono necessarie chiavi di accesso alle risorse di archiviazione aggiornate in Servizi multimediali per eseguire molte attività nell'account.

Di seguito sono riportati gli scenari principali che potrebbero comportare il mancato accesso, da parte di un account di Servizi multimediali, agli account di archiviazione collegati.

|Problema|Soluzione|
|---|---|
|L'account di Servizi multimediali o gli account di archiviazione collegati sono stati migrati per separare le sottoscrizioni. |Eseguire la migrazione degli account di archiviazione o dell'account di Servizi multimediali in modo che siano tutti nella stessa sottoscrizione. |
|L'account di Servizi multimediali usa un account di archiviazione associato in un'altra sottoscrizione perché si tratta di un account Servizi multimediali iniziale in cui l'account di archiviazione era già precedentemente supportato. Tutti i primi account di Servizi multimediali sono stati convertiti in account basati su Azure Resources Manager moderni e avranno lo stato Disconnesso.All early Media Services accounts were converted to modern Azure Resources Manager based accounts and will have a Disconnected. |Eseguire la migrazione dell'account di archiviazione o dell'account di Servizi multimediali in modo che siano tutti nella stessa sottoscrizione.|

## <a name="azure-storage-firewall"></a>Firewall di Archiviazione di AzureAzure Storage firewall

Servizi multimediali di Azure non supporta gli account di archiviazione con il firewall di Archiviazione di Azure o [gli endpoint privati](https://docs.microsoft.com/azure/storage/common/storage-network-security) abilitati.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come collegare un account di archiviazione all'account di Servizi multimediali, vedere [Creare un account](create-account-cli-quickstart.md).
