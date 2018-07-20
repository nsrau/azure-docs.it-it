---
title: Sicurezza dell'infrastruttura di Azure | Microsoft Docs
description: L'articolo descrive come Microsoft garantisce la sicurezza dei data center di Azure.
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
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903857"
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
È importante comprendere la suddivisione di responsabilità tra Microsoft e l'utente. In locale, si è proprietari dell'intero stack ma, con lo spostamento nel cloud, alcune responsabilità vengono trasferite a Microsoft. La matrice di responsabilità seguente illustra le aree dello stack in una distribuzione SaaS, PaaS e IaaS per cui è responsabile il cliente e quelle per cui è responsabile Microsoft.

![Responsabilità condivisa][1]

L'utente ha comunque in ogni caso la responsabilità di quanto segue, a prescindere dal tipo di distribuzione:

- Dati
- Endpoint
- Account
- gestione degli accessi

Verificare di aver compreso la divisione delle responsabilità tra il cliente e Microsoft in una distribuzione SaaS, PaaS e IaaS. Per altri dettagli, vedere [Shared Responsibilities for Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) (Responsabilità per il cloud computing).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](azure-infrastructure-availability.md)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](azure-infrastructure-network.md)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrità dell'infrastruttura di Azure](azure-infrastructure-integrity.md)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
