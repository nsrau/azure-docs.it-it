---
title: Scheda Impostazioni offerta macchina virtuale nel portale per i partner cloud per Azure MarketplaceVirtual Machine Offer Settings tab in the Cloud Partner Portal for Azure Marketplace
description: Descrive la scheda Impostazioni dell'offerta usata nella creazione di un'offerta per macchine virtuali in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 94be2e5d3c2c941ab17401a743ea86acbe8b6252
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273801"
---
# <a name="virtual-machine-offer-settings-tab"></a>Scheda Impostazioni dell'offerta per macchine virtuali

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo a spostare la gestione delle offerte di Macchine virtuali di Azure nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare un'offerta di macchina virtuale](https://aka.ms/CreateAzureVMoffer) di Azure per gestire le offerte migrate.

La pagina **Nuova offerta** per le macchine virtuali viene visualizzata con la prima scheda denominata **Impostazioni dell'offerta**.  

![Pagina Nuova offerta per macchine virtuali](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Campi di impostazioni dell'offerta

Nella scheda **Impostazioni offerta** è necessario specificare i seguenti campi.  Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **Offer ID (ID offerta)\***   | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. Ha una lunghezza massima di 50 caratteri, può essere composto solo da caratteri alfanumerici minuscoli e trattini (-), ma non può terminare con un trattino. Questo campo non può essere modificato dopo che l'offerta è diventata disponibile in Azure Marketplace. <br> Se ad esempio Contoso pubblica un'offerta con l'ID offerta **sample-vm**, gli viene assegnato l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Editore\***  | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome\***       | Nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome della tua organizzazione qui a meno che non sia così che viene commercializzato. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|   |   |
 
Dopo aver fornito tutti i campi, fare clic su **Salva.** 


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva si aggiungeranno gli [SKU](./cpp-skus-tab.md) all'offerta.
