---
title: Scheda Impostazioni dell'offerta per macchine virtuali nel portale Cloud Partner per Azure Marketplace | Microsoft Docs
description: Descrive la scheda Impostazioni dell'offerta usata nella creazione di un'offerta per macchine virtuali in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639080"
---
# <a name="virtual-machine-offer-settings-tab"></a>Scheda Impostazioni dell'offerta per macchine virtuali

La pagina **Nuova offerta** per le macchine virtuali viene visualizzata con la prima scheda denominata **Impostazioni dell'offerta**.  Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio. 

![Pagina Nuova offerta per macchine virtuali](./media/publishvm_004.png)

Nella scheda **Impostazioni dell'offerta** è necessario specificare i campi obbligatori seguenti.

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
| **ID offerta**       | Identificatore univoco dell'offerta (in un profilo di pubblicazione). Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. Ha una lunghezza massima di 50 caratteri, può essere composto solo da caratteri alfanumerici minuscoli e trattini (-), ma non può terminare con un trattino. Questo campo non può essere modificato dopo che l'offerta è diventata disponibile in Azure Marketplace. <br> Se ad esempio Contoso pubblica un'offerta con l'ID offerta **sample-vm**, gli viene assegnato l'URL di Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Autore**     | Identificatore univoco della propria organizzazione in Azure Marketplace. È consigliabile associare a tutte le proprie offerte il proprio ID editore. Questo valore non può essere modificato dopo che l'offerta è stata salvata. |
| **Nome**          | Nome visualizzato dell'offerta. Questo nome viene visualizzato in Azure Marketplace e nel portale Cloud Partner. Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome dell'organizzazione qui a meno che non corrisponda al nome con cui l'offerta viene commercializzata. Se si pubblicizza questa offerta in altri siti Web e altre pubblicazioni, assicurarsi che il nome sia esattamente uguale in tutte le pubblicazioni. |
|  |  |
 
Fare clic su **Salva** per salvare le informazioni immesse. Nella scheda successiva si aggiungeranno gli [SKU](./cpp-skus-tab.md) all'offerta.
