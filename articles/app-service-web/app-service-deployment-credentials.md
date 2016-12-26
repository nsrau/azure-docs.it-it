---
title: Credenziali per la distribuzione del Servizio app di Azure | Microsoft Docs
description: Informazioni su come usare le credenziali per la distribuzione del Servizio app di Azure.
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/21/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 73753bb96d2c9680ed5d5bdf5eb3780e371c08d3
ms.openlocfilehash: 53f971624c2c7a7f64eed257aeb0c402461cc7ba


---
# <a name="azure-app-service-deployment-credentials"></a>Credenziali per la distribuzione del Servizio app di Azure
La piattaforma [Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) supporta due tipi di credenziali per la distribuzione dei contenuti.
* Credenziali per la distribuzione: credenziali con ambito di utente
* Profilo di pubblicazione: credenziali con ambito di app 

## <a name="a-nameuserscopeauser-scoped-credentials"></a><a name="userscope"></a>Credenziali con ambito di utente
Le credenziali con ambito di utente vengono create dall'utente di Azure e il loro mapping viene eseguito direttamente a un account Microsoft anziché a qualsiasi app specifica del Servizio app. Le credenziali di distribuzione con ambito di utente possono essere impostate o reimpostate dal [portale di Azure](https://portal.azure.com), in cui ogni app del Servizio app ha un punto di ingresso per la modifica in **DISTRIBUZIONE APP > Credenziali per la distribuzione**. A prescindere dal punto di ingresso, le modifiche apportate a queste credenziali con ambito di utente vengono applicate nell'intero account Microsoft. Queste credenziali vengono usate spesso per la distribuzione FTP e Git.

![Credenziali per la distribuzione del Servizio app di Azure](./media/app-service-deployment-credentials/deployment_credentials.png)
 
Quando si delega l'accesso alle risorse di Azure tramite controllo degli accessi in base al ruolo o autorizzazioni di coamministratore, ogni utente Azure che riceve l'accesso può usare le sue credenziali con ambito di utente fino a quando l'accesso non viene revocato. Queste credenziali di distribuzione non devono essere condivise con altri utenti di Azure.

## <a name="a-nameappscopeaapp-scoped-credentials"></a><a name="appscope"></a>Credenziali con ambito di app
Le credenziali con ambito di app vengono create automaticamente dalla piattaforma Servizio app. Vengono archiviate nel profilo di pubblicazione XML per ogni app del Servizio app. Il profilo di pubblicazione è disponibile nel [portale di Azure](https://portal.azure.com) tramite l'azione **Recupera profilo** nel pannello **Panoramica** dell'app. Queste credenziali vengono usate spesso per la distribuzione basata su WebDeploy. Possono inoltre essere usate per la distribuzione su FTP o GIT. Visual Studio, ovvero un punto di ingresso per la distribuzione basata su WebDeploy, è in grado di analizzare il profilo di pubblicazione per l'autenticazione.

![Profilo di pubblicazione di Servizio app di Azure](./media/app-service-deployment-credentials/publish_profile.png)

Quando si delega l'accesso alle risorse di Azure tramite le controllo degli accessi in base al ruolo o autorizzazioni di coamministratore, ogni utente Azure che riceve l'accesso può scaricare lo stesso profilo di pubblicazione specifico dell'app. Il profilo di pubblicazione può essere reimpostato in qualsiasi momento dal pannello **Panoramica** del Portale di Azure. La reimpostazione delle credenziali con ambito di app può essere una buona idea dopo la revoca dell'accesso delegato.



<!--HONumber=Nov16_HO4-->


