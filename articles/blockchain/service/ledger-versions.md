---
title: Azure Blockchain Service supportate le versioni di contabilità, l'applicazione di patch e aggiornamento
description: Panoramica delle versioni Ledger supportati nel servizio di Azure Blockchain, inclusi i criteri relativi sistemi dell'applicazione di patch e sistema gestito e aggiornamenti gestita dall'utente.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027886"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioni supportate di contabilità generale del servizio di Azure Blockchain

Usa Azure Blockchain Service basato su Ethereum [Quorum](https://github.com/jpmorganchase/quorum/wiki) ledger progettato per l'elaborazione delle transazioni private all'interno di un gruppo di partecipanti noti, identificato come un consorzio nel servizio di Azure Blockchain.

Attualmente, il servizio di Blockchain di Azure supporta [Quorum versione 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) e [gestore transazioni Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti

Controllo delle versioni di quorum viene eseguita tramite una versione principale, versione principale e una versione secondaria. Ad esempio, se il Quorum versione 2.0.1, 2 è una versione principale, 0 è una versione principale e versione secondaria è 1. Il servizio applicherà automaticamente patch intermedie secondaria della contabilità. Attualmente, l'aggiornamento versioni principali e principali non sono supportate.

## <a name="availability-of-new-ledger-versions"></a>Disponibilità di nuove versioni di contabilità generale

Azure Blockchain Service fornisce le versioni più recenti di contabilità generale entro 60 giorni di essere disponibile dal produttore del libro mastro. Un massimo di quattro principali versioni intermedie vengono forniti per consorzi possibile scegliere durante il provisioning di un nuovo membro e consortium.

## <a name="next-steps"></a>Passaggi successivi

[Limiti di servizio di Azure Blockchain](limits.md)
