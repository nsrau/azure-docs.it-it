---
title: Impostazioni dell'offerta per un'immagine di contenitori di Azure | Azure Marketplace
description: Configurare le impostazioni dell'offerta per un contenitore di Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 61d9fa535d2bec0a52351ba6199183ea899a0e1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146269"
---
# <a name="container-offer-settings-tab"></a>Scheda delle impostazioni dell'offerta per i contenitori

> [!IMPORTANT]
> A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del contenitore di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Seguire le istruzioni riportate in [creare un'offerta di contenitore di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) per gestire le offerte migrate.

La pagina **Contenitori > Nuova offerta** si apre con la scheda **Offer Settings** (Impostazioni dell'offerta) visualizzata. 

![Identità dell'offerta](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Impostazioni di identità dell'offerta

In **Offer Identity** (Identità offerta) è necessario specificare le informazioni per i campi descritti nella tabella seguente. Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **Offer ID (ID offerta)\***       | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID sarà visibile negli URL dei prodotti e nei report delle informazioni dettagliate. Ha una lunghezza massima di 50 caratteri e può contenere caratteri alfanumerici minuscoli e trattini (-). L'identificatore non può terminare con un trattino. **Nota:** Questo campo non può essere modificato dopo che un'offerta è stata attivata. <br> Ad esempio, se Contoso pubblica un'offerta con l'ID offerta **sample-container**, viene assegnato all'offerta l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID editore\***     | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome\***          | Il nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. È consigliabile includere un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione, a meno che non si tratti del modo in cui viene commercializzato il prodotto. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|  |  |

Selezionare **Save (Salva** ) per salvare le impostazioni dell'offerta.


## <a name="next-steps"></a>Passaggi successivi

Usare la scheda [SKU](./cpp-skus-tab.md) per configurare gli SKU per l'offerta.
