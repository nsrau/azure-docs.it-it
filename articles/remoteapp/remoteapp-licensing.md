---
title: Licenze di Azure RemoteApp |Documentazione Microsoft
description: Informazioni sul funzionamento delle licenze in Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 43d0dbb905b2f2b9d98fb3bf8c073ba1c4b6f4c4
ms.lasthandoff: 03/31/2017


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Funzionamento delle licenze in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

È stato configurato il servizio Azure RemoteApp, sono stati creati i modelli e si è ora pronti per pubblicare le app per gli utenti. Ma c'è ancora un aspetto da prendere in considerazione: le licenze. Come funzionano le licenze per RemoteApp e per le app condivise tramite RemoteApp?

RemoteApp non richiede licenze Windows o licenze CAL per Desktop remoto. La parte RemoteApp è compresa nella sottoscrizione (verificare i dettagli dei [piani dei prezzi](https://azure.microsoft.com/pricing/details/remoteapp)).

Se si usa una delle immagini incluse nella sottoscrizione, è possibile condividere le app installate in tale immagine senza la necessità di una licenza separata. Ad esempio, se si utilizza l'immagine modello di Windows Server 2012 R2 per creare la raccolta, è possibile condividere System Center Endpoint Protection con gli utenti. Le uniche eccezioni a questa regola sono Office 365 ProPlus, che richiede una sottoscrizione separata, e Office 2013, che non può essere condiviso in una raccolta di produzione.

Se si desidera usare l'immagine modello di Office 365 inclusa in RemoteApp, è necessario disporre di un piano *esistente* di Office 365 ProPlus. Lo stesso vale per qualsiasi app di Office 365 pubblicate usando un modello personalizzato. È necessario attivare le app usando la propria sottoscrizione. Questo vale sia per le sottoscrizioni di valutazione che per quelle a pagamento. Per usare l'immagine modello di Office 365 durante la valutazione *e non si ha già una sottoscrizione*, andare alla pagina di Office 365 per [iscriversi](https://go.microsoft.com/fwlink/p/?LinkID=403802) a una sottoscrizione di valutazione. Per altre informazioni, vedere la pagina relativa al [funzionamento di RemoteApp e Office in combinazione](remoteapp-o365.md) .

Se durante il periodo di valutazione non si vuole ottenere una sottoscrizione di valutazione di Office 365, usare l'immagine modello di Office 2013 Professional Plus fornita con RemoteApp. Questa immagine modello può essere usata solo per 30 giorni e non può essere convertita in una raccolta a pagamento.

Per le altre app, è necessario assicurarsi di disporre della licenza per condividere l'app.

Riassumendo, è possibile pubblicare qualsiasi app di cui si detiene il diritto legale alla condivisione. È quindi necessario assicurarsi di avere effettivamente il diritto di condividere i propri programmi.

Si noti che non è possibile usare una licenza CAL o un contratto multilicenza in una raccolta nel cloud. È *possibile* usare un contratto multilicenza per attivare applicazioni nella raccolta ibrida (ad eccezione di Office). È sufficiente installarle nell'immagine modello dai supporti forniti per contratti multilicenza. Seguire le indicazioni del fornitore dell'applicazione per installare le licenze in un ambiente Desktop remoto.


