---
title: Panoramica dello sviluppo di applicazioni di database per il database di Azure per MySQL | Documentazione Microsoft
description: Introduce aspetti di progettazione che lo sviluppatore deve considerare quando scrive il codice dell'applicazione per la connessione al database di Azure per MySQL
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 6a9bd8f88383b5186e470163bc67f9233172fd49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Panoramica dello sviluppo di applicazioni per il database di Azure per MySQL 
Questo articolo tratta alcuni aspetti di progettazione che lo sviluppatore deve considerare quando scrive il codice dell'applicazione per la connessione a Database di Azure per MySQL. 

> [!TIP]
> Per un'esercitazione che mostra come creare un server, creare un firewall basato su server, visualizzare le proprietà del server, creare il database, connettersi ed eseguire query usando Workbench e mysql.exe, vedere [Progettare il primo database di Azure per il database MySQL](tutorial-design-database-using-portal.md).

## <a name="language-and-platform"></a>Linguaggio e piattaforma
Sono disponibili esempi di codice per svariati linguaggi di programmazione e piattaforme. È possibile trovare collegamenti a esempi di codice in: [Connectivity libraries used to connect to Azure Database for MySQL](concepts-connection-libraries.md) (Raccolte connessioni utilizzate per connettersi al database di Azure per MySQL)

## <a name="tools"></a>Strumenti
Il database di Azure per MySQL usa la versione community di MySQL, compatibile con comuni strumenti di gestione di MySQL quali Workbench o utilità di MySQL come mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql) e altre. È possibile anche usare il portale di Azure, l'interfaccia della riga di comando di Azure e le API REST per interagire con il servizio di database.

## <a name="resource-limitations"></a>Limiti delle risorse
Database di Azure per MySQL gestisce le risorse disponibili per un server usando due diversi meccanismi: 
- Governance delle risorse.
- Imposizione di limiti.

## <a name="security"></a>Sicurezza
Database di Azure per MySQL fornisce risorse per limitare l'accesso, proteggere i dati, configurare gli utenti e i ruoli e monitorare le attività in un database MySQL.

## <a name="authentication"></a>Autenticazione
Database di Azure per MySQL supporta l'autenticazione server degli utenti e degli account di accesso.

## <a name="resiliency"></a>Resilienza
Quando si verifica un errore temporaneo durante la connessione a un database MySQL, il codice deve ritentare la chiamata. Per la ripetizione dei tentativi si consiglia di usare una logica backoff, in modo da non sovraccaricare il database SQL con più client che ripetono i tentativi contemporaneamente.

- Esempi di codice: per alcuni esempi di codice che illustrano la logica di ripetizione dei tentativi, vedere gli esempi del linguaggio in uso in [Connectivity libraries used to connect to Azure Database for MySQL](concepts-connection-libraries.md) (Raccolte connessioni utilizzate per connettersi al database di Azure per MySQL)

## <a name="managing-connections"></a>Gestione delle connessioni
Le connessioni di database sono una risorsa limitata, pertanto si consiglia di usare in modo ragionevole le connessioni quando si accede al database MySQL per ottenere prestazioni migliori.
- Accedere al database usando il pool di connessioni o connessioni permanenti.
- Accedere al database tramite una connessione di breve durata. 
- Usare la logica di ripetizione tentativi nell'applicazione al momento del tentativo di connessione, per rilevare gli errori causati dal raggiungimento del massimo livello di connessioni simultanee consentito. Nella logica di ripetizione impostare un breve ritardo e quindi attendere un tempo casuale prima di eseguire altri tentativi di connessione.