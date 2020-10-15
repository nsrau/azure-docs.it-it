---
title: Uso dei certificati - Azure Batch
description: Usare i certificati per abilitare l'autenticazione delle applicazioni
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146386"
---
# <a name="using-certificates-with-batch"></a>Uso dei certificati con Batch

Attualmente, il motivo principale per usare i certificati con Batch è se sono presenti applicazioni in esecuzione nei pool che devono eseguire l'autenticazione con un endpoint. 

Se non si dispone già di un certificato, è possibile creare un certificato autofirmato usando la riga di comando di `makecert`.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Aggiornare i certificati manualmente tramite il portale di Azure

1. Dall'account Batch in cui si vuole caricare un certificato selezionare **Certificati** quindi selezionare **Aggiungi**. 

2. Caricare il certificato con estensione .pfx o .cer. 

Al termine del caricamento, il certificato viene aggiunto a un elenco di certificati ed è possibile verificare l'identificazione personale.

A questo punto, quando si crea un pool di Batch, è possibile accedere a Certificati nel pool e assegnare il certificato caricato al pool.

## <a name="next-steps"></a>Passaggi successivi

Batch ha un'API del certificato, [AZ batch certificate create](/cli/azure/batch/certificate)

Per informazioni sull'uso di Key Vault, vedere [Accedere in modo sicuro a Key Vault con Batch](credential-access-key-vault.md).
