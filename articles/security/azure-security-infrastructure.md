---
title: Sicurezza dell'infrastruttura di Azure | Microsoft Docs
description: L'articolo descrive come Microsoft opera per garantire la sicurezza dei data center di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 9385c6ea8d2a04e9a8595a22e6e2ff7638394b41
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172973"
---
# <a name="azure-infrastructure-security"></a>Sicurezza dell'infrastruttura di Azure
Microsoft Azure è in esecuzione in data center gestiti da Microsoft. Tali data center, distribuiti geograficamente, sono conformi agli standard di settore, ad esempio ISO/IEC 27001:2013 e NIST SP 800-53, per la sicurezza e l'affidabilità. I data center vengono gestiti, monitorati e amministrati da operatori Microsoft. Tali operatori vantano anni di esperienza nella fornitura dei maggiori servizi online nel mondo, con continuità 24 ore su 24, 7 giorni su 7.

Questa serie di articoli contiene informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure. L'articolo descrive gli aspetti seguenti:

- [Sicurezza fisica](azure-physical-security.md)
- [Disponibilità](azure-infrastructure-availability.md)
- [Componenti e limiti](azure-infrastructure-components.md)
- [Architettura di rete](azure-infrastructure-network.md)
- [Rete di produzione](azure-production-network.md)
- [Database SQL](azure-infrastructure-sql.md)
- [Operazioni](azure-infrastructure-operations.md)
- [Monitoraggio](azure-infrastructure-monitoring.md)
- [Integrità](azure-infrastructure-integrity.md)
- [Protezione dei dati](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Modello di responsabilità condivisa
È importante comprendere la suddivisione di responsabilità tra Microsoft e l'utente. In locale, si è proprietari dell'intero stack ma, con lo spostamento nel cloud, alcune responsabilità vengono trasferite a Microsoft. Il grafico seguente illustra le aree di responsabilità, a seconda del tipo di distribuzione di stack (software come un servizio [SaaS], piattaforma distribuita come servizio [PaaS], infrastruttura distribuita come servizio [IaaS] e in locale).

![Il grafico mostra le responsabilità][1]

Si è sempre responsabili di quanto segue, indipendentemente dal tipo di distribuzione:

- Dati
- Endpoint
- Account
- gestione degli accessi

Verificare di aver compreso la divisione delle responsabilità tra il cliente e Microsoft in una distribuzione SaaS, PaaS e IaaS. Per altri dettagli, vedere [Responsabilità condivise per il cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](azure-infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
