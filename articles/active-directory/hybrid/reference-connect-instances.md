---
title: 'Azure AD Connect: istanze del servizio di sincronizzazione | Microsoft Docs'
description: Questa pagina contiene considerazioni speciali per le istanze di Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 0a3cbb352774fa319ad8ea6ad77f0cf089f75ab7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489598"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerazioni speciali sule istanze
Azure AD Connect si usa soprattutto con l'istanza di Azure AD e Office 365 disponibili in tutto il mondo. Ma esistono anche altre istanze e hanno requisiti diversi per gli URL e altre considerazioni speciali.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) è un sovereign cloud gestito da un trustee dei dati tedesco.

| URL da aprire nel server proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Elenchi di revoche dei certificati |

Quando si accede al tenant di Azure AD, è necessario usare un account nel dominio onmicrosoft.de.

Funzionalità attualmente non presenti in Microsoft Cloud Germany:

* **Writeback delle password** è disponibile in anteprima con Azure AD Connect versione 1.1.570.0 e successive.
* Altri servizi di Azure AD Premium non sono disponibili.

## <a name="microsoft-azure-government-cloud"></a>Cloud di Microsoft Azure per enti pubblici
Il [cloud di Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/) è un cloud riservato al governo degli Stati Uniti.

È stato supportato da versioni precedenti di DirSync. A partire dalla build 1.1.180 di Azure AD Connect è supportata la generazione successiva del cloud. Questa generazione usa endpoint situati esclusivamente negli Stati Uniti e un elenco diverso di URL da aprire nel server proxy.

| URL da aprire nel server proxy |
| --- |
| \*.microsoftonline.com |
| \*. microsoftonline.us |
| \*.windows.net (necessario per il rilevamento automatico del tenant di Azure AD per enti pubblici) |
| \*.gov.us.microsoftonline.com |
| +Elenchi di revoche dei certificati |

> [!NOTE]
> A partire da AAD Connect versione 1.1.647.0, l'impostazione del valore di AzureInstance nel Registro di sistema non è più necessaria, a condizione che *.windows.net sia aperto nel server proxy.

Funzionalità attualmente non presenti nel cloud di Microsoft Azure per enti pubblici:

* **Writeback delle password** è disponibile in anteprima con Azure AD Connect versione 1.1.570.0 e successive.
* Altri servizi di Azure AD Premium non sono disponibili.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
