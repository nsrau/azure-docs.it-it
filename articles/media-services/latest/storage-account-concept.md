---
title: Account di archiviazione di Azure
titleSuffix: Azure Media Services
description: Informazioni su come creare un account di archiviazione di Azure da usare con servizi multimediali di Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499830"
---
# <a name="azure-storage-accounts"></a>Account di archiviazione di Azure

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali.

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. Si consiglia vivamente di usare gli account di archiviazione nella stessa località dell'account di servizi multimediali per evitare ulteriori costi di latenza e uscita dei dati.

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). Gli account solo BLOB non sono consentiti come **primari**.

Si consiglia di usare GPv2, in modo da poter sfruttare le funzionalità e le prestazioni più recenti. Per altre informazioni sugli account di archiviazione, vedere la [panoramica degli account di Archiviazione di Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Solo il livello di accesso frequente è supportato per l'uso con servizi multimediali di Azure, anche se gli altri livelli di accesso possono essere usati per ridurre i costi di archiviazione sul contenuto che non viene usato attivamente.

Esistono diversi SKU che è possibile scegliere per l'account di archiviazione. Per altre informazioni, vedere [account di archiviazione](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Tuttavia, quando si sceglie uno SKU per la produzione, è `--sku Standard_RAGRS`necessario prendere in considerazione, che fornisce la replica geografica per la continuità aziendale.

## <a name="assets-in-a-storage-account"></a>Asset in un account di archiviazione

In servizi multimediali V3, le API di archiviazione vengono usate per caricare file in asset. Per altre informazioni, vedere [asset in servizi multimediali di Azure V3](assets-concept.md).

> [!Note]
> Non tentare di modificare il contenuto dei contenitori BLOB generati da Media Services SDK senza usare le API di servizi multimediali.

## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, gli asset devono essere crittografati tramite la crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali versione 3:

|Opzione di crittografia|Descrizione|Servizi multimediali v3|
|---|---|---|
|Crittografia di archiviazione di servizi multimediali| Crittografia AES-256, chiave gestita da servizi multimediali. |Non supportato. <sup>(1)</sup>|
|[Crittografia del servizio di archiviazione per i dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da archiviazione di Azure, chiave gestita da Azure o dal cliente.|Supportato.|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta da archiviazione di Azure, la chiave gestita dal cliente in Key Vault.|Non supportata.|

<sup>1</sup> in servizi multimediali V3, la crittografia di archiviazione (crittografia AES-256) è supportata solo per la compatibilità con le versioni precedenti quando gli asset sono stati creati con servizi multimediali V2, il che significa che V3 funziona con asset crittografati di archiviazione esistenti ma non consente la creazione di nuove risorse.

## <a name="storage-account-errors"></a>Errori dell'account di archiviazione

Lo stato "Disconnesso" per un account Servizi multimediali indica che l'account non ha più accesso a uno o più degli account di archiviazione collegati a causa di una modifica alle chiavi di accesso alle risorse di archiviazione. Sono necessarie chiavi di accesso alle risorse di archiviazione aggiornate in Servizi multimediali per eseguire molte attività nell'account.

Di seguito sono riportati gli scenari principali che potrebbero comportare il mancato accesso, da parte di un account di Servizi multimediali, agli account di archiviazione collegati.

|Problema|Soluzione|
|---|---|
|L'account di Servizi multimediali o gli account di archiviazione collegati sono stati migrati per separare le sottoscrizioni. |Eseguire la migrazione degli account di archiviazione o degli account di servizi multimediali in modo che si trovino tutti nella stessa sottoscrizione. |
|L'account di Servizi multimediali usa un account di archiviazione associato in un'altra sottoscrizione perché si tratta di un account Servizi multimediali iniziale in cui l'account di archiviazione era già precedentemente supportato. Tutti gli account di servizi multimediali precedenti sono stati convertiti in account basati su Azure Resource Manager moderni e avranno uno stato disconnesso. |Eseguire la migrazione dell'account di archiviazione o dell'account di servizi multimediali in modo che si trovino tutti nella stessa sottoscrizione.|

## <a name="azure-storage-firewall"></a>Firewall di archiviazione di Azure

Servizi multimediali di Azure non supporta gli account di archiviazione con il firewall di archiviazione di Azure o gli [endpoint privati](https://docs.microsoft.com/azure/storage/common/storage-network-security) abilitati.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come collegare un account di archiviazione all'account di Servizi multimediali, vedere [Creare un account](create-account-cli-quickstart.md).
