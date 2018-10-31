---
title: Impostazioni dell'offerta per un modulo Azure IoT Edge | Microsoft Docs
description: Configurare le impostazioni dell'offerta per un modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a2ec41d7e2f21dc1550cd3c56009204842e1641
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432634"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Scheda Impostazioni dell'offerta del modulo IoT Edge

La pagina **Moduli IoT Edge > Nuova offerta** si apre con la scheda **Impostazioni dell'offerta** visualizzata. 

![Pagina Nuova offerta per moduli IoT Edge](./media/iot-edge-module-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Impostazioni di identità dell'offerta

In **Offer Identity** (Identità offerta) è necessario specificare le informazioni per i campi descritti nella tabella seguente. Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **ID offerta**       | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID sarà visibile negli URL dei prodotti e nei report delle informazioni dettagliate. Ha una lunghezza massima di 50 caratteri e può contenere caratteri alfanumerici minuscoli e trattini (-). L'identificatore non può terminare con un trattino. **Nota:** questo campo non può essere modificato dopo che l'offerta è diventata disponibile in Azure Marketplace. <br> Se ad esempio Contoso pubblica un'offerta con l'ID offerta **sample-iot-edge-module**, gli viene assegnato l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Autore**     | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome**          | Il nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. È consigliabile includere un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione a meno che non rappresenti il modo in cui il prodotto viene commercializzato. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|  |  |

Per salvare le impostazioni dell'offerta selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Usare la scheda [SKU](./cpp-skus-tab.md) per configurare gli SKU per l'offerta.
