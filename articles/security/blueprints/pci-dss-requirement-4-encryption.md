---
title: Piano per l'elaborazione dei pagamenti di Azure - Requisiti di crittografia
description: Requisito 4 di PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Requisiti di crittografia per ambienti conformi a PCI DSS 
## <a name="pci-dss-requirement-4"></a>Requisito 4 di PCI DSS

**Crittografare la trasmissione dei dati dei titolari di carte su reti pubbliche aperte**

> [!NOTE]
> Questi requisiti sono definiti dal [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) nell'ambito di [PCI Data Security Standard (DSS) versione 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Per informazioni sulle procedure di test e indicazioni per ogni requisito, vedere lo standard PCI DSS.

Le informazioni sensibili devono essere crittografate durante la trasmissione su reti a cui utenti non autorizzati possono accedere facilmente. Reti wireless configurate in modo errato e vulnerabilità in protocolli di crittografia e autenticazione legacy continuano a essere l'obiettivo di utenti non autorizzati che sfruttano queste vulnerabilità per ottenere l'accesso con privilegi agli ambienti dei dati dei titolari di carte.

## <a name="pci-dss-requirement-41"></a>Requisito 4.1 di PCI DSS

**4.1** Usare la crittografia avanzata e i protocolli di sicurezza (ad esempio, TLS, IPSec, SSH e così via) per proteggere i dati sensibili dei titolari di carte quando vengono trasmessi su reti pubbliche aperte, incluso quando:
- Vengono accettati solo certificati e chiavi affidabili.
- Il protocollo in uso supporta solo versioni o configurazioni sicure.
- Il livello di crittografia è corretto per la metodologia di crittografia in uso. 

> [!NOTE]
> Quando si usa SSL/Early TLS, è necessario soddisfare i requisiti nell'appendice A2.
>
> Gli esempi di reti pubbliche aperte includono, a titolo esemplificativo:
> - Internet
> - Tecnologie wireless, tra cui 802.11 e Bluetooth
> - Tecnologie mobili, ad esempio, comunicazioni GSM (Global System for Mobile), CDMA (Code Division Multiple Access)
> - GPRS (General Packet Radio Service)
> - Comunicazioni satellitari


**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Contoso Webstore è una soluzione PaaS che implementa la crittografia avanzata per la distribuzione, come segue:<br /><br />Per soddisfare i requisiti dei dati crittografati inattivi, [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) usa quanto segue:<br /><br /><ul><li>[Crittografia del servizio di archiviazione di Azure per dati inattivi](/azure/storage/storage-service-encryption)</li><li>Database SQL: un'istanza di database SQL PaaS viene usata per presentare le misure di sicurezza del database. Per altre informazioni, vedere [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Linee guida per PCI - Database SQL di Azure).</li><li>[Crittografia dischi di Azure (BitLocker)](/azure/security/azure-security-disk-encryption)</li></ul>L'uso di Azure Key Vault è allineato ai requisiti di Azure per enti pubblici, PCI DSS e HIPAA.|



### <a name="pci-dss-requirement-411"></a>Requisito 4.1.1 di PCI DSS

**4.1.1** Garantire che le reti wireless che trasmettono i dati dei titolari di carte o che sono connesse all'ambiente dei dati dei titolari di carte usino le procedure consigliate del settore (ad esempio, IEEE 802.11i) per implementare la crittografia avanzata per l'autenticazione e la trasmissione.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | Nella soluzione non vengono implementate funzionalità SNMP e wireless.|



## <a name="pci-dss-requirement-42"></a>Requisito 4.2 di PCI DSS

**4.2** Non inviare mai PAN non protetti tramite le tecnologie di messaggistica degli utenti finali (ad esempio, posta elettronica, messaggistica istantanea, SMS, chat e così via).

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | In Contoso Webstore non sono implementate soluzioni di messaggistica che possono inviare dati PAN (Primary Account Number).|



## <a name="pci-dss-requirement-43"></a>Requisito 4.3 di PCI DSS

**4.3** Assicurarsi che i criteri di sicurezza e le procedure operative per la crittografia delle trasmissioni dei dati di titolari di carte siano documentati, applicati e noti a tutte le parti interessate.

**Responsabilità:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Non applicabile |
| **Cliente<br />(piano&nbsp;PCI&#8209;DSS)** | I clienti sono responsabili della documentazione e della crittografia delle trasmissioni contenenti i dati dei titolari di carte.|




