---
title: Gli account di archiviazione di Azure con gli account di servizi multimediali di Azure | Microsoft Docs
description: Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali.
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
ms.openlocfilehash: 5c5bfa224b87040f5142663e6adab01072c6e6ba
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619292"
---
# <a name="azure-storage-accounts"></a>Account di Archiviazione di Azure

Per avviare le operazioni di gestione, crittografia, codifica, analisi e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. 

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). <br/>Gli account solo BLOB non sono consentiti come **primari**. 

È consigliabile usare per utilizzo generico v2, pertanto è possibile sfruttare le funzionalità più recenti sulle prestazioni. Per altre informazioni sugli account di archiviazione, vedere la [panoramica degli account di Archiviazione di Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Solo il livello ad accesso frequente è supportata per l'uso con servizi multimediali di Azure, anche se altri livelli di accesso possono essere utilizzati per ridurre i costi di archiviazione sul contenuto che non si è in uso.

Esistono diversi SKU è possibile scegliere per l'account di archiviazione. Per altre informazioni, vedere [account di archiviazione](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Tuttavia, quando si sceglie SKU per la produzione, è consigliabile usare `--sku Standard_RAGRS` che offre la replica geografica per la continuità aziendale. 

## <a name="assets-in-a-storage-account"></a>Asset in un account di archiviazione

In servizi multimediali v3, le API di archiviazione vengono usate per caricare file nell'asset. Per altre informazioni, vedere [concetto asset](assets-concept.md).

> [!Note]
> È consigliabile non tentare di modificare il contenuto dei contenitori BLOB generati dall'SDK di Servizi multimediali senza usare le API di Servizi multimediali.
 
## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali versione 3:

|Opzione di crittografia|Descrizione|Servizi multimediali v3|
|---|---|---|
|Crittografia di archiviazione di Servizi multimediali| Crittografia AES-256, chiave gestita da Servizi multimediali|Non supportato<sup>(1)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente|Supportato|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta da Archiviazione di Azure, chiave gestita dal cliente in Key Vault|Non supportate|

<sup>1</sup> In Servizi multimediali versione 3 la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. In altre parole, la versione 3 funziona con asset con crittografia di archiviazione esistenti, ma non consente la creazione di nuovi asset di questo tipo.

## <a name="storage-account-errors"></a>Errori di account di archiviazione

Lo stato "Disconnesso" per un account Servizi multimediali indica che l'account non ha più accesso a uno o più degli account di archiviazione collegati a causa di una modifica alle chiavi di accesso alle risorse di archiviazione. Sono necessarie chiavi di accesso alle risorse di archiviazione aggiornate in Servizi multimediali per eseguire molte attività nell'account.

Di seguito sono riportati gli scenari principali che potrebbero comportare il mancato accesso, da parte di un account di Servizi multimediali, agli account di archiviazione collegati. 

|Problema|Soluzione|
|---|---|
|L'account di Servizi multimediali o gli account di archiviazione collegati sono stati migrati per separare le sottoscrizioni. |Eseguire la migrazione degli account di archiviazione o dell'account di servizi multimediali in modo che si trovino tutti nella stessa sottoscrizione. |
|L'account di Servizi multimediali usa un account di archiviazione associato in un'altra sottoscrizione perché si tratta di un account Servizi multimediali iniziale in cui l'account di archiviazione era già precedentemente supportato. Tutti gli account di servizi multimediali iniziali sono stati convertiti in moderni account di Azure Resource Manager (ARM) e presentano lo stato Disconnesso. |Eseguire la migrazione dell'account di archiviazione o dell'account di Servizi multimediali in modo che si trovino tutti nella stessa sottoscrizione.|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come collegare un account di archiviazione all'account di Servizi multimediali, vedere [Creare un account](create-account-cli-quickstart.md).
