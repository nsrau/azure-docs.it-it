---
title: Panoramica dei vari portali necessari per creare un'offerta per Marketplace | Documentazione Microsoft
description: Panoramica dei vari portali necessari per creare un'offerta per Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 663d9a01b80b0c41e37037b0cbec10c93511d24c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="portals-you-will-need"></a>Portali necessari
Prima di iniziare il processo di pubblicazione di un’offerta, è necessario fornire alcune informazioni sui diversi portali che saranno necessari. Di seguito sono riepilogati i portali (centro per sviluppatori, portale di pubblicazione di Azure e portale di Azure) nell'ordine in cui si interagirà con loro.                                                                            

## <a name="developer-center"></a>Centro per sviluppatori
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>DESCRIZIONE
La creazione dell'account del Centro per sviluppatori Microsoft è un'attività una-tantum. Assicurarsi che la società non possieda già un account per il Centro per sviluppatori prima di tentare di crearne uno. Durante il processo, Microsoft raccoglie le informazioni sul conto bancario, i dati fiscali e le informazioni sull'indirizzo dell'azienda.

> [!NOTE]
> Se si intende pubblicare solo offerte gratuite (oppure offerte Bring Your Own License), le informazioni fiscali e bancarie non sono necessarie.
> 
> 

### <a name="identityaccount-used"></a>Identità/account usato
Idealmente, questa è una lista di distribuzione o un gruppo di sicurezza (ad esempio azurepublishing@*partnercompany*.com). La lista di distribuzione o il gruppo di sicurezza **devono** essere registrati come un account Microsoft.

> [!TIP]
> È consigliabile usare una lista di distribuzione o un gruppo di sicurezza perché in questo modo si elimina la dipendenza da una singola persona. È comunque possibile usare anche un singolo account.
> 
> 

## <a name="publishing-portal"></a>Portale di pubblicazione
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>DESCRIZIONE
Questo è il portale per lavorare sull'offerta e pubblicarla (marketing, prezzi, pubblicazione, eventuale certificazione e così via).

### <a name="identityaccount-used"></a>Identità/account usato
Il gruppo di protezione o l’elenco distribuzione precedente devono essere utilizzati per la prima volta che si accede al portale di pubblicazione. Successivamente, altri utenti possono essere aggiunti come coamministratori. In questo modo viene eseguita l'associazione ai dati di registrazione del Centro per sviluppatori.

## <a name="azure-portal"></a>Portale di Azure
[https://portal.azure.com](https://portal.azure.com)

### <a name="description"></a>DESCRIZIONE
Questo è il portale in cui è possibile visualizzare le proprie offerte in gestione temporanea e pubblicate in Azure Marketplace (applicabile per VM, modelli di soluzioni e servizi per gli sviluppatori basati su Gestione risorse di Azure)

### <a name="identityaccount-used"></a>Identità/account usato
Durante la gestione temporanea dell'offerta dal portale di pubblicazione, deve essere consentito un ID di sottoscrizione. La stessa sottoscrizione (sono associati un nome utente e una password) deve essere usata durante l'accesso a questo portale per testare l'offerta in gestione temporanea.

## <a name="see-also"></a>Vedere anche 
* [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

