---
title: Le impostazioni di offerta di applicazione di Azure | Azure Marketplace
description: Configurare le impostazioni per un'offerta di applicazione di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 789b783629b3cc3528eba1883b21051604cf6e14
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942938"
---
# <a name="azure-application-offer-settings-tab"></a>Scheda Impostazioni dell'offerta per un'applicazione di Azure

Questo articolo descrive come configurare le impostazioni dell'offerta per un'applicazione di Azure.

La pagina **Applicazioni Azure > Nuova offerta** si apre con la scheda **Impostazioni dell'offerta**. Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio.

![Modulo di identità dell'offerta](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Impostazioni di identità dell'offerta

In **Offer Identity** (Identità offerta) è necessario specificare le informazioni per i campi descritti nella tabella seguente.  

|    Campo         |       DESCRIZIONE                                                            |
|  ---------       |     ---------------                                                          |
| **ID offerta\***       | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID sarà visibile negli URL dei prodotti e nei report delle informazioni dettagliate. Ha una lunghezza massima di 50 caratteri e può contenere caratteri alfanumerici minuscoli e trattini (-). L'identificatore non può terminare con un trattino. **Nota:** questo campo non può essere modificato dopo la pubblicazione dell'offerta. <br> Ad esempio, se Contoso pubblica un'offerta con l'ID offerta **sample-container**, viene assegnato all'offerta l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID dell'editore\***     | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome\***          | Il nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. È consigliabile includere un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione a meno che non rappresenti il modo in cui il prodotto viene commercializzato. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|  |  |

Per salvare le impostazioni dell'offerta selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Usare la scheda [SKU](./cpp-skus-tab.md) per configurare gli SKU per l'offerta.