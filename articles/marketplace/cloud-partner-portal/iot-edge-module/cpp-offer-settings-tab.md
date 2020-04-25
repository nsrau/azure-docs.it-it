---
title: Impostazioni dell'offerta per un modulo di Azure IoT Edge | Azure Marketplace
description: Configurare le impostazioni dell'offerta per un modulo IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148028"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Scheda Impostazioni dell'offerta del modulo IoT Edge

>[!Important]
>A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del modulo IoT Edge al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare un'offerta di modulo IOT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) .

La pagina **Moduli IoT Edge > Nuova offerta** si apre con la scheda **Impostazioni dell'offerta** visualizzata.

![Pagina Nuova offerta per moduli IoT Edge](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Impostazioni di identità dell'offerta

In **Offer Identity** (Identità offerta) è necessario specificare le informazioni per i campi descritti nella tabella seguente. Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **Offer ID (ID offerta)\***       | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID sarà visibile negli URL dei prodotti e nei report delle informazioni dettagliate. Ha una lunghezza massima di 50 caratteri e può contenere caratteri alfanumerici minuscoli e trattini (-). L'identificatore non può terminare con un trattino. **Nota:** Questo campo non può essere modificato dopo che un'offerta è stata attivata. <br> Se ad esempio Contoso pubblica un'offerta con l'ID offerta **sample-iot-edge-module**, gli viene assegnato l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Editore\***     | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Name\***          | Il nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. È consigliabile includere un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione, a meno che non si tratti del modo in cui viene commercializzato il prodotto. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|  |  |


Selezionare **Save (Salva** ) per salvare le impostazioni dell'offerta.


## <a name="next-steps"></a>Passaggi successivi

Usare la scheda [SKU](./cpp-skus-tab.md) per configurare gli SKU per l'offerta.
