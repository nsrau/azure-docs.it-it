---
title: Attestazione di enclave in Azure
description: Informazioni su come usare l'attestazione per verificare che l'ambiente di riservatezza dei computer trusted sia sicuro
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997829"
---
# <a name="attesting-sgx-enclaves"></a>Attestazione di SGX enclave

Confidential computing in Azure offre macchine virtuali basate su Intel SGX che possono isolare una parte del codice o dei dati. Quando si lavora con queste [enclave](confidential-computing-enclaves.md), è opportuno ottenere la verifica e la convalida dell'ambiente attendibile. Questa verifica è il processo di attestazione. 

## <a name="overview"></a>Panoramica 

L'attestazione assicura a una relying party che il software è in esecuzione in un'enclave e che quest'ultima è aggiornata e protetta. Ad esempio, un'enclave chiede all'hardware sottostante di generare una credenziale che include la prova dell'esistenza dell'enclave nella piattaforma. Il report può quindi essere assegnato a una seconda enclave che verifica che è stato generato nella stessa piattaforma.

![attestare il codice nell'enclave](media/attestation/attestation.png)



L'attestazione deve essere implementata tramite un servizio di attestazione sicuro e compatibile con il software e il silicio del sistema. Alcuni esempi di servizi che è possibile usare sono

- [Attestazione Microsoft Azure (anteprima)](https://docs.microsoft.com/azure/attestation/overview) o
- [Servizi di attestazione e provisioning di Intel](https://software.intel.com/sgx/attestation-services)


entrambi sono compatibili con l'infrastruttura Intel SGX di Azure Confidential computing. 

## <a name="next-steps"></a>Passaggi successivi
Provare gli [esempi di attestazione Microsoft Azure per le app compatibili con l'enclave](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).