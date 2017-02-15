---
title: Operazioni consentite in stati diversi in Servizi BizTalk | Microsoft Docs
description: 'Informazioni sulle azioni/operazioni consentite nei diversi stati di MABS: arresto, avvio, riavvio, sospensione, ripresa, eliminazione, ridimensionamento, aggiornamento della configurazione e backup.'
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 331c03cd0819aa4935f9b486ff38f54d23d6a7fd
ms.openlocfilehash: e3d5f89b1c8525f791e73667d6f7cd6a999ab971


---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Cosa fare o non fare quando si usa lo stato del servizio BizTalk
Le operazioni che è possibile eseguire nel servizio BizTalk variano a seconda dello stato corrente del servizio.

Se ad esempio si esegue il provisioning di un nuovo servizio BizTalk nel portale di Azure classico. al termine lo stato del servizio BizTalk risulta `active`. Con lo stato attivo è possibile arrestare, sospendere ed eliminare il servizio BizTalk. Se si tenta di arrestare il servizio BizTalk e l'operazione non riesce, allora lo stato del servizio BizTalk diventa `StopFailed`. Con lo stato `StopFailed` è possibile riavviare il servizio BizTalk. Se si tenta di eseguire un'operazione non consentita, ad esempio riprendere l'attività del servizio, verrà generato l'errore seguente:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Visualizzare gli stati possibili

Le tabelle seguenti elencano le operazioni o le azioni che possono essere eseguite quando il servizio BizTalk è in uno stato specifico. Il segno ✔ indica che l'operazione è consentita in quel determinato stato. Uno spazio vuoto indica che l'operazione non può essere eseguita in quel determinato stato.

| Stato del servizio | Inizia | Arresto | Riavvio | Sospensione | Riprendi | Eliminazione | Scalabilità | Aggiornamento <br/> Configurazione | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Attivo |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Disabled |  |  |  |  |  | ✔ | |  |  | 
| Suspended |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Arrestato | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Service Update Failed |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Vedere anche
* [Creare un servizio BizTalk mediante il portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Operazioni eseguibili nelle schede Dashboard, Monitoraggio e Scalabilità nei servizi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Funzionalità e contenuti delle edizioni Developer, Basic, Standard e Premium nei servizi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Come eseguire il backup e il ripristino di un servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Le limitazioni spiegate nei servizi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Recuperare il bus di servizio e il nome e la chiave dell'autorità emittente del Controllo di accesso per il servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)




<!--HONumber=Nov16_HO3-->


