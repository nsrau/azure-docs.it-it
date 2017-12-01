---
title: Progetto per l'elaborazione dei pagamenti di Azure - Panoramica generale
description: "Progetto per l'elaborazione dei pagamenti di Azure - Matrice di responsabilità dei clienti (panoramica)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>Requisiti PCI DSS - Panoramica generale

Lo standard PCI DSS (Payment Card Industry Data Security Standard) è stato sviluppato per promuovere e ottimizzare la sicurezza dei dati di titolari di carte e facilitare l'ampia adozione globale di misure coerenti per la protezione dei dati. PCI DSS offre una linea di base di requisiti tecnici e operativi progettati per la protezione dei dati degli account. Lo standard PCI DSS si applica a tutte le entità coinvolte nell'elaborazione delle carte di pagamento, tra cui commercianti, elaboratori di dati, acquirenti, emittenti e provider di servizi. I requisiti dello standard PCI DSS si applicano anche a tutte le altre entità che archiviano, elaborano o trasmettono i dati di titolari di carte e/o dati di autenticazione sensibili. Di seguito è riportata una panoramica generale dei 12 requisiti di PCI DSS.

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

|   |   |
|---|---|
| **Creare e gestire una rete<br/>e sistemi protetti** | 1. [Installare e gestire una configurazione del firewall per proteggere i dati di titolari di carte](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Non usare i valori predefiniti del fornitore per le password del sistema e altri parametri di sicurezza](pci-dss-requirement-2-password.md) |  
| **Proteggere i dati di titolari di carte** | 3. [Proteggere i dati di titolari di carte archiviati](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Crittografare la trasmissione dei dati di titolari di carte su reti pubbliche aperte](pci-dss-requirement-4-encryption.md) |
| **Implementare un piano di<br/>gestione delle vulnerabilità** | 5. [Proteggere tutti i sistemi da malware e aggiornare regolarmente il software o i programmi antivirus](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Sviluppare e gestire sistemi e applicazioni sicuri](pci-dss-requirement-6-secure-system.md) |
| **Implementare misure di controllo<br/>di accesso avanzate** | 7. [Limitare l'accesso ai dati di titolari di carte al personale autorizzato per motivi professionali](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identificare e autenticare l'accesso ai componenti di sistema](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Limitare l'accesso fisico ai dati di titolari di carte](pci-dss-requirement-9-physical-access.md) |
| **Monitorare e testare<br/>regolarmente le reti** | 10. [Tenere traccia e monitorare tutti gli accessi alle risorse di rete e ai dati di titolari di carte](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Testare regolarmente sistemi e processi di sicurezza](pci-dss-requirement-11-testing.md) |
| **Implementare criteri di<br/>sicurezza delle informazioni** | 12. [Gestire criteri che garantiscano la sicurezza delle informazioni per tutto il personale](pci-dss-requirement-12-policy.md) |

