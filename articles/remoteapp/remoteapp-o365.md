---
title: Uso di Office con Azure RemoteApp | Documentazione Microsoft
description: Informazioni sull&quot;interazione tra Office e Azure RemoteApp
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: f1773baf-8aa1-423c-a2f9-e4679e0463d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8c04dc5a91b7859b5d75f919da55846599038f61


---
# <a name="using-office-with-azure-remoteapp"></a>Usare Office con Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Sono disponibili due opzioni per l'hosting di applicazioni di Office in Azure RemoteApp: Office 365 ProPlus o la versione di valutazione di Office 2013 Professional Plus.

**Salve, non tutti sanno che è disponibile un nuovo articolo migliore che presto sostituirà questo attuale. Vedere [Come usare la sottoscrizione di Office 365 con Azure RemoteApp](remoteapp-officesubscription.md). Vengono illustrate tutte le informazioni necessarie per l'utilizzo di Office 365 + RemoteApp di Azure.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
È possibile creare una raccolta RemoteApp usando l'immagine modello di Office 365 ProPlus. Questa opzione consente di estendere il servizio di Office 365 a RemoteApp. È necessario disporre di un piano di sottoscrizione esistente e gli utenti devono essere dotati di licenza per il servizio Office 365 ProPlus, autonoma o tramite i piani di servizio di Office 365.

RemoteApp supporta l'attivazione di computer condivisi di Office 365. Quando si abilita l'attivazione di computer condivisi e si usa lo [strumento di distribuzione di Office](http://www.microsoft.com/download/details.aspx?id=36778) per l'installazione, Office 365 ProPlus viene installato senza essere attivato. Quando un utente accede a una raccolta contenente Office 365, Office controlla se per l'utente è stato eseguito il provisioning per Office 365 ProPlus. In caso affermativo, Office attiva temporaneamente Office 365 ProPlus. L'attivazione viene mantenuta finché l'utente non esce dal servizio.

Per usare l'attivazione di computer condivisi di Office 365, è necessario creare un [modello personalizzato](remoteapp-create-custom-image.md) e installarvi Office 365 ProPlus, in base alle [istruzioni seguenti](https://technet.microsoft.com/library/dn782858.aspx).

È possibile gestire le licenze di Office 365 degli utenti nel [portale dell'amministratore di Office 365](https://portal.office365.com/). Per altre informazioni, vedere la pagina relativa ai [piani di servizio di Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  

## <a name="office-2013-professional-plus-trial"></a>Versione di valutazione di Office 2013 Professional Plus
Durante la valutazione di 30 giorni di RemoteApp, è possibile usare l'immagine modello di Office 2013 Professional Plus (versione di valutazione) per creare una raccolta RemoteApp. È possibile assegnare utenti a questa versione di valutazione della raccolta usando i rispettivi account di lavoro di Azure Active Directory oppure gli account Microsoft. Non è necessaria una sottoscrizione aggiuntiva.

Si tratta di un'ottima opzione per provare e toccare con mano i vantaggi di Office in RemoteApp. Questa opzione è però solo prevista per scopi di valutazione e di test. Le raccolte RemoteApp create usando l'immagine modello di Office 2013 Professional Plus (versione di valutazione) non possono essere trasferite alla modalità di produzione e verranno disabilitate al termine del periodo di valutazione.

## <a name="switching-from-trial-to-production"></a>Passaggio dalla versione di valutazione alla modalità di produzione
Quando si avvia la versione di valutazione gratuita di 30 giorni, una nota nella sezione RemoteApp del portale indicherà il tempo di valutazione rimanente prima che sia necessario passare a un account a pagamento. È possibile attivare l'account e passare alla modalità di produzione usando l'apposito collegamento in questa nota.

Quando si attiva l'account, l'operazione avrà effetto su tutte le raccolte RemoteApp presenti nell'account stesso.

* Le raccolte che vengono eseguite con Windows Server 2012 R2 o con le immagini modello di Office 365 ProPlus passeranno facilmente e trasparentemente alla modalità di produzione. Tutti i dati e le impostazioni utente, comprese le sessioni in corso, rimarranno invariate.
* Se sono state caricate immagini modello personalizzate, anche la transizione delle raccolte che usano tali immagini avverrà facilmente e in modo trasparente.
* L'immagine modello di Office 2013 Professional Plus (versione di valutazione) è destinata esclusivamente alla valutazione. Le raccolte in cui si esegue questa immagine modello non possono essere passate alla modalità di produzione. Queste raccolte verranno messe in stato di "disabilitazione".

Se non si esegue la transizione alla modalità di produzione entro la scadenza della versione di valutazione, le raccolte RemoteApp verranno disabilitate. Ciò non sarà comunque un problema: le impostazioni e i dati degli utenti verranno salvati per altri 90 giorni, in modo che sia comunque possibile attivare il servizio e passare alla modalità di produzione senza alcuna perdita di dati.




<!--HONumber=Dec16_HO2-->


