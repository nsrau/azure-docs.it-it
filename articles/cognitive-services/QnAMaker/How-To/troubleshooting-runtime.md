---
title: Risoluzione dei problemi - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker è costituito da componenti ospitati nell'account Azure dell'utente. Ai fini del debug, può essere necessario che gli utenti modifichino le proprie risorse di Azure per QnA Maker o forniscano al team di supporto di QnA Maker informazioni aggiuntive sulla configurazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559919"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Suggerimenti per la risoluzione dei problemi a supporto del servizio e del runtime di QnA Maker
QnA Maker è costituito da componenti ospitati nell'account Azure dell'utente. Ai fini del debug, può essere necessario che gli utenti modifichino le proprie risorse di Azure per QnA Maker o forniscano al team di supporto di QnA Maker informazioni aggiuntive sulla configurazione.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Come ottenere gli aggiornamenti del runtime di QnA Maker più recenti
Il runtime di QnA Maker fa parte di Servizio app di Azure, distribuito durante la [creazione di un servizio QnA Maker](./set-up-qnamaker-service-azure.md) nel portale di Azure. Vengono applicati aggiornamenti periodici al runtime. Il servizio app QnA Maker è in modalità di aggiornamento automatico dopo la versione dell'estensione del sito apr 2019 (versione 5 +). Questa operazione è già stata progettata in modo da occuparsi del tempo di inattività durante gli aggiornamenti. È possibile controllare la versione corrente in https://www.qnamaker.ai/UserSettings. Se la versione è precedente alla versione 5. x, è necessario riavviare il servizio app per applicare gli aggiornamenti più recenti da applicare al programma di installazione di QnAMaker.

1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Fare clic sul servizio app e aprire la sezione Panoramica

     ![Servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Riavviare il servizio app. Il riavvio dovrebbe richiedere un paio di secondi. La build delle applicazioni e/o dei bot a valle in questo servizio QnA Maker non sarà disponibile agli utenti finali durante il riavvio.

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Come ottenere il nome host del servizio QnA Maker
Il nome host del servizio QnA Maker è utile per scopi di debug quando si contatta il supporto di QnA Maker o UserVoice. Il nome host è l'URL in questo formato: https:// *{nomehost}* .azurewebsites.net.
    
1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse Azure per QnA Maker nel portale di Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Fare clic sul servizio app

     ![Selezionare il servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. L'URL del nome host è disponibile nella sezione Panoramica

    ![Nome host di QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Migliorare le domande della knowledge base con l'apprendimento attivo](./improve-knowledge-base.md)
