---
title: Uso di Outlook in Azure RemoteApp | Documentazione di Microsoft
description: Informazioni su come configurare e usare Outlook in Azure RemoteApp | Microsoft Azure
services: remoteapp
documentationcenter: 
author: pavithir
manager: mbaldwin
ms.assetid: cb2a498f-9539-4522-a874-542114926a61
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a6d4fbdf0e552f50673092183e893841ec0c5aa4
ms.lasthandoff: 03/31/2017


---
# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Uso di Microsoft Outlook in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Azure RemoteApp supporta Microsoft Outlook O365. Altre informazioni sul [funzionamento di Office in Azure RemoteApp](remoteapp-officesubscription.md). Per l'uso di Outlook in Azure RemoteApp è opportuno adottare alcune impostazioni consigliate.

## <a name="cached-mode"></a>Modalità cache
La modalità cache è una configurazione consigliata per l'uso di Outlook in Azure RemoteApp. Quando si configura un account di Outlook 2013 per l'uso della modalità cache, Outlook 2013 funziona da una copia locale della cassetta postale di Microsoft Exchange dell'utente archiviata in un file di dati offline (file OST) nel computer dell'utente, insieme alla Rubrica offline. La cassetta postale nella cache e la Rubrica offline vengono aggiornate periodicamente dal servizio di O365. Altre informazioni sulle [differenze tra la modalità cache e la modalità online](https://technet.microsoft.com/library/jj683103.aspx).

L'utente può selezionare la **modalità cache** o la **modalità online** durante la configurazione dell'account o modificando le impostazioni dell'account. È anche possibile distribuire una modalità o l'altra usando lo Strumento di personalizzazione di Office o i Criteri di gruppo.  

Vedere le [istruzioni dettagliate su come abilitare la modalità cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Search
In Azure RemoteApp, l'uso della ricerca all'interno di Outlook presenta alcune limitazioni. Azure RemoteApp usa macchine virtuali in pool per supportare le sessioni utente. L'indicizzazione della ricerca dipende dall'ID computer, che varia a seconda della macchina virtuale. È possibile che ogni volta che un utente accede ad Azure RemoteApp venga indirizzato a una nuova macchina virtuale. Ciò significa che, se si abilita la ricerca locale, l'indicizzatore viene eseguito ogni volta che l'ID computer cambia (quando l'utente è in una macchina virtuale diversa). A seconda delle dimensioni del file OST, l'esecuzione dell'indicizzatore può richiedere molto tempo e consumare risorse necessarie per altre applicazioni. La ricerca, quindi, può risultare lenta o non dare alcun risultato. L'uso di un profilo utente in modalità online risolverebbe il problema, ma le prestazioni complessive diminuirebbero per la mancanza di una cache locale. Vedere il collegamento precedente per altre informazioni sulle differenze tra modalità online e modalità cache. Purtroppo, in Outlook 2013 non è possibile disabilitare la ricerca locale/indicizzata e abilitare la ricerca online per impostazione predefinita.


