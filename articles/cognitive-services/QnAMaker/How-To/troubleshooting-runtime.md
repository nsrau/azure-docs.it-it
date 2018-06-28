---
title: Risoluzione dei problemi relativi a QnA Maker | Microsoft Docs
titleSuffix: Azure
description: Come risolvere i problemi relativi al runtime di QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "35378773"
---
# <a name="qnamaker-troubleshooting"></a>Risoluzione dei problemi relativi a QnA Maker
QnA Maker è costituito da componenti ospitati nell'account Azure dell'utente. Ai fini del debug, può essere necessario che gli utenti modifichino le proprie risorse di Azure per QnA Maker o forniscano al team di supporto di QnA Maker informazioni aggiuntive sulla configurazione.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Come ottenere gli aggiornamenti del runtime di QnA Maker più recenti
Il runtime di QnA Maker fa parte di Servizio app di Azure, distribuito durante la [creazione di un servizio QnA Maker](./set-up-qnamaker-service-azure.md) nel portale di Azure. Vengono applicati aggiornamenti periodici al runtime. Per applicare gli aggiornamenti più recenti alla configurazione di QnA Maker, è necessario riavviare il servizio app.
1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Fare clic sul servizio app e aprire la sezione Panoramica

     ![Servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Riavviare il servizio app. Il riavvio dovrebbe richiedere un paio di secondi. La build delle applicazioni e/o dei bot a valle in questo servizio QnA Maker non sarà disponibile agli utenti finali durante il riavvio.

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Come ottenere il nome host del servizio QnA Maker
Il nome host del servizio QnA Maker è utile per scopi di debug quando si contatta il supporto di QnA Maker o UserVoice. Il nome host è l'URL in questo formato: https://*{nomehost}*.azurewebsites.net.
    
1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Fare clic sul servizio app

     ![Servizio app QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. L'URL del nome host è disponibile nella sezione Panoramica

    ![Nome host di QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare l'API QnA Maker](./upgrade-qnamaker-service.md)
