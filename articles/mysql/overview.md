---
title: Panoramica del servizio di database relazionale Database di Azure per MySQL | Microsoft Docs
description: Panoramica del servizio di database relazionale Database di Azure per MySQL.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c8d75b0ce8bba4d0c065f86c199538b4a51f4bee
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---


# <a name="what-is-azure-database-for-mysql-service-introduction"></a>Database di Azure per MySQL: introduzione al servizio
Database di Azure per MySQL è un servizio di database relazionale in Microsoft Cloud basato sul motore di database [MySQL Community Edition](https://www.mysql.com/products/community/).  Database di Azure per MySQL offre:

- Prestazioni prevedibili a più livelli di servizio
- Scalabilità dinamica senza tempi di inattività dell'applicazione
- Disponibilità elevata predefinita
- Protezione dati

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Consentono di concentrarsi sullo sviluppo rapido di app e accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source scelti e a renderla disponibile con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze.

![Diagramma concettuale di Azure Database for MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Questo articolo è un'introduzione dei concetti fondamentali su Database di Azure per MySQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Per iniziare, vedere queste guide introduttive:
- [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- [Create an Azure Database for MySQL server using Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) (Creare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure)

Per un set di esempi dell'interfaccia della riga di comando di Azure, vedere:
- [Azure CLI samples for Azure Database for MySQL](sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per MySQL)

## <a name="adjust-performance-and-scale-without-downtime"></a>Regolare le prestazioni e scalabilità senza tempi di inattività
Il servizio Database di Azure per MySQL offre due livelli di servizio: Basic e Standard. Ogni livello offre diverse prestazioni e capacità per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi modificare il livello di servizio per ridimensionarlo in base alle esigenze della soluzione senza tempi di inattività. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
Come si conosce il giusto arresto quando si connette e si disconnette? Usare le funzionalità predefinite di monitoraggio delle prestazioni e avviso, combinate con le classificazioni delle prestazioni basate sull'unità di calcolo. Usando queste funzionalità, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione delle prestazioni in base alle esigenze correnti o previste relative alle prestazioni. Per informazioni dettagliate, vedere [Concepts: Service tiers](concepts-service-tiers.md) (Concetti: livelli di servizio).

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il Contratto di servizio per la disponibilità del 99,99% leader del settore di Azure, fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database di Azure per il server MySQL, è possibile sfruttare i vantaggi delle funzionalità di sicurezza, tolleranza di errore e protezione dei dati predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con Database di Azure per MySQL, è possibile usare un ripristino temporizzato per ripristinare uno stato precedente di un server, addirittura fino a 35 giorni prima.

## <a name="secure-your-data"></a>Protezione dei dati
I servizi di database di Azure vantano una tradizione di sicurezza dei dati rispettata anche da Database di Azure per MySQL con funzionalità che limitano l'accesso, proteggono i dati inattivi e in transito e consentono di monitorare l'attività. Visitare il [Centro protezione di Azure](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx) per informazioni sulla sicurezza della piattaforma Azure.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere letto l'introduzione a Database di Azure per MySQL e avere ottenuto informazioni su Database di Azure per MySQL, è possibile:
- Vedere la pagina relativa ai prezzi per confrontare i costi e usare i calcolatori. [Prezzi](https://azure.microsoft.com/pricing/details/mysql/)
- Per iniziare, creare il primo server. [Create an Azure Database for MySQL server using Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- Compilare la prima app in Python, PHP, Ruby, C\#, Java, Node.js: [Librerie di connettività usate per connettersi a Database di Azure per MySQL](concepts-connection-libraries.md)

