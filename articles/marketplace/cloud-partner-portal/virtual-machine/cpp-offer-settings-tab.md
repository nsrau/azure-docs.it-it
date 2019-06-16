---
title: Scheda Impostazioni di offerta di macchina virtuale nel portale per Cloud Partner per Azure Marketplace
description: Descrive la scheda Impostazioni dell'offerta usata nella creazione di un'offerta per macchine virtuali in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938162"
---
# <a name="virtual-machine-offer-settings-tab"></a>Scheda Impostazioni dell'offerta per macchine virtuali

La pagina **Nuova offerta** per le macchine virtuali viene visualizzata con la prima scheda denominata **Impostazioni dell'offerta**.  

![Pagina Nuova offerta per macchine virtuali](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Campi di impostazioni dell'offerta

Nel **impostazioni dell'offerta** scheda, è necessario specificare i campi seguenti.  Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **ID offerta\***   | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. Ha una lunghezza massima di 50 caratteri, può essere composto solo da caratteri alfanumerici minuscoli e trattini (-), ma non può terminare con un trattino. Questo campo non può essere modificato dopo che l'offerta è diventata disponibile in Azure Marketplace. <br> Se ad esempio Contoso pubblica un'offerta con l'ID offerta **sample-vm**, gli viene assegnato l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Publisher\***  | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome\***       | Nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione qui a meno che non corrisponda al nome con cui l'offerta viene commercializzata. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|   |   |
 
Fare clic su **salvare** dopo aver specificato tutti i campi. 


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva si aggiungeranno gli [SKU](./cpp-skus-tab.md) all'offerta.
