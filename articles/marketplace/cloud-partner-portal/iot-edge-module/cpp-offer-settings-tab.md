---
title: Impostazioni dell'offerta per un modulo di Azure IoT Edge | Azure Marketplace
description: Configurare le impostazioni dell'offerta per un modulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 1043f467a7363bc0e3eedba40fd2246015592276
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814081"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Scheda Impostazioni dell'offerta del modulo IoT Edge

La pagina **Moduli IoT Edge > Nuova offerta** si apre con la scheda **Impostazioni dell'offerta** visualizzata. 

![Pagina Nuova offerta per moduli IoT Edge](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Impostazioni di identità dell'offerta

In **Offer Identity** (Identità offerta) è necessario specificare le informazioni per i campi descritti nella tabella seguente. Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **ID offerta\***       | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID sarà visibile negli URL dei prodotti e nei report delle informazioni dettagliate. Ha una lunghezza massima di 50 caratteri e può contenere caratteri alfanumerici minuscoli e trattini (-). L'identificatore non può terminare con un trattino. **Nota:** Questo campo non può essere modificato dopo che un'offerta è stata attivata. <br> Se ad esempio Contoso pubblica un'offerta con l'ID offerta **sample-iot-edge-module**, gli viene assegnato l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **\* editore**     | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome\***          | Nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. È consigliabile includere un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione, a meno che non si tratti del modo in cui viene commercializzato il prodotto. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|  |  |


Per salvare le impostazioni dell'offerta selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

Usare la scheda [SKU](./cpp-skus-tab.md) per configurare gli SKU per l'offerta.
