---
title: Guida introduttiva a StriIm
description: Inizia rapidamente a usare StriIm e Azure sinapsi Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d6b5fa1a42201479c5d426a5bbb9188d0cb8e03d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89485975"
---
# <a name="striim-azure-synapse-analytics-marketplace-offering-install-guide"></a>Guida all'installazione dell'offerta StriIm di Azure sinapsi Analytics Marketplace

Questa Guida introduttiva presuppone che sia già presente un'istanza preesistente di Azure sinapsi Analytics.

Cercare StriIm in Azure Marketplace e selezionare l'opzione StriIm per l'integrazione dei dati in Azure sinapsi Analytics (staging) 

![Installare Striim][install]

Configurare la macchina virtuale di Striim con le proprietà specificate, prendendo nota del nome del cluster Striim, della password e della password amministratore

![Configurare Striim][configure]

Al termine della distribuzione, fare clic su \<VM Name>-masternode nel portale di Azure, fare clic su Connetti e copiare l'account di accesso usando l'account locale della macchina virtuale 

![Connettere StriIm ad Azure sinapsi Analytics][connect]

Scaricare il file sqljdbc42.jar da <https://www.microsoft.com/en-us/download/details.aspx?id=54671> nel computer locale. 

Aprire una finestra della riga di comando e passare alla directory in cui è stato scaricato il file JDBC con estensione jar. Copiare il file JAR nella macchina virtuale Striim ottenendo l'indirizzo e la password dal portale di Azure

![Copia del file JAR nella macchina virtuale][copy-jar]

Aprire un'altra finestra della riga di comando o usare un'utilità SSH per stabilire una connessione SSH al cluster Striim

![Connessione SSH al cluster][ssh]

Eseguire i comandi seguenti per spostare il file JDBC con estensione JAR nella directory lib di Striim e avviare e arrestare il server.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Avviare il cluster Striim][start-striim]

Aprire ora un browser a scelta e passare a \<DNS Name>:9080

![Passare alla schermata di accesso][navigate]

Accedere con il nome utente e la password impostati nel portale di Azure e selezionare la procedura guidata preferita per iniziare subito oppure accedere alla pagina delle app per iniziare a usare l'interfaccia utente con funzione di trascinamento e rilascio

![Accedere con le credenziali del server][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
