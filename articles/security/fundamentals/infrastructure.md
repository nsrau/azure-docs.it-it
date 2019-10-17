---
title: Sicurezza dell'infrastruttura di Azure | Microsoft Docs
description: L'articolo descrive come Microsoft opera per garantire la sicurezza dei data center di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a17d98d49d2c653e2498a663829d26e8a171fd74
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433503"
---
# <a name="azure-infrastructure-security"></a>Sicurezza dell'infrastruttura di Azure
Microsoft Azure è in esecuzione in data center gestiti da Microsoft. Tali data center, distribuiti geograficamente, sono conformi agli standard di settore, ad esempio ISO/IEC 27001:2013 e NIST SP 800-53, per la sicurezza e l'affidabilità. I data center vengono gestiti, monitorati e amministrati da operatori Microsoft. Tali operatori vantano anni di esperienza nella fornitura dei maggiori servizi online nel mondo, con continuità 24 ore su 24, 7 giorni su 7.

Questa serie di articoli contiene informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure. L'articolo descrive gli aspetti seguenti:

- [Sicurezza fisica](physical-security.md)
- [Disponibilità](infrastructure-availability.md)
- [Componenti e limiti](infrastructure-components.md)
- [Architettura di rete](infrastructure-network.md)
- [Rete di produzione](production-network.md)
- [Database SQL](infrastructure-sql.md)
- [Operazioni](infrastructure-operations.md)
- [Monitoraggio](infrastructure-monitoring.md)
- [Integrità](infrastructure-integrity.md)
- [Protezione dei dati](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Modello di responsabilità condivisa
È importante comprendere la suddivisione di responsabilità tra Microsoft e l'utente. In locale, si è proprietari dell'intero stack ma, con lo spostamento nel cloud, alcune responsabilità vengono trasferite a Microsoft. Il grafico seguente illustra le aree di responsabilità, a seconda del tipo di distribuzione di stack (software come un servizio [SaaS], piattaforma distribuita come servizio [PaaS], infrastruttura distribuita come servizio [IaaS] e in locale).

![Il grafico mostra le responsabilità](./media/infrastructure/responsibility-zones.png)

Si è sempre responsabili di quanto segue, indipendentemente dal tipo di distribuzione:

- Dati
- Endpoint
- Account
- gestione degli accessi

Verificare di aver compreso la divisione delle responsabilità tra il cliente e Microsoft in una distribuzione SaaS, PaaS e IaaS. Per altri dettagli, vedere [Responsabilità condivise per il cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](infrastructure-availability.md)
- [Azure information system components and boundaries](infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](infrastructure-network.md)
- [Rete di produzione di Azure](production-network.md)
- [Funzionalità di sicurezza del database SQL di Azure](infrastructure-sql.md)
- [Azure production operations and management](infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](protection-customer-data.md)


