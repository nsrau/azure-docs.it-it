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
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876333"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Suggerimenti per la risoluzione dei problemi a supporto del servizio e del runtime di QnA Maker

QnAMaker comprende risorse ospitate nella sottoscrizione di Azure dell'utente. Il debug può richiedere agli utenti di modificare le risorse QnAMaker di Azure o fornire al team di supporto di QnAMaker informazioni aggiuntive sulla configurazione.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Come ottenere gli aggiornamenti del runtime di QnA Maker più recenti

Il runtime di QnAMaker fa parte del servizio app Azure distribuito quando si [Crea un servizio QnAMaker](./set-up-qnamaker-service-azure.md) in portale di Azure. Vengono applicati aggiornamenti periodici al runtime. Il servizio app di QnA Maker si trova in modalità di aggiornamento automatico dopo la versione dell'estensione del sito apr 2019 (versione 5 +). Questa operazione è già stata progettata in modo da occuparsi del tempo di inattività durante gli aggiornamenti. 

È possibile controllare la versione corrente in https://www.qnamaker.ai/UserSettings. Se la versione è precedente alla versione 5. x, è necessario riavviare il servizio app per applicare gli aggiornamenti più recenti.

1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Fare clic sul servizio app e aprire la sezione Panoramica

     ![Servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Riavviare il servizio app. Il riavvio dovrebbe richiedere un paio di secondi. Si noti che tutte le applicazioni dipendenti o i bot che usano questo servizio QnAMaker non saranno disponibili per gli utenti finali durante questo periodo di riavvio.

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Come ottenere il nome host del servizio QnA Maker
Il nome host del servizio QnA Maker è utile per scopi di debug quando si contatta il supporto di QnA Maker o UserVoice. Il nome host è un URL nel formato seguente: https:// *{hostname}* . azurewebsites.NET.
    
1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse Azure per QnA Maker nel portale di Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare il servizio app associato alla risorsa QnA Maker. In genere, i nomi sono gli stessi.

     ![Selezionare il servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. L'URL del nome host è disponibile nella sezione Panoramica

    ![Nome host di QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Migliorare le domande della knowledge base con l'apprendimento attivo](./improve-knowledge-base.md)
